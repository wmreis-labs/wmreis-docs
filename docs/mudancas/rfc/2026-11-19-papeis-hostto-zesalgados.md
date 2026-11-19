# RFC: o que cada papel faz no Hostto e no Zé Salgados

**Status:** aceita em 2026-11-19
**Data:** 2026-11-19
**ADR:** [autenticação dos fronts](../adr/2026-11-18-autenticacao-dos-fronts.md)

## Contexto

Desde o ADR de autenticação, cada front entra no próprio pool do Cognito e a API recusa token de outro pool e troca de empresa. A empresa ficou fechada. O papel da pessoa dentro da empresa, não.

Na rodada de controle de acesso de 2026-11-19, em dev:

- No Hostto, Leitor, Financeiro, Operacional e Admin veem as mesmas telas e chamam as mesmas rotas. A tela não filtra o menu e a API não lê o grupo.
- No Zé Salgados, Pdv, Producao, Estoque e GestorCozinha também veem e alteram tudo da empresa.
- As rotas de usuários e empresas já exigem grupo desde a correção da escalada de privilégio (airtestto #246). Só o Admin do Hostto gerencia os usuários da própria empresa.

O admin e o console ATO já filtram o menu pelo grupo, e o ATO também recusa na API. Esta RFC propõe a mesma regra para o Hostto e o Zé Salgados.

## Proposta

### Hostto

| Área | Admin | Operacional | Financeiro | Leitor |
| --- | --- | --- | --- | --- |
| Dashboard e relatórios | ver | ver | ver | ver |
| Imóveis, pessoas, calendário | alterar | alterar | ver | ver |
| Contratos | alterar | alterar | alterar | ver |
| Flipping | alterar | alterar | ver | ver |
| Financeiro, contas e bank | alterar | — | alterar | ver |
| Assistente | usar | usar | usar | usar |
| Configurações (usuários da empresa) | alterar | — | — | — |

`ver` libera as leituras da área. `alterar` libera também criar, editar e apagar. Quem não tem nenhum dos quatro grupos não entra, como hoje no admin.

### Zé Salgados

| Área | GestorCozinha | Pdv | Producao | Estoque | Entregador |
| --- | --- | --- | --- | --- | --- |
| Relatórios | ver | ver (só o dia) | — | — | — |
| PDV e checkout | usar | usar | — | — | — |
| Pedidos (lista e detalhe) | alterar | alterar | ver | ver | — |
| Operação da cozinha (etapas do pedido) | alterar | ver | alterar | ver | — |
| Clientes | alterar | alterar | — | — | — |
| Produtos e categorias | alterar | ver | ver | alterar | — |
| Entregas: planejamento | alterar | ver | — | — | — |
| Entregas: rota do dia | ver | ver | — | — | ver |
| Pedido público pelo link | não depende de grupo | | | | |

GestorCozinha é o admin da empresa no Zé Salgados, como já é para convite e aprovação. Entregador é um grupo novo no pool do Zé Salgados: só a rota do dia.

### Onde a regra vale

- **API.** Cada stack recebe a tabela de rota por grupo junto com os pools aceitos. O authorizer compara método e caminho com a tabela e recusa com 403 quando o grupo não libera. Em sombra, só registra, como já faz com pool e empresa. A tabela fica no código do airtestto, ao lado da configuração do authorizer.
- **Front.** O menu e as rotas filtram pela mesma tabela, como `podeVerCaminho` no admin. Botões de alterar somem para quem só vê. A tela não decide sozinha: se a API recusar, mostra a recusa.
- **Ordem.** Primeiro a API em sombra em dev, para ver nos logs quem seria recusado. Depois a recusa em dev e os fronts. Produção segue o mesmo caminho dos cortes do ADR.

## Alternativas

- **Checar o grupo em cada handler.** Espalha a regra por mais de 200 handlers e não funciona em sombra sem duplicar código. Rejeitada.
- **Só filtrar o menu.** Não protege nada, porque a API continuaria aceitando a chamada. Rejeitada.
- **Um grupo por tela.** Dá controle fino, mas multiplica grupos no Cognito sem pedido de negócio. Fica para quando aparecer o caso.

## Respostas (2026-11-19)

1. Financeiro do Hostto altera contratos (valor, vencimento).
2. Operacional do Hostto não vê nada do financeiro.
3. Quem entrega ganha o grupo Entregador no Zé Salgados, só com a rota do dia.
4. Pdv vê os relatórios do dia.
5. Todos os usuários atuais do Hostto de produção entram no grupo Admin antes de a regra subir.
