---
title: Configuração de serviço - QnA Maker
description: Entenda como e onde configurar recursos.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 3be32d1778604121c2acac88415cbfbc4bdbca3d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804253"
---
# <a name="service-configuration"></a>Configuração de serviço

O QnA Maker usa vários recursos (serviços) do Azure, incluindo Pesquisa Cognitiva, Serviço de Aplicativo, Plano de Serviço de Aplicativo e Insights de Aplicativos.

Todas as personalizações para essas configurações suportadas pelo QnA Maker estão listadas abaixo.

## <a name="app-service"></a>Serviço de Aplicativo

O QnA Maker usa o Serviço de Aplicativo para fornecer o tempo de execução da consulta usado pela [API gerar Resposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Essas configurações estão disponíveis no portal Azure, para o Serviço de Aplicativos. As configurações estão disponíveis selecionando **Configurações**e **Configuração**.

Você pode definir uma configuração individual através da lista Configurações do aplicativo ou modificar várias configurações selecionando **edição avançada**.

|Recurso|Configuração|
|--|--|
|AzureSearchAdminKey|Pesquisa Cognitiva - usada para armazenamento de pares QnA e Ranker #1|
|AzureSearchName|Pesquisa Cognitiva - usada para armazenamento de pares QnA e Ranker #1|
|Resposta padrão|Texto de resposta quando nenhuma correspondência é encontrada|
|UserAppInsightsAppId|Registro de bate-papo e telemetria|
|UserAppInsightsKey|Registro de bate-papo e telemetria|
|UserAppInsightsNome|Registro de bate-papo e telemetria|

Saiba [como adicionar alterar seu serviço de Busca Cognitiva](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) ao seu serviço.

Você precisa **reiniciar** o serviço a partir da página **Visão Geral** do portal Azure, uma vez que você terminar de fazer alterações.

## <a name="qna-maker-service"></a>Serviço QnA Maker

O serviço QnA Maker fornece configuração para que os seguintes usuários colaborem em um único serviço QnA Maker e em todas as suas bases de conhecimento.

[Aprenda a adicionar colaboradores](./how-to/collaborate-knowledge-base.md) ao seu serviço.

## <a name="application-insights"></a>Application Insights

O Application Insights não tem configurações específicas do QnA Maker.

## <a name="app-service-plan"></a>Plano do Serviço de Aplicativo

O Plano de Serviço do Aplicativo não tem configurações específicas do QnA Maker.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [formatos](reference-document-format-guidelines.md) de documentos e URLs que você deseja importar para uma base de conhecimento.