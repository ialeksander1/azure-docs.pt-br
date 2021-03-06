---
title: Gerenciar backups de compartilhamento de arquivos do Azure com o PowerShell
description: Saiba como usar o PowerShell para gerenciar e monitorar as ações de arquivos do Azure apoiadas pelo serviço de backup do Azure.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083170"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Gerenciar backups de compartilhamento de arquivos do Azure com o PowerShell

Este artigo descreve como usar o Azure PowerShell para gerenciar e monitorar as partes de arquivos do Azure que são apoiadas pelo serviço de backup do Azure.

> [!WARNING]
> Certifique-se de que a versão ps seja atualizada para a versão mínima de 'Az.RecoveryServices 2.6.0' para backups AFS. Para obter mais detalhes, consulte [a seção](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) que descreve o requisito para esta alteração.

## <a name="modify-the-protection-policy"></a>Modificar a política de proteção

Para alterar a política usada para fazer backup do compartilhamento de arquivos Azure, use [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Especifique o item de backup relevante e a nova política de backup.

O exemplo a seguir altera a política de proteção de **testAzureFS** de **dailyafs** para **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Acompanhar tarefas de backup e restauração

As operações de backup e restauração demanda retornam um trabalho juntamente com um ID, como mostrado quando você [executa um backup demanda](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Use o [cmdlet Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) para acompanhar o andamento e os detalhes do trabalho.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>Interromper a proteção em um compartilhamento de arquivo

Há duas maneiras de interromper a proteção de compartilhamentos de arquivos do Azure:

* Pare todos os trabalhos de backup futuros e *exclua* todos os pontos de recuperação
* Pare todos os futuros trabalhos de backup, mas *deixe* os pontos de recuperação

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, já que os instantâneos subjacentes criados pelo Azure Backup serão mantidos. No entanto, a vantagem de deixar os pontos de recuperação é que você pode restaurar o compartilhamento de arquivos mais tarde, se desejar. Para obter informações sobre o custo de deixar os pontos de recuperação, confira os [detalhes de preços](https://azure.microsoft.com/pricing/details/storage/files/). Se você optar por excluir todos os pontos de recuperação, você não poderá restaurar o compartilhamento de arquivos.

## <a name="stop-protection-and-retain-recovery-points"></a>Pare a proteção e retenha pontos de recuperação

Para interromper a proteção durante a retenção de dados, use o [cmdlet Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *afsfileshare,* mas mantém todos os pontos de recuperação:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

O atributo Job ID na saída corresponde ao ID de trabalho do trabalho criado pelo serviço de backup para sua operação de "proteção contra paradas". Para acompanhar o status do trabalho, use o [cmdlet Get-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)

## <a name="stop-protection-without-retaining-recovery-points"></a>Parar a proteção sem reter pontos de recuperação

Para interromper a proteção sem reter pontos de recuperação, use o [cmdlet Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) e adicione o parâmetro **-RemoveRecoveryPoints.**

O exemplo a seguir interrompe a proteção para o compartilhamento de arquivos *afsfileshare* sem reter pontos de recuperação:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Próximas etapas

[Saiba como](manage-afs-backup.md) gerenciar backups de compartilhamento de arquivos do Azure no portal Azure.
