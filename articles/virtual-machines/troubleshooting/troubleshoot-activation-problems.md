---
title: Solução de problemas de ativação de máquina virtual do Windows no Azure | Microsoft Docs
description: Fornece as etapas de solução de problemas para corrigir problemas de ativação de máquina virtual do Windows no Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: fd38f646b8dfc58839cd2645f7fadf7332693854
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605998"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Solucionar problemas de ativação de máquina virtual do Windows Azure

Se você tiver problemas durante a ativação da máquina virtual do Windows Azure (VM) que é criada a partir de uma imagem personalizada, você pode usar as informações fornecidas neste documento para solucionar o problema. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Noções básicas sobre pontos de extremidade do Azure KMS para ativação do Windows de Máquinas Virtuais do Azure

O Azure usa diferentes pontos finais para ativação do KMS (Key Management Services), dependendo da região de nuvem onde a VM reside. Ao usar este guia de solução de problemas, use o ponto de extremidade apropriado do KMS que se aplica à região.

* Regiões de nuvem pública do Azure: kms.core.windows.net:1688
* Azure China 21Vianet national cloud regions: kms.core.chinacloudapi.cn:1688
* Regiões de nuvem nacional do Azure na Alemanha: kms.core.cloudapi.de:1688
* Regiões de nuvem nacional do Azure no Governo dos EUA: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Sintoma

Quando você tentar ativar uma VM do Windows Azure, você recebe um erro mensagem se parece com o exemplo a seguir:

**Erro: 0xC004F074 O Software LicensingService informou que o computador não poderia ser ativado. Nenhum Key ManagementService (KMS) poderia ser contatado. Consulte o Registro de Eventos do Aplicativo para obter informações adicionais.**

## <a name="cause"></a>Causa

Em geral, problemas de ativação da VM do Azure ocorrem se a VM do Windows não está configurada usando a chave de instalação de cliente do KMS adequada, ou a VM do Windows tem um problema de conectividade com o serviço KMS do Azure (kms.core.windows.net, porta 1688). 

## <a name="solution"></a>Solução

>[!NOTE]
>Se você estiver usando uma VPN local-para-site e um túnel forçado, consulte [Use rotas personalizadas do Azure para permitir a ativação do KMS com tunelamento forçado](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling). 
>
>Se você estiver usando o ExpressRoute e tiver uma rota padrão publicada, [veja Posso bloquear a conectividade da Internet com redes virtuais conectadas aos circuitos ExpressRoute?](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Passo 1 Configure a chave de configuração do cliente KMS apropriada

Para a VM criada a partir de uma imagem personalizada, você deve configurar a chave de configuração do cliente KMS apropriada para a VM.

1. Execute **slmgr.vbs /dlv** em um prompt de comando elevado. Verifique o valor de descrição na saída e, em seguida, determine se ele foi criado a partir da mídia de licença de varejo (canal RETAIL) ou de volume (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Se **slmgr.vbs /dlv** mostrar o canal RETAIL, execute os seguintes comandos para definir a [chave de instalação de cliente KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) para a versão do Windows Server que está sendo usada e forçá-la para efetuar a ativação novamente: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Por exemplo, para o Datacenter do Windows Server 2016, você executaria o seguinte comando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Etapa 2 Verificar a conectividade entre o serviço KMS do Azure e de VM

1. Baixe e extraia a ferramenta [PSping](https://docs.microsoft.com/sysinternals/downloads/psping) em uma pasta local na VM que não ative. 

2. Vá para Iniciar, pesquise no Windows PowerShell, clique com o botão direito no Windows PowerShell e, em seguida, selecione Executar como administrador.

3. Certifique-se de que a VM está configurada para usar o servidor correto do KMS do Azure. Para fazer isso, execute o seguinte comando:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    O comando deve retornar: Nome da máquina do Serviço de Gerenciamento de Chaves definido como kms.core.windows.net:1688 com sucesso.

4. Verifique usando Psping que você tem conectividade ao servidor KMS. Alterne para a pasta onde você extraiu o download de Pstools.zip e, em seguida, execute o seguinte:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   Na penúltima linha da saída, certifique-se de que você vê: Sent = 4, Received = 4, Lost = 0 (0% de perda).

   Se Lost for maior que 0 (zero), a VM não tem conectividade com o servidor KMS. Nessa situação, se a VM estiver em uma rede virtual e tiver um servidor DNS especificado, certifique-se de que o servidor DNS é capaz de resolver kms.core.windows.net. Ou então, altere o servidor DNS para um que resolva kms.core.windows.net.

   Observe que se você remover todos os servidores DNS de uma rede virtual, as VMs usarão o serviço interno de DNS do Azure. Esse serviço pode resolver kms.core.windows.net.
  
    Certifique-se também de que o tráfego de rede de saída para o ponto final kms com a porta 1688 não seja bloqueado pelo firewall na VM.

5. Verifique usando [o Network Watcher Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) que o próximo tipo de salto da VM em questão para o IP de destino 23.102.135.246 (para kms.core.windows.net) ou o IP do ponto final kms apropriado que se aplica à sua região é a **Internet**.  Se o resultado for VirtualAppliance ou VirtualNetworkGateway, é provável que exista uma rota padrão.  Entre em contato com o seu adminstrator de rede e trabalhe com eles para determinar o curso correto de ação.  Essa pode ser uma [rota personalizada](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) se essa solução for consistente com as políticas da sua organização.

6. Depois de verificar a conectividade com sucesso para kms.core.windows.net, execute o seguinte comando no prompt do Windows PowerShell com privilégios elevados. Esse comando tenta a ativação várias vezes.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Uma ativação bem-sucedida retorna informações semelhantes à seguinte:
    
    **Ativando windows(R), edição ServerDatacenter (12345678-1234-1234-1234-1234-12345678) ...  Produto ativado com sucesso.**

## <a name="faq"></a>Perguntas frequentes 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Criei o Windows Server 2016 a partir do Azure Marketplace. É necessário configurar a chave KMS para ativação do Windows Server 2016? 

 
Não. A imagem no Azure Marketplace já possui a chave de instalação de cliente KMS adequada configurada. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>A ativação do Windows funciona da mesma maneira independentemente se a VM estiver usando o Benefício de Uso Híbrido do Azure (HUB) ou não? 

 
Sim. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>O que acontece se o período de ativação do Windows expirar? 

 
Quando o período de cortesia expirar e o Windows ainda não está ativado, o Windows Server 2008 R2 e versões posteriores do Windows mostrarão notificações adicionais sobre a ativação. O papel de parede da área de trabalho permanece preto e o Windows Update instalará somente as atualizações de segurança e críticas, mas não as atualizações opcionais. Consulte a seção de Notificações na parte inferior da página [Condições de Licenciamento](https://technet.microsoft.com/library/ff793403.aspx).   

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
