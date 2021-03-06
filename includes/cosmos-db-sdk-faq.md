---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67171673"
---
**1. Como os clientes serão notificados sobre a desativação do SDK?**

A Microsoft enviará notificações com 12 meses de antecedência informando sobre o fim do suporte para o SDK desativado, a fim de facilitar uma transição suave para um SDK com suporte. Além disso, os clientes serão notificados por meio de vários canais de comunicação, como o Portal de Gerenciamento do Azure, a Central de Desenvolvedores, postagens de blog e a comunicação direta para os administradores de serviço indicados.

**2. os clientes podem criar aplicativos usando um SDK de Azure Cosmos DB "para serem" desativados durante o período de 12 meses?** 

Sim, os clientes terão acesso total para criar, implantar e modificar os aplicativos usando o SDK do Azure Cosmos DB prestes a ser desativado durante o período de 12 meses. Durante o período de carência de 12 meses, os clientes serão aconselhados a migrar para uma versão mais recente com suporte do SDK do Azure Cosmos DB, como apropriado.

**3. os clientes podem criar e modificar aplicativos usando um SDK de Azure Cosmos DB desativado após o período de notificação de 12 meses?**

Após o período de notificação de 12 meses, o SDK será desativado. O acesso de aplicativos ao Azure Cosmos DB com um SDK desativado não será permitido pela plataforma do Azure Cosmos DB. Além disso, a Microsoft não fornecerá suporte ao cliente para o SDK desativado.

**4. o que acontece com os aplicativos em execução pelo cliente que estão usando a versão sem suporte do SDK do Azure Cosmos DB?**

As tentativas de conexão com o serviço do Azure Cosmos DB com uma versão de SDK desativada serão recusadas. 

**5. novos recursos e funcionalidades serão aplicados a todos os SDKs não desativados?**

Os novos recursos e funcionalidades serão adicionadas apenas para as novas versões. Se estiver usando uma versão anterior mas não-obsoleta do SDK, suas solicitações para o Azure Cosmos DB continuarão funcionando como antes, mas você não terá acesso aos novos recursos.  

**6. o que devo fazer se não for possível atualizar meu aplicativo antes de uma data de fechamento?**

Recomendamos atualizar o mais rápido possível para o SDK mais recente. Quando um SDK é marcado para desativação, você tem 12 meses para atualizar o aplicativo. Caso você não possa concluir a atualização do aplicativo nesse prazo por algum motivo, contate a [Equipe do BD Cosmos](mailto:askcosmosdb@microsoft.com) e solicite ajuda antes da data de fechamento.

