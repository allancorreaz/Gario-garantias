# Métricas e regras de cálculo

[Início](../README.md) · [Funcionalidades](funcionalidades.md) · [Arquitetura](arquitetura.md) · [Stack](stack.md) · [Código e dados](codigo-e-dados.md) · [Dados e sincronização](dados-e-sincronizacao.md) · [Métricas](metricas.md) · [Segurança](seguranca.md) · [Operação](operacao.md) · [Versões](versoes.md)

Todo número do Gario vem da coluna **RESOLVIDAS** e das datas da planilha. Os dias são sempre **corridos**, contados a partir de **DATA ABERTURA**.

## O que conta como processo

- Cada linha com um **CRM formado só por algarismos**.
- Na planilha geral, entram só as **abas com nome de mês** (JANEIRO, FEVEREIRO…). Outras abas são ignoradas de propósito.

## Como a coluna RESOLVIDAS é lida

| Valor na planilha | Como o Gario entende |
|---|---|
| SIM | Resolvido |
| NÃO | Não resolvido |
| Vazio ou outro texto | Sem classificação, tratado como pendente |

## Indicadores

| Indicador | Como é calculado |
|---|---|
| Processos registrados | Linhas que passam pelos filtros de mês, produto e fornecedor |
| Resolvidos / não resolvidos | Contagem pela coluna RESOLVIDAS |
| Taxa de resolução | Resolvidos ÷ total |
| Aberturas por mês | Processos pelo mês de DATA ABERTURA |
| Resolvidos no mês | Processos pelo mês da data de solução; só a partir do primeiro mês com datas |
| Média de demora | Soma dos dias em aberto dos não resolvidos ÷ quantidade |
| Procedentes / improcedentes | Palavra PROCEDENTE ou IMPROCEDENTE nas colunas SITUAÇÃO e STATUS |
| Média até a aprovação interna | Dias entre DATA ABERTURA e a data da aprovação, nos processos com as duas datas |
| Dias no fornecedor | Dias desde a aprovação até a chegada da peça na loja (ou até hoje) |

## Prioridade por tempo em aberto

| Prioridade | Dias em aberto |
|---|---|
| No prazo | Até 30 |
| Atrasado | 31 a 60 |
| Alto | 61 a 90 |
| Crítico | Acima de 90 |

## Alertas

| Alerta | Quando dispara |
|---|---|
| Aprovação interna | Processo do parceiro com mais de 1 dia sem aprovação |
| Etapa no fornecedor | Mais de 30 dias depois da aprovação sem a peça chegar na loja |
| Prazo após o RMA | Data limite após o RMA já passou e o processo segue em aberto |
| Inconsistência | Marcado como resolvido, mas a situação ainda indica cliente aguardando |

## Data de solução

1. Se a planilha tiver uma coluna de data de resolução, ela vale.
2. Sem essa coluna, vale o **dia em que RESOLVIDAS mudou de NÃO para SIM**, registrado na leitura daquele minuto.
3. Processos já resolvidos antes do início do acompanhamento ficam sem data: o sistema não inventa datas.
4. Datas vindas da mudança de status aparecem com **asterisco** e aviso de possível divergência.

## Por que dois usuários podem ver totais diferentes

Cada operador vê só as lojas liberadas para ele. Os dois números estão certos.
