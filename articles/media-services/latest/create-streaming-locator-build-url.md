---
title: Crie um localizador de streaming e construa URLs - Azure Media Services
description: Este artigo demonstra como criar um localizador de streaming e construir URLs.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 2972c60aa5874c21a6f7bce21020ad58b5f3b556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304557"
---
# <a name="create-a-streaming-locator-and-build-urls"></a>Crie um localizador de streaming e construa URLs

No Azure Media Services, para criar uma URL de streaming, você precisa primeiro criar um [localizador de streaming](streaming-locators-concept.md). Em seguida, concatena o nome do host [do Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho **do Localizador de Streaming.** Neste exemplo, o *padrão do * **Ponto de Extremidade de Streaming** é usado. Quando você cria uma conta do Serviço de Mídia pela primeira vez, este **endpoint** de streaming *padrão* estará em um estado parado, então você precisa chamar **Start** para iniciar o streaming.

Este artigo demonstra como criar um localizador de streaming e construir uma URL de streaming usando SDKs Java e .NET.

## <a name="prerequisite"></a>Pré-requisito 

Pré-visualização [de embalagem dinâmica](dynamic-packaging-overview.md)

## <a name="java"></a>Java

```java
/**
* Creates a StreamingLocator for the specified asset and with the specified streaming policy name.
* Once the StreamingLocator is created the output asset is available to clients for playback.
* @param manager       The entry point of Azure Media resource management
* @param resourceGroup The name of the resource group within the Azure subscription
* @param accountName   The Media Services account name
* @param assetName     The name of the output asset
* @param locatorName   The StreamingLocator name (unique in this case)
* @return              The locator created
*/
private static StreamingLocator getStreamingLocator(MediaManager manager, String resourceGroup, String accountName,
    String assetName, String locatorName) {
    // Note that we are using one of the PredefinedStreamingPolicies which tell the Origin component
    // of Azure Media Services how to publish the content for streaming.
    System.out.println("Creating a streaming locator...");
    StreamingLocator locator = manager
        .streamingLocators().define(locatorName)
        .withExistingMediaservice(resourceGroup, accountName)
        .withAssetName(assetName)
        .withStreamingPolicyName("Predefined_ClearStreamingOnly")
        .create();

    return locator;
}

/**
* Checks if the streaming endpoint is in the running state, if not, starts it.
* @param manager       The entry point of Azure Media resource management
* @param resourceGroup The name of the resource group within the Azure subscription
* @param accountName   The Media Services account name
* @param locatorName   The name of the StreamingLocator that was created
* @param streamingEndpoint     The streaming endpoint.
* @return              List of streaming urls
*/
private static List<String> getStreamingUrls(MediaManager manager, String resourceGroup, String accountName,
    String locatorName, StreamingEndpoint streamingEndpoint) {
    List<String> streamingUrls = new ArrayList<>();

    ListPathsResponse paths = manager.streamingLocators().listPathsAsync(resourceGroup, accountName, locatorName)
        .toBlocking().first();
    
    for (StreamingPath path: paths.streamingPaths()) {
        StringBuilder uriBuilder = new StringBuilder();
        uriBuilder.append("https://")
            .append(streamingEndpoint.hostName())
            .append("/")
            .append(path.paths().get(0));

        streamingUrls.add(uriBuilder.toString());
    }
    return streamingUrls;
}
```

Veja a amostra completa do código: [CodificaçãoComMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/src/main/java/sample/EncodingWithMESPredefinedPreset.java)

## <a name="net"></a>.NET

```csharp
/// <summary>
/// Creates a StreamingLocator for the specified asset and with the specified streaming policy name.
/// Once the StreamingLocator is created the output asset is available to clients for playback.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="assetName">The name of the output asset.</param>
/// <param name="locatorName">The StreamingLocator name (unique in this case).</param>
/// <returns>A task.</returns>
private static async Task<StreamingLocator> CreateStreamingLocatorAsync(
    IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string assetName,
    string locatorName)
{
    Console.WriteLine("Creating a streaming locator...");
    StreamingLocator locator = await client.StreamingLocators.CreateAsync(
        resourceGroup,
        accountName,
        locatorName,
        new StreamingLocator
        {
            AssetName = assetName,
            StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
        });

    return locator;
}

/// <summary>
/// Checks if the streaming endpoint is in the running state,
/// if not, starts it. Then, builds the streaming URLs.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="locatorName">The name of the StreamingLocator that was created.</param>
/// <param name="streamingEndpoint">The streaming endpoint.</param>
/// <returns>A task.</returns>
private static async Task<IList<string>> GetStreamingUrlsAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    String locatorName,
    StreamingEndpoint streamingEndpoint)
{
    IList<string> streamingUrls = new List<string>();

    ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

    foreach (StreamingPath path in paths.StreamingPaths)
    {
        UriBuilder uriBuilder = new UriBuilder
        {
            Scheme = "https",
            Host = streamingEndpoint.HostName,

            Path = path.Paths[0]
        };
        streamingUrls.Add(uriBuilder.ToString());
    }

    return streamingUrls;
}
```

Veja a amostra completa do código: [CodificaçãoComMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/Program.cs)

## <a name="see-also"></a>Confira também

* [Criar filtros com o .NET](filters-dynamic-manifest-dotnet-howto.md)
* [Criar filtros com REST](filters-dynamic-manifest-rest-howto.md)
* [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>Próximas etapas

[Proteja seu conteúdo com DRM](protect-with-drm.md).
