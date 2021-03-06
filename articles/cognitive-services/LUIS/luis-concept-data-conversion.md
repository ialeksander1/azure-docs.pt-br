---
title: Conversão de dados-LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como as expressões podem ser alteradas antes das previsões no Reconhecimento vocal (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b2455df87c8eae1a48cb6c8b1381dad85d304bf4
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099233"
---
# <a name="convert-data-format-of-utterances"></a>Converter o formato de dados de declarações
O LUIS fornece as seguintes conversões de um usuário expressão antes da previsão "

* Fala para texto usando o serviço de [fala de serviços cognitivas](../Speech-Service/overview.md) .

## <a name="speech-to-text"></a>Conversão de fala em texto

A fala em texto é fornecida como uma integração com o LUIS.

### <a name="intent-conversion-concepts"></a>Conceitos de conversão de intenção
Conversão de fala em texto no LUIS permite que você envie expressões faladas a um ponto de extremidade e receba uma resposta de previsão LUIS. O processo é uma integração entre o serviço de [Fala](https://docs.microsoft.com/azure/cognitive-services/Speech) com LUIS. Saiba mais sobre a conversão de fala em intenção com um [tutorial](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Requisitos de chave
Você não precisa criar uma chave de **API de Fala do Bing** para esta integração. Uma chave de **Reconhecimento vocal** criada no portal do Azure funciona para este exercício. Não use a chave de inicialização do LUIS.

### <a name="pricing-tier"></a>Camada de preços
Essa integração usa um modelo de [preço](luis-limits.md#key-limits) diferente dos tipos de preço comuns do Reconhecimento vocal.

### <a name="quota-usage"></a>Uso da cota
Consulte [limites de chave](luis-limits.md#key-limits) para obter informações.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Extraindo dados](luis-concept-data-extraction.md)

