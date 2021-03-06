---
title: Compreender as definições de função no RBAC dos recursos do Azure | Microsoft Docs
description: Saiba mais sobre as definições de função no controle de acesso baseado em função (RBAC) para o gerenciamento de acesso refinado de recursos no Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 777ea7cc29679a3819e94d39913f167ea1cb3453
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641371"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Compreender as definições de função nos recursos do Azure

Se você estiver tentando compreender o funcionamento de uma função ou se estiver criando sua própria [função personalizada para recursos do Azure](custom-roles.md), é aconselhável entender como as funções são definidas. Este artigo descreve os detalhes das definições de função e fornece alguns exemplos.

## <a name="role-definition"></a>Definição de função

Uma *definição de função* é um conjunto de permissões. Às vezes, é chamada apenas de *função*. Uma definição de função lista as operações que podem ser executadas, como leitura, gravação e exclusão. Ele também pode listar as operações que não podem ser executadas ou operações relacionadas a dados subjacentes. Uma definição de função tem as seguintes propriedades:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

| Propriedade | Descrição |
| --- | --- |
| `Name` | O nome de exibição do papel. |
| `Id` | A ID exclusiva da função. |
| `IsCustom` | Indica se esta é uma função personalizada. Defina como `true` para funções personalizadas. |
| `Description` | A descrição do papel. |
| `Actions` | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento permitidas pela função. |
| `NotActions` | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento que são excluídas do `Actions` permitido. |
| `DataActions` | Uma matriz de cadeias de caracteres que especifica as operações de dados permitidas pela função em seus dados dentro desse objeto. |
| `NotDataActions` | Uma matriz de cadeias de caracteres que especifica as operações de dados excluídas do `DataActions` permitido. |
| `AssignableScopes` | Uma matriz de strings que especifica os escopos que a função está disponível para atribuição. |

### <a name="operations-format"></a>Formato de operações

Operações são especificadas com cadeias de caracteres que têm o seguinte formato:

- `{Company}.{ProviderName}/{resourceType}/{action}`

A parte `{action}` de uma cadeia de caracteres de operação especifica o tipo de operações que você pode executar em um tipo de recurso. Por exemplo, você verá as seguintes subcadeias de caracteres em `{action}`:

| Subcadeia de caracteres de ação    | Descrição         |
| ------------------- | ------------------- |
| `*` | O caractere curinga concede acesso a todas as operações que correspondem à cadeia de caracteres. |
| `read` | Habilita operações de leitura (GET). |
| `write` | Habilita operações de gravação (PUT ou PATCH). |
| `action` | Permite operações personalizadas como reiniciar máquinas virtuais (POST). |
| `delete` | Habilita operações de exclusão (DELETE). |

### <a name="role-definition-example"></a>Exemplo de definição de função

Aqui está a definição da função [Colaborador](built-in-roles.md#contributor) no formato JSON. A operação curinga (`*`) em `Actions` indica que a entidade de segurança atribuída a essa função pode executar todas as ações ou, em outras palavras, pode gerenciar tudo. Isso inclui ações definidas no futuro, conforme o Azure adiciona novos tipos de recurso. As operações em `NotActions` são subtraídas de `Actions`. No caso da função [Contribuidor](built-in-roles.md#contributor), `NotActions` remove a capacidade de essa função gerenciar o acesso a recursos e também atribuir acesso aos recursos.

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Gestão e operações de dados

O controle de acesso baseado em função para operações de gerenciamento é especificado nas propriedades `Actions` e `NotActions` de uma definição de função. Aqui estão alguns exemplos de operações de gerenciamento no Azure:

- Gerenciar o acesso a uma conta de armazenamento
- Criar, atualizar ou excluir um contêiner de blob
- Excluir um grupo de recursos e todos os seus recursos

O acesso ao gerenciamento não é herdado aos seus dados, desde que o método de autenticação do contêiner esteja definido como "Conta de Usuário Azure AD" e não "Chave de acesso". Essa separação impede que funções com curingas (`*`) tenham acesso irrestrito aos seus dados. Por exemplo, se um usuário tiver uma [leitor](built-in-roles.md#reader) função em uma assinatura, em seguida, eles podem exibir a conta de armazenamento, mas não podem exibir os dados subjacentes por padrão.

Anteriormente, o controle de acesso baseado em função não foi usado para operações de dados. Autorização para operações de dados variadas em provedores de recursos. O mesmo modelo de autorização de controle de acesso baseado em função usado para operações de gerenciamento foi estendido às operações de dados.

Para suportar operações de dados, novas propriedades de dados foram adicionadas à definição da função. Operações de dados são especificadas no `DataActions` e `NotDataActions` propriedades. Adicionando essas propriedades de dados, a separação entre o gerenciamento e de dados é mantida. Isso impede que as atribuições de função atual com curingas (`*`) de repente ter acesso a dados. Aqui estão algumas operações de dados que podem ser especificadas em `DataActions` e `NotDataActions`:

- Leia uma lista de blobs em um contêiner
- Gravar um blob de armazenamento em um contêiner
- Deletar uma mensagem em uma fila

Aqui está a definição de função [Armazenamento Blob Data Reader,](built-in-roles.md#storage-blob-data-reader) que inclui operações tanto nas propriedades `Actions` quanto `DataActions` nas propriedades. Essa função permite que você leia o contêiner de blob e também os dados blob subjacentes.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Apenas operações de dados podem ser adicionadas para o `DataActions` e `NotDataActions` propriedades. Provedores de recursos identificam quais operações são operações de dados, definindo o `isDataAction` propriedade `true`. Para ver uma lista das operações em que `isDataAction` é `true`, consulte [ Operações do provedor de recursos ](resource-provider-operations.md). Funções que não têm as operações de dados não precisam ter `DataActions` e `NotDataActions` propriedades dentro da definição de função.

Autorização para todas as chamadas de API de operação de gerenciamento é tratada pelo Gerenciador de recursos do Azure. A autorização para chamadas de API de operação de dados é tratada por um provedor de recursos ou o Gerenciador de recursos do Azure.

### <a name="data-operations-example"></a>Exemplo de operações de dados

Para entender melhor como funcionam as operações de gerenciamento e de dados, vamos considerar um exemplo específico. Alice foi atribuída a [proprietário](built-in-roles.md#owner) função no escopo da assinatura. O Bob recebeu a função [de contribuinte de dados do Blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) no escopo da conta de armazenamento. O diagrama a seguir mostra este exemplo.

![Controle de acesso baseado em função foi estendido para dar suporte a operações de dados e de gerenciamento](./media/role-definitions/rbac-management-data.png)

O [papel de Proprietário](built-in-roles.md#owner) para Alice e o papel de Contribuinte de Dados [blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) para Bob têm as seguintes ações:

Proprietário

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Colaborador de dados de blob de armazenamento

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Uma vez que Alice`*`tem uma ação curinga ( ) em um escopo de assinatura, suas permissões herdam para permitir que eles realizem todas as ações de gerenciamento. Alice pode ler, gravar e excluir os contêineres. No entanto, Alice não pode executar operações de dados sem realizar etapas adicionais. Por exemplo, por padrão, Alice não pode ler os blobs dentro de um contêiner. Para ler os blobs, Alice teria que recuperar as chaves de acesso de armazenamento e usá-las para acessar os blobs.

As permissões do Bob estão `Actions` `DataActions` restritas apenas à e especificadas na função [De contribuinte de dados blob](built-in-roles.md#storage-blob-data-contributor) de armazenamento. Com base na função, Bob pode executar operações de dados e de gerenciamento. Por exemplo, Bob pode ler, gravar e excluir contêineres na conta de armazenamento especificada e também pode ler, gravar e excluir as bolhas.

Para saber mais sobre a segurança de plano de dados e gerenciamento, confira o [Guia de segurança de Armazenamento do Azure](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Quais ferramentas de suportam usando o RBAC para operações de dados?

Para visualizar e trabalhar com operações de dados, você deve ter as versões corretas das ferramentas ou SDKs:

| Ferramenta  | Versão  |
|---------|---------|
| [PowerShell do Azure](/powershell/azure/install-az-ps) | 1.1.0 ou posterior |
| [CLI do Azure](/cli/azure/install-azure-cli) | 2.0.30 ou posterior |
| [Azure para .NET](/dotnet/azure/) | 2.8.0-versão prévia ou posterior |
| [SDK do Azure para linguagem Go](/azure/go/azure-sdk-go-install) | 15.0.0 ou posterior |
| [Azure para Java](/java/azure/) | 1.9.0 ou posterior |
| [Azure para Python](/azure/python/) | 0.40.0 ou posterior |
| [SDK do Azure para Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 ou posterior |

Para exibir e usar as operações de dados na API REST, você precisa definir o parâmetro **api-version** para a seguinte versão ou outra posterior:

- 2018-07-01

## <a name="actions"></a>Ações

Uma permissão `Actions`especifica as operações de gerenciamento permitidas pela função a ser realizada. É uma coleção de cadeias de operação que identificam as operações protegíveis dos provedores de recursos do Azure. Aqui estão alguns exemplos de operações de gerenciamento que podem ser usadas no `Actions`.

> [!div class="mx-tableFixed"]
> | Cadeia de caracteres da operação    | Descrição         |
> | ------------------- | ------------------- |
> | `*/read` | Concede acesso a operações de leitura a todos os tipos de recursos de todos os provedores de recursos do Azure.|
> | `Microsoft.Compute/*` | Concede acesso a todas as operações a todos os tipos de recursos no provedor de recursos Microsoft.Compute.|
> | `Microsoft.Network/*/read` | Concede acesso a operações de leitura a todos os tipos de recursos no provedor de recursos Microsoft.Network do Azure.|
> | `Microsoft.Compute/virtualMachines/*` | Concede acesso a todas as operações de máquinas virtuais e seus tipos de recursos filho.|
> | `microsoft.web/sites/restart/Action` | Concede acesso para reinicialização de um aplicativo Web.|

## <a name="notactions"></a>NotActions

A permissão `NotActions` especifica as operações de gerenciamento que são excluídas do `Actions` permitido. Use a permissão `NotActions` se o conjunto de operações que você deseja permitir fica definido mais facilmente excluindo as operações restritas. O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as operações `NotActions` das operações `Actions`.

> [!NOTE]
> Se um usuário for atribuído a uma função que exclui uma operação em `NotActions` e for atribuído a uma segunda função que concede acesso à mesma operação, ele terá permissão para executar essa operação. `NotActions` não é uma regra de negação, é simplesmente uma maneira conveniente de criar um conjunto de operações permitidas quando for necessário excluir operações específicas.
>

## <a name="dataactions"></a>DataActions

Uma permissão `DataActions` que especifica as operações de dados permitidas pela função em seus dados dentro desse objeto. Por exemplo, se um usuário tem acesso de leitura blob dados para uma conta de armazenamento, eles podem ler blobs dentro dessa conta de armazenamento. Aqui estão alguns exemplos de operações de dados que podem ser usados em `DataActions`.

> [!div class="mx-tableFixed"]
> | Cadeia de caracteres da operação    | Descrição         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Retorna um blob ou uma lista de blobs. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Retorna o resultado de escrever um blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Retorna uma mensagem. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Retorna uma mensagem ou o resultado de gravar ou excluir uma mensagem. |

## <a name="notdataactions"></a>NotDataActions

O `NotDataActions` permissão especifica as operações de dados que são excluídas do permitidos `DataActions`. O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as operações `NotDataActions` das operações `DataActions`. Cada provedor de recursos fornece seu respectivo conjunto de APIs para atender as operações de dados.

> [!NOTE]
> Se um usuário é atribuído a uma função que exclui uma operação de dados em `NotDataActions`e é atribuído a uma segunda função que concede acesso à mesma operação de dados, o usuário tem permissão para executar essa operação de dados. `NotDataActions` não é uma negação de regra – é simplesmente uma maneira conveniente de criar um conjunto de operações de dados permitidos quando precisam de operações de dados específicos a serem excluídos.
>

## <a name="assignablescopes"></a>AssignableScopes

A `AssignableScopes` propriedade especifica os escopos (grupos de gerenciamento, assinaturas ou grupos de recursos) que têm essa definição de função disponível. Você pode disponibilizar a função para atribuição apenas nos grupos de gerenciamento, assinaturas ou grupos de recursos que a requerem. Você deve usar pelo menos um grupo de gerenciamento, assinatura ou grupo de recursos.

As funções internas têm `AssignableScopes` definido como o escopo raiz (`"/"`). O escopo raiz indica que a função está disponível para atribuição em todos os escopos. Exemplos de escopos válidos que podem ser atribuídos incluem:

> [!div class="mx-tableFixed"]
> | A função está disponível para atribuição | Exemplo |
> |----------|---------|
> | Uma assinatura | `"/subscriptions/{subscriptionId1}"` |
> | Duas assinaturas | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Grupo de recursos de rede | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Um grupo de gestão | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Grupo de gestão e uma assinatura | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Todos os escopos (se aplica apenas a funções incorporadas) | `"/"` |

Para saber mais sobre `AssignableScopes` para funções personalizadas, confira as [Funções personalizadas para recursos do Azure](custom-roles.md).

## <a name="next-steps"></a>Próximas etapas

* [Funções internas para recursos do Azure](built-in-roles.md)
* [Funções personalizadas para recursos do Azure](custom-roles.md)
* [Operações do provedor de recursos do Azure Resource Manager](resource-provider-operations.md)
