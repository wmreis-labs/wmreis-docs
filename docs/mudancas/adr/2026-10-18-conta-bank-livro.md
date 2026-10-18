# ADR: conta do bank no livro do cashbook

**Status:** aceita em 2026-10-18.
**Complementa:** [Fase 1 do caixa](2026-09-27-fase-1-caixa.md). Livros, tipos e a porta do extrato continuam vigentes. Este ADR separa a identidade da conta bancária do livro.

## Contexto

O pacote `bank` já cadastra provedor, agência e número em `/bank/accounts`. O Hostto lê saldo e transação dessa conta como crédito e débito. O resumo soma créditos, soma débitos e faz a diferença.

O `cashbook` nasceu com outra conta, só com livro, banco e produto. As duas não se apontam. A coleta do Inter continua com uma conta na variável de ambiente e não lê `/bank/accounts`.

Uma transferência da poupança para a corrente aumenta o saldo e não é receita do mês. O pagamento da fatura é outro débito na conta corrente; a despesa já entrou na compra do cartão. Esses dois números não cabem no mesmo campo da transação do `bank`.

## Decisão

A identidade da conta fica no `bank`: provedor, agência e número. No cartão, os quatro últimos dígitos ocupam o lugar da agência. Nome e telefone do gerente são opcionais e não entram no resultado.

O `cashbook` guarda o vínculo: qual conta do `bank`, qual livro (Milton, TESTTO ou WMREIS), se o produto é conta ou cartão, e se a conta é corrente ou poupança. Não copia agência nem número.

A tela de contas grava os dois num cadastro só. A API do `cashbook` faz as duas gravações e devolve a visão junta. A tela não junta as fontes.

O Hostto continua lendo crédito, débito e saldo no `bank`. O painel e o sino continuam no `cashbook`.

Movimentação bancária nasce do extrato. O lembrete continua sendo a cobrança prevista e não cria transação no `bank`.

Cadastrar a conta no `bank` não troca a coleta do Inter. Ligar a conta da WMREIS ao coletor continua na tarefa já aberta.

## Consequências

O desenho do serviço está em [Conta do bank](https://wmreis-labs.github.io/airtestto-docs/mudancas/design/2026-10-18-conta-bank-cashbook/). O desenho da tela está em [Conta no admin](https://wmreis-labs.github.io/admin-docs/mudancas/design/2026-10-18-conta-bank-admin/).

Conta do `cashbook` já gravada sem vínculo continua válida até ser ligada. Conta sem livro segue para a revisão e não entra no resultado.

## Alternativas

Guardar agência e número só no `cashbook` foi rejeitado: o `bank` já é a identidade que o extrato e o Hostto usam. Classificar receita na transação do `bank` foi rejeitado: o saldo mostrado no Hostto deixaria de bater com o Inter.
