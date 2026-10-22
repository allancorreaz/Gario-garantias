# Segurança

[← Voltar ao início](../README.md)

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

## Roteiro de homologação

Cada atualização passa por um roteiro automatizado antes de entrar no ar:

1. **Dependências:** auditoria das bibliotecas usadas.
2. **Sincronização:** testes de leitura das planilhas, incluindo arquivo inexistente e planilha sem mudanças.
3. **Validação:** conferência completa planilha × banco.
4. **Compilação:** build da interface com checagem de tipos.
5. **Containers:** construção e subida.
6. **Acesso e segurança:** mais de 25 testes de login, CSRF, isolamento por loja, troca de senha, desativação e bloqueio por tentativas.
7. **Backup:** backup do banco.
8. **Outros sistemas:** conferência de que os outros sistemas do servidor continuaram funcionando.
