---
title: Como dimensionar seu ambiente - Azure Time Series Insights| Microsoft Docs
description: Aprenda a dimensionar seu ambiente Azure Time Series Insights usando o portal Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 9604f0c6eeb9d0ac4b7d07f1ab1ba03285fd60fe
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640076"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como dimensionar o ambiente do Time Series Insights

Este artigo descreve como alterar a capacidade do seu ambiente Time Series Insights usando o [portal Azure](https://portal.azure.com). A capacidade é o multiplicador aplicado à taxa de ingresso, à capacidade de armazenamento e ao custo associado com a SKU selecionada.

Você pode usar o Portal do Azure para aumentar ou diminuir a capacidade de um determinada SKU de preços.

No entanto, não é permitido alterar a SKU da camada de preços. Por exemplo, um ambiente com uma SKU de preço S1 não pode ser convertido em um S2 ou vice-versa.

## <a name="ga-limits"></a>Limites de GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Alterar a capacidade do seu ambiente

1. No Portal do Azure, localize e selecione seu ambiente Time Series Insights.

1. No menu do ambiente Time Series Insights, selecione **Configuração de armazenamento**.

   [![Configure sua capacidade de Insights da série do tempo](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Ajuste o controle deslizante **Capacidade** para selecionar a capacidade que atenda aos requisitos de suas taxas de entrada e capacidade de armazenamento. Observe que a **Taxa de entrada**, a **Capacidade de armazenamento** e o **Custo estimado** são atualizadas dinamicamente para mostrar o impacto da alteração.

   [![Configure seu ambiente usando o controle deslizante de capacidade](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Como alternativa, você pode digitar o número do multiplicador de capacidade na caixa de texto à direita do controle deslizante.

1. Selecione **Salvar** para dimensionar o ambiente. O indicador de progresso é exibido momentaneamente até que a alteração seja confirmada.

1. Verifique se a nova capacidade é [suficiente para evitar o estrangulamento](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Próximas etapas

- Para mais informações, veja [Noções básicas sobre retenção no Time Series Insights](time-series-insights-concepts-retention.md).

- Saiba mais sobre [a configuração da retenção de dados no Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Aprenda a [planejar seu ambiente.](time-series-insights-environment-planning.md)
