# Arquitetura

[← Voltar ao início](../README.md)

## Visão geral

```mermaid
flowchart LR
    subgraph Drive["Google Drive"]
        P1[("Planilha geral<br/>uma aba por mês")]
        P2[("Planilha do parceiro<br/>aba principal")]
    end

    subgraph Servidor["Servidor interno · Docker Compose · rede isolada"]
        SYNC["Sincronizador<br/>Node.js + rclone<br/>verificação a cada minuto"]
        DB[("SQLite<br/>modo WAL")]
        BK[("Backups diários<br/>7 dias")]
        API["API<br/>Node.js"]
        WEB["Web<br/>nginx sem privilégios<br/>React compilado"]
    end

    U["Navegador"]

    P1 -- "somente leitura" --> SYNC
    P2 -- "somente leitura" --> SYNC
    SYNC -- "grava em transação" --> DB
    SYNC -- "cópia verificada" --> BK
    API -- "lê e filtra por loja" --> DB
    WEB -- "proxy /api" --> API
    U -- "HTTP" --> WEB
```

| Container | Papel | Exposto na rede | Acesso ao Drive |
|---|---|---|---|
| **Web** | Serve a interface e repassa as chamadas da API | Sim, é o único | Não |
| **API** | Login, sessões, usuários, consulta das garantias | Não, só na rede interna | Não |
| **Sincronizador** | Lê as planilhas, valida, grava, confere e faz backup | Não | Sim, somente leitura |

- **Mesma imagem:** a API e o sincronizador usam a mesma imagem, com comandos diferentes.
- **Credencial do Drive:** fica apenas no sincronizador.
- **Pedido de leitura:** o botão "Sincronizar agora" não dá à API acesso ao Drive. A API grava um pedido no banco e o sincronizador atende em segundos.

## Fluxo da sincronização

```mermaid
sequenceDiagram
    autonumber
    participant T as Agendador (1 min)
    participant S as Sincronizador
    participant D as Google Drive
    participant I as Importador
    participant B as Banco

    T->>S: hora de verificar
    S->>S: confere que o acesso é somente leitura
    S->>D: lista a pasta (data e tamanho dos arquivos)
    alt planilha não mudou
        S->>B: registra só a verificação
    else planilha mudou
        S->>D: baixa uma cópia para arquivo temporário
        S->>S: confere que é um .xlsx válido
        S->>I: lê abas e cabeçalhos
        I->>I: valida colunas obrigatórias, normaliza datas e códigos
        S->>B: BEGIN
        S->>B: compara a cobertura com a leitura anterior
        alt planilha incompleta (aba vazia ou queda acima de 20%)
            S->>B: ROLLBACK e registra o erro
        else leitura coerente
            S->>B: grava processos, histórico e alterações
            S->>B: COMMIT
            S->>D: consulta quem salvou o arquivo (histórico de versões)
            S->>S: validação independente planilha × banco
        end
    end
```

- **Leitura só quando muda:** a cada minuto o sincronizador faz **uma única consulta de metadados** e só baixa o arquivo que mudou. Isso reduz o uso da API do Google e evita limites de requisição.
- **Limite de requisições:** se o Google pedir para diminuir o ritmo, a rodada é adiada por alguns minutos, sem contar como falha.
- **Tela sempre atual:** a tela consulta a cada 20 segundos uma "versão" dos dados, uma consulta leve, e só recarrega quando há importação nova.

## Fluxo de login

```mermaid
sequenceDiagram
    autonumber
    participant N as Navegador
    participant W as Web (nginx)
    participant A as API
    participant B as Banco

    N->>W: envia usuário e senha
    W->>W: limite de requisições por IP
    W->>A: repassa
    A->>A: confere proteção anti-CSRF e origem
    A->>A: bloqueado por tentativas repetidas?
    A->>B: busca o usuário
    A->>A: scrypt + comparação em tempo constante
    alt senha incorreta
        A->>B: auditoria de falha
        A-->>N: mensagem genérica
    else senha correta
        A->>B: grava o hash do token de sessão (expira em 8 h)
        A-->>N: cookie HttpOnly, SameSite=Strict
    end
```

## Consulta com isolamento por loja

```mermaid
flowchart LR
    R["Pedido de dados"] --> S{"Sessão válida?"}
    S -- não --> X["401"]
    S -- sim --> T{"Troca de senha pendente?"}
    T -- sim --> Y["403"]
    T -- não --> P{"Perfil"}
    P -- administrador --> TODAS["Todas as lojas"]
    P -- operador --> LOJAS["Só as lojas liberadas"]
    TODAS --> F["Remove dados pessoais do cliente"]
    LOJAS --> F
    F --> OK["Resposta"]
```

O filtro por loja é aplicado **no servidor**: um operador não consegue ver outra loja nem mexendo na URL ou na requisição. Apelidos de uma mesma loja são unificados.

## Banco de dados

| Tabela | Conteúdo |
|---|---|
| Garantias | Um registro por processo, com os dados da última leitura |
| Histórico | Cada criação ou alteração de processo |
| Alterações | O que mudou em cada leitura: novos, alterados campo a campo, removidos e quem salvou |
| Sincronizações | Execuções de leitura, validação e backup |
| Estado | Última verificação de cada planilha |
| Usuários, sessões e auditoria | Acesso ao sistema |

Nada é apagado: um processo que sai da planilha é marcado como ausente, some da tela e continua no banco com o histórico.
