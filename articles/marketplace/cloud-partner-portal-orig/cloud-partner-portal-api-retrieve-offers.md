---
title: Retrieve oferece API | Mercado Azure
description: API recupera uma lista resumida de ofertas em um namespace do editor.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255950"
---
<a name="retrieve-offers"></a>Recuperar ofertas
===============

> [!NOTE]
> As APIs do Portal de Parceiros em Nuvem são integradas ao Partner Center e continuarão a funcionar depois que suas ofertas forem migradas para o Partner Center. A integração introduz pequenas mudanças. Revise as alterações listadas na [API do Portal do Parceiro na Nuvem](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que seu código continue funcionando após a migração para o Partner Center.

Recupera uma lista resumida de ofertas em um namespace do editor.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros de URI
--------------

| **Nome**         |  **Descrição**                         |  **Tipo de dados** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identificador do editor, por exemplo `contoso` |   String    |
|  api-version     | Última versão da API                    |    Data        |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |         **Valor**       |
|  --------------- |       ----------------  |
|  Tipo de conteúdo    | `application/json`      |
|  Autorização   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Resposta

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**       |       **Descrição**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifica o tipo de oferta                                                                                           |
|  publisherId    | Identificador que identifica exclusivamente o editor                                                                      |
|  status         | Status da oferta. Para a lista de valores possíveis, consulte [Status da oferta](#offer-status) abaixo.                         |
|  id             | GUID que identifica exclusivamente a oferta no namespace do editor.                                                    |
|  version        | Versão atual da oferta. A propriedade de versão não pode ser modificada pelo cliente. Isso é incrementado após cada publicação. |
|  definição     | Contém uma visão resumida da definição real da carga de trabalho. Para obter uma definição detalhada, use a [API Recuperar oferta específica](./cloud-partner-portal-api-retrieve-specific-offer.md). |
|  changedTime    | Hora em UTC quando a oferta foi modificada pela última vez                                                                              |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código**  |  **Descrição**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - A solicitação foi processada com êxito e todas as ofertas do editor foram devolvidas ao cliente.  |
|  400      | `Bad/Malformed request` - O corpo da resposta de erro pode conter mais informações.                                    |
|  403      | `Forbidden` - O cliente não tem acesso ao namespace especificado.                                          |
|  404      | `Not found` - A entidade especificada não existe.                                                                 |
|  |  |


### <a name="offer-status"></a>Status da oferta

|  **Nome**                    | **Descrição**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | A oferta nunca foi publicada.                  |
|  NotStarted                  | A oferta é nova e não iniciada.                 |
|  WaitingForPublisherReview   | A oferta aguarda aprovação do editor.         |
|  Executando                     | O envio da oferta está sendo processado.             |
|  Teve êxito                   | O envio da oferta concluiu o processamento.       |
|  Canceled                    | O envio da oferta foi cancelado.                   |
|  Falhou                      | O envio da oferta falhou.                         |
|  |  |
