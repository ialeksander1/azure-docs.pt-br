---
title: Use o Key Vault para armazenar e acessar chaves do Azure Cosmos DB
description: Use o Azure Key Vault para armazenar e acessar a cadeia de conexão, chaves e pontos de extremidade do Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 225221635f978e3d70cec4ce7e9d78d6b100b4fd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618764"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Proteger chaves do Azure Cosmos usando o Azure Key Vault 

>[!IMPORTANT]
> A solução recomendada para acessar as chaves db do Azure Cosmos é usar uma [identidade gerenciada atribuída pelo sistema](managed-identity-based-authentication.md). Se o seu serviço não puder tirar proveito das identidades gerenciadas, use a [solução baseada em cert](certificate-based-authentication.md). Se a solução de identidade gerenciada e a solução baseada em cert não atenderem às suas necessidades, use a solução do cofre chave abaixo.

Ao usar o Azure Cosmos DB para seus aplicativos, você pode acessar o banco de dados, coleções, documentos usando o ponto final e a chave dentro do arquivo de configuração do aplicativo.  No entanto, não é seguro colocar chaves e URL diretamente no código do aplicativo porque eles estão disponíveis em formato de texto claro para todos os usuários. Você deseja verificar se o ponto de extremidade e as chaves estão disponíveis, mas por meio de um mecanismo seguro. É aí que o Azure Key Vault pode ajudá-lo a armazenar e gerenciar os segredos do aplicativo com segurança.

As etapas a seguir são necessárias para armazenar e ler as chaves de acesso do Azure Cosmos DB usando o Key Vault:

* Criar um cofre de chaves  
* Adicionar chaves de acesso do Azure Cosmos DB no Key Vault  
* Criar um aplicativo Web do Azure  
* Registrar o aplicativo e conceder permissões para ler o Key Vault  


## <a name="create-a-key-vault"></a>Criar um cofre de chaves

1. Faça login no [portal Azure](https://portal.azure.com/).  
2. Selecione **Criar um recurso > Segurança > Key Vault**.  
3. A seção **Criar cofre de chaves** fornece as seguintes informações:  
   * **Nome:** forneça um nome exclusivo para seu Key Vault.  
   * **Assinatura:** escolha a assinatura que você usará.  
   * Em **Grupo de Recursos**, escolha **Criar novo** e digite um nome para o grupo de recursos.  
   * No menu suspenso Local, escolha um local.  
   * Deixe as outras opções com os padrões.  
4. Depois de fornecer as informações acima, selecione **Criar**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Adicione as chaves de acesso do Azure Cosmos DB ao Key Vault.
1. Navegue até o Key Vault que você criou na etapa anterior e abra a guia **Segredos**.  
2. Selecione **+Gerar/Importar**, 

   * Selecione **Manual** para **opções de upload**.
   * Forneça um **Nome** para seu segredo
   * Insira a cadeia de conexão da sua conta do Cosmos DB no campo **Valor**. E, em seguida, selecione **Criar**.

   ![Criar um segredo](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Depois que o segredo for criado, abra-o e copie o **Identificador do Segredo que está no formato a seguir. Você usará esse identificador na próxima seção. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Criar um aplicativo Web do Azure

1. Crie um aplicativo Web do Azure ou baixe o aplicativo do [Repositório do GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Trata-se de um aplicativo MVC simples.  

2. Descompacte o aplicativo baixado e abra o arquivo **HomeController.cs**. Atualize a ID do segredo na linha a seguir:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Salve** o arquivo **Crie** a solução.  
4. Em seguida, implante o aplicativo no Azure. Clique com o botão direito do mouse no projeto e escolha **Publicar**. Crie um perfil do Serviço de Aplicativo (você pode nomear o aplicativo como WebAppKeyVault1) e selecione **Publicar**.   

5. Depois que o aplicativo for implantado. No portal do Azure, navegue até o aplicativo Web que você implantou e habilite a **Identidade de Serviço Gerenciada** desse aplicativo.  

   ![Identidade de Serviço Gerenciada](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Se você for executar o aplicativo agora, ocorrerá o seguinte erro, pois você ainda não concedeu nenhuma permissão para esse aplicativo no Key Vault.

![Aplicativo implantado sem acesso](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registrar o aplicativo e conceder permissões para ler o Key Vault

Nesta seção, você registrará o aplicativo no Azure Active Directory e concederá permissões para o aplicativo ler o Key Vault. 

1. Navegue até o portal do Azure e abra o **Key Vault** criado na seção anterior.  

2. Abra **Políticas de acesso**, selecione **+Adicionar Novo**, localize o aplicativo Web implantado, escolha as permissões e selecione **OK**.  

   ![Adicionar política de acesso](./media/access-secrets-from-keyvault/add-access-policy.png)

Agora, ao executar o aplicativo, você poderá ler o segredo do Key Vault.

![Aplicativo implantado com o segredo](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Da mesma forma, você pode adicionar um usuário para acessar o Key Vault. Você precisa adicionar você mesmo ao Key Vault selecionando **Políticas de Acesso**. Em seguida, conceda todas as permissões que você precisa para executar o aplicativo usando o Visual Studio. Quando esse aplicativo estiver em execução na área de trabalho, ele usará sua identidade.

## <a name="next-steps"></a>Próximas etapas

* Para configurar um firewall para o Azure Cosmos DB, consulte o artigo [de suporte ao firewall.](firewall-support.md)
* Para configurar o ponto de extremidade de serviço de rede virtual, confira o artigo [Proteger o acesso usando o ponto de extremidade de serviço da VNET](vnet-service-endpoint.md).
