---
title: Veja as métricas de uso da API do Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a visualizar as métricas para as chamadas da API do Microsoft Azure Maps no portal Azure.
author: philmea
ms.author: philmea
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 0eb117af712b3b1f63a3f99c96cba9775f8e3996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335158"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Exibir as métricas de uso da API do Azure Mapas

Este artigo mostra como visualizar as métricas de uso da API, para sua conta do Azure Maps, no [portal Azure](https://portal.azure.com). As métricas são mostradas em um formato gráfico conveniente ao longo de uma duração de tempo personalizáveis.

## <a name="view-metric-snapshot"></a>Exibir instantâneo de métrica

Você pode ver algumas métricas comuns sobre a página de **visão geral** de sua conta de mapas. Atualmente, ele mostra *Total de Solicitações*, *Total de Erros*, e *Disponibilidade* em um período de tempo selecionável.

![Visão geral das métricas de uso do Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Continue para a próxima seção se você precisar personalizar esses gráficos para sua análise específica.

## <a name="view-detailed-metrics"></a>Exibir métricas detalhadas

1. Entre em sua assinatura do Azure no [portal](https://portal.azure.com).

2. Clique o **Todos os recursos** no lado esquerdo do item de menu e navegue até a sua *conta do Azure Mapas*.

3. Quando sua conta de Mapas for aberta, clique no menu **Métricas** à esquerda.

4. No painel **Métricas,** escolha uma das seguintes opções:

   1. **Disponibilidade** - que mostra a *Média* da disponibilidade da API durante um período de tempo.
   2. **Uso** -que mostra como o uso *contagem* para sua conta.

      ![Painel de métricas de uso do Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Em seguida, você pode selecionar *Intervalo de tempo* clicando em **Últimas 24 horas (Automático)**. Por padrão, o intervalo de tempo é definido como 24 horas. Depois de clicar, você verá todos os intervalos de tempo selecionáveis. Você pode selecionar *Granularidade de tempo* e optar por mostrar a hora como *local* ou *GMT* no mesmo menu suspenso. Clique em **Aplicar**.

    ![Intervalo de tempo de métricas do Azure Maps](media/how-to-view-api-usage/time-range.png)

6. Depois de adicionar sua métrica, você pode **adicionar filtro** das propriedades relevantes a essa métrica. Em seguida, selecione o valor da propriedade que você deseja ver refletido no gráfico.

    ![Filtro de métricas de uso do Azure Maps](media/how-to-view-api-usage/filter.png)

7. Você também pode **Aplicar a divisão** para sua métrica com base em sua propriedade de métrica selecionada. Ele permite que o gráfico seja dividido em vários gráficos, para cada valor dessa propriedade. Na figura a seguir, a cor de cada gráfico corresponde ao valor da propriedade mostrado na parte inferior do gráfico.

    ![Métricas de uso do Azure Maps](media/how-to-view-api-usage/splitting.png)

8. Você também pode observar várias métricas no mesmo gráfico, simplesmente clicando no botão **Adicionar métrica** na parte superior.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as APIs do Azure Maps para as quais você deseja acompanhar o uso:
> [!div class="nextstepaction"] 
> [Mapas do Azure Web SDK How-To](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Mapas do Azure Android SDK How-To](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Documentação API REST do Azure Mapas](https://docs.microsoft.com/rest/api/maps)
