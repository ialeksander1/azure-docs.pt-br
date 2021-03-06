---
title: Azure Active Directory quebrando mudanças de referência
description: Saiba mais sobre as alterações realizadas nos protocolos do Azure AD que podem afetar o aplicativo.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a60b927f7239818b582ffcd85ddb4b7d69594482
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535954"
---
# <a name="whats-new-for-authentication"></a>Quais são as novidades para autenticação?

>Seja notificado sobre atualizações nesta página. Basta adicionar [esta URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) ao leitor de RSS feed.

O sistema de autenticação altera e adiciona recursos em uma base contínua para melhorar a segurança e a conformidade com os padrões. Para manter-se atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre os seguintes detalhes:

- Os recursos mais recentes
- Problemas conhecidos
- Alterações do protocolo
- Funcionalidades preteridas

> [!TIP]
> Esta página é atualizada regularmente, então, visite-a com frequência. Salvo indicação contrária, essas alterações só serão aplicadas para aplicativos registrados recentemente.

## <a name="upcoming-changes"></a>Alterações futuras

Nenhum agendado neste momento.  Veja abaixo as mudanças que estão dentro ou chegando à produção.

## <a name="march-2020"></a>Março de 2020

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>As senhas de usuário serão restritas a 256 caracteres.

**Data de vigência**: 13 de março de 2020

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo impactado**: Todos os fluxos de usuário.

Os usuários com senhas com mais de 256 caracteres que fazem login diretamente no Azure AD (em oposição a um IDP federado como o ADFS) não poderão entrar a partir de 13 de março de 2020 e serão solicitados a redefinir sua senha.  Os admins podem receber solicitações para ajudar a redefinir a senha dos usuários.

O erro nos logs de login será AADSTS 50052: InvalidPasswordExceedsMaxLength

Mensagem:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Remediação:

O usuário não pode fazer login porque sua senha excede o comprimento máximo permitido. Eles devem entrar em contato com o seu admin para redefinir a senha. Se o SSPR estiver habilitado para o inquilino, ele poderá redefinir sua senha seguindo o link "Esqueci sua senha".



## <a name="february-2020"></a>Fevereiro de 2020

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Fragmentos vazios serão anexados a cada redirecionamento HTTP do ponto final de login.

**Data de vigência**: 8 de fevereiro de 2020

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo impactado**: Fluxos OAuth e OIDC que usam response_type=consulta - isso abrange o fluxo de [código de autorização](v2-oauth2-auth-code-flow.md) em alguns casos, e o fluxo [implícito](v2-oauth2-implicit-grant-flow.md).

Quando uma resposta de autenticação é enviada de login.microsoftonline.com para um aplicativo via redirecionamento HTTP, o serviço anexará um fragmento vazio à URL de resposta.  Isso evita uma classe de ataques de redirecionamento, garantindo que o navegador elimine qualquer fragmento existente na solicitação de autenticação.  Nenhum aplicativo deve ter uma dependência desse comportamento.


## <a name="august-2019"></a>Agosto de 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>Semântica pós-forma será aplicada mais estritamente - espaços e citações serão ignorados

**Data de vigência**: 2 de setembro de 2019

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo impactado**: Em qualquer lugar usado[(credenciais do cliente,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) [resgate de código de autorização,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow) [ROPC,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)e [resgate de tokens de atualização](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token))

A partir da semana de 02/09, as solicitações de autenticação que utilizam o método POST serão validadas usando padrões HTTP mais rigorosos.  Especificamente, espaços e aspas duplas (") não serão mais removidos dos valores do formulário de solicitação. Essas alterações não devem quebrar nenhum cliente existente e garantirão que as solicitações enviadas ao Azure AD sejam tratadas de forma confiável todas as vezes. No futuro (ver acima) planejamos rejeitar adicionalmente parâmetros duplicados e ignorar o BOM dentro das solicitações.

Exemplo:

Hoje, `?e=    "f"&g=h` é analisado de `?e=f&g=h` forma idêntica `e`  ==  `f`como - assim .  Com essa mudança, seria agora analisado de `e`  ==  `    "f"` modo que - este é improvável ser um argumento válido, e o pedido agora fracassaria.


## <a name="july-2019"></a>Julho de 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Os tokens somente de aplicativos para aplicativos de inquilino único só são emitidos se o aplicativo cliente existir no inquilino de recursos

**Data de vigência**: 26 de julho de 2019

**Pontos finais impactados**: [Tanto v1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) quanto [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protocolo impactado:** [Credenciais do cliente (tokens somente para aplicativos)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Uma mudança de segurança entrou em operação em 26 de julho, que altera a forma como os tokens somente de aplicativos (através da concessão de credenciais do cliente) são emitidos. Anteriormente, os aplicativos eram autorizados a obter tokens para chamar qualquer outro aplicativo, independentemente da presença no inquilino ou funções consentiu para esse aplicativo.  Esse comportamento foi atualizado para que, para que os recursos (às vezes chamados de APIs da Web) definidos como inquilino único (o padrão), o aplicativo cliente deve existir dentro do inquilino de recursos.  Observe que o consentimento existente entre o cliente e a API ainda não é necessário, `roles` e os aplicativos ainda devem estar fazendo suas próprias verificações de autorização para garantir que uma reclamação esteja presente e contenha o valor esperado para a API.

A mensagem de erro para este cenário atualmente afirma:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Para remediar esse problema, use a experiência de Consentimento De Dum para criar o principal de serviço de solicitação do cliente em seu inquilino ou criá-lo manualmente.  Essa exigência garante que o inquilino tenha dado permissão ao aplicativo para operar dentro do inquilino.

#### <a name="example-request"></a>Solicitação de exemplo

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`Neste exemplo, o inquilino de recursos (autoridade) é contoso.com, o `gateway.contoso.com/api` aplicativo de recursos é um `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`aplicativo de inquilino único chamado para o inquilino contoso, e o aplicativo cliente é .  Se o aplicativo do cliente tiver um principal de serviço dentro de Contoso.com, essa solicitação pode continuar.  Se isso não funcionar, no entanto, a solicitação falhará com o erro acima.

Se o aplicativo de gateway Contoso fosse um aplicativo multi-inquilino, no entanto, a solicitação continuaria independentemente do aplicativo cliente ter um principal de serviço dentro Contoso.com.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Uris de redirecionamento agora podem conter parâmetros de seqüência de consulta

**Data de vigência**: 22 de julho de 2019

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo impactado**: Todos os fluxos

Por [RFC 6749,](https://tools.ietf.org/html/rfc6749#section-3.1.2)os aplicativos Azure AD agora podem se registrar e usar URIs de redirecionamento (resposta) com parâmetros de consulta estática (como `https://contoso.com/oauth2?idp=microsoft`) para solicitações OAuth 2.0.  Uris de redirecionamento dinâmico ainda são proibidos, pois representam um risco de segurança, e isso `state` não pode ser usado para reter informações de estado através de uma solicitação de autenticação - para isso, use o parâmetro.

O parâmetro de consulta estática está sujeito à correspondência de seqüência de strings para redirecionar URIs como qualquer outra parte do URI de redirecionamento - se nenhuma seqüência for registrada que corresponda ao redirect_uri decodificado uri, então a solicitação será rejeitada.  Se o URI for encontrado no registro do aplicativo, toda a seqüência será usada para redirecionar o usuário, incluindo o parâmetro de consulta estática.

Observe que neste momento (final de julho de 2019), o ux de registro de aplicativo no portal Azure ainda bloqueia os parâmetros de consulta.  No entanto, você pode editar o manifesto do aplicativo manualmente para adicionar parâmetros de consulta e testá-lo em seu aplicativo.


## <a name="march-2019"></a>Março de 2019

### <a name="looping-clients-will-be-interrupted"></a>Os clientes de looping serão interrompidos

**Data de vigência**: 25 de março de 2019

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo impactado**: Todos os fluxos

Os aplicativos clientes às vezes podem se comportar mal, emitindo centenas da mesma solicitação de login em um curto período de tempo.  Essas solicitações podem ou não ser bem sucedidas, mas todas contribuem para a baixa experiência do usuário e cargas de trabalho aumentadas para o IDP, aumentando a latência para todos os usuários e reduzindo a disponibilidade do IDP.  Essas aplicações estão operando fora dos limites de uso normal, e devem ser atualizadas para se comportarem corretamente.

Os clientes que emitirem solicitações duplicadas várias vezes serão enviados um `invalid_grant` erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`.

A maioria dos clientes não precisará mudar de comportamento para evitar esse erro.  Apenas clientes mal configurados (aqueles sem cache de token ou aqueles que já exibem loops imediatos) serão impactados por esse erro.  Os clientes são rastreados por instância localmente (via cookie) sobre os seguintes fatores:

* Dica do usuário, se houver

* Escopos ou recursos sendo solicitados

* ID do Cliente

* URI de redirecionamento

* Tipo e modo de resposta

Os aplicativos que fizerem várias solicitações (15+) em `invalid_grant` um curto período de tempo (5 minutos) receberão um erro explicando que estão em looping.  Os tokens solicitados têm vida útil suficientemente longa (mínimo de 10 minutos, 60 minutos por padrão), de modo que solicitações repetidas durante esse período de tempo são desnecessárias.

Todos os `invalid_grant` aplicativos devem lidar mostrando um prompt interativo, em vez de solicitar silenciosamente um token.  Para evitar esse erro, os clientes devem garantir que estão registrando corretamente os tokens que recebem.


## <a name="october-2018"></a>Outubro de 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Não é mais possível reutilizar códigos de autorização

**Data de efetivação**: 15 de novembro de 2018

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolo afetado**: [Fluxo de código](v2-oauth2-auth-code-flow.md)

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Qualquer aplicativo novo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para obter mais informações sobre tokens de atualização, consulte [Atualização de tokens de acesso](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Se usar a ADAL ou MSAL, isso é feito para você pela biblioteca – substituir a segunda instância de 'AcquireTokenByAuthorizationCodeAsync' por 'AcquireTokenSilentAsync'.

## <a name="may-2018"></a>Maio de 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Os tokens de ID não podem ser usados para o fluxo OBO

**Data**: 1º de maio de 2018

**Pontos de extremidade afetados**: v1.0 e v2.0

**Protocolos impactados**: Fluxo implícito e [em nome do fluxo](v2-oauth2-on-behalf-of-flow.md)

Após 1 de maio de 2018, os id_tokens não podem ser usados como a declaração em um Fluxo OBO para novos aplicativos. Em vez disso, é necessário usar tokens de acesso para proteger as APIs, até mesmo entre um cliente e a camada intermediária do mesmo aplicativo. Os aplicativos registrados antes de 1º de maio de 2018 continuarão funcionando e poderão trocar id_tokens por um token de acesso, no entanto, esse padrão não é considerado uma melhor prática.

Para contornar essa alteração, é possível fazer o seguinte:

1. Crie uma API web para sua aplicação, com um ou mais escopos. Esse ponto de entrada explícito permitirá que segurança e controle mais precisos.
1. No manifesto do aplicativo, no [portal do Azure](https://portal.azure.com) ou no [portal de registro do aplicativo](https://apps.dev.microsoft.com), verifique se o aplicativo tem permissão para emitir tokens de acesso por meio do fluxo implícito. Isso é controlado pela chave `oauth2AllowImplicitFlow`.
1. Quando seu aplicativo cliente `response_type=id_token`solicitar um id_token via`response_type=token`, também solicite um token de acesso ( ) para a API web criada acima. Portanto, ao usar o ponto de extremidade v 2.0, o parâmetro `scope` deverá ser semelhante ao `api://GUID/SCOPE`. No ponto de extremidade v1.0, o parâmetro `resource` deve ser o URI da API do aplicativo Web.
1. Passe esse token de acesso para a camada intermediária no lugar de id_token.
