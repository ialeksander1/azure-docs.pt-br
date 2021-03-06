---
title: Amostras do PowerShell de máquina virtual do Azure
description: Amostras do PowerShell de máquina virtual do Azure
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 8d7db5fe88890b7f807263e50757e637ad808eb1
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759312"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Amostras do PowerShell de máquina virtual do Azure

A tabela a seguir inclui links para amostras de scripts do PowerShell que criam e gerenciam máquinas virtuais Linux.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um grupo de recursos, a máquina virtual e todos os recursos relacionados.|
| [Criar uma máquina virtual com o Docker habilitado](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, configura essa VM como um host Docker e executa um contêiner NGINX. |
| [Criar uma máquina virtual e executar o script de configuração](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e usa a extensão de Script do Azure personalizado para instalar o NGINX. |
| [Criar uma máquina virtual com WordPress instalado](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e usa a extensão de Script do Azure personalizado para instalar o WordPress. |
| [Criar VM por meio de um disco do sistema operacional gerenciado](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual anexando um disco gerenciado existente como disco do sistema operacional. |
| [Criar uma VM de um instantâneo](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo criando primeiro um disco gerenciado a partir do instantâneo e, em seguida, anexando o novo disco gerenciado como disco do sistema operacional. |
|**Gerenciar armazenamento**||
| [Crie um disco gerenciado a partir de um VHD na mesma ou em uma assinatura diferente](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerenciado a partir de um VHD especializado como um disco do sistema operacional ou de um VHD de dados como um disco de dados, na mesma assinatura ou em uma assinatura diferente.  |
| [Criar um disco gerenciado com base em um instantâneo](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerenciado com base em um instantâneo. |
| [Exportar um instantâneo como um VHD para uma conta de armazenamento](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta um instantâneo gerenciado como um VHD para uma conta de armazenamento em uma região diferente. |
| [Exportar o VHD de um disco gerenciado para uma conta de armazenamento](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta o VHD subjacente de um disco gerenciado para uma conta de armazenamento em uma região diferente. |
| [Criar um instantâneo de um VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um instantâneo de um VHD e, em seguida, usa esse instantâneo para criar vários discos gerenciados idênticos rapidamente.  |
| [Copie um instantâneo para a mesma ou para uma assinatura diferente](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia um instantâneo para a mesma ou uma assinatura diferente que está na mesma região do instantâneo pai. |
|**Monitorar máquinas virtuais**||
| [Monitorar uma VM com logs do Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, instala o agente Log Analytics e a VM em um espaço de trabalho do Log Analytics.  |
| [Copie um disco gerenciado para a mesma ou para uma assinatura diferente](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia um disco gerenciado para a mesma ou uma assinatura diferente que está na mesma região que o disco gerenciado pai.
| [Colete detalhes sobre todas as VMs em uma assinatura com o PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um csv que contém o nome vm, nome do grupo de recursos, região, rede virtual, sub-rede, endereço IP privado, tipo de OS e endereço IP público das VMs na assinatura fornecida.
| | |
