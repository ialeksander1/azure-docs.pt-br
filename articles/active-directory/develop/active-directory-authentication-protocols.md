---
title: Protocolos de autenticação de plataforma sitivas da Microsoft
description: Uma visão geral dos protocolos de autenticação suportados pela plataforma de identidade Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884674"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protocolos de autenticação de plataforma sitivas da Microsoft

A plataforma de identidade da Microsoft suporta vários dos protocolos de autenticação e autorização mais utilizados. Os tópicos desta seção descrevem os protocolos suportados e sua implementação na plataforma de identidade Microsoft. Os tópicos incluem uma revisão dos tipos de declarações com suporte, uma introdução ao uso de metadados de federação, documentação detalhada de referência dos protocolos OAuth 2.0. e SAML 2.0 e uma seção de solução de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Protocolos de autenticação artigos e referência

* [Informações importantes sobre a assinatura de rolagem de chaves na plataforma de identidade da Microsoft](active-directory-signing-key-rollover.md) – Conheça a cadência de rolagem de chaves da plataforma de identidade da Microsoft, as alterações que você pode fazer para atualizar a chave automaticamente e a discussão sobre como atualizar os cenários de aplicativos mais comuns.
* [Tipos de token e reclamação suportados](id-tokens.md) - Conheça as reivindicações nos tokens que a plataforma de identidade da Microsoft emite.
* [OAuth 2.0 na plataforma de identidade Microsoft](v2-oauth2-auth-code-flow.md) - Conheça a implementação do OAuth 2.0 na plataforma de identidade Microsoft.
* [OpenID Connect 1.0](v2-protocols-oidc.md) - Saiba como usar o OAuth 2.0, um protocolo de autorização para autenticação.
* [Serviço para Chamadas de Serviço com Credenciais do Cliente](v2-oauth2-client-creds-grant-flow.md) – Saiba como utilizar o fluxo de concessão de credenciais de cliente OAuth 2.0 para chamadas de serviços.
* [Serviço para Chamadas de Serviço com Fluxo Em Nome De](v2-oauth2-on-behalf-of-flow.md) – Saiba como utilizar o fluxo de Em Nome De OAuth 2.0 para chamadas de serviços.
* [Referência de protocolo SAML](active-directory-saml-protocol-reference.md) - Conheça os perfis SAML de inscrição única e de saque único da plataforma de identidade Microsoft.

## <a name="see-also"></a>Confira também

* [Visão geral da plataforma de identidade da Microsoft](v2-overview.md)
* [Exemplos de código do Active Directory](sample-v2-code.md)
