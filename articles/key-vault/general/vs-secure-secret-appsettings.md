---
title: Como salvar com segurança as configurações de aplicativo secretas para um aplicativo Web - Azure Key Vault | Microsoft Docs
description: Como salvar com segurança salvar as configurações de aplicativo secretas, como credenciais do Azure ou chaves de API de terceiros usando o Provedor do Key Vault do ASP.NET Core, o segredo do usuário ou os construtores de configuração do .NET 4.7.1
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: bcacd5d2ed9e325383ec7ae75002ae0a6213111c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429753"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Salvar com segurança as configurações de aplicativo secretas para um aplicativo Web

## <a name="overview"></a>Visão geral
Este artigo descreve como salvar com segurança os parâmetros secretos de configuração de aplicativos para aplicativos do Azure.

Tradicionalmente, todas as configurações de configuração do aplicativo web são salvas em arquivos de configuração, como Web.config. Essa prática leva à verificação de configurações secretas, como credenciais da Nuvem para sistemas públicos de controle de fontes como o GitHub. Enquanto isso, pode ser difícil seguir a melhor prática de segurança devido à sobrecarga necessária para mudar o código-fonte e redefinir as configurações de desenvolvimento.

Para garantir que o processo de desenvolvimento seja seguro, as bibliotecas de ferramentas e estruturas são criadas para salvar as configurações secretas do aplicativo com segurança com alteração mínima ou nenhuma de código-fonte.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET e .NET Core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Salvar configurações secretas no repositório de segredo do usuário que está fora da pasta de controle do código-fonte
Se você estiver fazendo um protótipo rápido ou não tiver acesso à Internet, comece a mover suas configurações secretas da pasta de controle do código-fonte para o repositório de segredo do usuário. O repositório de segredo do usuário é um arquivo salvo na pasta do criador de perfil de usuário, para que segredos não sejam verificados no controle do código-fonte. O diagrama a seguir demonstra como funciona o [segredo do usuário](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio).

![O segredo do usuário mantém as configurações secretas fora do controle do código-fonte](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Se você estiver executando um aplicativo de console .NET Core, use o Key Vault para salvar seu segredo com segurança.

### <a name="save-secret-settings-in-azure-key-vault"></a>Salvar configurações secretas no Azure Key Vault
Se você estiver desenvolvendo um projeto e precisar compartilhar com segurança o código-fonte, use o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Crie um Key Vault na assinatura do Azure. Preencha todos os campos obrigatórios na interface do usuário e clique em *Criar* na parte inferior da folha

    ![Criar Azure Key Vault](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Conceder acesso a você e aos membros da equipe para o Key Vault. Se você tiver uma equipe grande, crie um [grupo do Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) e adicione a esse grupo de segurança acesso ao Key Vault. No menu suspenso *Permissões do Segredo*, marque *Obter* e *Listar* em *Operações de Gerenciamento de Segredos*.
Se você já tiver seu aplicativo web criado, conceda ao aplicativo web acesso ao Key Vault para que ele possa acessar o cofre de chaves sem armazenar configuração secreta em Configurações de aplicativos ou arquivos. Pesquise seu aplicativo web pelo seu nome e adicione-o da mesma maneira que você concede aos usuários acesso.

    ![Adicionar Política de acesso ao Key Vault](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Adicione seu segredo ao Key Vault no portal Azure. Para definições de configuração aninhadas, substitua ':' por '-' para que o nome de segredo do Key Vault seja válido. ':' não pode ser usado no nome de um segredo do Key Vault.

    ![Adicionar segredo do Key Vault](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Antes do Visual Studio 2017 V15.6, costumávamos recomendar a instalação da extensão de Autenticação de Serviços Azure para o Visual Studio. Mas agora é preterido, pois a funcionalidade está integrada dentro do Visual Studio. Portanto, se você estiver em uma versão mais antiga do visual Studio 2017, sugerimos que você atualize para pelo menos VS 2017 15.6 ou mais para que você possa usar essa funcionalidade nativamente e acessar o Key-vault usando o próprio Visual Studio sign-in Identity.
    >

4. Adicione os seguintes pacotes NuGet ao seu projeto:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Adicione o seguinte código ao arquivo Program.cs:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                        keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Adicione a URL do Key Vault ao arquivo launchsettings.json. O nome da variável de ambiente *KEYVAULT_ENDPOINT* é definido no código que você adicionou na etapa 6.

    ![Adicionar URL do Key Vault como uma variável de ambiente do projeto](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Comece a depuração do projeto. Ele deve ser executado com êxito.

## <a name="aspnet-and-net-applications"></a>Aplicativos do ASP.NET e .NET

O .NET 4.7.1 é compatível com construtores de configuração de segredo e do Key Vault, que garante que os segredos podem ser movidos fora da pasta de controle do código-fonte sem alterações de código.
Para continuar, [baixe o .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) e migre o seu aplicativo se ele estiver usando uma versão mais antiga do .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Salvar configurações secretas no arquivo de segredo que está fora da pasta de controle do código-fonte
Se você estiver escrevendo um protótipo rápido e não quiser provisionar recursos do Azure, use esta opção.

1. Instale o pacote NuGet a seguir no seu projeto.
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Crie um arquivo semelhante ao seguinte. Salve-o em um local fora da sua pasta de projeto.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Defina o arquivo secreto para ser um construtor de configuração no arquivo Web.config. Coloque esta seção antes da seção *appSettings*.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Especifique se a seção appSettings está usando o construtor de configuração secreta. Certifique-se de que há uma entrada para a configuração secreta com um valor de boneco.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Depure o aplicativo. Ele deve ser executado com êxito.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Salvar configurações secretas em um Azure Key Vault
Siga as instruções da seção do ASP.NET Core para configurar um Key Vault para o seu projeto.

1. Instale o pacote NuGet a seguir no seu projeto.
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Defina o construtor de configuração key vault no Web.config. Coloque esta seção antes da seção *'Configurações de aplicativos'.* Substitua *vaultName* para ser o nome do Key Vault se o Key Vault no Azure público ou URI completo se você estiver usando a nuvem soberana.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. Especifique se a seção appSettings está usando o construtor de configuração do Key Vault. Verifique se que há alguma entrada para a configuração secreta com valor fictício.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Comece a depuração do projeto. Ele deve ser executado com êxito.
