# PRD: Testto Pay, cobrança e conciliação de pagamentos

**Status:** aberta
**Data:** 2026-11-21
**Produto:** Testto Pay (novo), usado por Hostto e Zé Salgados, e vendido a clientes de fora

## Problema

Nove em cada dez hóspedes da Miralis pagam a reserva com cartão, por um link gerado à mão no app da maquininha. Nada liga o pagamento à reserva. O dinheiro cai na conta da maquininha, vai para o banco por transferência e chega somado, sem taxa e sem dizer de qual reserva veio. A conciliação é feita de olho.

O Zé Salgados vai precisar da mesma coisa para os pedidos. Se cada sistema integrar o próprio provedor, serão duas integrações, dois jeitos de tratar o pagamento confirmado e duas conciliações que não conversam com o caixa.

Outras empresas têm o mesmo problema e resolvem com planilha. Os gateways de mercado cobram, mas não ligam a cobrança ao que foi vendido no sistema do cliente nem ao extrato do banco.

## Quem usa

- Quem paga: o hóspede ou o cliente do Zé. Recebe um link, vê quem está cobrando e paga com cartão, à vista ou parcelado.
- Quem vende: o financeiro da Miralis no Hostto, o atendente do Zé. Gera o link a partir da reserva ou do pedido e quer ver quando foi pago, sem sair do sistema.
- O financeiro de cada empresa: acompanha todas as cobranças, o que foi pago, a taxa, o líquido, o repasse e o que não fechou.
- O cliente de fora: cadastra a empresa, liga a própria conta do provedor e usa o Testto Pay pelo console e pela API.
- Os sistemas: Hostto, Zé Salgados e os sistemas dos clientes de fora pedem a cobrança e são avisados do resultado.
- O operador da WMReis: acompanha todas as empresas, os provedores e as falhas.

## Resultado esperado

Toda reserva da Miralis paga com cartão nasce de um link gerado pelo Hostto, e a reserva aparece como paga sozinha quando o pagamento é confirmado. No dia seguinte, cada venda aparece com valor bruto, taxa, líquido, parcelas e data do repasse, e cada repasse aparece ligado à entrada no banco. O financeiro abre o console e vê o que está pago, o que está pendente e o que não fechou, sem planilha.

O Zé usa o mesmo produto nos pedidos, sem uma integração nova. Uma empresa de fora começa a cobrar no mesmo dia em que liga a conta do provedor.

## Escopo

1. Cobrança por link de pagamento: valor, descrição, parcelamento máximo, parcelas sem juros e validade, ligada à venda que a originou no sistema de quem pediu.
2. Página de pagamento hospedada pelo provedor, com a marca da empresa que cobra e o endosso "Processado por Testto Pay".
3. Confirmação do pagamento pelo aviso do provedor, conferida antes de valer, sem baixar duas vezes a mesma cobrança.
4. Aviso do resultado aos sistemas internos e aos sistemas dos clientes de fora: paga, recusada, expirada, cancelada, estornada.
5. Extrato de cartão do provedor, linha a linha: venda, taxa, líquido, parcelas, chargeback, cancelamento e repasse.
6. Conciliação: cobrança com a venda do provedor, e repasse com a entrada no extrato bancário. O que não fecha aparece como pendente.
7. Console próprio: painel, cobranças, conciliação, repasses, taxas, empresas, conta do provedor, chaves de API, avisos enviados e usuários.
8. Várias empresas, cada uma vendo só o que é seu. Perfis: administrador, financeiro e leitor da empresa; operador da WMReis.
9. API pública para clientes de fora, com chave por empresa e avisos de saída.
10. Ligação da conta do provedor autorizada pelo próprio cliente, sem copiar token. As empresas da WMReis podem começar com token cadastrado pelo operador.
11. Um provedor no começo, o PagBank. O produto admite outros sem mudar o que os sistemas consomem.
12. Identidade Testto Pay sobre o design system Testto: barras inclinadas com verde de marca, botão em tinta, verde só na marca e no estado pago.

## Fora do escopo

- Subadquirência. O dinheiro nunca passa por uma conta da Testto; cai direto na conta do provedor de cada empresa.
- Checkout transparente. O número do cartão nunca é digitado nem trafega em tela ou serviço da Testto.
- Cartão salvo, assinatura e cobrança recorrente.
- Divisão de pagamento entre recebedores (split).
- Pix e boleto pelo Testto Pay. Entram quando houver provedor e demanda; o Pix pelo banco continua como está.
- Transferência automática do saldo do provedor para o banco. O repasse continua manual e é conciliado.
- Maquininha física. A venda feita nela entra no extrato e na conciliação, mas não nasce no Testto Pay.
- Antecipação controlada pelo Testto Pay. Prazo e antecipação ficam no plano de cada empresa no provedor.

## Critério de aceite

A primeira entrega é a reserva da Miralis no Hostto. Os critérios abaixo valem para o produto inteiro, na ordem em que as entregas entram.

- O Hostto gera o link de uma reserva com o valor da própria reserva; o hóspede paga em até 12 vezes, e a reserva aparece como paga sem ninguém digitar nada.
- Repetir o aviso do provedor não muda a reserva; um aviso forjado é recusado.
- O valor do link não pode ser alterado por quem gera o link na tela.
- No dia seguinte, a venda aparece no console com bruto, taxa, líquido e data do repasse, ligada à reserva.
- O repasse do provedor aparece ligado à entrada no extrato do banco; o que não fechar aparece como pendente.
- Um pedido do Zé é cobrado pelo mesmo produto, sem integração nova com o provedor.
- Uma empresa de fora cadastra-se, liga a própria conta do provedor e recebe o aviso de cobrança paga no sistema dela.
- Uma empresa nunca vê cobrança, extrato ou chave de outra.

## Métrica

- Cobranças de cartão que nascem no Testto Pay, sobre o total de vendas com cartão de cada empresa.
- Cobranças pagas que ficam conciliadas sozinhas, sem intervenção.
- Tempo entre o pagamento e a baixa no sistema de origem.
- Pendências de conciliação abertas há mais de três dias.
- Empresas de fora ativas e cobranças pagas por mês.

## Perguntas em aberto

- Como a Testto cobra pelo serviço: mensalidade, valor por cobrança paga ou plano.
- Se a ligação autorizada pelo cliente (PagBank Connect) está disponível para a conta da Testto, e em que condições.
- O que o extrato do provedor traz por venda, e qual campo casa com a cobrança. Só se sabe na primeira coleta real.
- Contrato, termos de uso e política de privacidade para clientes de fora.
- Registro da marca Testto Pay e do domínio.

## Decisões e desenho

A escolha do PagBank e da baixa pelo aviso conferido está em [Cobrança de reserva por link do PagBank](../adr/2026-11-19-cobranca-reserva-pagbank.md). Este PRD amplia o escopo: o domínio de pagamentos passa a ser central e neutro quanto ao sistema e ao provedor, e ganha console e API próprios. Essa decisão está em [Testto Pay, pagamentos como domínio central](../adr/2026-11-21-testto-pay-pagamentos.md), que substitui o anterior. O desenho do serviço, com rotas, eventos e campos, vai para o [airtestto-docs](https://wmreis-labs.github.io/airtestto-docs/mudancas/); as telas e a identidade visual, para o site próprio do Testto Pay.
