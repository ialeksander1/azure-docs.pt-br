---
title: Copiar dados de fontes ODBC usando a Fábrica de Dados do Azure
description: Saiba como copiar dados de fontes OData para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: da5c53f8953960c382070be658add2877fff3f8c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416891"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copiar dados de e para armazenamentos de dados ODBC usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-odbc-connector.md)
> * [Versão atual](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para um armazenamento de dados ODBC. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector ODBC é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados de uma fonte ODBC para qualquer armazenamento de dados de coletor com suporte ou copiar de qualquer armazenamento de dados de origem com suporte para um coletor ODBC. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector ODBC dá suporte à cópia de dados de/para **quaisquer armazenamentos de dados compatíveis com ODBC** usando a autenticação **Básica** ou **Anônima**. Um **driver ODBC de 64 bits** é necessário.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector ODBC, você precisa:

- Configurar um Integration Runtime auto-hospedado. Consulte [o artigo 'Executtime de integração'.](create-self-hosted-integration-runtime.md)
- Instale o driver ODBC de 64 bits para o armazenamento de dados na máquina Integration Runtime.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector ODBC.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do ODBC:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Odbc** | Sim |
| connectionString | A cadeia de conexão, exceto a parte de credencial. Você pode especificar a cadeia de conexão com um padrão como `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` ou usar DSN (nome da fonte de dados) do sistema que você configurou no computador do Integration Runtime com o `"DSN=<name of the DSN on IR machine>;"` (é necessário ainda especificar a parte de credencial no serviço vinculado adequadamente).<br>Você também pode colocar uma senha no Azure Key Vault e puxar a `password` configuração para fora da seqüência de conexão.Consulte as [credenciais da Loja no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes.| Sim |
| authenticationType | Tipo de autenticação usado para se conectar ao armazenamento de dados ODBC.<br/>Os valores permitidos são: **Básica** e **Anônima**. | Sim |
| userName | Especifique o nome de usuário se você estiver usando a autenticação Básica. | Não |
| password | Especifique a senha da conta de usuário que você especificou para userName. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Não |
| credencial | A parte da credencial de acesso da cadeia de conexão especificada no formato propriedade-valor específico do driver. Exemplo: `"RefreshToken=<secret refresh token>;"`. Marque esse campo como uma SecureString. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo 1: usando a autenticação Básica**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

**Exemplo 2: usando a autenticação Anônima**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados ODBC.

Para copiar dados do armazenamento de dados compatível com ODBC, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida como: **OdbcTable** | Sim |
| tableName | Nome da tabela no repositório de dados ODBC. | Não para fonte (se "query" na fonte da atividade for especificada);<br/>Sim para coletor |

**Exemplo**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Se você `RelationalTable` estava usando conjunto de dados digitado, ele ainda é suportado como está, enquanto você é sugerido para usar o novo daqui para frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte ODBC.

### <a name="odbc-as-source"></a>ODBC como fonte

Para copiar dados do armazenamento de dados compatível com ODBC, as seguintes propriedades são suportadas na seção **origem da** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de atividade de cópia deve ser definida como: **OdbcSource** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você `RelationalSource` estava usando fonte digitada, ela ainda é suportada como está, enquanto você é sugerido para usar a nova daqui para frente.

### <a name="odbc-as-sink"></a>ODBC como coletor

Para copiar dados para um armazenamento de dados compatível com ODBC, defina o tipo de coletor na atividade de cópia como **OdbcSink**. As seguintes propriedades são suportadas na seção **de socedção de** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | O tipo de propriedade do coletor da atividade de cópia deve ser definido como: **BlobSink** | Sim |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são: período.  Exemplo: "00:30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Os valores permitidos são: inteiro (número de linhas). |Não (o padrão é 0 – detectado automaticamente) |
| preCopyScript |Especifica uma consulta SQL para a atividade de cópia, a ser executada antes de gravar dados no armazenamento de dados em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. |Não |

> [!NOTE]
> Para "writeBatchSize", se não estiver definida (detectada automaticamente), a atividade de cópia primeiro detecta se o driver dá suporte a operações em lote e define-a como 10000, se houver suporte, ou como 1, caso não haja. Se você definir explicitamente o valor como diferente de 0, a atividade de cópia respeitará o valor e falhará em runtime se o driver não der suporte a operações em lote.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="sap-hana-sink"></a>Coletor do SAP HANA

>[!NOTE]
>Para copiar dados de um armazenamento de dados do SAP HANA, consulte o [conector nativo do SAP HANA](connector-sap-hana.md). Para copiar dados para o SAP HANA, siga estas instruções para usar o conector ODBC. Observe que os serviços vinculados para o conector do SAP HANA e o conector ODBC são de tipos diferentes, portanto, não podem ser reutilizados.
>

Você pode copiar dados de um banco de dados SAP HANA usando o conector ODBC genérico.

Configure um Integration Runtime auto-hospedado em um computador com acesso ao seu armazenamento de dados. O Integration Runtime usa o driver ODBC para o SAP HANA se conectar ao armazenamento de dados. Portanto, instale o driver, se ainda não estiver instalado no mesmo computador. Confira a seção [Pré-requisitos](#prerequisites) para saber detalhes.

Antes de usar o coletor do SAP HANA em uma solução de Data Factory, verifique se o Integration Runtime pode se conectar ao armazenamento de dados, usando as instruções na seção [Solucionar problemas de conectividade](#troubleshoot-connectivity-issues).

Crie um serviço vinculado de ODBC para vincular um armazenamento de dados SAP HANA a um Azure Data Factory, como mostrado no seguinte exemplo:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Leia o artigo desde o início para uma visão geral detalhada do uso de armazenamentos de dados ODBC como armazenamentos de dados de origem/coletor em uma operação de cópia.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).


## <a name="troubleshoot-connectivity-issues"></a>Solucionar problemas de conectividade

Para solucionar problemas de conexão, use a guia **Diagnósticos** do **Gerenciador de configuração do Integration Runtime**.

1. Inicie o **Gerenciador de configuração do Integration Runtime**.
2. Mude para a guia **Diagnósticos.**
3. Na seção "Testar Conexão", selecione o **tipo** do armazenamento de dados (serviço vinculado).
4. Especifique a **cadeia de conexão** usada para se conectar ao armazenamento de dados, escolha a **autenticação** e digite o **nome de usuário**, a **senha** e/ou as **credenciais**.
5. Clique em **Testar Conexão** para testar a conexão com o armazenamento de dados.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
