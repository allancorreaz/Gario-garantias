# Stack: tecnologias e por que foram escolhidas

[Início](../README.md) · [Funcionalidades](funcionalidades.md) · [Arquitetura](arquitetura.md) · [Stack](stack.md) · [Código e dados](codigo-e-dados.md) · [Dados e sincronização](dados-e-sincronizacao.md) · [Métricas](metricas.md) · [Segurança](seguranca.md) · [Operação](operacao.md)

O Gario foi construído só com tecnologias **gratuitas, maduras e leves**, para rodar num servidor interno modesto sem custo de licença.

## Visão rápida

| Camada | Tecnologia | Versão |
|---|---|---|
| Linguagem da interface | TypeScript | 6 |
| Biblioteca da interface | React | 19 |
| Empacotador | Vite | 8 |
| Gráficos | Recharts | 3 |
| Ícones | lucide-react | 1 |
| API | Node.js (módulo `node:http`, sem framework) | 22 |
| Banco de dados | SQLite com better-sqlite3, modo WAL | — |
| Leitura de Excel | read-excel-file | 9 |
| Acesso ao Google Drive | rclone (somente leitura) | — |
| Servidor web | nginx sem privilégios | 1.27 |
| Infraestrutura | Docker e Docker Compose | — |
| Qualidade | ESLint, checagem de tipos, testes próprios | — |

## O papel de cada peça

### Interface (o que o usuário vê)

- **React 19:** monta as telas em componentes reaproveitáveis. O mesmo componente de tabela, por exemplo, serve à Visão gerencial, às Pendências e aos painéis de detalhe.
- **TypeScript:** cada dado tem um tipo definido. Um campo com nome errado, ou uma data tratada como número, nem chega a compilar; o erro aparece antes de o sistema subir.
- **Vite:** transforma o código em arquivos otimizados para o navegador. O build leva menos de um segundo.
- **Recharts:** desenha o gráfico mensal de aberturas e resoluções. Cada mês é clicável.
- **Cálculos no navegador:** a API entrega os processos já filtrados por loja, e os indicadores, rankings e filtros são calculados na própria tela. Por isso trocar um filtro é instantâneo, sem nova consulta ao servidor.

### API (o que entrega os dados)

- **Node.js com `node:http` nativo:** a API não usa framework. Menos dependências significam menos código de terceiros que possa ter falhas de segurança, uma preocupação real depois dos ataques recentes à cadeia de pacotes do ecossistema JavaScript.
- **Rotas próprias:** login, sessão, troca de senha, garantias por origem, status da sincronização, alterações e administração.

### Dados

- **SQLite:** o banco inteiro é um único arquivo. Não há servidor de banco para instalar, atualizar ou proteger, e o backup é a cópia verificada desse arquivo.
- **Modo WAL:** permite que a tela continue lendo enquanto o sincronizador grava, sem travar ninguém.
- **read-excel-file:** lê o `.xlsx` como dados puros, sem executar macros nem fórmulas.

### Integração com o Google Drive

- **rclone:** lista a pasta e baixa as planilhas. A autorização é concedida só com o escopo de leitura (`drive.readonly`), e o sincronizador confere isso antes de cada leitura.
- **Histórico de versões do Drive:** consultado só para leitura, para mostrar quem salvou o arquivo.

### Infraestrutura

- **Docker Compose:** sobe três containers (web, API e sincronizador) numa rede isolada, sem interferir nos outros sistemas do servidor.
- **nginx sem privilégios:** entrega a interface, repassa as chamadas da API, limita requisições e aplica os cabeçalhos de segurança.
- **Containers endurecidos:** usuário sem privilégios, sistema de arquivos somente leitura e nenhuma capacidade extra do Linux.

### Qualidade

- **ESLint e checagem de tipos** em todo build.
- **Testes próprios** de sincronização, de autenticação e de segurança.
- **Validação contínua** planilha × banco, com código independente do importador.
- **Roteiro de homologação** que roda tudo isso antes de cada atualização entrar no ar (veja [Operação](operacao.md)).
