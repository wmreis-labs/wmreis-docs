# PRD: painel inicial

**Status:** aberta
**Data:** 2026-10-17
**Produto:** Hostto

## Problema

Quem abre o Hostto cai num painel com quatro números do mesmo tamanho: quantos imóveis existem, quantos contratos existem, o saldo de uma conta e quantos eventos há hoje. O saldo pequeno de uma conta ocupa o mesmo lugar que a operação do dia. O gráfico chamado resumo financeiro desenha só os dias em que o banco teve lançamento, sem dizer o período, sem legenda e sem total. A lista de próximos eventos mistura estadia cancelada com confirmada, mostra a origem e não diz em qual imóvel a pessoa entra ou sai. Contrato parado antes da assinatura e projeto de flipping em andamento não aparecem. O calendário de ocupação e o dashboard do banco já respondem essas perguntas nas telas deles. O painel não leva até lá com o recorte de hoje.

## Quem usa

Quem abre o Hostto de manhã e precisa saber quem chega, quem sai, quem está na casa, e se dinheiro, contrato ou flipping pedem uma olhada antes de abrir o módulo.

## Resultado esperado

O dia nas casas vem primeiro. Chegada, saída e quem permanece aparecem com o nome do imóvel, o título da estadia, as datas e o status que ainda vale. Cancelado não lidera a lista. O clique abre o calendário naquele imóvel. Uma linha de dinheiro mostra o saldo, as entradas, as saídas e o resultado de um período escrito, e abre o banco. Contratos que ainda não foram assinados e projetos de flipping em busca ou em andamento aparecem como atenção, cada um com o caminho para a tela que já existe.

## Escopo

1. O dia nas casas: chegadas, saídas e quem permanece, com imóvel, título, datas e status. Pendente e confirmado na frente. Cancelado e bloqueio fora da lista principal.
2. Dinheiro numa linha: saldo das contas, entradas, saídas e resultado do mês corrente, com o período escrito. Sem gráfico nesta tela.
3. Contratos que ainda não foram assinados, em contagem e lista curta.
4. Projetos de flipping em busca ou em andamento, com nome e status.
5. O imóvel aparece dentro da estadia. A contagem solta de imóveis e de pessoas sai do painel.

## Fora do escopo

- Repetir a grade de ocupação ou o extrato do banco.
- Gráfico de fluxo no painel.
- Pessoas e total de imóveis como número de destaque.
- Varrer o calendário de cada imóvel para montar o dia. Se a lista de próximos eventos não incluir a estadia que já começou, isso vira decisão de serviço, com desenho próprio.
- Relatórios e orçamento de reforma.

## Critério de aceite

A pessoa abre o painel e vê, antes dos totais, quem chega, quem sai e quem permanece hoje, com o nome do imóvel. Estadia cancelada não ocupa essa lista. O saldo e o resultado do mês estão escritos com o período, e o clique abre o banco. Contratos ainda não assinados e flipping em aberto aparecem com caminho para a tela de cada um. Sem movimento no dia, a tela diz que não há chegada, saída nem hospedagem, em vez de um número solto.

## Métrica

A pergunta que a tela responde é o que acontece hoje nas casas e o que mais pede atenção. O sinal de melhora é o imóvel escrito na estadia e o cancelado fora da lista principal.

## Decisões e desenho

Não há ADR: o serviço não muda nesta etapa. O desenho da tela está em [Painel inicial](https://wmreis-labs.github.io/hostto-docs/mudancas/design/2026-10-17-painel-inicial/).
