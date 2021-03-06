---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421985"
---
## <a name="create-a-speech-resource"></a>Criar um recurso de fala

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Clique em Criar recurso [ **de discurso** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer localização próxima e disponível|
    |Camada de preços|`F0` – o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponível|

1. Clique em **Criar** e aguarde até que o recurso seja criado. Depois de criado, navegue até a página de recursos
1. Coletar configurado `endpoint` e uma chave de API:

    |Guia de recursos no Portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto final. Parece com`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**simétricas**|Chave de API|Copie uma das duas chaves. É uma seqüência de caracteres alfanuméricos de `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`32 sem espaços ou traços, .|
