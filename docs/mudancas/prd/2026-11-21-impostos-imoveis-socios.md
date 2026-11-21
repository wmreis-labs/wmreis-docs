# PRD: impostos dos imóveis por sócio

**Status:** aberta
**Data:** 2026-11-21
**Produto:** Hostto

## Problema

A Testto aluga imóveis e paga os tributos do Lucro Presumido sobre a receita desses aluguéis. Cada imóvel pertence, na divisão entre os sócios, a um sócio. O imposto sai numa guia só, mas cada sócio responde pela parte que vem dos seus imóveis.

Hoje essa conta vive numa planilha mantida à mão:

- Alguém copia do extrato cada aluguel recebido, com o imóvel e o mês.
- A planilha soma a receita do mês, aplica as alíquotas e divide o imposto pela fatia de cada sócio na receita.
- O dono de cada imóvel está escrito dentro das fórmulas.
- As guias emitidas ficam numa aba à parte, que parou de ser atualizada, e nada diz se foram pagas.

O extrato da conta do Inter já entra no Hostto a cada hora, e o Hostto já tem o razão de cada imóvel. Mesmo assim, o crédito do banco não vira receita do imóvel, e não existe nenhum conceito de imposto ou de guia no sistema. A planilha também tem erros que ninguém vê:

- O trimestre do IRPJ e da CSLL virou uma janela móvel dos três meses anteriores, em vez do trimestre do calendário.
- Há duas alíquotas de estimativa diferentes.
- Falta o adicional do IRPJ.

## Quem usa

- **O financeiro da Testto no Hostto:**
  - Classifica o que o extrato não identificou sozinho.
  - Fecha o mês e o trimestre.
  - Cadastra as guias e confere o pagamento.
- **Os sócios:** querem saber quanto receberam dos seus imóveis e quanto devem das guias no mês, no trimestre e no ano.
- **A contabilidade:** recebe a base do período e compara com as guias que emite.

## Resultado esperado

1. O aluguel que cai na conta aparece como receita do imóvel certo, no mês certo, sem digitação. Quando o sistema não sabe de qual imóvel é, o crédito espera numa fila curta de revisão. A escolha feita ali vale para os próximos créditos do mesmo pagador.
2. A pessoa abre a tela de impostos e vê:
   - a receita de cada imóvel mês a mês;
   - a base e cada tributo do mês e do trimestre;
   - quanto cada sócio recebeu e deve.
3. Ao fechar o período, os números ficam guardados. O que chegar depois aparece como diferença.
4. As guias mostram o vencimento e ficam pagas quando o débito aparece no extrato. A diferença entre o valor estimado e o valor da guia fica visível.
5. A planilha deixa de ser necessária.

## Escopo

1. **Sócios do imóvel:** a ficha do imóvel mostra os sócios e a posse de cada um, que soma 100%.
2. **Receita a partir do extrato:**
   - O crédito da conta da Testto vira receita do imóvel, com a data em que caiu e o mês de competência.
   - Regras por pagador ligam o crédito ao imóvel. O que não casa vai para revisão.
3. **Histórico:** a receita mensal dos imóveis desde outubro de 2024 entra uma vez, a partir da planilha.
4. **Apuração no Lucro Presumido:**
   - PIS e COFINS por mês; IRPJ e CSLL por trimestre do calendário, com o adicional quando houver.
   - As alíquotas e a presunção têm vigência, para a mudança da reforma tributária entrar como configuração.
   - O período aberto recalcula; o período fechado fica guardado.
5. **Rateio por sócio:** o imposto do período é dividido pela fatia de cada sócio na receita, pela posse de cada imóvel. A visão sai por mês, trimestre e ano, com a média mensal, e exporta para planilha.
6. **Guias:**
   - Cada guia tem tipo, período, valor, vencimento e código de barras, e passa por prevista, emitida e paga.
   - O pagamento é confirmado a partir do débito do extrato.
7. **Paralelo:** dois fechamentos mensais com a planilha e o sistema lado a lado antes de largar a planilha.

## Fora do escopo

- Pagar a guia pelo sistema. O pagamento continua no banco; o sistema só confere o débito.
- A apuração da Miralis, que está no Simples Nacional.
- Despesas dos imóveis na apuração. O Lucro Presumido incide sobre a receita.
- Regras da reforma tributária além da troca de alíquotas por vigência.
- Emitir a guia na Receita.

## Perguntas à contabilidade

Estas respostas fecham o desenho antes do código:

- A receita do aluguel entra pela data do crédito (caixa) ou pelo mês do aluguel (competência)?
- Na locação por temporada, a base é o repasse líquido da plataforma ou o valor bruto da reserva?
- IRPJ e CSLL saem em cota única ou em três cotas?

## Critério de aceite

1. Em dois meses seguidos, a receita de cada imóvel e o valor por sócio no sistema batem com a planilha.
2. O trimestre do IRPJ e da CSLL segue o calendário.
3. O aluguel de um pagador conhecido entra no imóvel certo sem ação manual. O de um pagador novo aparece na revisão e, depois de classificado, entra no razão do imóvel.
4. As guias do período aparecem pagas a partir do extrato.
5. Quem tem o papel Leitor vê os números e não fecha período.

## Métrica

A pergunta que a tela responde é quanto cada sócio deve das guias deste mês e deste trimestre, e se elas já foram pagas. O sinal de sucesso é fechar o mês sem abrir a planilha e sem copiar lançamentos do extrato.

## Decisões e desenho

- ADR: a escrever ([wmreis-docs#16](https://github.com/wmreis-labs/wmreis-docs/issues/16)). Registra que a receita vem do razão de imóveis alimentado pelo extrato, o domínio novo de apuração e a vigência das alíquotas.
- Design do serviço: a escrever.
- Design da tela: a escrever.
