---
title: Use os modelos do Azure Resource Manager para gerenciamento de atualizações a bordo | Microsoft Docs
description: Você pode usar um modelo do Azure Resource Manager para embarcar na solução azure Automation Update Management.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/30/2020
ms.openlocfilehash: 81f9d242d93ffe513c0c3733ceb9d38ca9cadc1c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617451"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Solução de gerenciamento de atualização a bordo usando o modelo do Azure Resource Manager

Você pode usar [os modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) para ativar a solução azure Automation Update Management em seu grupo de recursos. Este artigo fornece um modelo de exemplo que automatiza o seguinte:

* Criação de um espaço de trabalho do Azure Monitor Log Analytics.
* Criação de uma conta do Azure Automation.
* Vinculando a conta de Automação ao espaço de trabalho do Log Analytics, se ainda não estiver vinculado.
* Onboarding da solução Azure Automation Update Management.

O modelo não automatiza o onboarding de uma ou mais VMs Azure ou não-Azure.

Se você já tem uma conta de espaço de trabalho e Automação do Log Analytics implantada em uma região suportada em sua assinatura, elas não estão vinculadas. O espaço de trabalho ainda não tem a solução Update Management implantada. O uso deste modelo cria com sucesso o link e implanta a solução Update Management. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="api-versions"></a>Versões de API

A tabela a seguir lista as versões da API para os recursos utilizados neste modelo.

| Recurso | Tipo de recurso | Versão da API |
|:---|:---|:---|
| Workspace | workspaces | 2017-03-15-preview |
| Conta de automação | automação | 31-10-2015 | 
| Solução | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Antes de usar o modelo

Se você optar por instalar e usar o PowerShell localmente, este artigo requer o módulo Azure PowerShell Az. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa fazer a atualização, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, você também precisa executar [o Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) para criar uma conexão com o Azure. Com o Azure PowerShell, a implantação usa [o New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se você optar por instalar e usar o CLI localmente, este artigo requer que você esteja executando a versão 2.1.0 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Com o Azure CLI, essa implantação usa [a criação de implantação de grupo AZ](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

O modelo JSON está configurado para solicitar:

* O nome do workspace
* A região em que criar o espaço de trabalho
* O nome da conta automação
* A região em que criar a conta

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente serão usados para uma configuração padrão em seu ambiente. Você pode armazenar o modelo em uma conta de armazenamento do Azure para acesso compartilhado em sua organização. Para obter mais informações sobre como trabalhar com modelos, consulte [Implantar recursos com modelos do Gerenciador de Recursos e CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho do Log Analytics:

* SKU – assume por padrão o novo tipo de preço por GB lançado no modelo de preço de abril de 2018
* retenção de dados - padrão para trinta dias
* reserva de capacidade - padrão para 100 GB

>[!WARNING]
>Se criar ou configurar um espaço de trabalho do Log Analytics em uma assinatura que optou pelo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é **o PerGB2018**.
>

>[!NOTE]
>Antes de usar este modelo, revise [detalhes adicionais](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) para entender completamente as opções de configuração do espaço de trabalho, como modo de controle de acesso, nível de precificação, retenção e nível de reserva de capacidade. Se você é novo nos registros do Azure Monitor e ainda não implantou um espaço de trabalho, você deve rever a orientação de design do [espaço de trabalho](../azure-monitor/platform/design-logs-deployment.md) para aprender sobre controle de acesso e entender as estratégias de implementação de design que recomendamos para sua organização.

## <a name="deploy-template"></a>Implantar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. Edite o modelo para atender às suas necessidades. Considere criar um [arquivo de parâmetros do Gerenciador](../azure-resource-manager/templates/parameter-files.md) de recursos em vez de passar parâmetros como valores inline.

3. Salve este arquivo em uma pasta local como **deployUMSolutiontemplate.json**.

4. Você está pronto para implantar o modelo. Você pode usar o PowerShell ou o Azure CLI. Quando você for solicitado para um nome de conta de espaço de trabalho e automação, forneça um nome que seja globalmente único em todas as assinaturas do Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **CLI do Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:

    ![Resultados de exemplo, quando a implantação for concluída](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você tem a solução update management implantada, você pode habilitar VMs para gerenciamento, revisar avaliações de atualização e implantar atualizações para trazê-las em conformidade.

- A partir de sua [conta Azure Automation](automation-onboard-solutions-from-automation-account.md) para uma ou mais máquinas Azure e manualmente para máquinas não-Azure.

- Para uma única VM Azure da página da máquina virtual no portal Azure. Este cenário está disponível para [VMs Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- Para [várias VMs do Azure,](manage-update-multi.md) selecionando-as na página **de máquinas virtuais** no portal Azure. 