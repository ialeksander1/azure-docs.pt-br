---
title: Criar e gerenciar grupos de ações no portal do Azure
description: Este artigo mostra como criar e gerenciar grupos de ações no portal do Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 4/17/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 339b11664308962962c59b2e9386ff122681293a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116206"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerenciar grupos de ações no portal do Azure
Um grupo de ações é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure. Alertas do Azure Monitor e da Integridade do Serviço usam grupos de ações para notificar usuários de que um alerta foi disparado. Vários alertas podem usar o mesmo grupo de ação ou grupos de ações diferentes dependendo dos requisitos do usuário. Você pode configurar até 2 mil grupos de ação em uma assinatura.

Você configura uma ação para notificar uma pessoa por email ou SMS, elas recebem uma confirmação indicando que foram adicionadas ao grupo de ações.

Este artigo mostra como criar e gerenciar grupos de ação no Portal do Azure.

Cada ação é composta das seguintes propriedades:

* **Nome**: um identificador exclusivo dentro do grupo de ações.  
* **Tipo de ação**: a ação executada. Exemplos incluem o envio de um email de chamada, SMS, voz; ou disparando vários tipos de ações automatizadas. Veja os tipos mais adiante neste artigo.
* **Detalhes**: os detalhes correspondentes que variam de acordo com o *tipo de ação*.

Para saber mais sobre como usar modelos do Azure Resource Manager para configurar grupos de ação, veja [Modelos do Resource Manager de grupos de ações](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Como criar um grupo de ações usando o portal do Azure

1. No [portal do Azure](https://portal.azure.com), procure e selecione **Monitor**. O painel **Monitor** consolida todas as suas configurações e dados de monitoramento em uma exibição.

1. Selecione **Alertas**, em seguida, selecione **Gerenciar ações**.

    ![Botão Gerenciar ações](./media/action-groups/manage-action-groups.png)
    
1. Selecione **Adicionar grupo de ações** e preencha os campos.

    ![O comando "Adicionar grupo de ações"](./media/action-groups/add-action-group.png)
    
1. Insira um nome na caixa **nome do grupo de ações** e insira um nome na caixa **nome curto** . O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

      ![Caixa de diálogo Adicionar grupo de ações](./media/action-groups/action-group-define.png)

1. A caixa de **assinatura** é preenchida com sua assinatura atual. Esta assinatura é aquela na qual o grupo de ação é salvo.

1. Selecione o **grupo de recursos** no qual o grupo de ações é salvo.

1. Defina uma lista de ações. Forneça o seguinte para cada ação:

    1. **Nome**: insira um identificador exclusivo para esta ação.

    1. **Tipo de ação**: selecione runbook de automação, Azure Function, email Azure Resource Manager função, email/SMS/Push/voz, ITSM, aplicativo lógico, webhook seguro, webhook.

    1. **Detalhes**: de acordo com o tipo de ação, insira um número de telefone, endereço de email, URI de webhook, aplicativo do Azure, conexão de ITSM ou runbook de Automação. Para Ação do ITSM, além disso, especifique **Item de Trabalho** e outros campos necessários para a ferramenta de ITSM.
    
    1. **Esquema de alerta comum**: você pode optar por habilitar o [esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs), que fornece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Azure monitor.

1. Selecione **OK** para criar o grupo de ações.

## <a name="manage-your-action-groups"></a>Gerenciar seus grupos de ação

Depois de criar um grupo de ações, você pode exibir **grupos de ações** selecionando **Gerenciar ações** na página de aterrissagem **alertas** no painel **Monitor** . Selecione o grupo de ações que você deseja gerenciar:

* Adicionar, editar ou remover ações.
* Excluir o grupo de ação.

## <a name="action-specific-information"></a>Informações específicas da ação

> [!NOTE]
> Consulte [limites de serviço de assinatura para o monitoramento](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) de limites numéricos em cada um dos itens abaixo.  

### <a name="automation-runbook"></a>Runbook de automação
Consulte os [limites do serviço de assinatura do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) para limites em cargas de runbook.

Você pode ter um número limitado de ações de runbook em um grupo de ações. 

### <a name="azure-app-push-notifications"></a>Notificações por push do aplicativo do Azure
Você pode ter um número limitado de ações de aplicativo do Azure em um grupo de ação.

### <a name="email"></a>Email
Os emails serão enviados dos endereços de email a seguir. Certifique-se de que a filtragem de email esteja configurada adequadamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Você pode ter um número limitado de ações de email em um grupo de ações. Consulte o artigo [informações de limitação de taxa](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>Função de Azure Resource Manager de email
Envie um email para os membros da função da assinatura. O email será enviado somente aos membros do **usuário do Azure ad** da função. O email não será enviado aos grupos ou entidades de serviço do Azure AD.

Você pode ter um número limitado de ações de email em um grupo de ações. Consulte o artigo [informações de limitação de taxa](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="function"></a>Função
Chama um ponto de extremidade de gatilho HTTP existente no [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Você pode ter um número limitado de ações de função em um grupo de ações.

### <a name="itsm"></a>ITSM
Ação de ITSM exige uma Conexão de ITSM. Saiba como criar uma [Conexão de ITSM](../../azure-monitor/platform/itsmc-overview.md).

Você pode ter um número limitado de ações de ITSM em um grupo de ações. 

### <a name="logic-app"></a>Aplicativo Lógico
Você pode ter um número limitado de ações de aplicativo lógico em um grupo de ação.

### <a name="secure-webhook"></a>Proteger webhook
A ação de webhook de grupos de ações permite que você aproveite Azure Active Directory para proteger a conexão entre o grupo de ação e a API Web protegida (ponto de extremidade do webhook). O fluxo de trabalho geral para aproveitar essa funcionalidade é descrito abaixo. Para obter uma visão geral dos aplicativos do Azure AD e das entidades de serviço, consulte [visão geral da plataforma Microsoft Identity (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Crie um aplicativo do Azure AD para sua API Web protegida. Consulte https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure sua API protegida para ser chamada por um aplicativo daemon.
    
1. Habilite grupos de ação para usar seu aplicativo do Azure AD.

    > [!NOTE]
    > Você deve ser membro da função de [administrador de aplicativos do Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) para executar esse script.
    
    - Modifique a chamada Connect-AzureAD do script do PowerShell para usar sua ID de locatário do Azure AD.
    - Modifique a variável do script do PowerShell $myAzureADApplicationObjectId para usar a ID de objeto do seu aplicativo do Azure AD
    - Execute o script modificado.
    
1. Configure a ação de webhook segura do grupo de ações.
    - Copie o valor $myApp. ObjectId do script e insira-o no campo ID de objeto do aplicativo na definição de ação do webhook.
    
    ![Ação proteger webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script do PowerShell de webhook seguro

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
Consulte as [informações de limitação de taxa](./../../azure-monitor/platform/alerts-rate-limiting.md) e o [comportamento de alerta do SMS](../../azure-monitor/platform/alerts-sms-behavior.md) para obter informações importantes adicionais. 

Você pode ter um número limitado de ações de SMS em um grupo de ações.

Se a interface do usuário do grupo de ações portal do Azure não permitir que você selecione o código do país, o SMS não terá suporte para o seu país. Os preços dos países com suporte são listados na [página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Se o código do seu país não estiver disponível, você poderá votar em ter seu país adicionado no [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  

  

### <a name="voice"></a>Voz
Consulte o artigo [informações de limitação de taxa](./../../azure-monitor/platform/alerts-rate-limiting.md) .

Você pode ter um número limitado de ações de voz em um grupo de ações.

Se a interface do usuário do grupo de ações portal do Azure não permitir que você selecione o código do país, as chamadas de voz não serão suportadas para seu país. Os preços dos países com suporte são listados na [página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Se o código do seu país não estiver disponível, você poderá votar em ter seu país adicionado no [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  

### <a name="webhook"></a>webhook
Os WebHooks são repetidos usando as regras a seguir. A chamada de webhook é repetida no máximo duas vezes quando os seguintes códigos de status HTTP são retornados: 408, 429, 503, 504 ou o ponto de extremidade HTTP não responde. A primeira nova tentativa ocorre após 10 segundos. A segunda tentativa ocorre após 100 segundos. Após duas falhas, nenhum grupo de ação chamará o ponto de extremidade por 30 minutos. 

Intervalos de endereços IP de fonte
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Para receber atualizações sobre alterações nesses endereços IP, recomendamos que você configure um alerta de integridade do serviço, que monitora as notificações informativas sobre o serviço de grupos de ação.

Você pode ter um número limitado de ações de webhook em um grupo de ações.



## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [comportamento de alertas por SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Tenha uma [compreensão do esquema de webhook de alerta do log de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Saiba mais sobre o [Conector de ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Saiba mais sobre a [limitação de taxa](../../azure-monitor/platform/alerts-rate-limiting.md) em alertas.
* Obtenha uma [visão geral dos alertas do log de atividades](../../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
