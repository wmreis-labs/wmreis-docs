# ADR: cobrança de reserva por link do PagBank

**Status:** aceita em 2026-11-19.
**Complementa:** [Conta do bank no livro](2026-10-18-conta-bank-livro.md). O `bank` continua sendo a identidade da conta e o lugar do extrato.

## Contexto

Nove em cada dez hóspedes pagam a reserva com cartão, por um link gerado à mão no app do PagSeguro (PagBank). O dinheiro cai na conta PagBank, que só depois vai para o Inter. Nada liga o pagamento à reserva: o Hostto não sabe qual reserva foi paga, e a conciliação é feita de olho.

O airtestto já lê o extrato do Inter pela API e publica as transações na fila do `bank`. O extrato do banco, porém, só mostra o repasse somado. Taxa, parcela e qual venda gerou cada valor ficam com quem processa o cartão.

O link do Inter Pag não tem API. O PagBank tem duas: o checkout, que cria o link de pagamento com uma referência nossa e avisa quando ele é pago, e a EDI, que entrega no dia seguinte cada venda, cancelamento, chargeback e repasse da conta.

## Decisão

A cobrança por cartão de uma reserva nasce no airtestto, pelo checkout do PagBank. Um link por reserva, com o identificador da reserva como referência. Parcelamento máximo, parcelas sem juros e validade do link são definidos na geração.

A reserva só é baixada como paga quando o PagBank confirma. O aviso do PagBank dispara a baixa, e o airtestto confere o pedido no PagBank antes de gravar. O valor pago e o valor em aberto continuam na própria reserva do `calendar`. Aviso repetido não baixa duas vezes.

O extrato de cartão entra pela EDI, uma coleta por dia, no `bank`, como mais um provedor ao lado do Inter, pela mesma fila. Cada venda vira uma linha com valor bruto, taxa, líquido, parcelas e data prevista do repasse. Vendas da maquininha e de link feito no app também entram, ainda sem reserva ligada.

O repasse do PagBank para o Inter continua sendo uma transferência feita à mão. A conciliação casa a saída registrada na EDI com o crédito que aparece no extrato do Inter.

Os tokens do PagBank ficam no Secrets Manager, um segredo por empresa, como já acontece com o Inter. O token da EDI é outro, pedido ao PagBank, e só existe em produção.

O Hostto mostra o botão de gerar o link e o estado do pagamento que a API devolve. A tela não fala com o PagBank.

## Consequências

O desenho do serviço vai para o [airtestto-docs](https://wmreis-labs.github.io/airtestto-docs/mudancas/), e o da tela para o [hostto-docs](https://wmreis-labs.github.io/hostto-docs/). Rotas, eventos e campos ficam lá.

O checkout tem sandbox; a EDI não. A coleta da EDI é testada com dados reais, só em leitura, e depende do token pedido ao PagBank.

Duas perguntas ficam para a primeira coleta real: se a EDI traz cada venda do link e da maquininha separada, e qual campo dela permite casar a venda com o pedido do checkout, já que a referência da reserva não aparece na EDI. Se a venda vier somada, a conciliação por reserva passa a depender só do aviso do checkout.

Reservas pagas por Pix ou por fora do link continuam com baixa manual.

## Alternativas

Link do Inter Pag foi rejeitado: não tem API, e a taxa com recebimento no dia seguinte não é menor que a do PagBank. Asaas foi rejeitado por agora: a API é completa, mas troca a operação, não tem maquininha e o dinheiro antecipado leva até dois dias úteis. Adyen foi rejeitada pelo porte: preço negociado por volume e implantação pesada. Conciliar só pelo extrato do Inter foi rejeitado: o repasse chega somado e sem taxa.
