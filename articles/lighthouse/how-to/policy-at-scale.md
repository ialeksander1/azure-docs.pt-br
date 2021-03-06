---
title: Implantar o Azure Policy em assinaturas delegadas em escala
description: Saiba como o gerenciamento de recursos delegado do Azure permite implantar uma definição de política e uma atribuição de política em vários locatários.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 3fe7e48c56e9a5af93e9642ee16c50cfbce34f9e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481818"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Implantar o Azure Policy em assinaturas delegadas em escala

Como provedor de serviços, é provável que você tenha integrado vários locatários de clientes no gerenciamento de recursos delegados do Azure. O [Azure Lighthouse](../overview.md) permite que os provedores de serviços realizem operações em escala em vários locatários ao mesmo tempo, tornando as tarefas de gerenciamento mais eficientes.

Esse tópico mostra como usar o [Azure Policy](../../governance/policy/index.yml) para implantar uma definição de política e uma atribuição de política em vários locatários por meio de comandos do PowerShell. Neste exemplo, a definição de política garante que as contas de armazenamento estejam protegidas permitindo apenas o tráfego HTTPS.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Usar o Azure Resource Graph para fazer consultas em locatários do cliente

Você pode usar o [Azure Resource Graph](../../governance/resource-graph/index.yml) para realizar consultas em todas as assinaturas nos locatários do cliente que você gerencia. Neste exemplo, identificaremos quaisquer contas de armazenamento nessas assinaturas que não requerem tráfego HTTPS no momento.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Implantar uma política em vários locatários do cliente

O exemplo a seguir mostra como usar um [modelo do Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) para implantar uma definição de política e uma atribuição de política entre assinaturas delegadas em vários locatários do cliente. Essa definição de diretiva exige que todas as contas de armazenamento usem tráfego HTTPS, impedindo a criação de novas contas de armazenamento que não cumpram e marcam contas de armazenamento existentes sem a configuração como não compatível.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Validar a implantação da política

Depois de ter implantado o modelo do Azure Resource Manager, você pode confirmar que a definição de diretiva foi aplicada com sucesso ao tentar criar uma conta de armazenamento com **EnableHttpsTrafficOnly** definida como **falsa** em uma de suas assinaturas delegadas. Devido à atribuição de política, não será possível criar essa conta de armazenamento.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar, remova a definição de política e a atribuição criada pela implantação.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Policy](../../governance/policy/index.yml).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
