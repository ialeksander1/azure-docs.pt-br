---
title: Crie ou atualize funções personalizadas para recursos do Azure com a API REST
description: Saiba como listar, criar, atualizar ou excluir funções personalizadas com rbac (role-based access control, controle de acesso baseado em função) para recursos do Azure usando a API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062184"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Crie ou atualize funções personalizadas para recursos do Azure usando a API REST

> [!IMPORTANT]
> A adição `AssignableScopes` de um grupo de gerenciamento está atualmente em pré-visualização.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se as [funções incorporadas para os recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, você poderá criar suas próprias funções personalizadas. Este artigo descreve como listar, criar, atualizar ou excluir funções personalizadas usando a API REST.

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar todas as funções personalizadas em um diretório, use a API [Role Definitions - List](/rest/api/authorization/roledefinitions/list) REST.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Substitua *{filtro}* pelo tipo de função.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtro baseado no tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Listar funções personalizadas em um escopo

Para listar funções personalizadas em um escopo, use a API ['Definições de função ' Lista](/rest/api/authorization/roledefinitions/list) REST.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Recurso |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |

1. Substitua *{filtro}* pelo tipo de função.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtro baseado no tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Liste uma definição de função personalizada por nome

Para obter informações sobre uma função personalizada pelo nome de exibição, use a API [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) REST.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Recurso |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |

1. Substitua *{filter}* com o nome de exibição para a função.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Use a forma codificada da URL do nome de exibição exato da função. Por exemplo, `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Liste uma definição de função personalizada por ID

Para obter informações sobre uma função personalizada por seu identificador exclusivo, use a API [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) REST.

1. Use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) API REST para obter o identificador GUID para a função.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Recurso |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da definição de função.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, use [Definições de Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) API REST. Para chamar esta API, você deve estar conectado com um usuário `Microsoft.Authorization/roleDefinitions/write` que tenha `assignableScopes`uma função que tenha a permissão em todos os . Das funções incorporadas, apenas [o Proprietário](built-in-roles.md#owner) e [o Administrador de Acesso ao Usuário](built-in-roles.md#user-access-administrator) incluem essa permissão.

1. Examine a lista de [operações do provedor de recursos](resource-provider-operations.md) que estão disponíveis para criar as permissões para a função personalizada.

1. Use uma ferramenta GUID para gerar um identificador exclusivo que será usado para o identificador de função personalizada. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Comece com a solicitação e corpo a seguir:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Dentro da URI, substita *{scope}* pelo primeiro `assignableScopes` da função personalizada.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

1. Dentro do órgão de solicitação, substitua *{roleDefinitionId}* pelo identificador GUID.

1. Se `assignableScopes` for um grupo de assinatura ou recurso, substitua as instâncias *{subscriptionId}* ou *{resourceGroup}* por seus identificadores.

1. Se `assignableScopes` for um grupo de gerenciamento, substitua a instância *{groupId}* pelo identificador do grupo de gerenciamento. A adição `assignableScopes` de um grupo de gerenciamento está atualmente em pré-visualização.

1. Em `actions` propriedade, adicione as operações que permitem que a função seja executada.

1. Em `notActions` propriedade, adicione as operações que são excluídas do permitidos `actions`.

1. Em `roleName` e `description` propriedades, especifique um nome exclusivo de função e uma descrição. Para obter informações sobre as propriedades, confira [Funções personalizadas](custom-roles.md).

    O exemplo a seguir mostra um exemplo de um corpo de solicitação:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, use [Definições de Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) API REST. Para chamar esta API, você deve estar conectado com um usuário `Microsoft.Authorization/roleDefinitions/write` que tenha `assignableScopes`uma função que tenha a permissão em todos os . Das funções incorporadas, apenas [o Proprietário](built-in-roles.md#owner) e [o Administrador de Acesso ao Usuário](built-in-roles.md#user-access-administrator) incluem essa permissão.

1. Use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) ou [Definições de Função - Obter](/rest/api/authorization/roledefinitions/get) API REST para obter informações sobre a função personalizada. Para obter mais informações, consulte a seção ['Listas de funções personalizadas'](#list-custom-roles) anteriores.

1. Comece com a solicitação a seguir:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro da URI, substita *{scope}* pelo primeiro `assignableScopes` da função personalizada.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

1. Com base nas informações sobre a função personalizada, crie um corpo de solicitação com o seguinte formato:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Atualize o corpo de solicitação com as alterações que você deseja fazer a função personalizada.

    O exemplo a seguir mostra um exemplo de um corpo de solicitação com uma nova ação de configurações de diagnóstico adicionada:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

Para excluir uma função personalizada, use [Definições de Função - Excluir](/rest/api/authorization/roledefinitions/delete) API REST. Para chamar esta API, você deve estar conectado com um usuário `Microsoft.Authorization/roleDefinitions/delete` que tenha `assignableScopes`uma função que tenha a permissão em todos os . Das funções incorporadas, apenas [o Proprietário](built-in-roles.md#owner) e [o Administrador de Acesso ao Usuário](built-in-roles.md#user-access-administrator) incluem essa permissão.

1. Use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) ou [Definições de Função - Obter](/rest/api/authorization/roledefinitions/get) API REST para obter o identificador GUI da função personalizada. Para obter mais informações, consulte a seção ['Listas de funções personalizadas'](#list-custom-roles) anteriores.

1. Comece com a solicitação a seguir:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja excluir a função personalizada.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |

1. Substitua *{roleDefinitionId}* pelo identificador GUID da função personalizada.

## <a name="next-steps"></a>Próximas etapas

- [Funções personalizadas para recursos do Azure](custom-roles.md)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e a API REST](role-assignments-rest.md)
- [Referência da API Azure REST](/rest/api/azure/)
