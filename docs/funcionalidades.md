# Funcionalidades

[← Voltar ao início](../README.md)

Todas as imagens usam **dados fictícios**.

---

## 1. Acesso

![Tela de acesso](../imagens/01-login.png)

- **Contas:** cada pessoa tem login próprio, criado pelo administrador. No primeiro acesso, a troca da senha é obrigatória.
- **Tentativas de senha:** repetições seguidas bloqueiam o login temporariamente. A mensagem de erro é sempre a mesma, para não revelar se o usuário existe.
- **Fase do sistema:** o aviso de período de testes deixa claro que o sistema ainda está em homologação.

## 2. Visão gerencial

![Visão gerencial](../imagens/02-visao-gerencial.png)

- **Indicadores:** processos registrados, resolvidos, não resolvidos e taxa de resolução, sempre respeitando os filtros por mês, produto e fornecedor.
- **Aviso de atualização:** quando alguém salva a planilha com mudanças, aparece um aviso verde para todos ("Planilha atualizada às 14:45 por Fulana: 3 novos, 6 alterados"), com o botão **Ver o que mudou**.
- **Aviso de falha:** se a leitura de uma planilha falhar, todos veem um aviso vermelho de que os números podem estar desatualizados.

![Gráficos e rankings](../imagens/03-graficos-e-rankings.png)

- **Aberturas e resoluções por mês:** a linha laranja mostra quantos processos foram abertos no mês, e a azul quantos desses já estão resolvidos hoje. A linha verde (resolvidos no mês) aparece a partir do primeiro mês em que existe data de solução registrada.
- **Clique no gráfico:** abre os processos do mês em abas: abertos, já resolvidos, ainda em aberto e resolvidos naquele mês.
- **Rankings:** produtos, fornecedores, lojas, maior demora de retorno, procedentes e improcedentes. Todo item é clicável, e todo ranking tem o botão **Ver ranking completo**.

## 3. Detalhamento com um clique

![Painel de detalhe](../imagens/04-painel-de-detalhe.png)

Qualquer indicador, item de ranking, faixa de atraso ou mês do gráfico abre um painel com **todos os processos daquele número**:

- **Pesquisa e exportação:** busca por CRM, produto, código, fornecedor, loja ou situação, e exportação em CSV.
- **Ficha completa:** ao clicar num processo, mostra datas, produto, fornecedor, situação, observações, sinais de atenção e, para o fornecedor parceiro, todas as etapas.
- **Tamanho do painel:** abre centralizado e pode ir para tela cheia. No celular, ocupa a tela inteira e vira cartões.
- **Campo vazio:** aparece como "—" com um ícone de ajuda, que orienta a conferir a planilha e avisar o TI se lá estiver preenchido.

## 4. Pendências e alertas

![Pendências e alertas](../imagens/05-pendencias-e-alertas.png)

Tudo o que ainda não foi resolvido, do mais antigo para o mais recente:

- **Indicadores:** não resolvidos, acima de 30 dias (atrasados), acima de 90 dias (críticos), sem classificação e inconsistências. Cada um abre a lista.
- **Tempo em aberto:** faixas clicáveis de 30, 60 e 90 dias.
- **Pendências por loja e por fornecedor.**
- **Sinais de atenção:** por exemplo, prazo após o RMA vencido, aguardando aprovação interna, mais de 30 dias no fornecedor ou resolvido com a situação ainda indicando cliente aguardando.

![Maior demora de retorno](../imagens/06-maior-demora-de-retorno.png)

**Maior demora de retorno.** Média de dias em aberto dos processos não resolvidos, por fornecedor, loja e produto, além da lista dos processos há mais tempo sem retorno. Cada item mostra quantos processos estão em aberto e há quantos dias está o mais antigo.

![Ranking completo](../imagens/07-ranking-completo.png)

## 5. Etapas do fornecedor parceiro

![Etapas do parceiro](../imagens/08-etapas-do-parceiro.png)

A planilha do parceiro tem um fluxo próprio. O Gario acompanha:

- **Aprovação interna:** média de dias, processos aguardando a aprovação e a maior espera.
- **Etapa no fornecedor:** do OK até a peça voltar para a loja, com alerta acima de 30 dias.
- **Acompanhamento das etapas:** RMA, prazo após o RMA, nota fiscal, coleta e chegada na loja, como estão na planilha.

## 6. Alterações nas planilhas

![Alterações](../imagens/09-alteracoes-nas-planilhas.png)

A cada atualização da planilha, o Gario compara com a leitura anterior e registra:

- **Novo:** processos que entraram.
- **Alterado:** cada coluna que mudou, com o valor antigo riscado e o novo em destaque.
- **Removido:** processos que saíram da planilha.
- **Quem salvou:** quem salvou o arquivo no Google Drive no período, lido do histórico de versões do arquivo.

Cada pessoa vê apenas as alterações das lojas liberadas para ela.

## 7. Guia da data de solução

![Guia da data de solução](../imagens/10-guia-da-data-de-solucao.png)

A planilha não tinha uma coluna com a data em que o processo foi resolvido. O Gario passou a anotar o dia em que a coluna RESOLVIDAS muda de NÃO para SIM. A página **Sobre** explica, em linguagem simples:

- o que escrever;
- quando marcar;
- o que acontece se o processo voltar para NÃO;
- como ter a data exata, criando uma coluna própria na planilha.

As datas registradas dessa forma aparecem com um asterisco e um aviso de possível divergência.

## 8. Administração

![Administração](../imagens/11-administracao.png)

- **Usuários:** criação com senha inicial, perfis de administrador e operador, lojas liberadas, troca de senha de qualquer usuário, desativação e encerramento de sessões.
- **Histórico de execuções:** sincronizações, validações planilha × banco e backups diários.
- **Auditoria:** logins, tentativas recusadas, trocas de senha e alterações de acesso, com data, usuário e IP.

## 9. Ajuda e tutorial

![Tutorial guiado](../imagens/12-tutorial-guiado.png)

- **Tutorial guiado:** no primeiro acesso, um tour destaca cada área da tela. Ele pode ser revisto a qualquer momento.
- **Ícone de ajuda:** cada bloco e cada coluna têm um ícone que explica de onde vem a informação e como ela é calculada.

## 10. Celular e tablet

<p>
  <img src="../imagens/13-celular-visao-gerencial.png" width="240" alt="Visão gerencial no celular">
  <img src="../imagens/14-celular-pendencias.png" width="240" alt="Pendências em cartões">
  <img src="../imagens/15-celular-ficha-do-processo.png" width="240" alt="Ficha do processo">
</p>

No celular, o menu vira gaveta, as tabelas viram cartões com o nome de cada campo e o painel de detalhe ocupa a tela inteira. No tablet, os processos aparecem em duas colunas.
