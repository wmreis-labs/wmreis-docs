# ADR: emissor fiscal próprio

**Status:** aberta em 2026-11-20.
**Produto:** [Emitto, emissor fiscal](../prd/2026-11-20-emitto-emissor-fiscal.md).

## Contexto

Zé Salgados precisa de NFC-e no PDV e de NF-e para clientes com CNPJ. A Miralis e o ATO podem precisar de NFS-e quando a reserva ou o plano é pago. A WMReis emite nota de serviço todo mês. Nenhum sistema emite documento fiscal hoje, e o airtestto não guarda inscrição, regime, NCM nem CFOP.

O mercado resolve isso com emissores por API (Focus NFe, Nuvem Fiscal, PlugNotas, NFE.io). Eles cobram por documento e absorvem as mudanças de layout, mas cada sistema teria a própria integração, e o emissor não enxerga a venda, o caixa nem o canal de WhatsApp do ATO.

Falar direto com os órgãos é permitido e não exige intermediário. O custo é de manutenção: XML assinado com o certificado do emitente, conexão autenticada pelo mesmo certificado, um endereço por estado e por contingência, numeração sem buraco nem repetição, impressão do DANFE e várias notas técnicas por ano, além das mudanças da reforma tributária até 2033.

## Decisão

A WMReis constrói o próprio emissor, o Emitto, e ele é o único caminho de emissão do ecossistema. Os sistemas não falam com SEFAZ, prefeitura ou emissor de mercado.

O Emitto fala direto com as SEFAZ, com os ambientes de contingência e com o ambiente nacional da NFS-e. Atende primeiro os sistemas da Testto e da WMReis e nasce preparado para vários emitentes e vários clientes, para poder ser vendido depois.

O serviço mora no airtestto, com o mesmo modelo de tenant, em duas partes: um núcleo fiscal sem dependência de nuvem (montar o XML a partir dos schemas oficiais, validar, assinar, transmitir, imprimir) e o serviço em volta (API, filas, emitentes, numeração, guarda). O console é um aplicativo próprio, com pool de usuários próprio, seguindo a regra de um pool por sistema.

Pessoas entram no console pelo Cognito. Sistemas chamam a API com chave própria por cliente e ambiente, nunca com o token de um usuário. Os pacotes do airtestto podem pedir a nota por evento no tópico de mensagens. Todo pedido carrega uma chave de idempotência: a mesma venda nunca gera duas notas. O resultado volta por evento e por webhook assinado.

O sistema que vende manda o fato: quem comprou, o quê, por quanto e como pagou. A tributação sai das regras fiscais cadastradas no Emitto, com vigência, e pode ser sobrescrita item a item.

O certificado digital de cada emitente fica no Secrets Manager, cifrado com chave própria, lido só pela função que assina, com aviso antes do vencimento.

Regras de validação, tabelas e endereços dos órgãos ficam como dados versionados com vigência, e os tipos do XML são gerados dos schemas oficiais. Um radar acompanha os portais oficiais todos os dias; a IA resume cada mudança, abre a tarefa e, quando a mudança é mecânica, o pull request. Merge e publicação seguem o fluxo de sempre, com aprovação humana.

A emissão passa por uma porta única de provedor. Um emissor de mercado pode ficar configurado como reserva da NFC-e até o emissor próprio provar estabilidade no caixa.

## Consequências

O desenho do serviço vai para o [airtestto-docs](https://wmreis-labs.github.io/airtestto-docs/mudancas/), com o contrato da API, os eventos e os estados do documento. O console, a identidade visual e a documentação pública da API ganham um site próprio do Emitto.

A primeira entrega é a NF-e do Zé Salgados em produção: o núcleo nasce com o layout 4.00 do modelo 55, a conexão com a SEFAZ da UF do Zé, o DANFE A4 e a tributação dos produtos do Zé. A NFC-e vem em seguida, aproveitando o mesmo layout, com contingência desde o primeiro dia. A NFS-e no padrão nacional vem depois, com a nota de serviço da própria WMReis como piloto, conferida com a emitida pela contabilidade, e então a Miralis e o ATO. CT-e só entra com um caso real.

A WMReis passa a responder pela manutenção fiscal: acompanhar notas técnicas, testar em homologação e publicar antes da vigência. Rejeição em produção é incidente do Emitto, não do sistema que vendeu.

Guardar certificado de outras empresas exige controle de acesso mínimo e auditoria desde o começo.

Produto do Zé e serviço da Miralis passam a ter classificação fiscal. O cadastro de pessoa passa a ter documento validado e endereço completo para destinatário de NF-e.

## Alternativas

Contratar um emissor de mercado foi rejeitado como caminho principal: custo por documento para sempre, uma integração por sistema e nenhuma ligação com venda, caixa e canal. Ele fica como possível reserva da NFC-e.

Usar os emissores gratuitos dos órgãos foi rejeitado: servem para nota avulsa digitada à mão, não para o PDV nem para emissão automática.

Integrar cada sistema direto com a SEFAZ foi rejeitado: repetiria certificado, numeração e contingência em quatro lugares.

Colocar o console dentro do admin foi rejeitado: mistura o operador da plataforma com o cliente do emissor e atrapalha a venda para terceiros.
