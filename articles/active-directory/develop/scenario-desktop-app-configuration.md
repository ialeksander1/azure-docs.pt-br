---
title: Configure aplicativos de desktop que chamam APIs web - plataforma de identidade da Microsoft | Azure
description: Saiba como configurar o código de um aplicativo de desktop que chama APIs da Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e0b43f7563c9dfac6374590f6b081197536fe31e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869004"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Aplicativo de desktop que chama APIs da Web: Configuração de código

Agora que você criou seu aplicativo, você aprenderá como configurar o código com as coordenadas do aplicativo.

## <a name="microsoft-authentication-libraries"></a>Bibliotecas de Autenticação Microsoft

As MSALs (Microsoft Authentication Libraryes, bibliotecas de autenticação da Microsoft) a seguir suportam aplicativos de desktop.

  Biblioteca de Autenticação da Microsoft | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Suporta a construção de um aplicativo de desktop em várias plataformas, como Linux, Windows e macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Suporta a construção de um aplicativo de desktop em várias plataformas.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Suporta a construção de um aplicativo de desktop em várias plataformas.
  ![IOS MSAL](media/sample-v2-code/logo_iOS.png) <br/> IOS MSAL | Suporta aplicativos de desktop que são executados apenas no macOS.

## <a name="public-client-application"></a>Aplicação pública de clientes

Do ponto de vista do código, os aplicativos de desktop são aplicativos clientes públicos. A configuração será um pouco diferente com base em se você usa autenticação interativa ou não.

# <a name="net"></a>[.NET](#tab/dotnet)

Você precisará construir e manipular `IPublicClientApplication`MSAL.NET.

![Aplicativo iPublicClient](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivamente por código

O código a seguir instancia um aplicativo cliente público e sinaliza em usuários na nuvem pública do Microsoft Azure com uma conta de trabalho ou escola ou uma conta microsoft pessoal.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se você pretende usar autenticação interativa ou fluxo de código `.WithRedirectUri` do dispositivo, como visto anteriormente, use o modificador.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Usar arquivos de configuração

O código a seguir instancia um aplicativo cliente público a partir de um objeto de configuração, que pode ser preenchido programáticamente ou lido a partir de um arquivo de configuração.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Você pode elaborar o edifício de aplicativos adicionando um número de modificadores. Por exemplo, se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional, como o governo dos EUA mostrado aqui, você pode escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET também contém um modificador para os Serviços da Federação de Diretórios Ativos 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Finalmente, se você quiser adquirir tokens para um inquilino B2C do Azure Active Directory (Azure AD), especifique seu inquilino conforme mostrado no seguinte trecho de código:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Saiba mais

Para saber mais sobre como configurar um aplicativo de desktop MSAL.NET:

- Para obter uma lista de `PublicClientApplicationBuilder`todos os modificadores disponíveis, consulte a documentação de referência [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Para obter uma descrição de `PublicClientApplicationOptions`todas as opções expostas, consulte [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) na documentação de referência.

### <a name="complete-example-with-configuration-options"></a>Exemplo completo com opções de configuração

Imagine um aplicativo de console .NET Core que tenha o seguinte `appsettings.json` arquivo de configuração:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Você tem pouco código para ler neste arquivo usando o . Estrutura de configuração fornecida pela NET:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Agora, para criar seu aplicativo, escreva o seguinte código:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Antes da chamada `.Build()` para o método, você pode `.WithXXX` substituir sua configuração com chamadas para métodos, como visto anteriormente.

# <a name="java"></a>[Java](#tab/java)

Aqui está a classe usada em amostras de desenvolvimento MSAL Java para configurar as amostras: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

O código a seguir instancia um aplicativo cliente público e sinaliza em usuários na nuvem pública do Microsoft Azure com uma conta de trabalho ou escola ou uma conta microsoft pessoal.

### <a name="quick-configuration"></a>Configuração rápida

Objective-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Você pode elaborar o edifício de aplicativos adicionando um número de modificadores. Por exemplo, se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional, como o governo dos EUA mostrado aqui, você pode escrever:

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adquira um token para um aplicativo de desktop](scenario-desktop-acquire-token.md)
