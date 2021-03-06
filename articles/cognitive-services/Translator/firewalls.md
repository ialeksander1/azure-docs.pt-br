---
title: Traduzir atrás de firewalls – API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: A API de texto do Tradutor de Serviços Cognitivos do Azure pode ser traduzida atrás de firewalls usando o nome de domínio ou a filtragem IP.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837399"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Como traduzir atrás de firewalls de IP com a API de Tradução de Texto

A API de Tradução de Texto pode ser traduzida por trás de firewalls usando filtragem de IP ou nome de domínio. A filtragem de nome de domínio é o método preferencial. **Não é recomendável** executar o Microsoft Translator por trás de um firewall filtrado por IP. A configuração é suscetível a interrupção no futuro sem aviso prévio.

## <a name="translator-ip-addresses"></a>Endereços IP do Tradutor
Os endereços IP para api.cognitive.microsofttranslator.com - Microsoft Translator Text API a partir de 21 de agosto de 2019:

* **Ásia-Pacífico:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **América do Norte:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Traduzir atrás de firewalls de IP na chamada à API do Tradutor](reference/v3-0-translate.md)
