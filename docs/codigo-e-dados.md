# Código e modelo de dados

[Início](../README.md) · [Funcionalidades](funcionalidades.md) · [Arquitetura](arquitetura.md) · [Stack](stack.md) · [Código e dados](codigo-e-dados.md) · [Dados e sincronização](dados-e-sincronizacao.md) · [Métricas](metricas.md) · [Segurança](seguranca.md) · [Operação](operacao.md) · [Versões](versoes.md)

O código-fonte é privado. Esta página descreve **como ele está organizado**, sem expor configurações nem dados.

## Organização do código

| Pasta | Conteúdo |
|---|---|
| `server/` | Tudo que roda no servidor: sincronizador, importadores, validação, banco, API, autenticação e backup |
| `src/` | A interface em React e TypeScript |
| `scripts/` | Ferramentas de manutenção: homologação, diagnósticos e migrações |
| `docker/` | Dockerfiles e configuração do nginx |

## Módulos do servidor

| Módulo | Responsabilidade |
|---|---|
| Worker | Agenda a checagem a cada minuto, a validação a cada hora e o backup diário |
| Sincronização | Lista a pasta do Drive, decide se a planilha mudou, baixa e chama o importador |
| Importador geral | Lê abas de mês, colunas obrigatórias, datas e códigos da planilha geral |
| Importador do parceiro | Lê aprovação, RMA, prazos, nota fiscal e coleta |
| Alterações | Compara a leitura nova com a anterior: novos, alterados campo a campo e removidos |
| Editores do Drive | Consulta no histórico de versões quem salvou o arquivo |
| Validação | Relê a planilha com código independente e compara com o banco |
| Banco | Cria as tabelas, grava em transação e faz as consultas |
| API | Rotas, proteção anti-CSRF e filtro por loja |
| Autenticação | Usuários, senhas, sessões, bloqueio por tentativas e auditoria |
| Backup | Cópia diária verificada e retenção de 7 dias |
| Configuração | Lê do ambiente os nomes configuráveis, para o código não carregar nomes da empresa |

## Módulos da interface

| Módulo | Responsabilidade |
|---|---|
| App | Estrutura da tela: menu, avisos, filtros gerais, cards, gráficos e rankings |
| Dados | Regras de cálculo: dias em aberto, prioridades, agrupamentos e rankings |
| Componentes | Peças reaproveitadas: card, ranking, tabelas, ajuda |
| Filtros da lista | Busca e filtros por coluna da rastreabilidade |
| Detalhe | Painel que abre ao clicar em card, gráfico ou ranking |
| Mudanças | Selo "Mudou" no CRM e a janela com o que mudou |
| Alterações, Pendências, Instruções, Administração | Uma tela cada |

## Modelo de dados

Um único arquivo SQLite. **Nenhuma tabela apaga processos**: um processo que sai da planilha é marcado como ausente e mantém o histórico.

| Tabela | O que guarda |
|---|---|
| Garantias | Um registro por processo, com os dados da última leitura, a data de solução detectada e a marca de ausente |
| Histórico | Toda criação ou mudança de cada processo |
| Alterações | Uma linha por leitura com mudanças: origem, data, quem salvou e totais |
| Itens de alteração | Cada processo que mudou, com as colunas e os valores antigo e novo |
| Sincronizações | Cada leitura, validação e backup, com status e mensagem |
| Estado | Última checagem de cada planilha: data e tamanho do arquivo no Drive |
| Pedidos de sincronização | Pedidos do botão "Sincronizar agora" |
| Usuários, sessões e auditoria | Acesso ao sistema; senhas e tokens guardados só como hash |

## Rotas da API, por grupo

| Grupo | O que faz | Quem acessa |
|---|---|---|
| Autenticação | Entrar, sair, dados do usuário, troca de senha | Todos |
| Garantias | Processos de cada planilha, já filtrados pelas lojas do usuário | Usuários logados |
| Status | Versão dos dados (a tela consulta a cada 20 s) e situação da última leitura | Usuários logados |
| Alterações | Histórico de mudanças das planilhas | Usuários logados |
| Administração | Usuários, senhas, sessões, sincronização imediata, histórico e auditoria | Administradores |
| Saúde | Diz se a API está viva | Docker |
