# RFC: menu e layout do admin

**Status:** em revisão
**Data:** 2026-10-18
**PRD:** [console do admin](../prd/2026-10-18-console-do-admin.md)

## Contexto

O airtestto é um backend. O tenant separa os produtos: o endereço do Hostto resolve um tenant, o do Zé Salgados resolve outro, e o header da sessão resolve o restante. O admin já consome essa API com o tenant fixo do próprio build. Não é um front que entra no Hostto ou no Zé Salgados.

O seletor do topo nasceu dessa ideia de um shell único para todos os produtos. Hoje ele só guarda um valor local e navega para um caminho sem tela. A pessoa volta para a entrada. O tenant da chamada não muda. O menu também não muda.

A entrada ainda é o template de uma operação de entregas: pedidos, clientes, produtos, usuários e um gráfico de receita e despesa com números locais. Isso disputa atenção com o painel do caixa, que já lê a API.

O menu lateral é uma lista. Cada tela nova de caixa virou mais um item ao lado de usuários, grupos, tenants e logs. Parte desses itens não tem rota. O clique cai na entrada.

## Proposta

O admin permanece o console. Dois grupos no menu:

- Administração: Início, Usuários, Grupos, Tenants e Logs.
- Caixa: Painel, Contas e Contas a vencer. Extrato, revisão e lançamento entram neste grupo quando as histórias do caixa chegarem, sem voltar para a raiz da lista.

O seletor de produto sai da barra. A marca do console fica no menu. Configurações e sair ficam no rodapé. Perfil fica no avatar. O sino de lembrete continua na barra, porque o aviso do dia não depende da tela aberta.

A entrada responde só o que a API de administração devolver. Enquanto essa leitura não existir, a entrada diz o que falta e aponta os dois grupos. Não mostra pedido, entrega nem série financeira.

No caixa, o resultado do mês é o número principal. Receita e despesa apoiam. Interno, retirada, fatura, investimento, patrimonial e empréstimo ficam visualmente atrás. O cadastro de conta abre sob demanda. A lista é a primeira coisa da tela. Filtro de livro e de mês fica numa linha só.

## Alternativas

Manter o seletor e fazê-lo trocar o tenant foi rejeitado nesta proposta. O operador do admin não deve passar a enxergar o Hostto como se estivesse naquele produto. Cada front continua dono da própria sessão.

Um terceiro aplicativo só para a administração foi rejeitado no [ADR de admin e ingestão](../adr/2026-09-27-admin-e-ingestao.md). O login já está aqui.

Deixar o menu plano e só esconder os itens sem rota adia o problema. A próxima tela de caixa volta a alongar a lista.

## Consequência se for aceita

O desenho da casca em [admin-docs](https://wmreis-labs.github.io/admin-docs/mudancas/design/2026-10-18-casca-do-admin/) passa a vigente. As rotas já publicadas do caixa não mudam. As tarefas de usuários, grupos, tenants e logs só nascem depois deste aceite. Não há contrato novo de API nesta RFC.
