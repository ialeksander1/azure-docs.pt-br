---
title: As credenciais do cliente OAuth 2.0 fluem na plataforma de identidade da Microsoft | Azure
description: Crie aplicativos web usando a implementação da plataforma de identidade Microsoft do protocolo de autenticação OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 36a5fdf990432e3a41cf8fc578fa20b4910250b2
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868450"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>A plataforma de identidade da Microsoft e o fluxo de credenciais do cliente OAuth 2.0

Você pode usar a [ concessão de credenciais do cliente OAuth 2.0 ](https://tools.ietf.org/html/rfc6749#section-4.4) especificada na RFC 6749, às vezes chamada de * OAuth de duas etapas *, para acessar recursos hospedados na Web usando a identidade de um aplicativo. Esse tipo de concessão normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. Esses tipos de aplicativo normalmente são referidos como *daemons* ou *contas de serviço*.

Este artigo descreve como programar diretamente contra o protocolo em sua aplicação. Quando possível, recomendamos que você use as Bibliotecas de Autenticação Microsoft (MSAL) suportadas em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Também dê uma olhada nos [aplicativos de exemplo que usam msal](sample-v2-code.md).

O fluxo de concessão de credenciais do cliente OAuth 2.0 permite que um serviço Web (cliente confidencial) use suas próprias credenciais, em vez de representar um usuário, para autenticar ao chamar outro serviço Web. Nesse cenário, o cliente é geralmente um serviço Web de camada intermediária, um serviço daemon ou um site. Para um nível mais alto de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

No tipo mais comum de *OAuth com três pernas*, um aplicativo cliente tem permissão para acessar um recurso em nome de um usuário específico. A permissão é delegada do usuário para o aplicativo, geralmente durante o processo de [consentimento](v2-permissions-and-consent.md) . No entanto, no fluxo de credenciais do cliente (*OAuth de dois segmentos*), as permissões são concedidas diretamente ao próprio aplicativo. Quando o aplicativo apresenta um token a um recurso, o recurso impõe que o próprio aplicativo tenha autorização para executar uma ação, e não o usuário.

## <a name="protocol-diagram"></a>Diagrama de protocolo

Todo o fluxo de credenciais do cliente é semelhante ao diagrama a seguir. Descrevemos cada uma das etapas neste artigo.

![Diagrama mostrando o fluxo de credenciais do cliente](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Obter autorização direta

Um aplicativo geralmente recebe autorização direta para acessar um recurso de duas maneiras:

* [Por meio de uma ACL (lista de controle de acesso) no recurso](#access-control-lists)
* [Por meio da atribuição de permissão de aplicativo no Azure AD](#application-permissions)

Esses dois métodos são os mais comuns no Azure AD e são recomendados para clientes e recursos que executam o fluxo de credenciais de cliente. Um recurso também pode optar por autorizar seus clientes de outras maneiras. Cada servidor de recursos pode escolher o método que faz mais sentido para seu aplicativo.

### <a name="access-control-lists"></a>Listas de controle de acesso

Um provedor de recursos pode impor uma verificação de autorização com base em uma lista de IDs de aplicativo (cliente) que ele conhece e concede um nível específico de acesso. Quando o recurso recebe um token do ponto final da plataforma de identidade da Microsoft, ele `appid` `iss` pode decodificar o token e extrair o ID do aplicativo do cliente das reclamações. Em seguida, compara o aplicativo com uma ACL (lista de controle de acesso) que ele mantém. Granularidade e o método a ACL podem variar significativamente entre os recursos.

Um caso de uso comum é usar uma ACL para executar testes para um aplicativo Web ou para uma API Web. A API Web pode conceder apenas um subconjunto das permissões completas para um cliente específico. Para executar testes de ponta a ponta na API, crie um cliente de teste que adquira tokens do ponto final da plataforma de identidade da Microsoft e, em seguida, os envie para a API. A API então verifica a ACL para a ID do aplicativo do cliente de teste a fim de ter acesso completo à funcionalidade total da API. Se você usar esse tipo de ACL, certifique-se de validar não apenas o valor `appid` do chamador, mas também de validar que o valor `iss` do token é confiável.

Esse tipo de autorização é comum para daemons e contas de serviço que precisam acessar dados pertencentes a usuários consumidores com contas pessoais da Microsoft. Para dados de propriedade de organizações, é recomendável que você obtenha a autorização necessária por meio de permissões de aplicativo.

### <a name="application-permissions"></a>Permissões de aplicativo

Em vez de usar ACLs, você pode usar APIs para expor um conjunto de **permissões**de aplicativos . Uma permissão de aplicativo é concedida a um aplicativo por um administrador de uma organização e só pode ser usada para acessar os dados pertencentes a essa organização e a seus funcionários. Por exemplo, o Microsoft Graph expõe várias permissões de aplicativo para fazer o seguinte:

* Ler emails em todas as caixas de correio
* Ler e gravar mensagens em todas as caixas de correio
* Enviar emails como qualquer usuário
* Ler dados do diretório

Para saber mais sobre permissões de aplicativo, vá para [Microsoft Graph](https://developer.microsoft.com/graph).

Para usar permissões de aplicativo no seu aplicativo, siga as etapas discutidas nas próximas seções.


> [!NOTE]
> Ao autenticar como um aplicativo, ao contrário de um usuário, você não pode usar "permissões delegadas" (escopos que são concedidos por um usuário).  Você deve usar "permissões de aplicativo", também conhecidas como "funções", que são concedidas por um admin para o aplicativo (ou via pré-autorização pela API web).


#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro do aplicativo

1. Registre-se e crie um aplicativo através da nova [experiência de registros de aplicativos (Preview).](quickstart-register-app.md)
2. Vá para o seu aplicativo na experiência de registros de aplicativos (Preview). Navegue até a seção **de segredos & Certificados** e adicione um **novo segredo de cliente,** pois você precisará de pelo menos um segredo de cliente para solicitar um token.
3. Localize a seção **Permissões de API** e adicione as **permissões de aplicativo** que seu aplicativo requer.
4. **Salve** o registro do aplicativo.

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: Inscreva o usuário em seu aplicativo

Normalmente, quando você cria um aplicativo que usa as permissões de aplicativo, o aplicativo exige uma página ou exibição na qual o administrador aprova as permissões do aplicativo. Esta página pode ser parte do fluxo de entrada do aplicativo, parte das configurações do aplicativo ou pode ser um fluxo dedicado de "conexão". Em muitos casos, faz sentido que o aplicativo somente mostre o modo de exibição "conectar" depois que o usuário entra com uma conta corporativa ou de estudante da Microsoft.

Se você inscrever o usuário em seu aplicativo, você pode identificar a organização à qual o usuário pertence antes de pedir ao usuário para aprovar as permissões do aplicativo. Embora não seja estritamente necessário, isso pode ajudá-lo a criar uma experiência mais intuitiva para os usuários. Para fazer login com o usuário, siga [nossos tutoriais de protocolo da plataforma de identidade da Microsoft.](active-directory-v2-protocols.md)

#### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar permissões de um administrador de diretório

Quando você estiver pronto para solicitar permissões do admin da organização, você pode redirecionar o usuário para o *ponto final de consentimento da*plataforma de identidade Microsoft .

> [!TIP]
> Tente executar a solicitação no Postman! (Use seu próprio ID de aplicativo para obter melhores resultados - o aplicativo tutorial não solicitará permissões úteis.) [Tente executar este pedido no Carteiro ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Dica pro: Tente colar a seguinte solicitação em um navegador.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O locatário do diretório para o qual você deseja solicitar permissão. Pode estar no formato de nome amigável ou de GUID. Se você não souber a qual locatário o usuário pertence e se quiser deixá-lo entrar com qualquer locatário, use `common`. |
| `client_id` | Obrigatório | O **ID do aplicativo (cliente)** que o [portal Azure – Registros de aplicativos](https://go.microsoft.com/fwlink/?linkid=2083908) experimenta atribuído ao seu aplicativo. |
| `redirect_uri` | Obrigatório | O URI de redirecionamento onde você deseja que a resposta seja enviada para ser tratada pelo aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, com exceção de que ele deve ser codificado por URL e podem ter mais segmentos de caminho. |
| `state` | Recomendadas | Um valor incluído na solicitação que também é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |

Neste ponto, o Azure AD reforça que apenas um administrador inquilino pode assinar a solicitação completa. O administrador deverá aprovar todas as permissões diretas do aplicativo que você solicitou para o aplicativo no portal de registro de aplicativos.

##### <a name="successful-response"></a>Resposta bem-sucedida

Se o administrador aprovar as permissões para o seu aplicativo, a resposta bem-sucedida terá esta aparência:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O locatário do diretório que concedeu as permissões solicitadas, no formato GUID. |
| `state` | Um valor incluído na solicitação que também é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `admin_consent` | Defina como **True**. |

##### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para o seu aplicativo, a resposta de falha terá esta aparência:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros, e que você pode usar para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro. |

Depois de receber uma resposta bem-sucedida do ponto de extremidade de provisionamento do aplicativo, o aplicativo terá as permissões diretas solicitadas. Agora você pode solicitar um token para o recurso desejado.

## <a name="get-a-token"></a>Obter um token

Após adquirir a autorização necessária para seu aplicativo, continue adquirindo os tokens de acesso para as APIs. Para obter um token usando a concessão de `/token` credenciais do cliente, envie uma solicitação POST para o ponto final da plataforma de identidade da Microsoft:

> [!TIP]
> Tente executar a solicitação no Postman! (Use seu próprio ID de aplicativo para obter melhores resultados - o aplicativo tutorial não solicitará permissões úteis.) [Tente executar este pedido no Carteiro ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Na primeira ocorrência: solicitação de token de acesso com um segredo compartilhado

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O aplicativo de locatário do diretório planos operar, no formato de nome de domínio ou GUID. |
| `client_id` | Obrigatório | A ID do aplicativo atribuído ao aplicativo. É possível localizar essas informações no portal onde você registrou o aplicativo. |
| `scope` | Obrigatório | O valor transmitido ao parâmetro `scope` na solicitação deve ser o identificador de recurso (URI de ID do aplicativo) do recurso que você deseja, com o sufixo `.default`. Para o exemplo do Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. <br/>Esse valor informa ao ponto final da plataforma de identidade da Microsoft que de todas as permissões diretas de aplicativos configuradas para o seu aplicativo, o ponto final deve emitir um token para aqueles associados ao recurso que você deseja usar. Para saber mais sobre o escopo `/.default`, confira [Documentação de consentimento](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Obrigatório | O segredo do cliente que você gerou para o seu aplicativo no portal de registro do aplicativo. O segredo do cliente deve ser codificada como URL antes de serem enviados. |
| `grant_type` | Obrigatório | Deve ser definido como `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitação de token de acesso com um certificado

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O aplicativo de locatário do diretório planos operar, no formato de nome de domínio ou GUID. |
| `client_id` | Obrigatório |A ID do aplicativo (cliente) atribuída ao seu aplicativo. |
| `scope` | Obrigatório | O valor transmitido ao parâmetro `scope` na solicitação deve ser o identificador de recurso (URI de ID do aplicativo) do recurso que você deseja, com o sufixo `.default`. Para o exemplo do Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. <br/>Esse valor informa o ponto final da plataforma de identidade da Microsoft que de todas as permissões diretas de aplicativos configuradas para o seu aplicativo, ele deve emitir um token para aqueles associados ao recurso que você deseja usar. Para saber mais sobre o escopo `/.default`, confira [Documentação de consentimento](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Obrigatório | O valor precisa ser definido como `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Obrigatório | Uma asserção (um token Web JSON) que você precisa para criar e assinar com o certificado registrado como credenciais do seu aplicativo. Leia mais sobre [credenciais de certificado](active-directory-certificate-credentials.md) para saber como registrar seu certificado e saber sobre o formato da asserção.|
| `grant_type` | Obrigatório | Deve ser definido como `client_credentials`. |

Observe que os parâmetros são praticamente os mesmos como no caso da solicitação pelo segredo compartilhado, exceto pelo fato de o parâmetro client_secret ser substituído por dois parâmetros: um client_assertion_type e uma client_assertion.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida tem a seguinte aparência:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` | O token de acesso solicitado. O aplicativo pode usar esse token para autenticar o recurso protegido, como uma API web. |
| `token_type` | Indica o valor do tipo de token. O único tipo que a `bearer`plataforma de identidade da Microsoft suporta é . |
| `expires_in` | A quantidade de tempo que um token de acesso é válido (em segundos). |

### <a name="error-response"></a>Resposta de erro

Uma resposta de erro tem esta aparência:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de códigos de erro que você pode usar para classificar tipos de erro que ocorrem e para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico. |
| `timestamp` | A hora em que o erro ocorreu. |
| `trace_id` | Um identificador exclusivo da solicitação para ajudar com diagnósticos. |
| `correlation_id` | Um identificador exclusivo da solicitação para ajudar com diagnósticos entre componentes. |

## <a name="use-a-token"></a>Usar um token

Agora que você já adquiriu um token, use-o para fazer solicitações ao recurso. Quando o token expirar, repita a solicitação para o ponto de extremidade `/token` a fim de adquirir um novo token de acesso.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Exemplos de código e outras documentações

Leia a [documentação de visão geral de credenciais de cliente](https://aka.ms/msal-net-client-credentials) da biblioteca de autenticação da Microsoft

| Amostra | Plataforma |Descrição |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Console do .NET Core 2.1 | Um aplicativo .NET Core simples que exibe os usuários de um locatário consultando o Microsoft Graph usando a identidade do aplicativo, em vez de uma consulta em nome do usuário. O exemplo também ilustra a variação usando certificados para autenticação. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Um aplicativo Web que sincroniza dados do Microsoft Graph usando a identidade do aplicativo, em vez de fazer em nome do usuário. |
