# ADR: apuração dos impostos dos imóveis por sócio

**Status:** aberta em 2026-11-21.
**Produto:** [Impostos dos imóveis por sócio](../prd/2026-11-21-impostos-imoveis-socios.md).

## Contexto

A Testto está no Lucro Presumido, e o cálculo das guias e da parte de cada sócio vive numa planilha. Para entender como o dinheiro anda, comparamos a planilha com o extrato do Inter da Testto que o airtestto já guarda, de janeiro de 2025 a novembro de 2026.

### Guias

As guias pagas batem com a planilha:

- **PIS e COFINS:** saem por mês sobre a receita do mês e são pagos perto do dia 25 do mês seguinte.
- **IRPJ e CSLL:** saem por trimestre do calendário, em cota única, no último dia útil do mês seguinte ao trimestre. O valor é a soma dos três valores mensais da planilha (4,8% e 2,88% da receita do mês).

Isso vale para todos os trimestres pagos, do segundo de 2025 ao terceiro de 2026. A janela móvel que aparece em algumas células da planilha não chegou às guias.

Além das guias do período, a Testto paga todo mês uma parcela de um parcelamento de impostos atrasados. Essa parcela não sai da apuração.

### Receita

A receita de cada imóvel chega por canais diferentes:

- **Locação longa:** repasse da imobiliária por uma plataforma de cobrança, perto do mesmo dia todo mês, ou Pix direto do inquilino ou do corretor. Um imóvel pode ter mais de um inquilino e mais de um canal.
- **Temporada:** repasses das plataformas, já líquidos, vários por mês. O repasse não diz de qual anúncio veio.
- **Conta compartilhada:** pela mesma conta entram receitas que não são aluguel, como eventos, aportes e estornos.

A planilha lança cada aluguel no mês em que o dinheiro entrou, e foi sobre essa base que as guias foram pagas.

### Sócios

O aluguel de cada imóvel é repassado por Pix ao sócio dono dele. O sócio devolve à Testto, também por Pix, a parte dele nas guias e nas despesas comuns: contabilidade e parcelamento.

### O que o código tem hoje

- O airtestto tem o razão de imóveis (`financial`), com competência e data de caixa, e o extrato do Inter (`bank`). Os dois não se ligam.
- Não existe nenhum conceito de imposto ou guia.
- O dono do imóvel já existe como papel de pessoa, com a posse em porcentagem.

## Decisão

**A receita do imóvel vem do razão de imóveis.**
- Cada crédito do Inter da Testto que for aluguel vira um lançamento de receita do imóvel no `financial`.
- O lançamento aponta para a transação do banco, guarda a data do crédito como data de caixa e o mês do aluguel como competência.
- O extrato não ganha campo de imóvel. O livro do caixa continua separado do razão de imóveis.

**A competência é o mês do aluguel.**
- Por padrão é o mês do crédito, que é o que a planilha e as guias pagas usam.
- A pessoa muda a competência do lançamento quando o crédito se refere a outro mês.
- A apuração sempre soma por competência.

**A base da temporada é o repasse líquido da plataforma.**
- O valor do crédito é a receita, sem reconstruir o valor bruto da reserva.
- Quando o repasse não identifica o imóvel, ele vai para a revisão. A escolha ali é manual, com sugestão pela reserva do calendário quando houver.

**Regras por pagador classificam o crédito.**
- Documento, nome ou descrição levam ao imóvel e à categoria.
- O que não casa com nenhuma regra fica na revisão, e a escolha feita lá pode virar regra.
- Créditos que não são aluguel são marcados como tal e não entram na base.

**Um domínio novo de apuração, `tax`, com path `/tax`.**
- Ele lê o razão e não guarda receita.
- Guarda o regime da empresa e as alíquotas e a presunção com vigência, para a mudança da reforma tributária entrar como dado.
- Calcula PIS e COFINS por mês, e IRPJ e CSLL por trimestre do calendário, com o adicional quando o lucro presumido do trimestre passar do limite.
- Fecha o período guardando a base, os valores e o rateio.

**IRPJ e CSLL em cota única.** A guia do trimestre vence no último dia útil do mês seguinte. O parcelamento em três cotas fica fora até haver um caso.

**O rateio segue a posse dos imóveis.**
- A parte de cada sócio é o imposto do período vezes a fatia dele na receita do período.
- A fatia vem da posse de cada imóvel no papel de dono da pessoa, que soma 100% por imóvel.

**Guias são registros do domínio `tax`.**
- Cada guia tem tipo, código de receita, período, valor, vencimento e código de barras.
- A guia fica paga quando um débito do extrato com valor e data compatíveis é confirmado.
- A parcela de parcelamento é um tipo de guia sem apuração por trás.

**O front mostra o número que a API devolve.**

## Consequências

- O `financial` passa a consumir o evento de transações capturadas do banco e ganha a origem "transação bancária".
- O domínio `tax` ganha tabelas, uma stack de API com papéis do financeiro e um design próprio no [airtestto-docs](https://wmreis-labs.github.io/airtestto-docs/mudancas/).
- O Hostto ganha `/financial/impostos` e a revisão de recebimentos, com design no [hostto-docs](https://wmreis-labs.github.io/hostto-docs/mudancas/).
- O histórico da planilha entra uma vez no razão.
- As guias já pagas servem de gabarito para o motor: o cálculo tem de reproduzir cada guia de PIS, COFINS, IRPJ e CSLL paga desde o segundo trimestre de 2025.
- Mudar a competência de um lançamento depois do fechamento aparece como diferença no período, não altera o fechado.

## Em aberto

- **Despesas comuns.** O extrato mostra que os sócios devolvem mais do que a parte deles nas guias: devolvem também parte da contabilidade e do parcelamento. A regra dessa divisão ainda não está escrita, e a planilha não a calcula. Até ser decidida, o domínio mostra só a parte de cada sócio nas guias do período.
- **Conta do sócio.** Os Pix que a Testto manda ao sócio e os que ele devolve poderiam ser conciliados numa conta do sócio, mostrando o saldo dele. Isso depende da regra das despesas comuns.

## Alternativas

- **Ler a receita direto do extrato no domínio de impostos.** Rejeitado: o razão de imóveis já tem imóvel, competência e lançamento manual, e o extrato não sabe de imóvel.
- **Calcular a receita pelo contrato ou pela reserva.** Rejeitado como fonte: o contrato não registra o recebido, e a base da temporada é o repasse que caiu na conta. A reserva fica só como sugestão na revisão.
- **Pôr o cálculo no front.** Rejeitado: a regra fiscal tem vigência e precisa de teste com gabarito. A tela só mostra o que a API calcula.
