# Segurança

[Início](../README.md) · [Funcionalidades](funcionalidades.md) · [Arquitetura](arquitetura.md) · [Stack](stack.md) · [Código e dados](codigo-e-dados.md) · [Dados e sincronização](dados-e-sincronizacao.md) · [Métricas](metricas.md) · [Segurança](seguranca.md) · [Operação](operacao.md) · [Versões](versoes.md)

| Camada | Proteção |
|---|---|
| **Planilhas** | Acesso ao Google Drive apenas para leitura, conferido antes de cada leitura. O sincronizador se recusa a rodar se a permissão mudar. |
| **Senhas** | Hash scrypt com sal individual e comparação em tempo constante. Exigência de tamanho mínimo, letras e números. Senha inicial definida pelo administrador e troca obrigatória no primeiro acesso. |
| **Sessão** | Token aleatório de 32 bytes em cookie HttpOnly e SameSite=Strict. O banco guarda só o hash do token. A sessão expira em 8 horas e é encerrada na troca de senha ou na desativação do usuário. |
| **CSRF** | Cabeçalho próprio obrigatório nas requisições e conferência da origem. |
| **Força bruta** | Bloqueio temporário após falhas repetidas, por usuário e por IP. Limite de requisições no nginx. Mesma resposta para usuário inexistente e senha errada. |
| **Autorização** | Perfis de administrador e operador. Filtro por loja aplicado no servidor. Sempre existe pelo menos um administrador ativo. |
| **Dados pessoais** | Os nomes de clientes que estão na planilha não saem da API. |
| **Cabeçalhos HTTP** | Content Security Policy, proteção contra clickjacking e contra detecção de tipo. |
| **Containers** | Usuários sem privilégios, sistema de arquivos somente leitura, nenhuma capacidade extra do Linux (`cap_drop: ALL`), `no-new-privileges`, limite de memória e de log. A API não tem porta publicada. |
| **Segredos** | Credenciais, nomes da empresa e configurações ficam fora do código, só no servidor. A autorização do Drive fica apenas no container de sincronização e nunca é exibida. |
| **Dependências** | Auditoria de vulnerabilidades no roteiro de homologação. |
| **Auditoria** | Logins, tentativas recusadas ou bloqueadas, trocas de senha, alterações de usuários e sincronizações pedidas, com data, usuário e IP. |

## Backup fora do servidor

As planilhas permitem recriar os processos, mas não os usuários e senhas, a auditoria, o histórico de alterações e as datas de solução detectadas. Por isso cada backup diário pode ganhar uma cópia numa pasta do Google Drive:

| Proteção | Como funciona |
|---|---|
| **Acesso separado** | Um segundo acesso ao Drive, autorizado só com o escopo `drive.file`: o Gario enxerga e apaga apenas os arquivos que ele mesmo criou. O acesso das planilhas continua somente leitura, e o sistema se recusa a enviar se os escopos não estiverem assim |
| **Criptografia** | AES-256-GCM, com chave derivada por scrypt. Sem a chave, guardada fora do servidor, o arquivo no Drive não abre; qualquer alteração no arquivo é detectada |
| **Retenção** | 30 dias no Drive, além dos 7 dias no servidor |
| **Falha isolada** | Se o envio falhar, o backup local continua valendo e o aviso aparece na Administração |

## Roteiro de homologação

Cada atualização passa por um roteiro automatizado antes de entrar no ar:

1. **Dependências:** auditoria das bibliotecas usadas.
2. **Sincronização:** testes de leitura das planilhas, incluindo arquivo inexistente e planilha sem mudanças.
3. **Validação:** conferência completa planilha × banco.
4. **Testes automáticos e compilação:** regras de prazo, filtros e criptografia do backup; depois, build da interface com checagem de tipos.
5. **Containers:** construção e subida.
6. **Acesso e segurança:** mais de 25 testes de login, CSRF, isolamento por loja, troca de senha, desativação e bloqueio por tentativas.
7. **Backup:** backup do banco.
8. **Limpeza:** remoção só das imagens antigas do próprio Gario, com o uso do disco.
9. **Outros sistemas:** conferência de que os outros sistemas do servidor continuaram funcionando.
