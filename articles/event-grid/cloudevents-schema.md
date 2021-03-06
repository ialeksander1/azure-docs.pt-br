---
title: Usar a Grade de Eventos do Azure com eventos no esquema CloudEvents
description: Descreve como usar o esquema CloudEvents para eventos no Azure Event Grid. O serviço suporta eventos na implementação json de Eventos em Nuvem.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 404052984cb99e37f7404a47f3ac374088d32d6c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393485"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Use o esquema CloudEvents v1.0 com event grid
Além do seu [esquema de eventos padrão,](event-schema.md)o Azure Event Grid suporta nativamente eventos na [implementação jSON do cloudevents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e [da vinculação](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)do protocolo HTTP . [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever dados de eventos.

O CloudEvents simplifica a interoperabilidade, fornecendo um esquema comum do evento para publicar e consumir eventos com base em nuvem. Esse esquema permite ferramentas uniforme, formas padrão de roteamento e manipulação de eventos e maneiras universais de desserializar o esquema de evento externo. Com um esquema comum, você pode integrar facilmente mais trabalho entre plataformas.

O CloudEvents está sendo criado por vários [colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, por meio da [Cloud Native Computing Foundation](https://www.cncf.io/). Está atualmente disponível como versão 1.0.

Este artigo descreve como usar o esquema de CloudEvents com a Grade de Eventos.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalar versão prévia do recurso

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Esquema de CloudEvent

Aqui está um exemplo de um evento de Armazenamento de Blob do Azure no formato CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Uma descrição detalhada dos campos disponíveis, seus tipos e definições em CloudEvents v1.0 está [disponível aqui](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Os valores dos cabeçalhos para eventos entregues no esquema CloudEvents e no esquema da Grade de Eventos são os mesmos, exceto para `content-type`. Para o esquema CloudEvents, esse valor de cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"`. Para o esquema Grade de Eventos, esse valor de cabeçalho é `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Configurar a Grade de Eventos para CloudEvents

Você pode usar a Grade de Eventos para entrada e saída de eventos no esquema CloudEvents. Você pode usar o CloudEvents para eventos do sistema, como eventos do Armazenamento de Blob e eventos do Hub IoT e eventos personalizados. Também pode transformar esses eventos durante a transmissão para trás e para frente.


| Esquema de entrada       | Esquema de saída
|--------------------|---------------------
| Formato de CloudEvents | Formato de CloudEvents
| Formato da Grade de Eventos  | Formato de CloudEvents
| Formato da Grade de Eventos  | Formato da Grade de Eventos

Para todos os esquemas de evento, a Grade de Eventos requer validação ao publicar em um tópico de grade de eventos e ao criar uma inscrição de evento. Para saber mais, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).

### <a name="input-schema"></a>Esquema de entrada

Quando você cria o tópico personalizado, você definir o esquema de entrada para um tópico personalizado.

Para a CLI do Azure, use:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Para o PowerShell, use:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Esquema de saída

Quando você cria a assinatura de evento, você definir o esquema de saída.

Para a CLI do Azure, use:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Para o PowerShell, use:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 No momento, você não pode usar um gatilho de Grade de Eventos para um aplicativo do Azure Functions, quando o evento é entregue no esquema do CloudEvents. Use um gatilho HTTP. Para exemplos de implementação de um gatilho HTTP que recebe eventos no esquema CloudEvents, consulte [Usando CloudEvents com Funções Azure](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto final com CloudEvents v1.0

Se você já está familiarizado com event grid, você pode estar ciente do aperto de mão de validação de ponto final da Event Grid para evitar abusos. CloudEvents v1.0 implementa sua própria [semântica de proteção](security-authentication.md#webhook-event-delivery) contra abuso usando o método HTTP OPTIONS. Saiba mais sobre isso [aqui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ao usar o esquema CloudEvents para saída, event grid usa com a proteção de abuso CloudEvents v1.0 no lugar do mecanismo de evento de validação event Grid.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Use com funções do Azure

A [vinculação Azure Functions Event Grid](../azure-functions/functions-bindings-event-grid.md) não suporta nativamente CloudEvents, então as funções acionadas pelo HTTP são usadas para ler mensagens do CloudEvents. Ao usar um gatilho HTTP para ler CloudEvents, você tem que escrever código para o que o gatilho da Grade de Eventos faz automaticamente:

* Envie uma resposta de validação para [uma solicitação de validação de assinatura](../event-grid/security-authentication.md#webhook-event-delivery).
* Invoque a função uma vez por elemento da matriz de eventos contida no corpo da solicitação.

Para obter informações sobre a URL a ser utilizada para invocar a função localmente ou quando for executada no Azure, consulte a [documentação de referência de associação de gatilho HTTP](../azure-functions/functions-bindings-http-webhook.md)

O código C# de exemplo a seguir para um gatilho HTTP simula o comportamento do gatilho da Grade de Eventos.  Use este exemplo para eventos entregues no esquema CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == "OPTIONS")
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

O código JavaScript de exemplo a seguir para um gatilho HTTP simula o comportamento do gatilho da Grade de Eventos. Use este exemplo para eventos entregues no esquema CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS) {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200, body: { "ValidationResponse": code } };
        context.res.headers.append('Webhook-Allowed-Origin', 'eventgrid.azure.net');
    }
    else
    {
        var message = req.body;
        
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Incentivamos você a testar, comentar e [contribuir](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) para CloudEvents.
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
