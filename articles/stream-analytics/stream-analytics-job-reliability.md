---
title: Evitar interrupções de serviço nos trabalhos do Azure Stream Analytics
description: Este artigo descreve as diretrizes para tornar o upgrade dos trabalhos do Stream Analytics mais resiliente.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425984"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantir a confiabilidade do trabalho do Stream Analytics durante atualizações do serviço

A capacidade de introduzir novos aprimoramentos e funcionalidades de serviço em um ritmo acelerado faz parte do serviço completamente gerenciado. Como resultado, o Stream Analytics pode ter uma implantação semanal (ou mais frequente) da atualização do serviço. Não importa a quantidade de testes realizados, ainda existe o risco de um trabalho atualmente em execução ser interrompido devido à introdução de um bug. Se você está executando trabalhos de missão crítica, esses riscos precisam ser evitados. Você pode reduzir esse risco seguindo o modelo de **[região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** do Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como as regiões emparelhadas do Azure podem resolver essa questão?

O Stream Analytics garante que os trabalhos em regiões emparelhadas sejam atualizados em lotes separados. Como resultado, há uma diferença de tempo suficiente entre as atualizações para identificar possíveis problemas e corrigi-los.

_Com exceção da Índia central_ (cuja região emparelhada, sul da Índia, não tem a presença do Stream Analytics), não ocorrerá a implantação de uma atualização simultânea do Stream Analytics em um conjunto de regiões emparelhadas. Implantações em várias regiões **no mesmo grupo de** podem ocorrer **ao mesmo tempo**.

O artigo em **[regiões de disponibilidade e emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tem as informações mais atualizadas sobre quais regiões são emparelhadas.

Recomenda-se implantar trabalhos idênticos em ambas as regiões pareadas. Você deve então [monitorar esses trabalhos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) para ser notificado quando algo inesperado acontecer. Se um desses trabalhos acabar em um [estado com falha](https://docs.microsoft.com/azure/stream-analytics/job-states) após uma atualização do serviço stream analytics, você pode entrar em contato com o suporte ao cliente para ajudar a identificar a causa raiz. Você também deve falhar sobre qualquer consumidor a jusante para a produção de emprego saudável.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
