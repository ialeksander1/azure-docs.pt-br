---
title: Problemas de agente e extensão
description: Sintomas, causas e resoluções para falhas do Backup do Azure relacionados a agente, extensão e discos.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: a3eedb5440711c7a45a13dcd53dd489c490588fc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677418"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Solucionar problemas de falha do Backup do Azure: problemas com o agente ou a extensão

Este artigo fornece etapas de solução de problemas que podem ajudar você a resolver erros de Backup do Azure relacionados à comunicação com a extensão e o agente da VM.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - o agente de VM não pode se comunicar com o Backup do Azure

**Código de erro**: UserErrorGuestAgentStatusUnavailable <br>
**Mensagem de erro**: Agente VM incapaz de se comunicar com o Backup do Azure<br>

O agente Azure VM pode ser parado, desatualizado, em um estado inconsistente, ou não instalado. Esses estados impedem que o serviço de backup do Azure acione instantâneos.

- **Abrir o portal Do zure > o painel De configurações > > > propriedades da VM >** garantir que **o status** da VM esteja **funcionando** e o status **do agente** esteja **pronto**. Se o agente vm estiver parado ou estiver em um estado inconsistente, reinicie o agente<br>
  - Para VMs do Windows, siga [estas etapas](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) para reiniciar o Agente Convidado.<br>
  - Para VMs Linux, siga [estas etapas](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) para reiniciar o Agente Convidado.
- **O portal Open Azure > as configurações > > de configurações > da VM** > garantir que todas as extensões estejam no provisionamento do estado **bem sucedido.** Caso assim, siga [estas etapas](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) para resolver o problema.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – não foi possível se comunicar com o agente de VM para o status do instantâneo

**Código de erro**: GuestAgentSnapshotTaskStatusError<br>
**Mensagem de erro**: Não foi possível se comunicar com o agente VM para status do instantâneo <br>

Depois de se registrar e agendar uma VM para o serviço de backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup da VM para tirar um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  

**Causa 1: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Causa 3: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Causa 4: As [opções de configuração do VM-Agent não estão definidas (para VMs Linux)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed - A VM está em estado de provisionamento com falha

**Código de erro**: UserErrorVmProvisioningStateFailed<br>
**Mensagem de erro**: A VM está em estado de provisionamento com falha<br>

Esse erro ocorre quando uma das falhas de extensão coloca a VM em estado de falha no provisionamento.<br>**O portal Open Azure > o > status de Configurações > Extensões > De extensões > da VM** e verifique se todas as extensões estão em **provisionamento do** estado de sucesso. Para saber mais, consulte [Estados de Provisionamento](https://docs.microsoft.com/azure/virtual-machines/windows/states-lifecycle#provisioning-states).

- Se a extensão VMSnapshot estiver em um estado com falha, clique com o botão direito do mouse na extensão com falha e remova-a. Acione um backup sob demanda. Esta ação reinstalará as extensões e executará o trabalho de backup.  <br>
- Se qualquer outra extensão estiver em um estado falho, então ela pode interferir com o backup. Certifique-se de que esses problemas de extensão sejam resolvidos e tente novamente a operação de backup.
- Se o estado de provisionamento da VM estiver em estado de atualização, ele pode interferir com o backup. Certifique-se de que está saudável e tente novamente a operação de backup.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – o limite máximo da coleção de pontos de restauração foi atingido

**Código de erro**: UserErrorRpCollectionLimitReached <br>
**Mensagem de erro**: o limite máximo da coleção de pontos de restauração foi atingido. <br>

- Esse problema pode acontecer se houver um bloqueio no grupo de recursos do ponto de recuperação que impede a limpeza automática dos pontos de recuperação.
- Esse problema também pode ocorrer se vários backups são disparados por dia. Atualmente recomendamos apenas um backup por dia, pois os pontos de restauração instantânea são retidos por 1-5 dias por retenção de instantâneo configurada e apenas 18 RPs instantâneos podem ser associados a uma VM a qualquer momento. <br>
- O número de pontos de restauração em coleções de pontos de restauração e grupos de recursos para uma VM não pode exceder 18. Para criar um novo ponto de restauração, exclua os pontos de restauração existentes.

Ação recomendada:<br>
Para resolver esse problema, remova o bloqueio do grupo de recursos da VM e repita a operação para disparar a limpeza.
> [!NOTE]
> O serviço de backup cria um grupo de recursos separado que o grupo de recursos da VM para armazenar a coleção de pontos de restauração. Os clientes são aconselhados a não bloquear o grupo de recursos criado para uso pelo serviço de Backup. O formato de nomenclatura do grupo de recursos criado pelo serviço de Backup é: AzureBackupRG_`<Geo>`_`<number>` Por exemplo: AzureBackupRG_northeurope_1

**Etapa 1: [ Remover bloqueio do grupo de recursos do ponto de restauração](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Etapa 2: [limpar a coleção de pontos de restauração](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured – o backup não tem permissões suficientes no cofre de chaves para o backup de VMs criptografadas

**Código de erro**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Mensagem de erro**: o backup não tem permissões suficientes do cofre de chaves para fazer backup de VMs criptografadas. <br>

Para que uma operação de backup tenha sucesso em VMs criptografadas, ela deve ter permissões para acessar o cofre-chave. As permissões podem ser definidas através do [portal Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) ou através [do PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork: falha na operação do instantâneo por falta de conectividade à rede na máquina virtual

**Código de erro**: ExtensionSnapshotFailedNoNetwork<br>
**Mensagem de erro**: falha na operação de instantâneo por falta de conectividade à rede na máquina virtual<br>

Depois de se registrar e agendar uma VM para o serviço de backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup da VM para tirar um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Complete a seguinte etapa de solução de problemas e tente novamente sua operação:

**[O status do instantâneo não pode ser recuperado, ou um instantâneo não pode ser tomado](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensãoOperaçãoFailedForManagedDisks - Falha na operação de extensão VMSnapshot

**Código de erro:** ExtensãoOperaçãoFalhaPorGerenciamentodiscos <br>
**Mensagem de erro**: falha na operação da extensão VMSnapshot<br>

Depois de se registrar e agendar uma VM para o serviço de backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup da VM para tirar um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 – falha no backup, com um erro interno

**Código de erro**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Mensagem de erro**: falha no backup com um erro interno – tente novamente a operação dentro de alguns minutos <br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 4: [O serviço de backup não tem permissão para excluir os pontos de restauração antigos por causa de um bloqueio de grupo de recursos](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize - O tamanho do disco configurado não é suportado pelo Azure Backup

**código de erro**: UserErrorUnsupportedDiskSize <br>
**Mensagem de erro**: O tamanho do disco configurado não é suportado pelo Azure Backup. <br>

Sua operação de backup pode falhar ao fazer backup de uma VM com um tamanho de disco maior que 32 TB. Além disso, o backup de discos criptografados maiores que 4 TB de tamanho não é suportado no momento. Certifique-se de que o tamanho do disco é menor ou igual ao limite suportado dividindo o disco(s).

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – não é possível iniciar o backup porque outra operação de backup já está em andamento

**Código de erro**: UserErrorBackupOperationInProgress <br>
**Mensagem de erro**: Não é possível iniciar o backup, pois outra operação de backup está em andamento<br>

Seu trabalho de backup recente falhou porque há um trabalho de backup existente em andamento. Você não pode iniciar um novo trabalho de backup antes que o trabalho atual seja concluído. Verifique se a operação de backup em andamento foi concluída antes de iniciar ou agendar outras operações de backup. Para verificar o status das vagas de backup, faça as seguintes etapas:

1. Faça login no portal Azure, clique em **Todos os serviços**. Digite Serviços de Recuperação e clique em **Cofres dos Serviços de Recuperação**. A lista de cofres de Serviços de Recuperação aparecerá.
2. Na lista de cofres dos serviços de recuperação, selecione um cofre no qual o backup esteja configurado.
3. No menu do dashboard do cofre, clique em **Trabalhos de Backup** para exibir todos os trabalhos de backup.
   - Se um trabalho de backup estiver em andamento, aguarde a sua conclusão ou cancele-o.
     - Para cancelar o trabalho de backup, clique com o botão direito do mouse no trabalho de backup e clique **em Cancelar** ou usar [powerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
   - Se você reconfigurou o backup em um cofre diferente, então certifique-se de que não há trabalhos de backup sendo executados no cofre antigo. Se ele existe, então cancele o trabalho de backup.
     - Para cancelar o trabalho de backup, clique com botão direito do mouse no trabalho de backup e clique em **Cancelar** ou use o [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Tente novamente a operação de backup.

Se a operação de backup programada estiver demorando mais, conflitante com a próxima configuração de backup, então revise as [Práticas recomendadas,](backup-azure-vms-introduction.md#best-practices) [desempenho de backup](backup-azure-vms-introduction.md#backup-performance)e [consideração de restauração.](backup-azure-vms-introduction.md#backup-and-restore-considerations)

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - Falha no backup devido a um erro. Para obter detalhes, consulte detalhes da mensagem de erro de trabalho

**Código de erro**: UserErrorCrpReportedUserError <br>
**Mensagem de erro**: Falha no backup devido a um erro. Para obter detalhes, consulte detalhes da mensagem de erro de trabalho.

Este erro é relatado a partir do VM IaaS. Para identificar a causa raiz do problema, vá para as configurações do cofre dos Serviços de Recuperação. Na seção **Monitoramento,** selecione **Trabalhos de backup** para filtrar e visualizar o status. Clique em **Falhas** para revisar os detalhes da mensagem de erro subjacente. Tome outras ações de acordo com as recomendações na página de detalhes de erro.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresente - Falha de backup: Esta máquina virtual não está (ativamente) protegida pelo Backup do Azure

**Código de erro**: UserErrorBcmDatasourceNotPresent <br>
**Mensagem de erro**: Falha de backup: Esta máquina virtual não está (ativamente) protegida pelo Backup do Azure.

Verifique se a determinada máquina virtual está ativamente (não em estado de pausa) protegida pelo Azure Backup. Para superar esse problema, certifique-se de que a máquina virtual esteja ativa e, em seguida, tente novamente a operação.

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente é instalado na VM, mas não responde (para VMs do Windows)

#### <a name="solution"></a>Solução

O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. A reinstalação do agente de VM permite obter a versão mais recente. Ela também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de agente convidado do Microsoft Azure está executando nos serviços de VM (services.msc). Tente reiniciar o serviço de agente convidado do Microsoft Azure e inicie o backup.
2. Se o serviço de agente convidado do Microsoft Azures não estiver visível nos serviços, no Painel de Controle, acesse **Programas e Recursos** para determinar se o serviço de agente convidado do Microsoft Azure está instalado.
3. Se o serviço de agente convidado do Microsoft Azures aparecer em **Programas e Recursos**, desinstale o serviço de agente convidado do Microsoft Azure.
4. Baixe e instale a [versão mais recente do MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa ter direitos de Administrador para concluir a instalação.
5. Verifique se os serviços de agente convidado do Microsoft Azures aparecem nos serviços.
6. Execute um backup sob demanda:
   - No portal, selecione **Fazer Backup Agora**.

Verifique também se o [Microsoft .NET 4.5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. O .NET 4.5 é necessário para que o agente de VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução

A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux ](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Estamos *altamente recomendável* que você atualizar o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente para a sua distribuição não está disponível, entre em contato com o suporte da distribuição para obter instruções de como instalá-lo. Para verificar o agente mais recente, vá para a página [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

2. Verifique se o agente do Azure está em execução na VM, executando o seguinte comando: `ps -e`

   Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:

   - Para o Ubuntu: `service walinuxagent start`
   - Para outras distribuições: `service waagent start`

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Execute um novo backup de teste. Se a falha persistir, colete os seguintes logs da VM:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Se você precisar de registro verbose para waagent, siga estas etapas:

1. No arquivo /etc/waagent.conf, localize a seguinte linha: **habilitar o log detalhado (y | n)**
2. Altere o valor **Logs.Verbose** de *n* para *s*.
3. Salve a alteração e, em seguida, reinicie o waagent, completando as etapas descritas anteriormente nesta seção.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>As opções de configuração do VM-Agent não estão definidas (para VMs Linux)

Um arquivo de configuração (/ etc/waagent.conf) controla as ações de waagent. Extensões de opções de arquivo **de configuração.Habilitar** e **Provisionar.Agent** deve ser definido **como y** para backup para funcionar.
Para obter a lista completa de opções de arquivo de configuração do agente VM, consulte<https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>O status do instantâneo não pode ser recuperado, ou não é possível obter um instantâneo

O backup da máquina virtual depende de emitir um comando de instantâneo para a conta de armazenamento subjacente. O backup pode falhar porque não tem acesso à conta de armazenamento ou porque a execução da tarefa de instantâneo está atrasada.

#### <a name="solution"></a>Solução

As seguintes condições podem causar falha na tarefa de instantâneo:

| Causa | Solução |
| --- | --- |
| O status da VM foi informado incorretamente porque a VM foi desligada no protocolo RDP. | Se você desligar a VM no RDP, verifique o portal para determinar se o status da VM está correto. Se não estiver correto, desligue a VM no portal usando a opção **Shutdown** no painel da VM. |
| A VM não pode obter o endereço do host ou da malha do DHCP. | O DHCP deve estar habilitado no convidado para que o backup da VM IaaS funcione. Se a VM não puder obter o endereço do host ou da malha por meio da resposta 245 do DHCP, ela não poderá baixar ou executar nenhuma extensão. Se você precisar de um IP privado estático, você deve configurá-lo através do **portal Azure** ou **PowerShell** e certificar-se de que a opção DHCP dentro da VM está ativada. [Saiba mais](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre como configurar um endereço IP estático com o PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Remover o bloqueio do grupo de recursos de ponto de recuperação

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Ir para a **opção Todos os recursos,** selecione`<Geo>`o`<number>`grupo de recursos de coleta de pontos de restauração no formato a AzureBackupRG_ _ .
3. Na seção **Configurações**, selecione **Bloqueios** para exibir os bloqueios.
4. Para remover o bloqueio, selecione as reticências e clique em **Excluir**.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>Limpar a coleta de pontos de restauração

Depois de remover o bloqueio, os pontos de restauração precisam ser limpos.

Se você excluir o Grupo de recursos da VM ou a própria VM, os instantâneos de restauração instantânea dos discos gerenciados permanecerão ativos e expirarão de acordo com o conjunto de retenção. Para excluir os instantâneos de restauração instantânea (se você não precisar mais) que são armazenados na Coleção De Ponto de Restauração, limpe a coleção de pontos de restauração de acordo com as etapas abaixo.

Para limpar os pontos de restauração, siga qualquer um dos seguintes métodos:<br>

- [Limpe a coleta de pontos de restauração executando backup sob demanda](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Limpar a coleção de pontos de restauração do portal do Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Limpe a coleta de pontos de restauração executando backup sob demanda

Depois de remover o bloqueio, acione um backup sob demanda. Esta ação garantirá que os pontos de restauração sejam automaticamente limpos. Espere que esta operação sob demanda falhe na primeira vez; no entanto, ele garantirá a limpeza automática em vez da exclusão manual dos pontos de restauração. Após a limpeza, seu próximo backup programado deve ter sucesso.

> [!NOTE]
> A limpeza automática acontecerá após algumas horas de acionamento do backup sob demanda. Se o backup agendado ainda falhar, tente excluir manualmente a coleção de pontos de restauração usando as etapas listadas [aqui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Limpar a coleção de pontos de restauração do portal do Azure <br>

Para limpar manualmente a coleta de pontos de restauração, que não é liberada por causa do bloqueio no grupo de recursos, tente as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. No menu **Hub**, clique em **Todos os recursos**, selecione o grupo de recursos com o formato AzureBackupRG_`<Geo>`_`<number>` a seguir em que sua VM se encontra.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Clique em Grupo recurso, o painel **Visão geral** é exibido.
4. Selecione a opção **Mostrar tipos ocultos** opção para exibir todos os recursos ocultos. Selecione as coleções de ponto de restauração com o seguinte formato: AzureBackupRG_`<VMName>`_`<number>`.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Clique **em Excluir** para limpar a coleção de pontos de restauração.
6. Tente a operação de backup novamente.

> [!NOTE]
 >Se o recurso (RP Collection) tiver um grande número de Pontos de Restauração, a exclusão deles do portal pode ser eliminado e falhar. Esse é um problema conhecido do CRP, em que todos os pontos de restauração não são excluídos no tempo determinado e a operação atinge o tempo limite; no entanto, a operação de exclusão geralmente terá êxito após 2 ou 3 tentativas.
