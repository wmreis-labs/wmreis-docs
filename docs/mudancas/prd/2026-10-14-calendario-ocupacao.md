# PRD: calendário de ocupação

**Status:** aceita
**Data:** 2026-10-14
**Produto:** Hostto

## Problema

Quem administra vários imóveis abre o calendário e espera. A tela trava enquanto junta imóvel, foto e evento, inclusive informação que a grade não mostra. A visão de todos os imóveis fica presa na semana corrente, sem semana anterior nem seguinte, e ainda desloca um dia. Ao escolher um imóvel, essa visão some e entra um calendário mensal. As duas ideias disputam a mesma tela. A intenção era uma grade de ocupação, no jeito de ver várias casas ao mesmo tempo, e o calendário de um imóvel só quando a pessoa entra no detalhe.

## Quem usa

Quem opera o Hostto e precisa ver, num relance, quem está em cada imóvel, e abrir a agenda de um deles para criar ou rever um evento.

## Resultado esperado

A tela principal continua sendo a grade de todos os imóveis enquanto a pessoa anda no tempo. Cada estadia é uma barra da entrada à saída. Escolher um imóvel abre o calendário daquele imóvel. Voltar traz a grade, sem montar de novo a lista de imóveis. A grade aparece com o nome; a foto entra depois. Andar no tempo não cobre a tela inteira.

## Escopo

1. Grade com os imóveis em linhas e cerca de três semanas em colunas, começando na segunda-feira, com anterior, hoje e próximo.
2. Barra contínua da estadia, na cor do status. O título fica na barra. Data e status aparecem ao apontar. O clique abre o evento que já existe.
3. Calendário de um imóvel, em mês, semana ou dia, para criar e rever o evento. A volta retorna à grade.
4. Lista dos eventos do período escolhido, com paginação perceptível.

## Fora do escopo

- Um serviço único que devolva os eventos de todos os imóveis num período. Se a grade, já limitada ao que está na tela, continuar lenta, isso vira decisão própria, com desenho no serviço.
- Alterar o formulário de criar ou editar evento.
- Ligar esta tela a um calendário externo.

## Critério de aceite

A pessoa abre o calendário e vê a grade dos imóveis no período visível, sem a tela inteira presa em foto, contrato ou pessoa. Consegue ir à semana anterior, voltar a hoje e seguir. A estadia atravessa os dias, sem repetir tipo, status e nota em cada célula. O clique no imóvel abre só o calendário dele. A volta mostra a mesma grade. Trocar o período mantém a tela utilizável, com um aviso leve de carregamento. A lista do período pagina.

## Métrica

A pergunta que a tela responde é quem está em cada imóvel nestas semanas, e o que muda se um imóvel for aberto. O sinal de melhora é a grade aparecer antes das fotos e a troca de período deixar de travar a tela.

## Decisões e desenho

Não há ADR: o contrato do serviço não muda nesta etapa. O desenho da tela está em [Calendário de ocupação](https://wmreis-labs.github.io/hostto-docs/mudancas/design/2026-10-14-calendario-ocupacao/).
