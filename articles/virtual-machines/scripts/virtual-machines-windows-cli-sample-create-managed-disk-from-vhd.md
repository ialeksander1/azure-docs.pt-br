---
title: 'Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura: amostra da CLI'
description: Amostra de script da CLI do Azure – Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: b0ce935e03a6202ac444987cbecf853ee6717d3b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459511"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura com a CLI

Este script cria um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura. Use esse script para importar um VHD especializado (não generalizado/do Sysprep) para um disco do sistema operacional gerenciado para criar uma máquina virtual. Ou, use-o para importar um VHD de dados para um disco de dados gerenciados.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um VHD. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Cria um disco gerenciado usando o URI de um arquivo VHD em uma conta de armazenamento na mesma assinatura |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Windows do Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
