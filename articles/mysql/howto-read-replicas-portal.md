---
title: Gerenciar réplicas de leitura - Portal Azure - Banco de dados Azure para MySQL
description: Saiba como configurar e gerenciar réplicas de leitura no Banco de Dados Azure para MySQL usando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: b7226e5ae8c468339e02dbe87e279266e4609da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063486"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Como criar e gerenciar réplicas de leitura no Banco de Dados do Azure para MySQL usando o portal do Azure

Neste artigo, você aprenderá como criar e gerenciar réplicas de leitura no banco de dados Azure para o serviço MySQL usando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um [Banco de Dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será usado como servidor mestre.

> [!IMPORTANT]
> O recurso de réplica de leitura está disponível apenas para bancos de dados do Azure para servidores MySQL nas camadas de preços de uso geral ou de otimização de memória. Assegure-se de que o servidor principal esteja em um desses níveis de preços.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando as seguintes etapas:

1. Inscreva-se no [portal Azure](https://portal.azure.com/).

2. Selecione o banco de dados existente do Azure para servidor MySQL que você deseja usar como um mestre. Essa ação abre a página **Visão geral** do runbook.

3. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

4. Selecione **Adicionar réplica**.

   ![Banco de Dados do Azure para MySQL - Replicação](./media/howto-read-replica-portal/add-replica.png)

5. Digite um nome para o servidor de réplica.

    ![Banco de dados Azure para MySQL - Nome da réplica](./media/howto-read-replica-portal/replica-name.png)

6. Selecione o local para o servidor de réplicas. O local padrão é o mesmo do servidor mestre.

    ![Banco de dados Azure para MySQL - Localização de réplica](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo de conceitos de réplica de leitura](concepts-read-replicas.md). 

7. Selecione **OK** para confirmar a criação da réplica.

> [!NOTE]
> Réplicas de leitura são criadas com a mesma configuração de servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de criada. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou maiores que o mestre para garantir que a réplica seja capaz de acompanhar o mestre.

Depois que o servidor de réplica tiver sido criado, ele poderá ser visualizado no blade **Replication**.

   ![Banco de Dados do Azure para MySQL - Listar Réplicas](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação para um servidor é irreversível. Depois que a replicação tiver parado entre um mestre e uma réplica, ela não poderá ser desfeita. O servidor de réplica então se torna um servidor autônomo e agora suporta tanto leitura quanto gravação. Este servidor não pode ser transformado em uma réplica novamente.

Para interromper a replicação entre um servidor mestre e um servidor de réplica do portal do Azure, use as seguintes etapas:

1. No portal do Azure, selecione o seu banco de dados principal do Azure para o servidor MySQL. 

2. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

3. Selecione o servidor de réplica para o qual você deseja interromper a replicação.

   ![Banco de Dados do Azure para MySQL - Parar servidor de seleção de replicação](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selecione **'Stop replication '**

   ![Banco de Dados do Azure para MySQL - Parar replicação](./media/howto-read-replica-portal/stop-replication.png)

5. Confirme se deseja interromper a replicação clicando em **OK**.

   ![Banco de Dados do Azure para MySQL - Confirme a replicação de parada](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

Para excluir um servidor de réplica de leitura do portal do Azure, use as seguintes etapas:

1. No portal do Azure, selecione o seu banco de dados principal do Azure para o servidor MySQL.

2. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.

3. Selecione o servidor de réplica que você deseja excluir.

   ![Banco de Dados do Azure para MySQL - Excluir Servidor de Seleção de Réplica](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selecione **'Excluir'**

   ![Banco de dados do Azure para MySQL - Excluir réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Digite o nome da réplica e clique em **Excluir** para confirmar a exclusão da réplica.  

   ![Confirmar do banco de dados do Azure para MySQL - excluir réplica](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Excluir um servidor mestre

> [!IMPORTANT]
> A exclusão de um servidor mestre interrompe a replicação para todos os servidores de réplica e exclui o próprio servidor mestre. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor mestre do portal do Azure, use as seguintes etapas:

1. No portal do Azure, selecione o seu banco de dados principal do Azure para o servidor MySQL.

2. Na **visão geral,** **selecione Excluir**.

   ![Banco de dados do Azure para MySQL - Excluir mestre](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digite o nome do servidor mestre e clique em **excluir** para confirmar a exclusão do servidor mestre.  

   ![Banco de dados do Azure para MySQL - Excluir mestre](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorar a replicação

1. No [Portal do Azure](https://portal.azure.com/), selecione a réplica do Banco de Dados do Azure para o servidor MySQL que você deseja monitorar.

2. Na seção **Monitoramento** da barra lateral, selecione **Métricas**:

3. Selecione o **atraso de replicação em segundos** na lista suspensa de métricas disponíveis.

   ![Selecione o retardo de replicação](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selecione o intervalo de tempo que você deseja visualizar. A imagem abaixo seleciona um intervalo de tempo de 30 minutos.

   ![Selecionar intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Veja o atraso de replicação para o intervalo de tempo selecionado. A imagem a seguir exibe os últimos 30 minutos.

   ![Selecionar intervalo de tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)