# PRD: console do admin

**Status:** aceita
**Data:** 2026-10-18
**Produto:** Admin

## Problema

O admin mistura duas funções na mesma lista. De um lado, a administração comum dos sistemas que usam o mesmo backend: usuário, grupo, tenant e auditoria. Do outro, o caixa pessoal e o das empresas. Os dois crescem no mesmo menu, sem grupo.

Vários itens dessa lista não abrem tela. O seletor do topo oferece Hostto, Zé Salgados e Admin, mas não entra em nenhum desses produtos. A entrada do admin mostra pedidos, entregas e um gráfico financeiro que não vieram da API. O caixa real está em outras telas, com filtro, número e tabela empilhados sem hierarquia.

## Quem usa

Quem administra a plataforma e quem opera o caixa. Quem cuida de imóvel continua no Hostto. Quem opera a loja continua no Zé Salgados. Os dois produtos seguem com o front deles.

## Resultado esperado

O admin é o lugar das tarefas administrativas comuns e do caixa. A pessoa distingue as duas áreas assim que entra. A entrada não mostra número inventado. O seletor de produto sai, porque este console não é uma porta para o Hostto nem para o Zé Salgados.

## Escopo

1. Casca: menu em dois grupos, Administração e Caixa. A entrada deixa de exibir dado falso. Configurações e perfil ficam no lugar de conta, fora da lista de trabalho.
2. Usuários e grupos da plataforma, com a mesma sessão das demais telas.
3. Tenants da plataforma.
4. Registro de auditoria.
5. O caixa já decidido continua neste console. Extrato, revisão e lançamento seguem o [PRD do caixa](2026-09-27-caixa-pessoal-profissional.md).

## Fora do escopo

- Levar o caixa para o Hostto ou para o Zé Salgados.
- Criar outro aplicativo para a administração.
- Um catálogo de aplicativos. Hostto e Zé Salgados já são fronts, cada um com o tenant do próprio endereço.
- Trocar o tenant da sessão por um seletor na barra.
- Pedidos, clientes, produtos e entregas do template que hoje ocupa a entrada.

## Critério de aceite

A pessoa abre o admin e vê Administração e Caixa separados. Hostto e Zé Salgados não aparecem como troca de produto. Usuários, grupos, tenants e logs estão no quadro, cada um com o desenho da tela. A entrada não mostra pedido, entrega nem gráfico financeiro sem origem na API.

## Métrica

A pergunta que a casca responde é em qual área a pessoa está. O sinal de melhora é o menu deixar de crescer um item solto por tela e a entrada deixar de competir com o painel do caixa.

## Decisões e desenho

A proposta de menu e de layout está na [RFC](../rfc/2026-10-18-menu-do-admin.md). O desenho da casca está em [Admin](https://wmreis-labs.github.io/admin-docs/mudancas/design/2026-10-18-casca-do-admin/). O caixa continua no [ADR de admin e ingestão](../adr/2026-09-27-admin-e-ingestao.md).
