---
title: Problemas ao usar extensões de VM em sistemas de máquinas virtuais Linux do Azure habilitadas para Python 3
description: Saiba mais sobre como usar extensões de VM em sistemas Linux habilitados para Python 3
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: 944abc62f25473ea52836af7dc1fdcd1e16d9269
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120776"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Problemas ao usar extensões de VM em sistemas de máquinas virtuais Linux do Azure habilitadas para Python 3

> [!NOTE]
> A Microsoft incentiva os usuários a adotar o **Python 3. x** em seus sistemas, a menos que sua carga de trabalho exija suporte a **Python 2. x** . Exemplos desse requisito podem incluir scripts de administração herdados ou extensões como **Azure Disk Encryption** e **Azure monitor**.
>
> Antes de instalar o **Python 2. x** em produção, considere a questão do suporte a longo prazo do Python 2. x, particularmente sua capacidade de receber atualizações de segurança. Como produtos, incluindo parte da extensão mencionada, atualização com suporte a **Python 3,8** , você deve descontinuar o uso do Python 2. x.

Algumas distribuições do Linux passaram para o Python 3,8 e removidam `/usr/bin/python` o ponto de entrada herdado para o Python completamente. Essa transição afeta a implantação automatizada e pronta para uso de determinadas extensões de VM (máquina virtual) com as seguintes condições:

- Extensões que ainda estão em transição para o suporte do Python 3. x
- Extensões que usam o ponto `/usr/bin/python` de entrada herdado

Os usuários de distribuição do Linux que fizeram a transição para o **Python 3. x** devem garantir que o ponto de entrada herdado `/usr/bin/python` exista antes de tentar implantar essas extensões em suas VMs. Caso contrário, a implantação da extensão poderá falhar. 

- As distribuições do Linux endossadas que são afetadas incluem o **Ubuntu Server 20, 4 LTS** e o **Ubuntu pro 20, 4 LTS**.

- As extensões de VM afetadas incluem **Azure Disk Encryption**, **log Analytics**, **acesso à VM** (usado para redefinição de senha) e **diagnósticos de convidado** (usados para contadores de desempenho adicionais).

As atualizações in-loco, como a atualização do **ubuntu 18, 4 LTS** para o **Ubuntu 20, 4 LTS**, devem manter o `/usr/bin/python` symlink e permanecer inalteradas.

## <a name="resolution"></a>Resolução

Considere as seguintes recomendações gerais antes de implantar extensões nos cenários conhecidos afetados descritos anteriormente no Resumo:

1.  Antes de implantar a extensão, reinstale `/usr/bin/python` o symlink usando o método fornecido pelo fornecedor de distribuição do Linux.

    - Por exemplo, para **Python 2,7**, use:`sudo apt update && sudo apt install python-is-python2`

2.  Se você já tiver implantado uma instância que exibe esse problema, use a funcionalidade **executar comando** na **folha da VM** para executar os comandos mencionados acima. A própria extensão de comando de execução não é afetada pela transição para Python 3,8.

3.  Se você estiver implantando uma nova instância e precisar definir uma extensão no tempo de provisionamento, use dados de usuário de **inicialização de nuvem** para instalar os pacotes mencionados acima.

    Por exemplo, para Python 2,7:

    ```
    # create cloud-init config
    cat > cloudinitConfig.json <<EOF
    #cloud-config
    package_update: true
    
    runcmd:
    - sudo apt update
    - sudo apt install python-is-python2 
    EOF
    
    # create VM
    az vm create \
        --resource-group <resourceGroupName> \
        --name <vmName> \
        --image <Ubuntu 20.04 Image URN> \
        --admin-username azadmin \
        --ssh-key-value "<sshPubKey>" \
        --custom-data ./cloudinitConfig.json
    ```

4.  Se os administradores de política da sua organização determinarem que as extensões não devem ser implantadas em VMs, você poderá desabilitar o suporte de extensão no momento do provisionamento:

    - API REST

      Para desabilitar e habilitar extensões quando você pode implantar uma VM com esta propriedade:

      ```
        "osProfile": {
          "allowExtensionOperations": false
        },
      ```

## <a name="next-steps"></a>Próximas etapas

Consulte [outras alterações do sistema base desde 18, 4 LTS-Python 3 por padrão](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) para obter informações adicionais.
