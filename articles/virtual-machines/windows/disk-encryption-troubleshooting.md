---
title: Guia de solução de problemas do Azure Disk Encryption
description: Este artigo fornece dicas de solução de problemas para o Microsoft Azure Disk Encryption para VMs Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 11c1e0bf10725173a2a341addf4c3f845bbb7fba
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085681"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de solução de problemas do Azure Disk Encryption

Este guia destina-se a profissionais de TI, analistas de segurança da informação e administradores de nuvem cujas organizações usam o Azure Disk Encryption. Este artigo é para ajudar na solução de problemas relacionados à criptografia de disco.

Antes de tomar qualquer uma das etapas abaixo, primeiro certifique-se de que as VMs que você está tentando criptografar estão entre os [tamanhos e sistemas operacionais de VM suportados,](disk-encryption-overview.md#supported-vms-and-operating-systems)e que você cumpriu todos os pré-requisitos:

- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de diretiva de grupo](disk-encryption-overview.md#group-policy-requirements)
- [Requisitos de armazenamento de chaves de criptografia](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Solucionando problemas do Azure Disk Encryption por trás de um firewall

Quando a conectividade estiver restrita por requisitos de proxy, firewall ou NSG (grupo de segurança de rede), a capacidade da extensão para executar as tarefas necessárias poderá ser interrompida. Essa interrupção pode resultar em mensagens de status como "Status da extensão não disponível na VM". Em cenários esperados, a criptografia não é concluída. As seções que se seguem têm alguns problemas comuns de firewall que podem ser investigados.

### <a name="network-security-groups"></a>Grupos de segurança de rede
Qualquer configuração do grupo de segurança de rede aplicada ainda deve permitir que o ponto de extremidade atenda aos [pré-requisitos](disk-encryption-overview.md#networking-requirements) da configuração de rede documentada para criptografia de disco.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault por trás de um firewall

Quando a criptografia é habilitada com [credenciais do Azure AD](disk-encryption-windows-aad.md#), a VM de destino deve permitir a conectividade com pontos de extremidade do Azure Active Directory e pontos de extremidade do Key Vault. Os pontos de extremidade de autenticação do Active Directory do Azure atuais são mantidos nas seções 56 e 59 da documentação [Intervalos de endereços IP e URLs do Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges). As instruções do Key Vault são fornecidas na documentação sobre como [Acessar o Azure Key Vault por trás de um firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure 
A VM precisa conseguir acessar o ponto de extremidade do [Serviço de Metadados de Instância do Azure](../windows/instance-metadata-service.md), que usa um endereço IP não roteável conhecido (`169.254.169.254`) que pode ser acessado somente na VM.  As configurações proxy que alteram o tráfego HTTP local para este endereço (por exemplo, adicionando um cabeçalho X-Forwarded-For) não são suportadas.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Solução de problemas de Server Core do Windows Server 2016

No Windows Server 2016 Server Core, o componente bdehdcfg não está disponível por padrão. Esse componente é exigido pelo Azure Disk Encryption. É usado para dividir o volume do sistema do volume do sistema operacional, o que é feito apenas uma vez para o tempo de vida da VM. Esses binários não são necessários durante as operações posteriores de criptografia.

Para contornar esse problema, copie os quatro arquivos a seguir de uma VM do Data Center do Windows Server 2016 para o mesmo local no Núcleo do Servidor:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Insira o seguinte comando:

   ```
   bdehdcfg.exe -target default
   ```

1. Esse comando cria uma partição de sistema de 550 MB. Reinicialize o sistema.

1. Use DiskPart para verificar os volumes e continue.  

Por exemplo:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Solução de problemas de status de criptografia 

O portal pode exibir um disco como criptografado mesmo depois de não ter sido criptografado dentro da VM.  Isso pode ocorrer quando comandos de baixo nível são usados para descriptografar diretamente o disco de dentro da VM, em vez de usar os comandos de gerenciamento de criptografia de disco Azure de nível mais alto.  Os comandos de nível mais alto não apenas descriptografam o disco de dentro da VM, mas fora da VM eles também atualizam importantes configurações de criptografia de nível de plataforma e configurações de extensão associadas à VM.  Se estes não forem mantidos em alinhamento, a plataforma não será capaz de relatar o status da criptografia ou provisionar a VM corretamente.   

Para desativar a criptografia de disco do Azure com o PowerShell, use [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguido de [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Executando remove-AzVMDiskEncryptionExtensão antes que a criptografia seja desativada falhará.

Para desativar a criptografia de disco do Azure com cli, use [a criptografia az vm desativada](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu mais sobre alguns problemas comuns no Azure Disk Encryption e como solucioná-los. Para saber mais sobre esse serviço e seus recursos, confira os seguintes artigos:

- [Aplicar a criptografia de disco na Central de Segurança do Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Criptografia de dados em repouso do Azure](../../security/fundamentals/encryption-atrest.md)
