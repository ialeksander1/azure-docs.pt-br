---
title: Configure um túnel VPN sempre ligado
titleSuffix: Azure VPN Gateway
description: Etapas para configurar o túnel Always On VPN para VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371699"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurar um túnel de dispositivo VPN Always On

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurar o gateway

Configure o gateway VPN para usar ikev2 e autenticação baseada em certificadousando o artigo [de conexão VPN ponto a ponto do site.](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

## <a name="configure-the-device-tunnel"></a>Configure o túnel do dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover o perfil, execute o seguinte comando:

![Limpeza](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Próximas etapas

Para solução de problemas, consulte [problemas de conexão ponto a ponto do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
