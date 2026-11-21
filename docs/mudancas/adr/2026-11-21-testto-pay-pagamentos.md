# ADR: Testto Pay, pagamentos como domínio central

**Status:** aceita em 2026-11-21.
**Produto:** [Testto Pay](../prd/2026-11-21-testto-pay.md).
**Substitui:** [Cobrança de reserva por link do PagBank](2026-11-19-cobranca-reserva-pagbank.md). A escolha do PagBank, a baixa pelo aviso conferido e o extrato pela EDI continuam valendo; muda quem é dono da cobrança.

## Contexto

O ADR de 2026-11-19 pôs a cobrança da reserva dentro do caminho do Hostto: um pacote do PagBank e a baixa direto na reserva. O Zé Salgados vai precisar da mesma cobrança nos pedidos, e o produto passa a ser vendido para empresas de fora, que querem um console e uma API. Do jeito antigo, cada sistema teria a própria integração com o provedor e a própria conciliação.

O ecossistema já separa capacidades que servem a vários sistemas: o canal de WhatsApp do ATO recebe o aviso da Meta, confere, descarta repetidos e publica o evento para quem consome. O Emitto segue o mesmo caminho para a nota fiscal. Pagamento é o mesmo problema com outro provedor.

## Decisão

A cobrança é um domínio próprio do airtestto, o Testto Pay, e ele é o único caminho de cobrança por cartão do ecossistema. Hostto, Zé Salgados e os sistemas dos clientes de fora não falam com o PagBank nem com outro provedor.

O domínio não conhece reserva nem pedido. Cada cobrança carrega a empresa e a origem: qual sistema, que tipo de coisa e qual identificador. Quem consome guarda o identificador da cobrança, e o Testto Pay guarda a origem.

O valor da cobrança vem do backend do sistema que vende, nunca da tela. O botão do Hostto pede "cobrar a reserva X"; o serviço da reserva lê o valor da própria reserva e pede a cobrança ao Testto Pay.

O provedor fica atrás de uma porta única. O PagBank é o primeiro, pelo checkout hospedado. Outro provedor entra como outro adaptador, sem mudar o que os sistemas consomem.

O aviso do provedor é conferido no provedor antes de valer e tratado uma vez só. O resultado sai como evento no tópico de mensagens para os pacotes do airtestto e como webhook assinado para os sistemas de fora. Quem consome dá baixa no que é seu: a reserva no `calendar`, o pedido no Zé.

Pessoas entram no console pelo Cognito, num pool próprio do Testto Pay, seguindo a regra de um pool por sistema, com os papéis administrador, financeiro e leitor por empresa. O operador da WMReis entra pelo pool de operadores. Sistemas chamam a API com chave própria por empresa e ambiente, nunca com o token de um usuário.

A conta do provedor é de cada empresa. As empresas da WMReis começam com o token cadastrado pelo operador. As empresas de fora ligam a conta autorizando o Testto Pay no provedor, sem copiar token. Tokens e autorizações ficam no Secrets Manager, um por empresa.

O número do cartão nunca passa pela Testto: só checkout hospedado pelo provedor. O dinheiro nunca passa por conta da Testto: cai na conta do provedor de cada empresa.

O extrato do provedor continua entrando no `bank`, como mais um provedor ao lado do Inter. A conciliação é do Testto Pay: liga a cobrança à venda do extrato e o repasse à entrada no banco.

O console é um aplicativo próprio, no design system Testto, com a marca Testto Pay. O botão de cobrar continua dentro de cada sistema; o console é para quem cuida do dinheiro.

## Consequências

O desenho do serviço vai para o [airtestto-docs](https://wmreis-labs.github.io/airtestto-docs/mudancas/), com a API, os eventos, os estados da cobrança e a porta do provedor. O console, a identidade visual e a documentação pública da API ganham um site próprio do Testto Pay.

A entrega vai em três cortes. O primeiro é a reserva da Miralis no Hostto, com o PagBank e o extrato. O segundo é o console para as empresas da WMReis e o pedido do Zé. O terceiro abre para empresas de fora: cadastro, ligação autorizada da conta, chave de API, webhooks de saída e cobrança pelo serviço.

As tarefas do pacote do PagBank e da baixa direta na reserva são reescritas: o domínio cria a cobrança e publica o resultado, e a reserva consome o evento.

Guardar o acesso à conta de outras empresas exige controle de acesso mínimo e auditoria desde o começo, como o certificado no Emitto.

## Alternativas

Uma integração por sistema foi rejeitada: repetiria provedor, aviso e conciliação no Hostto, no Zé e em cada cliente.

O console dentro do admin foi rejeitado: mistura o operador da plataforma com o financeiro do cliente e atrapalha a venda para terceiros.

Um backend separado do airtestto foi rejeitado: perderia o modelo de tenant, o tópico de mensagens e o `bank` que já existem.

Virar subadquirente foi rejeitado: exige regras do Banco Central e contratos com as bandeiras, sem ganho para o problema.
