---
title: Solução de problemas de implantação de VM do Linux | Microsoft Docs
description: Solucionar problemas de implantação do Resource Manager ao criar uma nova máquina virtual Linux no Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 98c3a6b14230e30ccbb103be741595696a20c236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981413"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Solucionar problemas de implantação do Resource Manager com a criação de uma nova máquina virtual Linux no Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Para outros problemas de implantação de VM e perguntas, confira [Solucionar problemas de implantação de máquina virtual Linux no Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Coletar logs de atividades
Para iniciar a solução de problemas, colete os logs de atividades para identificar o erro associado ao problema. Os links a seguir contêm informações detalhadas sobre o processo a ser seguido.

[Exibir operações de implantação](../../azure-resource-manager/templates/deployment-history.md)

[Exibir logs de atividades para gerenciar recursos do Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** se o sistema operacional for Linux generalizado e ele for carregado e/ou capturado com a configuração generalizada, não haverá erros. Da mesma forma, se o sistema operacional for Linux especializado e ele for carregado e/ou capturado com a configuração especializada, não haverá erros.

**Erros de upload:**

**N<sup>1</sup>:** se o sistema operacional for Linux generalizado e ele for carregado como especializado, você receberá um erro de tempo limite de provisionamento, pois a VM estará paralisada no estágio de provisionamento.

**N<sup>2</sup>:** se o sistema operacional for Linux especializado e ele for carregado como generalizado, você receberá um erro de falha no provisionamento, pois a nova VM estará em execução com o nome do computador, nome de usuário e senha originais.

**Solução:**

Para resolver esses dois erros, carregue o VHD original, disponível no local, com a mesma configuração do SISTEMA OPERACIONAL (generalizado/especializado). Para carregar como generalizado, lembre-se de executar -deprovision primeiro.

**Erros de captura:**

**N<sup>3</sup>:** se o sistema operacional for Linux generalizado e ele for capturado como especializado, você receberá um erro de tempo limite de provisionamento, pois a VM original não será utilizável, já que estará marcada como generalizada.

**N<sup>4</sup>:** se o sistema operacional for Linux especializado e ele for capturado como generalizado, você receberá um erro de falha no provisionamento, pois a nova VM estará em execução com o nome do computador, nome de usuário e senha originais. Além disso, a VM original não será utilizável, já que estará marcada como especializada.

**Solução:**

Para resolver ambos os erros, exclua a imagem atual do portal e [recapture-a dos VHDs atuais](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) com a mesma configuração usada para o sistema operacional (generalizado/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: imagem personalizada/da galeria/do Marketplace; falha de alocação
Esse erro ocorre em situações nas quais a nova solicitação de VM é fixada em um cluster que não tem suporte para o tamanho da VM sendo solicitado ou não tem espaço livre disponível para acomodar a solicitação.

**Causa 1:** o cluster não dá suporte ao tamanho de VM solicitado.

**Resolução 1:**

* Repita a solicitação com um tamanho de VM menor.
* Se o tamanho da VM solicitada não puder ser alterado:
  * Pare todas as VMs no conjunto de disponibilidade.
    Clique **em Grupos de** > *recursos* > Recursos**Recursos** > sua disponibilidade*definir* > **Máquinas** > Virtuais sua máquina > *virtual***Stop**.
  * Depois de parar todas as máquinas virtuais, crie a nova VM no tamanho desejado.
  * Inicie a nova VM primeiro e selecione cada uma das VMs paradas e clique **em Iniciar**.

**Causa 2:** o cluster não tem recursos livres.

**Resolução 2:**

* Repita a solicitação mais tarde.
* Se a nova VM puder ser parte de um conjunto de disponibilidade diferente
  * Crie uma nova VM em um conjunto de disponibilidade diferente (na mesma região).
  * Adicione a nova VM à mesma rede virtual.

## <a name="next-steps"></a>Próximas etapas
Se você encontrar problemas ao iniciar uma VM do Linux parada ou redimensionar uma VM do Linux existente no Azure, consulte [Solucionar problemas de implantação do Resource Manager ao reinicializar ou redimensionar uma máquina virtual Linux existente no Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

