# Dados e sincronização

[← Voltar ao início](../README.md)

## Regras que protegem os dados

1. **Somente leitura nas planilhas.** A autorização do Google Drive tem escopo apenas de leitura, e o sincronizador confere isso antes de cada leitura. Se a permissão mudar, ele se recusa a rodar. O próprio Google recusa qualquer escrita.
2. **Download validado.** O arquivo novo só substitui o anterior depois de confirmado que é uma planilha válida.
3. **Estrutura validada.** Se uma aba de mês existente estiver sem cabeçalho ou com coluna obrigatória faltando, a leitura inteira é cancelada.
4. **Cobertura validada.** Uma planilha cortada não consegue "apagar" processos. Se uma aba que tinha registros vier vazia, se uma aba cair pela metade ou se o total cair mais de 20%, nada é gravado.
5. **Transação única.** Ou a leitura inteira entra, ou nada entra.
6. **Nada é apagado.** Um processo que sai da planilha fica marcado como ausente, com o histórico preservado.
7. **Códigos protegidos.** Um código de produto que o Excel mostra como data é recuperado pelo valor original da célula, sem adivinhação.

## Validação contínua

Depois de cada importação e a cada hora, o sincronizador relê as planilhas com **uma leitura independente do importador**, ou seja, outro código, e compara linha a linha com o banco: CRM, loja, datas, fornecedor, produto, situação, aprovação, RMA e status de resolução.

- **Sem diferença:** o painel informa a hora da última conferência.
- **Com diferença:** todos os usuários veem um aviso, e os administradores veem também os exemplos das divergências.

## Alterações nas planilhas

A cada leitura com mudanças, o Gario compara a versão nova com a anterior:

| Tipo | Como é identificado |
|---|---|
| **Novo** | Processo que não existia na leitura anterior |
| **Alterado** | Mesmo processo com alguma coluna diferente. Mostra cada coluna, com o valor antigo e o novo |
| **Removido** | Processo que existia e saiu da planilha |

- **O que fica de fora:** colunas que mudam sozinhas todo dia, como uma fórmula de "dias em aberto", e os dados pessoais dos clientes.
- **Quem salvou:** vem do histórico de versões do arquivo no Google Drive, consultado apenas para leitura. Como o Drive não informa quem mudou cada linha, aparecem todas as pessoas que salvaram o arquivo no período.

## Data de solução

As planilhas não tinham uma coluna com a data em que o processo foi resolvido. O Gario resolve isso assim:

1. **Coluna própria:** se a planilha tiver uma coluna de data de resolução, ela tem prioridade.
2. **Mudança de status:** sem essa coluna, vale o **dia em que a coluna RESOLVIDAS mudou de NÃO para SIM**. Como a planilha é conferida a cada minuto, essa data é registrada no mesmo dia. Essas datas aparecem com asterisco e aviso de possível divergência.
3. **Processos antigos:** os que já estavam resolvidos antes do início do acompanhamento ficam sem data, porque o sistema não inventa datas.

Com isso o painel mostra os **dias até resolver** e o gráfico ganha a linha **resolvidos no mês**, que só aparece a partir do primeiro mês com dados, para não confundir.

## Prazos e alertas

| Regra | Como funciona |
|---|---|
| Dias em aberto | Da data de abertura até hoje; para resolvidos, até a data de solução |
| Prioridade | No prazo até 30 dias; atrasado de 31 a 60; alto de 61 a 90; crítico acima de 90 |
| Aprovação interna | Alerta quando o processo do parceiro passa de 1 dia sem aprovação |
| Etapa no fornecedor | Alerta acima de 30 dias depois da aprovação sem a peça chegar na loja |
| Inconsistência | Marcado como resolvido, mas a situação ainda indica cliente aguardando |

## Backups

- **Quando:** um backup por dia, de madrugada, numa pasta própria.
- **Verificação:** cada cópia passa por verificação de integridade e é salva como arquivo único, com permissões restritas.
- **Retenção:** 7 dias, sempre mantendo os 7 mais recentes.
- **Histórico:** o resultado de cada backup aparece na tela de administração.
