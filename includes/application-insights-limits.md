---
title: incluir arquivo
description: incluir arquivo
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 14e2bd4af2616e9dd33fe8267de132ab6c0f1cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "69982626"
---
Existem alguns limites no número de métricas e eventos por aplicação, ou seja, por chave de instrumentação. Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

| Recurso | Limite padrão | Observação
| --- | --- | --- |
| Total de dados por dia | 100 GB | Você pode reduzir os dados ao definir um limite. Caso precise de mais dados, é possível aumentar o limite até 1.000 GB. Para capacidades superiores a 1.000 AIDataCap@microsoft.comGB, envie e-mail para .
| Limitação | 32.000 eventos/segundo | O limite é medido em um minuto.
| Retenção de dados | [30 - 730 dias](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)  | Este recurso destina-se a [Pesquisa](../articles/azure-monitor/app/diagnostic-search.md), [Análise](../articles/azure-monitor/app/analytics.md) e [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| Retenção de resultados detalhados do [Teste de disponibilidade de várias etapas](../articles/azure-monitor/app/availability-multistep.md) | 90 dias | Esse recurso fornece resultados detalhados de cada etapa.
| Tamanho máximo do item de telemetria | 64 kB |
| Itens máximos de telemetria por lote | 64 k |
| Tamanho dos nomes de propriedade e métrica | 150 | Veja [esquemas de tipo](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Tamanho da cadeia de caracteres do valor da propriedade | 8.192  | Veja [esquemas de tipo](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Comprimento da mensagem de rastreamento e de exceção | 32.768  | Veja [esquemas de tipo](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Contagem de [testes de disponibilidade](../articles/azure-monitor/app/monitor-web-app-availability.md) por aplicativo | 100 |
| Retenção de dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) | 5 dias |
| Dados do [criador de perfil](../articles/azure-monitor/app/profiler.md) enviados por dia | 10 GB |

Para obter mais informações, consulte [sobre preços e cotas no Application Insights](../articles/azure-monitor/app/pricing.md).