# ADR: autenticação e autorização dos fronts

**Status:** aberta
**Referência:** console do [ATO](https://wmreis-labs.github.io/ato-docs/), que já entra direto no pool dele.

## Contexto

Cada sistema tem o próprio User Pool por ambiente: `AuthUserPool` no Hostto, `AtoUserPool-{stage}` no ATO, `ZesalgadosUserPool-{stage}` no Zé Salgados e `PlatformUserPool-{stage}` no admin central. Os fronts entram de jeitos diferentes:

| Front | Login hoje |
| --- | --- |
| ATO | Amplify Auth direto no pool, variáveis no Amplify |
| Zé Salgados | Amplify Auth direto no pool; `REACT_APP_TENANT` vai no `x-tenant` |
| Hostto | `POST /auth/signin` e `/auth/refresh`; client de reserva fixo no código |
| Admin | `/login` no pool do Hostto com `x-tenant: admin`; a tela de identidade escolhe o client do `PlatformUserPool` pelo hostname |

Do lado da API, as stacks de negócio usam um authorizer que aceita o access token de qualquer pool da conta: ele chama `GetUser` e não confere o emissor nem o grupo. A maior parte dos handlers lê o tenant do `x-tenant`, e, sem o header, do subdomínio do `Host` ou do `Referer`. Quem decide a empresa da chamada é o build do front, não o usuário. O authorizer do ATO também não confere o emissor e aceita o grupo `Admin`, que existe no pool do Hostto.

## Decisão

1. **Login direto no Cognito.** Todo front autentica no pool do próprio produto, sem Lambda no caminho: entrar, trocar a senha no primeiro acesso, renovar e sair. O módulo de auth do `ato-console` (Amplify Auth v6) é o padrão que os outros fronts adotam.
2. **Configuração no Amplify.** Cada app guarda, em dev e em produção, o id do pool, o client, a região e a URL da API. Os nomes são os mesmos em todos os apps, com o prefixo do bundler (`REACT_APP_` ou `VITE_`): `COGNITO_USER_POOL_ID`, `COGNITO_CLIENT_ID`, `COGNITO_REGION`, `API_URL`. Nenhum id fica no código nem tem valor de reserva. Sem a variável, a tela de login diz que o app não está configurado.
3. **O front manda só o token.** Toda chamada leva `Authorization: Bearer <access token>`. `REACT_APP_TENANT` deixa de existir. `x-tenant` só sai do front quando um operador de plataforma escolhe a empresa na tela.
4. **A Lambda entra só no authorizer.** Uma implementação única, configurada por stack com os pools aceitos, os grupos exigidos e os grupos que podem trocar de empresa. Ela confere assinatura, validade, `token_use=access` e o `iss` contra os pools da stack, confere o grupo e resolve o `custom:tenant` do usuário. Token de outro pool recebe 401.
5. **O tenant vem do authorizer.** O handler usa a empresa que o authorizer devolveu. Resolver o tenant por header, host ou referer fica só nas rotas públicas, sem token.
6. **Troca de empresa só para operador.** O `x-tenant` diferente do tenant do token passa apenas para os grupos de troca da stack (`AdminPlataforma` no ATO, operador do caixa no admin) e só para empresas da lista permitida. Fora disso a chamada é recusada.
7. **Pool por API.** Cada stack lista os pools que aceita. O token do `PlatformUserPool` não abre API de produto, exceto nas stacks que o listam (caixa, `bank` e `inter`).

## Consequências

O backend muda antes dos fronts. O authorizer novo entra primeiro só registrando quem seria recusado; os fronts de hoje mandam um `x-tenant` igual ao tenant do token, então ligar a recusa não derruba ninguém. Só depois cada front tira o `x-tenant`, um app por vez, com as variáveis publicadas no Amplify antes do deploy.

O ATO passa a conferir o emissor já no primeiro corte. Isso fecha a entrada do grupo `Admin` do Hostto no console.

O access token do Cognito não traz atributo customizado. O authorizer busca o `custom:tenant` com `GetUser` e guarda o resultado por alguns minutos por token.

O caixa grava no tenant `admin`. Essa é a primeira empresa da lista do operador; mover os livros para outra empresa é decisão à parte.

As rotas `/auth/*` saem do caminho dos fronts web e continuam no ar enquanto `hostto-app` e `zesalgados-app` dependerem delas. Os apps adotam o mesmo módulo em cortes próprios.

Esta decisão completa o ADR *Tenant em toda tabela e em toda busca* (2026-11-17): a empresa já vinha da sessão; agora o header deixa de existir para o usuário comum.

## Alternativas

Login por Lambda (`/auth/signin`): rejeitado. A Lambda repete o que o Cognito faz e é ela que hoje amarra a empresa ao build do front.

Authorizer nativo do API Gateway para Cognito: rejeitado. Ele confere pool e escopo, mas não resolve a empresa nem a troca do operador.

Pôr `custom:tenant` no access token com o gatilho Pre Token Generation: adiado. Exige o plano pago do Cognito; o `GetUser` com cache basta por enquanto.

Um pool só para todos os sistemas: rejeitado pelo plano de identidade por sistema e pelo pool dedicado do ATO.
