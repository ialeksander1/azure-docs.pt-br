---
title: Copiar dados no Dynamics (Serviço comum de dados)
description: Saiba como copiar dados do Microsoft Dynamics CRM ou Microsoft Dynamics 365 (Common Data Service) para armazenamentos de dados de coletor com suporte ou de armazenamentos de dados de fonte com suporte para Dynamics CRM ou Dynamics 365 usando uma atividade de cópia em um pipeline do Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: c891cb4eca2c286b3ac636e5995714accd591772
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417353"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copiar dados de e para Dynamics 365 (Common Data Service) ou Dynamics CRM usando o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo estrutura como usar atividade de cópia no Azure Data Factory para copiar dados de e para Microsoft Dynamics 365 ou Microsoft Dynamics CRM. Ele se baseia no artigo visão geral da [Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados do Dynamics 365 (Common Data Service) ou Dynamics CRM para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer repositório de dados de fonte com suporte para Dynamics 365 (Common Data Service) ou Dynamics CRM. Para obter uma lista de repositórios de dados com suporte como fontes ou coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Este conector Dynamics suporta a versão Dynamics 7.x a 9.x para on-line ou on-premise. Mais especificamente,

- Versão 7.x mapas para Dynamics CRM 2015
- Versão 8.x mapas para Dynamics CRM 2016 e a versão inicial do Dynamics 365
- Versão 9.x mapas para a versão posterior do Dynamics 365

Consulte a tabela a seguir sobre os tipos e configurações de autenticação suportadas para as respectivas versões/produtos Dynamics. (IFD é abreviação de implantação para a Internet.)

| Versões do Dynamics | Tipos de autenticação | Exemplos de serviço vinculado |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 online <br> Dynamics CRM Online | Diretor de serviços AAD <br> Office365 | [Dinâmica on-line + Diretor de serviço SAD ou Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local com IFD <br> Dynamics CRM 2016 local com IFD <br> Dynamics CRM 2015 local com IFD | IFD | [Dinâmica no local com IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Para o Dynamics 365 especificamente, os seguintes tipos de aplicativos são compatíveis:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Outros tipos de aplicação, por exemplo Finanças e Operações, Talentos, etc. não são suportados por este conector.

Este conector Dynamics é construído em cima da [ferramenta Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Para copiar dados de **Finanças e Operações do Dynamics 365**, você pode usar o [Conector do Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Dynamics.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida como **Dynamics,** **DynamicsCrm**ou **CommonDataServiceForApps**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Deve ser **"Online"** para o Dynamics online. | Sim |
| serviceUri | A URL de serviço da instância do Dynamics, por exemplo, `https://adfdynamics.crm.dynamics.com`. | Sim |
| authenticationType | O tipo de autenticação para se conectar a um servidor do Dynamics. Os valores permitidos são: **AADServicePrincipal** ou **"Office365"**. | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo do Azure Active Directory. | Sim, `AADServicePrincipal` ao usar a autenticação |
| serviçoPrincipalCredentialType | Especifique o tipo de credencial a ser usada para autenticação da entidade de serviço. Os valores permitidos são: **ServicePrincipalKey** ou **ServicePrincipalCert**. | Sim, `AADServicePrincipal` ao usar a autenticação |
| serviçoPrincipalCredential | Especifique a credencial principal do serviço. <br>Ao `ServicePrincipalKey` usar como tipo `servicePrincipalCredential` de credencial, pode ser uma string (o ADF irá criptografá-lo após a implantação do serviço vinculado) ou uma referência a um segredo no AKV. <br>Ao `ServicePrincipalCert` usar como credencial, `servicePrincipalCredential` deve ser uma referência a um certificado em AKV. | Sim, `AADServicePrincipal` ao usar a autenticação | 
| Nome de Usuário | Especifique o nome de usuário para se conectar ao Dynamics. | Sim, `Office365` ao usar a autenticação |
| password | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Sim, `Office365` ao usar a autenticação |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não para a fonte, Sim para o coletor se o serviço vinculado à fonte não possuir um integration runtime |

>[!NOTE]
>O conector do Dynamics costumava usar a propriedade "organizationName" opcional para identificar a instância do Dynamics CRM/365 Online. Embora continue funcionando, sugerimos que você especifique a nova propriedade "serviceUri" para obter melhor desempenho para a descoberta da instância.

**Exemplo: Dinâmica on-line usando o principal do serviço AAD + autenticação de chave**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**Exemplo: Dinâmica on-line usando o principal do serviço AAD + autenticação de certificado**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**Exemplo: Dynamics online usando a autenticação do Office365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM local com IFD

*Propriedades adicionais que comparam com o Dynamics online são "hostName" e "port".*

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida como **Dynamics,** **DynamicsCrm**ou **CommonDataServiceForApps**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Deve ser **"OnPremisesWithIfd"** para o Dynamics local com IFD.| Sim |
| hostName | O nome do host do servidor do Dynamics local. | Sim |
| porta | O nome da porta do servidor do Dynamics local. | Não, o padrão é 443 |
| organizationName | O nome da organização da instância do Dynamics. | Sim |
| authenticationType | O tipo de autenticação para se conectar ao servidor do Dynamics. Especifique **"Ifd"** para o Dynamics local com IFD. | Sim |
| Nome de Usuário | Especifique o nome de usuário para se conectar ao Dynamics. | Sim |
| password | Especifique a senha da conta de usuário que você especificou para o nome de usuário. Você pode optar por marcar este campo como uma SecureString para armazená-la com segurança no ADF ou armazenar a senha no Azure Key Vault e permitir o pull de atividade de cópia a partir daí, ao executar a cópia de dados - Saiba mais de [Armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não para fonte, Sim para o coletor |

**Exemplo: Dynamics local com IFD usando a autenticação de IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Dynamics.

Para copiar dados de e para dinâmica, as seguintes propriedades são suportadas.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida como **DynamicsEntity,** **DynamicsCrmEntity**ou **CommonDataServiceForAppsEntity**. |Sim |
| entityName | O nome lógico da entidade a ser recuperada. | Não para fonte (se "query" na fonte da atividade for especificada), Sim para coletor |

**Exemplo:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e tipos de coletor do Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics como um tipo de fonte

Para copiar dados do Dynamics, as seguintes propriedades são suportadas na seção origem da atividade **de** cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de atividade de cópia deve ser definida como **DynamicsSource,** **DynamicsCrmSource**ou **CommonDataServiceForAppsSource**. | Sim |
| Consulta | FetchXML é uma linguagem de consulta proprietária que é usada no Dynamics (online e local). Veja os exemplos a seguir. Para saber mais, consulte [Criar consultas com fetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Não (se "entityName" no conjunto de dados for especificada) |

>[!NOTE]
>A coluna PK sempre será copiada, mesmo que ela não esteja contida na projeção da coluna que você configurar na consulta FetchXML.

> [!IMPORTANT]
>- Quando você copia dados do Dynamics, o mapeamento explícito da coluna do Dynamics para o afundamento é opcional, mas altamente recomandado para garantir um resultado de cópia determinista.
>- Ao importar esquema na autoria da UI, a ADF infere o esquema amostrando as linhas superiores do resultado da consulta Dynamics para inicializar a lista de colunas de origem, nesse caso, as colunas sem valores nas linhas superiores serão omitidas. O mesmo comportamento se aplica às execuções de cópia se não houver mapeamento explícito. Você pode rever e adicionar mais colunas no mapeamento, que será honrado durante o tempo de execução da cópia.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Exemplo de consulta FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics como um tipo de coletor

Para copiar dados para o Dynamics, as seguintes propriedades são suportadas na seção **de dissipação de** atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo do dissipador de atividadede cópia deve ser definida como **DynamicsSink,** **DynamicsCrmSink**ou **CommonDataServiceForAppsSink**. | Sim |
| writeBehavior | O comportamento da operação de gravação.<br/>O valor permitido é **"Upsert".** | Sim |
| nome-chave alternativo | Especifique o nome de chave alternativa definido em sua entidade para executar "Upsert". | Não |
| writeBatchSize | A contagem de linhas de dados gravados no Dynamics em cada lote. | Não (o padrão é 10) |
| ignoreNullValues | Indica se deve ignorar valores nulos de dados de entrada (exceto campos de chave) durante uma operação de gravação.<br/>Os valores permitidos são **True** e **False**.<br>- **True**: deixa os dados no objeto de destino inalterados quando você faz uma operação upsert/atualização. Insira um valor padrão definido quando você faz uma operação insert.<br/>- **False**: atualiza os dados no objeto de destino como NULL quando você faz uma operação upsert/atualização. Insira um valor NULL quando você faz uma operação insert. | Não (padrão é falso) |

>[!NOTE]
>O valor padrão da pia "**writeBatchSize**" e a atividade de cópia "**[parallelCopies](copy-activity-performance-features.md#parallel-copy)**" para o dissipador Dynamics são ambos 10. Portanto, 100 registros são enviados ao Dynamics simultaneamente.

Para o Dynamics 365 online, há um limite de [2 chamadas simultâneas de lote por organização](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Se esse limite for excedido, uma falha de "Servidor ocupado" será lançada antes que a primeira solicitação seja executada. Manter "writeBatchSize" menor ou igual a 10 evitaria essa limitação de chamadas simultâneas.

A combinação ideal de "**writeBatchSize**" e "**parallelCopies**" depende do esquema de sua entidade, por exemplo, número de colunas, tamanho da linha, número de plugins/fluxos de trabalho/atividades de fluxo de trabalho ligadas a essas chamadas, etc. A configuração padrão de 10 writeBatchSize * 10 parallelCopies é a recomendação de acordo com o serviço Dynamics, que funcionaria para a maioria das entidades Dynamics, embora possa não ser o melhor desempenho. Você pode ajustar o desempenho ajustando a combinação em suas configurações de atividade de cópia.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mapeamento de tipo de dados para Dynamics

Ao copiar dados do Dynamics, os seguintes mapeamentos são usados de tipos de dados do Dynamics para tipos de dados intermediários do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

Configure o tipo de dados do Data Factory correspondente em uma estrutura do conjunto de dados com base em sua fonte de tipo de dados do Dynamics, usando a tabela de mapeamento a seguir.

| Tipo de dados do Dynamics | Tipo de dados provisório do Data Factory | Tem suporte como origem | Tem suporte como coletor |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (com o único destino associado) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Os tipos de dados Dynamics AttributeType.CalendarRules, AttributeType.MultiSelectPicklist e AttributeType.PartyList não são suportados.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
