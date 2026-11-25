# ADR: chave de serviço emitida pelo admin

**Status:** aceita em 2026-11-25
**Design do serviço:** [Chave de serviço](https://wmreis-labs.github.io/airtestto-docs/mudancas/design/2026-11-25-chave-de-servico/)
**Design da tela:** [Chaves](https://wmreis-labs.github.io/admin-docs/mudancas/design/2026-11-25-chaves/)

## Contexto

Um sistema da Testto precisa ler a API de uma empresa sem o access token de uma pessoa. Esse token expira. A primeira chamada é o ATO lendo a agenda da Miralis. A mesma porta serve depois para outra leitura ou para outro sistema.

O atalho que compara o Bearer com um segredo único da conta passa em qualquer rota. Ele não está ligado no authorizer da API principal e não deve ser ligado. A chave desta decisão substitui esse atalho, com empresa e lista de rotas fechadas.

## Decisão

1. O admin da Testto emite a chave. Ela vale para uma empresa e para as rotas marcadas na emissão. Não expira por tempo. Revogar corta a chamada seguinte.
2. O pacote `platform-admin` é o dono do registro. Guarda o SHA-256 do segredo. O texto puro aparece uma vez, na resposta de criação, e não volta na lista nem no log.
3. O authorizer da API principal que protege imóvel e calendário confere a chave com uma leitura direta. Não chama o Cognito. A empresa do cabeçalho tem de ser a da chave. Método e caminho têm de estar na lista gravada. Grupo de pessoa não entra nessa autorização: a lista da chave é a permissão.
4. Qualquer outro Bearer continua no fluxo de pessoa, com pool, papel e empresa.
5. A chave de um sistema de fora, como a de um parceiro guardada no ATO, continua nascendo naquele sistema. O admin emite a chave de quem chama a API da Testto.

## Consequências

Trocar o catálogo depois não alarga uma chave já gravada: as rotas são copiadas no momento de gravar. Outra chave é outra criação, com outro segredo. Chave revogada não volta a ligar e não troca de acessos.

Produção desta chave espera um OK explícito. A primeira entrega cobre a leitura da agenda do imóvel.

## Alternativas

Access token de uma pessoa de serviço: rejeitado. Expira e carrega grupo, e um grupo de pessoa abriria o resto da API.

Um segredo único da conta, válido em qualquer rota: rejeitado. Não dá para limitar a empresa nem a leitura, e revogar derrubaria todo mundo.
