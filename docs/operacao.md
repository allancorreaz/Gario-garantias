# Operação e manutenção

[Início](../README.md) · [Funcionalidades](funcionalidades.md) · [Arquitetura](arquitetura.md) · [Stack](stack.md) · [Código e dados](codigo-e-dados.md) · [Dados e sincronização](dados-e-sincronizacao.md) · [Métricas](metricas.md) · [Segurança](seguranca.md) · [Operação](operacao.md) · [Versões](versoes.md)

Os **dados** se atualizam sozinhos. O **código** só muda quando o TI roda a atualização. Nenhum dos comandos abaixo mostra senhas ou tokens.

**Nesta página:** [Atualizar o sistema](#atualizar-o-sistema) · [Homologação](#o-que-a-homologação-confere) · [Planilha renomeada](#quando-a-planilha-for-renomeada) · [Aba ou planilha nova](#quando-surgir-uma-aba-ou-uma-planilha-nova) · [Problemas comuns](#problemas-comuns) · [Backups](#backups)

## Atualizar o sistema

Na pasta do Gario, no servidor:

```bash
git fetch origin && git reset --hard origin/main
bash scripts/homologacao.sh
```

O repositório do código é privado. O servidor acessa com uma **chave de deploy**: uma chave SSH que só lê este repositório, sem senha a cada atualização e que pode ser revogada no GitHub a qualquer momento. Sem a chave, o `fetch` pede um token de leitura. Depois, **Ctrl+F5** no navegador.

## O que a homologação confere

1. Auditoria de vulnerabilidades das dependências.
2. Testes da sincronização, incluindo arquivo inexistente e planilha sem mudança.
3. Validação completa planilha × banco.
4. Testes automáticos das regras de prazo, dos filtros e da criptografia do backup; se algum falhar, nada sobe.
5. Compilação da interface com checagem de tipos.
6. Construção e subida dos containers.
7. Mais de 25 testes de login, CSRF, isolamento por loja e bloqueio por tentativas.
8. Backup do banco.
9. Limpeza só das imagens antigas do Gario e uso do disco.
10. Conferência de que os outros sistemas do servidor continuam de pé.

## Quando a planilha for renomeada

O Gario acha cada planilha pelo **nome exato do arquivo** no Drive. Se alguém renomear, a leitura para e todos veem o aviso de falha, mas **nenhum dado se perde**: a tela continua com a última leitura boa.

1. Ver os nomes configurados: `grep -E '^GARIO_(RCLONE_REMOTE|DRIVE_GERAIS|DRIVE_PARCEIRO)=' .env`
2. Listar os arquivos da pasta, usando o valor de `GARIO_RCLONE_REMOTE` (ele já inclui a pasta): `rclone lsf "<valor de GARIO_RCLONE_REMOTE>"`
3. Fazer uma cópia do `.env` (`cp .env .env.bak-$(date +%F)`) e trocar, num editor (`vi .env`), o texto entre aspas de `GARIO_DRIVE_GERAIS` ou `GARIO_DRIVE_PARCEIRO` pelo nome novo, idêntico ao da lista: maiúsculas, acentos, espaços e `.xlsx`.
4. Recriar os containers: `docker compose up -d sync api web`
5. Conferir: `docker compose logs --tail=20 sync` deve mostrar `SUCESSO`.

> **Por que `up -d` e não `restart`:** o `restart` reaproveita o container antigo e não relê o `.env`. E o `web` entra junto porque o nginx guarda o endereço interno da API; se só a API for recriada, o login responde **HTTP 502**.

## Quando surgir uma aba ou uma planilha nova

| Situação | O que fazer |
|---|---|
| Aba de mês nova na planilha geral | Nada: entra sozinha na próxima leitura |
| Aba com outro nome na planilha geral | Fica de fora de propósito; para entrar, precisa de mudança no código |
| Planilha substituída por outra de mesma estrutura (ex.: a do ano seguinte) | Trocar o nome no `.env`, como na seção anterior |
| Planilha nova, com outro formato | Exige desenvolvimento: importador próprio, nova origem no banco, validação e telas |
| Coluna obrigatória renomeada ou apagada | A leitura é cancelada e o banco fica intacto; voltar o nome da coluna ou ajustar o código |

## Problemas comuns

| Sintoma | Causa provável | O que fazer |
|---|---|---|
| Login responde "Falha na comunicação (HTTP 502)" | A API foi recriada e o nginx ficou com o endereço antigo | `docker compose restart web` |
| Aviso de falha na leitura para todos | Planilha renomeada, movida ou com coluna faltando | Ver o log do sync e seguir a seção da planilha renomeada |
| Aviso de divergência planilha × banco | Leitura interrompida ou planilha mudando durante a leitura | Sincronizar agora; se continuar, ver Administração → Sincronizações |
| Disco do servidor cheio | Cache de build do Docker acumulado | A homologação já limpa as imagens antigas do Gario. O cache de build é compartilhado com outros projetos do servidor: limpe só combinando com os responsáveis |
| GitHub recusa o login no servidor | Token de leitura expirado | Gerar um novo token, só com leitura do repositório |
| Google pede para diminuir o ritmo | Limite de requisições do Drive | Nada: a leitura é adiada 5 minutos e retoma sozinha |
| Número diferente na tela | Planilha preenchida diferente do esperado | Conferir a planilha; se estiver certa, avisar o TI |

## Backups

- **Quando:** um por dia, de madrugada, além de um a cada homologação.
- **Verificação:** cada cópia passa por checagem de integridade.
- **Retenção:** 7 dias, sempre mantendo os mais recentes.
- **Cópia no Drive (opcional):** criptografada, em pasta própria, com 30 dias de retenção (veja [Segurança](seguranca.md)).
- **Onde ver:** o resultado aparece em Administração.
