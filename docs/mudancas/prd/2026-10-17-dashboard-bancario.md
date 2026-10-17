# PRD: dashboard bancário

**Status:** aceita
**Data:** 2026-10-17
**Produto:** Hostto

## Problema

Quem olha o banco no Hostto vê saldo, entradas, saídas e extrato, mas não sabe de que período são os números. O saldo divide a tela com médias que não ajudam a decidir. O extrato pinta a linha inteira, esconde o sinal do valor e não deixa buscar. Entradas e saídas que se repetem todo mês — o mesmo pagador, o mesmo recebedor — não aparecem juntas, então a falta de um mês passa despercebida.

## Quem usa

Quem opera o financeiro do Hostto e precisa saber o saldo, o que entrou e saiu no período, e quais recebimentos e pagamentos se repetem.

## Resultado esperado

A pessoa escolhe a conta e o período e lê o saldo em destaque, com entradas, saídas e resultado daquele intervalo. O extrato abaixo deixa buscar, filtrar entrada ou saída e ver o valor com sinal. Uma matriz dos últimos doze meses agrupa créditos pelo pagador e débitos pelo recebedor, e o mês sem lançamento fica vazio. Clicar num dia do gráfico ou numa célula da matriz leva ao extrato correspondente.

## Escopo

1. Período visível, com atalhos e intervalo livre, valendo para os números, os gráficos e o extrato.
2. Saldo em destaque. Entradas, saídas e resultado do período ao lado. Quantidade e médias em texto de apoio.
3. Extrato com busca, filtro de entrada ou saída, valor com sinal, total carregado e detalhe legível do lançamento.
4. Gráficos em moeda, com legenda no fluxo de caixa. O clique num dia filtra o extrato.
5. A conta e o período ficam no endereço da tela, para o link reabrir o mesmo recorte.
6. Matriz de doze meses: recebimentos por pagador e pagamentos por recebedor, com a recorrência em destaque e o mês faltante vazio.

## Fora do escopo

- Visão somada de todas as contas.
- Saldo dentro da lista de contas.
- Exportar planilha.
- Editar a categoria do lançamento.
- Saldo corrido linha a linha.
- Um total pronto no serviço. A matriz usa os lançamentos que a tela já recebe. Se doze meses ficarem pesados, isso vira decisão própria.

## Critério de aceite

A pessoa abre o banco, vê de qual conta e de qual período são os números, e encontra o saldo antes das médias. Consegue buscar um lançamento, separar entrada de saída e ler o valor com sinal. O gráfico e a matriz, ao serem clicados, mostram os lançamentos daquele recorte. O link da tela reabre a mesma conta e o mesmo período. A matriz separa pagador de recebedor e deixa vazio o mês em que a recorrência não apareceu.

## Métrica

A pergunta que a tela responde é quanto há na conta, o que entrou e saiu neste período, e qual pagador ou recebedor faltou num mês. O sinal de melhora é o período escrito junto dos números e a recorrência visível sem percorrer o extrato dia a dia.

## Decisões e desenho

Não há ADR: o serviço de banco não muda nesta etapa. O desenho da tela está em [Dashboard bancário](https://wmreis-labs.github.io/hostto-docs/mudancas/design/2026-10-17-dashboard-bancario/).
