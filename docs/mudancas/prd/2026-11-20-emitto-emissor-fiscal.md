# PRD: Emitto, emissor fiscal

**Status:** aberta
**Data:** 2026-11-20
**Produto:** Emitto (novo), usado por Zé Salgados, Hostto, ATO e admin

## Problema

Nenhum sistema do ecossistema emite documento fiscal. O PDV do Zé Salgados imprime um cupom não fiscal. A cobrança das reservas e dos planos do ATO não gera nota de serviço. As notas de serviço da própria WMReis são pedidas por e-mail à contabilidade, uma a uma. Quando a venda acontece num sistema e a nota em outro lugar, a nota atrasa, o valor diverge e ninguém concilia.

Cada sistema poderia contratar um emissor de mercado e integrar por conta própria. Seriam quatro integrações, quatro cadastros de certificado e quatro jeitos de tratar rejeição, e nenhum deles conversaria com o caixa, com o pedido ou com o WhatsApp do ATO.

## Quem usa

- Quem vende: o caixa do Zé no PDV, o financeiro da Miralis no Hostto, o operador do ATO. Não quer saber de CFOP; quer a nota saindo sozinha e um aviso claro quando algo dá errado.
- Quem administra o emitente: cadastra o CNPJ, o certificado digital e as séries, e decide quando sair da homologação para a produção.
- O contador: confere o que foi emitido e recebe os arquivos do mês.
- O operador da WMReis: acompanha todos os emitentes, a disponibilidade dos órgãos e as mudanças de regra.
- Os sistemas: pedem a nota a partir da venda, da reserva ou da cobrança.

## Resultado esperado

A venda paga no PDV do Zé sai com NFC-e impressa, mesmo com a SEFAZ fora do ar. O pedido de um cliente com CNPJ sai com NF-e. A reserva e o plano pagos geram NFS-e quando há serviço tributável. A WMReis emite a própria nota de serviço sem depender de e-mail. Tudo isso por um único produto, o Emitto, que fala direto com as SEFAZ e com o ambiente nacional da NFS-e, guarda os arquivos por cinco anos e avisa cada sistema do resultado.

Quando a legislação muda, o Emitto sabe antes de a mudança valer: o que mudou, para quem, a partir de quando, e o que precisa ser feito no código.

## Escopo

1. Documentos: NFS-e no padrão nacional, NFC-e, NF-e e CT-e, cada um com emissão, consulta, cancelamento e, onde existe, carta de correção e inutilização de numeração.
2. Emitentes: vários CNPJs, cada um com inscrições, regime, certificado digital, séries e ambiente de homologação ou produção.
3. Regras fiscais por produto e serviço, com data de vigência, sugeridas por IA e confirmadas por uma pessoa.
4. Pedido de nota pelos sistemas, por API ou por evento, sem nota duplicada para a mesma venda; resultado avisado de volta.
5. Contingência da NFC-e e da NF-e, para o caixa não parar.
6. DANFE, DANFE NFC-e de 80 mm, DANFSe e DACTE; consulta pública do documento pela chave.
7. Console próprio: painel, documentos, emissão manual, emitentes, regras fiscais, numeração, contingência, integrações, normas, relatórios, auditoria e uso.
8. Perfis: operador da WMReis, administrador do cliente, financeiro, contador e leitor.
9. Rejeição explicada em português, com a correção sugerida.
10. Pacote mensal para a contabilidade: arquivos XML, PDF e resumo.
11. Radar normativo: acompanha as publicações oficiais, resume a mudança, abre a tarefa e, quando a mudança é mecânica, o pull request.
12. Campos de IBS e CBS desde o início.

## Fora do escopo

- NFS-e de município que não usa o padrão nacional, até existir um emitente nessa situação.
- Escrituração fiscal, apuração de imposto e envio de obrigações acessórias. Isso continua com a contabilidade.
- Manifestação do destinatário e download de notas recebidas.
- MDF-e.
- Cobrança de terceiros, contrato de nível de serviço e domínio próprio. O desenho admite vários clientes; a venda fica para depois.
- Mudança de regra aplicada sem revisão humana.

## Critério de aceite

- A nota de serviço mensal da WMReis sai pelo Emitto e confere com a emitida pela contabilidade.
- Uma venda paga no PDV do Zé gera uma única NFC-e autorizada e impressa; com a SEFAZ indisponível, a venda continua e a nota é transmitida depois.
- Um pedido com destinatário pessoa jurídica gera NF-e.
- Repetir o mesmo pedido não gera segunda nota.
- Uma rejeição aparece no console com o campo errado e a correção, e o reenvio corrigido é autorizado.
- O contador recebe o pacote do mês sem pedir.
- Uma nota técnica nova publicada nos portais oficiais aparece no console e no quadro do GitHub em até um dia útil, com resumo e data de vigência.

## Métrica

- Vendas, reservas e cobranças pagas que têm documento autorizado, sobre as que deveriam ter.
- Tempo entre o pagamento e a autorização.
- Rejeições por mil documentos, e quanto tempo levam para ser corrigidas.
- Mudanças normativas implementadas antes da data de vigência.

## Perguntas em aberto

- Quais CNPJs emitem, com que inscrição e em que município.
- Se a locação por temporada da Miralis exige documento, ou só os serviços em volta.
- CFOP e situação tributária dos produtos do Zé.
- Registro da marca Emitto e do domínio.

## Decisões e desenho

A decisão de construir um emissor próprio está em [Emissor fiscal próprio](../adr/2026-11-20-emissor-fiscal-proprio.md). O desenho do serviço, com rotas, eventos e campos, vai para o [airtestto-docs](https://wmreis-labs.github.io/airtestto-docs/mudancas/); as telas e a identidade visual, para o site próprio do Emitto.
