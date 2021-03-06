---
title: FAQ - Criptografia de disco azure para VMs windows
description: Este artigo fornece respostas a perguntas frequentes sobre o Microsoft Azure Disk Encryption para VMs Windows IaaS.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: b71384e0a42af5481af7b17b91cd0b1d0ed82ee8
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082587"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Criptografia de disco azure para máquinas virtuais Windows PERGUNTAS FREQUENTES

Este artigo fornece respostas a perguntas frequentes (FAQ) sobre a Criptografia de Disco Azure para VMs windows. Para obter mais informações sobre este serviço, consulte [a visão geral da criptografia de disco do Azure](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>O que é criptografia de disco azure para VMs windows?

A criptografia de disco do Azure para VMs windows usa o recurso Bitlocker do Windows para fornecer criptografia de disco completo do disco do SISTEMA OPERACIONAL e discos de dados. Além disso, ele fornece criptografia do disco de recursos efêmeros quando o [parâmetro VolumeType é All](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  O conteúdo flui criptografado da VM para o backend de armazenamento. Assim, fornecendo criptografia de ponta a ponta com uma chave gerenciada pelo cliente.
 
Consulte [VMs e sistemas operacionais suportados.](disk-encryption-overview.md#supported-vms-and-operating-systems)
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde o Azure Disk Encryption está na GA (disponibilidade geral)?

A Criptografia de Disco Do Azure está em disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quais experiências de usuário estão disponíveis com o Azure Disk Encryption?

A GA do Azure Disk Encryption dá suporte a modelos do Azure Resource Manager, ao Azure PowerShell e à CLI do Azure. As diferentes experiências de usuário oferecem flexibilidade. Você tem três opções diferentes para ativar a criptografia de disco para suas VMs. Para obter mais informações sobre a experiência do usuário e orientação passo a passo disponível no Azure Disk Encryption, consulte [os cenários de criptografia de disco do Azure para Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa o Azure Disk Encryption?

Não há custo para criptografar discos VM com criptografia de disco Azure, mas há cargas associadas ao uso do Azure Key Vault. Para obter mais informações sobre os custos do Cofre de Chaves do Azure, consulte a página de preços [Cofre de chaves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a usar o Azure Disk Encryption?

Para começar, leia as [visão geral do Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Quais tamanhos de VM e sistemas operacionais suportam criptografia de disco Azure?

O [artigo de visão geral do Azure Disk Encryption](disk-encryption-overview.md) lista os tamanhos de [VM](disk-encryption-overview.md#supported-vms) e [os sistemas operacionais VM](disk-encryption-overview.md#supported-operating-systems) que suportam a criptografia de disco do Azure.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Posso criptografar volumes de dados e de inicialização com o Azure Disk Encryption?

Você pode criptografar volumes de inicialização e dados, mas não pode criptografar os dados sem antes criptografar o volume do Sistema Operacional.

Depois de criptografar o volume do Sistema Operacional, desativar a criptografia no volume do so não é suportado.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Posso criptografar um volume desmontado com criptografia de disco Do Zure?

Não, a Criptografia de Disco Do Azure só criptografa volumes montados.

## <a name="what-is-storage-server-side-encryption"></a>O que é criptografia do lado do servidor de armazenamento?

A criptografia do lado do servidor de armazenamento criptografa os discos gerenciados do Azure no Azure Storage. Os discos gerenciados são criptografados por padrão com criptografia do lado do servidor com uma chave gerenciada pela plataforma (a partir de 10 de junho de 2017). Você pode gerenciar a criptografia de discos gerenciados com suas próprias chaves especificando uma chave gerenciada pelo cliente. Para obter mais informações, consulte [a criptografia do lado do servidor dos discos gerenciados do Azure](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Como a criptografia de disco do Azure é diferente da criptografia do lado do servidor de armazenamento com a chave gerenciada pelo cliente e quando devo usar cada solução?

O Azure Disk Encryption fornece criptografia de ponta a ponta para o disco do SISTEMA OPERACIONAL, discos de dados e o disco de recursos efêmeros com uma chave gerenciada pelo cliente.

- Se seus requisitos incluem criptografar toda a criptografia acima e de ponta a ponta, use a Criptografia de Disco do Azure. 
- Se seus requisitos incluem criptografar apenas dados em repouso com a chave gerenciada pelo cliente, use [criptografia do lado do servidor com chaves gerenciadas pelo cliente](disk-encryption.md). Não é possível criptografar um disco com criptografia de disco Azure e criptografia do lado do servidor de armazenamento com chaves gerenciadas pelo cliente.
- Se você estiver usando um cenário chamado em [cenários não suportados para windows,](disk-encryption-windows.md#unsupported-scenarios)considere [a criptografia do lado do servidor com chaves gerenciadas pelo cliente](disk-encryption.md). 
- Se a política da sua organização permitir que você criptografe conteúdo em repouso com uma chave gerenciada pelo Azure, então nenhuma ação é necessária - o conteúdo é criptografado por padrão. Para discos gerenciados, o conteúdo dentro do armazenamento é criptografado por padrão com criptografia do lado do servidor com chave gerenciada pela plataforma. A chave é gerenciada pelo serviço de armazenamento Azure. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como faço para rodar segredos ou chaves de criptografia?

Para rodar segredos, basta chamar o mesmo comando que você usou originalmente para ativar a criptografia de disco, especificando um Cofre de Chaves diferente. Para girar a chave de criptografia da chave, chame o mesmo comando que você usou originalmente para habilitar a criptografia de disco, especificando a nova criptografia de chave. 

>[!WARNING]
> - Se você já usou anteriormente [o azure Disk Encryption com o aplicativo Azure AD](disk-encryption-windows-aad.md) especificando credenciais Azure AD para criptografar esta VM, você terá que continuar usando essa opção para criptografar sua VM. Você não pode usar o Azure Disk Encryption nesta VM criptografada, pois este não é um cenário suportado, o que significa que mudar para longe do aplicativo AAD para este VM criptografado ainda não é suportado.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Como adicionar ou remover uma chave de criptografia se eu não usei originalmente uma?

Para adicionar uma chave de criptografia, chame o comando enable novamente passando o parâmetro de chave de criptografia de chave. Para remover uma chave de criptografia, chame novamente o comando enable sem o parâmetro de chave de criptografia chave.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>O Azure Disk Encryption permite o recurso BYOK (Bring Your Own Key)?

Sim, você pode fornecer suas próprias chaves de criptografia de chave. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre os cenários de suporte às chaves de criptografia das chaves, consulte [Criando e configurando um cofre de chaves para criptografia de disco Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Posso usar uma chave de criptografia de chave criada pelo Azure?

Sim, você pode usar o Azure Key Vault para gerar uma chave de criptografia de chave para ser usada pela criptografia de disco do Azure. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre a chave de criptografia da chave, consulte [Criar e configurar um cofre de chaves para criptografia de disco Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso usar um serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia?

Você não pode usar o serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia com o Azure Disk Encryption. Você só pode usar o serviço Azure Key Vault para proteger as chaves de criptografia. Para obter mais informações sobre os principais cenários de suporte à chave de criptografia, consulte [Criando e configurando um cofre de chaves para criptografia de disco Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption?

Há pré-requisitos para o Azure Disk Encryption. Consulte o Criando e configurando um cofre de chaves para o artigo [Azure Disk Encryption](disk-encryption-key-vault.md) para criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso à criptografia de disco para ativar criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte à chave de criptografia, consulte [Criando e configurando um cofre de chaves para criptografia de disco Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption com um aplicativo do Azure AD (versão anterior)?

Há pré-requisitos para o Azure Disk Encryption. Consulte a [criptografia de disco do Azure com conteúdo Azure AD](disk-encryption-windows-aad.md) para criar um aplicativo do Azure Active Directory, criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso à criptografia de disco para ativar criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte à chave de criptografia, consulte [Criando e configurando um cofre de chaves para criptografia de disco Azure com o Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Ainda há suporte para o Azure Disk Encryption usando um aplicativo do Azure AD (versão anterior)?
Sim. Ainda há suporte para criptografia de disco usando um aplicativo do Azure AD. No entanto, ao criptografar novas VMs, é recomendável que você use o novo método em vez de criptografar com um aplicativo do Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Posso migrar VMs que foram criptografadas com um aplicativo do Azure AD para a criptografia sem um aplicativo do Azure AD?
  Atualmente, não há um caminho de migração direto para computadores que foram criptografados com um aplicativo do Azure AD para criptografia sem um aplicativo do Azure AD. Além disso, também não há um caminho direto da criptografia sem um aplicativo do Azure AD para a criptografia com o aplicativo do AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>À qual versão do Azure PowerShell o Azure Disk Encryption dá suporte?

Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). O Azure Disk Encryption *não* é suportado pela versão 1.1.0 do SDK do Azure.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>O que é o disco "Volume Bek" ou "/mnt/azure_bek_disk"?

O "Volume de Bek" é um volume de dados local que armazena com segurança as chaves de criptografia para VMs Azure criptografados.

> [!NOTE]
> Não exclua ou edite nenhum conteúdo neste disco. Não desmonte o disco, uma vez que a presença da chave de criptografia é necessária para operações de criptografia na VM IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Qual método de criptografia é usado pela criptografia de disco do Azure?

O Azure Disk Encryption seleciona o método de criptografia no BitLocker com base na versão do Windows da seguinte forma:

| Versões do Windows                 | Versão | Método de criptografia        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 ou superior  | >=1511 |XTS-AES 256 bits           |
| Windows Server 2012, Windows 8, 8.1, 10 | < de 1511 |AES 256 bits *              |
| Windows Server 2008R2            |        |AES 256 bit com Difusor |

\*AES 256 bit com Difusor não é suportado no Windows 2012 e posterior.

Para determinar a versão do Sistema Operacional Windows, execute a ferramenta 'winver' em sua máquina virtual.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se eu usar EncryptFormatAll e especificar todos os tipos de volume, ele apagará os dados em unidades de dados que já tivermos criptografado?
Não, os dados não serão apagados de unidades de dados que já tiverem sido criptografadas usando o Azure Disk Encryption. Semelhante ao modo como o EncryptFormatAll não criptografou novamente a unidade do sistema operacional, ele não criptografará novamente a unidade de dados já criptografada. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Posso fazer backup e restaurar uma VM criptografada? 

O Azure Backup fornece um mecanismo para fazer backup e restaurar VMs criptografadas dentro da mesma assinatura e região.  Para obter instruções, consulte [Fazer backup e restaurar máquinas virtuais criptografadas com o Azure Backup](../../backup/backup-azure-vms-encryption.md).  A restauração de uma VM criptografada para uma região diferente não é suportada no momento.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso fazer perguntas ou fornecer comentários?

Você pode fazer perguntas ou fornecer comentários no [Fórum do Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relativas ao Azure Disk Encryption. Para obter mais informações sobre esse serviço, veja os seguintes artigos:

- [Visão geral da criptografia de disco do Azure](disk-encryption-overview.md)
- [Aplicar a criptografia de disco na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Criptografia de dados em repouso do Azure](../../security/fundamentals/encryption-atrest.md)
