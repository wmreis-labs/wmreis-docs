# ADR: lembrete antes do painel

**Status:** aceita em 2026-10-17.
**Complementa:** [Fase 1 do caixa](2026-09-27-fase-1-caixa.md). Livros, tipos e a porta do extrato continuam vigentes. Este ADR muda a ordem em que o caixa aparece no admin.

## Contexto

Conta a pagar chega por e-mail e se perde. O conceito foi validado no SQLite `financeiro.sqlite`, na tabela `lembrete`, e numa rotina diária do Cursor que resume o dia na agenda. O painel do mês ainda não existe no código. A pasta do Drive continua fora da fase 1.

O admin já tem o ícone de notificações na barra logada. O número exibido hoje não vem de API.

## Decisão

O primeiro corte visível do caixa é o lembrete, não o painel. A base continua no `cashbook`: livro, lembrete e a lista. A tela só consome `/cashbook`.

O sino mostra três grupos: atrasado, vence hoje e novo. Novo é cobrança chegada pelos rótulos de boleto, fatura e imposto que ainda não foi confirmada como rotina nem como avulso. Rotina ativa cujo dia cai depois de hoje não entra no número.

A tela Contas a vencer confirma rotina ou avulso, dá baixa na ocorrência, encerra com motivo ou ajusta dia e valor. A baixa tira o alerta da ocorrência e mantém a rotina. Encerrar grava o motivo e a mesma cobrança não volta sozinha.

O job diário lê só esses rótulos, para os livros Milton e WMREIS. Não lê a caixa inteira. Guia de Miralis e de Testto não vira lembrete. O que não casa com um lembrete ativo entra como novo. O job encerra só quando a mensagem diz que a cobrança acabou, ou quando a nota da linha tem data de encerramento já passada.

A pasta do Drive, a conciliação do previsto com o extrato, o painel do mês e a reserva continuam nos cortes seguintes. A rotina do Cursor e a agenda do Google permanecem até este corte existir no admin. O sino não avisa com a sessão fechada.

## Consequências

O desenho do serviço está em [Lembrete](https://wmreis-labs.github.io/airtestto-docs/mudancas/design/2026-10-17-lembrete-cashbook/). O desenho da tela está em [Lembrete no admin](https://wmreis-labs.github.io/admin-docs/mudancas/design/2026-10-17-lembrete-admin/).

A implementação começa pelo modelo de conta e livro, segue no modelo do lembrete, na lista e no job, e só então no sino e na tela.

## Alternativas

Começar pelo painel do mês foi adiado: a dor validada é perder o vencimento, e o painel não avisa a data. Tratar o sino como snackbar de sucesso foi rejeitado: o snackbar avisa o resultado de uma ação, e o lembrete permanece até a baixa ou o encerramento. Avisar às 7h fora do admin foi deixado na rotina do Cursor: este corte não cria canal novo.
