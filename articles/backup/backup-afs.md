---
title: Fazer backup de compartilhamentos de arquivo do Azure no portal do Azure
description: Saiba como usar o portal do Azure para fazer backup de compartilhamentos de arquivos do Azure no cofre dos serviços de recuperação
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: da2c7fa4cc5c3b7b948604a6f6d3999671cb3697
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101223"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Fazer backup de compartilhamentos de arquivos do Azure em um cofre dos serviços de recuperação

Este artigo explica como usar o portal do Azure para fazer backup de [compartilhamentos de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Neste artigo, você aprenderá a:

* Crie um cofre dos Serviços de Recuperação.
* Descubra compartilhamentos de arquivos e configure backups.
* Execute um trabalho de backup sob demanda para criar um ponto de restauração.

## <a name="prerequisites"></a>Pré-requisitos

* Identifique ou crie um [cofre dos serviços de recuperação](#create-a-recovery-services-vault) na mesma região que a conta de armazenamento que hospeda o compartilhamento de arquivos.
* Verifique se o compartilhamento de arquivos está presente em um dos [tipos de conta de armazenamento com suporte](azure-file-share-support-matrix.md).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por padrão, os cofres usam o [grs (armazenamento com redundância geográfica)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for seu mecanismo de backup primário, recomendamos que você use GRS.
* Você pode usar o [LRS (armazenamento com redundância local)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) como uma opção de baixo custo.

Para modificar o tipo de replicação de armazenamento:

1. No novo cofre, selecione **Propriedades** na seção **configurações** .

1. Na página **Propriedades** , em **configuração de backup**, selecione **Atualizar**.

1. Selecione o tipo de replicação de armazenamento e selecione **salvar**.

    ![Atualizar configuração de backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Não é possível modificar o tipo de replicação de armazenamento depois que o cofre é configurado e contém itens de backup. Se desejar fazer isso, você precisará recriar o cofre.
>

## <a name="discover-file-shares-and-configure-backup"></a>Descobrir compartilhamentos de arquivos e configurar o backup

1. No [portal do Azure](https://portal.azure.com/), abra o cofre dos serviços de recuperação que você deseja usar para fazer backup do compartilhamento de arquivos.

1. No painel do **cofre dos serviços de recuperação** , selecione **+ backup**.

   ![Cofre dos Serviços de Recuperação](./media/backup-afs/recovery-services-vault.png)

    a. Em **meta de backup**, defina **onde sua carga de trabalho está em execução? para o** **Azure**.

    ![Escolha o compartilhamento de arquivos do Azure como meta de backup](./media/backup-afs/backup-goal.png)

    b.  Em **o que você deseja fazer backup?**, selecione **compartilhamento de arquivos do Azure** na lista suspensa.

    c.  Selecione **backup** para registrar a extensão de compartilhamento de arquivos do Azure no cofre.

    ![Selecione backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-afs/register-extension.png)

1. Depois de selecionar **backup**, o painel **backup** é aberto e solicita que você selecione uma conta de armazenamento de uma lista de contas de armazenamento descobertos com suporte. Eles estão associados a este cofre ou estão presentes na mesma região que o cofre, mas ainda não estão associados a nenhum cofre de serviços de recuperação.

1. Na lista de contas de armazenamento descobertas, selecione uma conta e selecione **OK**. O Azure pesquisa a conta de armazenamento para compartilhamentos de arquivos cujo backup pode ser feito. Se você adicionou recentemente os compartilhamentos de arquivos e não os vê na lista, aguarde algum tempo para que os compartilhamentos de arquivos apareçam.

    ![Descobrindo compartilhamentos de arquivos](./media/backup-afs/discovering-file-shares.png)

1. Na lista **compartilhamentos de arquivos** , selecione um ou mais dos compartilhamentos de arquivos que você deseja fazer backup. Selecione **OK**.

   ![Selecionar os compartilhamentos de arquivos](./media/backup-afs/select-file-shares.png)

1. Depois de escolher os compartilhamentos de arquivos, o menu **backup** alterna para a **política de backup**. Nesse menu, selecione uma política de backup existente ou crie uma nova. Em seguida, selecione **habilitar backup**.

    ![Selecionar a Política de backup](./media/backup-afs/select-backup-policy.png)

Depois de definir uma política de backup, um instantâneo dos compartilhamentos de arquivos é obtido no horário agendado. O ponto de recuperação também é mantido para o período escolhido.

>[!NOTE]
>O backup do Azure agora dá suporte a políticas com retenção diária/semanal/mensal/anual para o backup do compartilhamento de arquivos do Azure.

## <a name="create-an-on-demand-backup"></a>Criar um backup sob demanda

Ocasionalmente, talvez você queira gerar um instantâneo de backup ou um ponto de recuperação, fora dos horários agendados na política de backup. Um motivo comum para gerar um backup sob demanda é logo depois de configurar a política de backup. Com base na agenda na política de backup, pode ser horas ou dias até que um instantâneo seja tirado. Para proteger seus dados até que a política de backup seja ativada, inicie um backup sob demanda. Muitas vezes, é necessário criar um backup sob demanda antes de fazer alterações planejadas em seus compartilhamentos de arquivos.

### <a name="create-a-backup-job-on-demand"></a>Criar um trabalho de backup sob demanda

1. Abra o cofre dos serviços de recuperação que você usou para fazer backup do compartilhamento de arquivos. No painel **visão geral** , selecione **itens de backup** na seção **itens protegidos** .

   ![Selecionar itens de backup](./media/backup-afs/backup-items.png)

1. Depois de selecionar **itens de backup**, um novo painel que lista todos os **tipos de gerenciamento de backup** é exibido ao lado do painel **visão geral** .

   ![Lista de tipos de gerenciamento de backup](./media/backup-afs/backup-management-types.png)

1. Na lista **tipo de gerenciamento de backup** , selecione **armazenamento do Azure (arquivos do Azure)**. Você verá uma lista de todos os compartilhamentos de arquivos e das contas de armazenamento correspondentes com backup usando esse cofre.

   ![Itens de backup do armazenamento do Azure (arquivos do Azure)](./media/backup-afs/azure-files-backup-items.png)

1. Na lista de compartilhamentos de arquivos do Azure, selecione o compartilhamento de arquivos desejado. Os detalhes do **item de backup** são exibidos. No menu **item de backup** , selecione **fazer backup agora**. Como esse trabalho de backup está sob demanda, não há nenhuma política de retenção associada ao ponto de recuperação.

   ![Selecione fazer backup agora](./media/backup-afs/backup-now.png)

1. O painel **fazer backup agora** é aberto. Especifique até que dia deseja manter o ponto de recuperação escolhendo o último dia. Você pode ter uma retenção máxima de 10 anos para um backup sob demanda.

   ![Escolher data de retenção](./media/backup-afs/retention-date.png)

1. Selecione **OK** para confirmar o trabalho de backup sob demanda que é executado.

1. Monitore as notificações do portal para manter um controle da conclusão da execução do trabalho de backup. Você pode monitorar o andamento do trabalho no painel do cofre. Selecione os **trabalhos** > **de backup em andamento**.

>[!NOTE]
>O backup do Azure bloqueia a conta de armazenamento quando você configura a proteção para qualquer compartilhamento de arquivos na conta correspondente. Isso fornece proteção contra a exclusão acidental de uma conta de armazenamento com compartilhamentos de arquivos de backup.

## <a name="best-practices"></a>Práticas recomendadas

* Não exclua os instantâneos criados pelo backup do Azure. A exclusão de instantâneos pode resultar na perda de pontos de recuperação e/ou em falhas de restauração.

* Não remova o bloqueio obtido na conta de armazenamento pelo backup do Azure. Se você excluir o bloqueio, sua conta de armazenamento será propenso a exclusão acidental e, se ela for excluída, você perderá seus instantâneos ou backups.

## <a name="next-steps"></a>Próximas etapas

Saiba como:

* [Restaurar compartilhamentos de arquivos do Azure](restore-afs.md)
* [Gerenciar backups do compartilhamento de arquivos do Azure](manage-afs-backup.md)
