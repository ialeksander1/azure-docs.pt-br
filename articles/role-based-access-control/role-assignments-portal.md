---
title: Adicionar ou remover atribuições de função com o RBAC e o portal Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, diretores de serviçoou identidades gerenciadas usando o RBAC (Role-Based Access Control, controle de acesso baseado em função) do Azure e do portal Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246351"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Adicionar ou remover atribuições de função usando o Azure RBAC e o portal Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Este artigo descreve como atribuir funções usando o portal Azure.

Se você precisar atribuir funções de administrador no Diretório Ativo do Azure, consulte [Exibir e atribuir funções de administrador no Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Controle de acesso (IAM)

**O controle de acesso (IAM)** é a lâmina que você usa para atribuir funções para conceder acesso aos recursos do Azure. Também é conhecido como gerenciamento de identidade e acesso e aparece em vários locais no portal Azure. O exemplo a seguir mostra um exemplo de folha de controle (IAM) de acesso para uma assinatura.

![Folha IAM (controle) de acesso para uma assinatura](./media/role-assignments-portal/access-control-subscription.png)

Para ser o mais eficaz com a lâmina de controle de acesso (IAM), ajuda se você puder responder as três perguntas a seguir quando estiver tentando atribuir uma função:

1. **Quem precisa de acesso?**

    Que se refere a um usuário, grupo, diretor de serviço ou identidade gerenciada. Isso também é chamado de diretor de *segurança.*

1. **Que papel eles precisam?**

    As permissões são agrupadas em papéis. Você pode selecionar a partir de uma lista de várias [funções incorporadas](built-in-roles.md) ou pode usar suas próprias funções personalizadas.

1. **Onde eles precisam de acesso?**

    Quando se refere ao conjunto de recursos a que o acesso se aplica. Onde pode estar um grupo de gerenciamento, assinatura, grupo de recursos ou um único recurso, como uma conta de armazenamento. Isso é chamado de *escopo*.

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No Azure RBAC, para conceder acesso a um recurso do Azure, você adiciona uma atribuição de função. Siga essas etapas para atribuir um papel.

1. No portal Azure, clique em **Todos os serviços** e selecione o escopo ao que deseja conceder acesso. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico para esse escopo.

1. Clique **em Acessar controle (IAM)**.

1. Clique na guia **Atribuições de função** para exibir as atribuições de função neste escopo.

    ![Guia de atribuições de controle de acesso (IAM) e de função](./media/role-assignments-portal/role-assignments.png)

1. Clique **em Adicionar** > **atribuição de função**.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione um usuário, grupo, entidade de serviço ou identidade gerenciada. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.

1. Clique em **Salvar** para atribuir a função.

   Após alguns instantes, a entidade de segurança é atribuída a função no escopo selecionado.

    ![Adicionar atribuição de função salva](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um usuário como um administrador de uma assinatura

Para tornar um usuário administrador de uma assinatura do Azure, atribua-o à função [Proprietário](built-in-roles.md#owner) no escopo da assinatura. A função Proprietário dá ao usuário acesso total a todos os recursos da assinatura, incluindo a permissão para conceder acesso a outros. Essas etapas são as mesmas que as de qualquer outra atribuição de função.

1. No portal do Microsoft Azure, clique em **Todos os serviços** e, em seguida, em **Assinaturas**.

1. Clique na assinatura em que você deseja conceder acesso.

1. Clique **em Acessar controle (IAM)**.

1. Clique na guia **Atribuições de função** para exibir as atribuições de função desta assinatura.

    ![Guia de atribuições de controle de acesso (IAM) e de função](./media/role-assignments-portal/role-assignments.png)

1. Clique **em Adicionar** > **atribuição de função**.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista suspensa **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um usuário. Se o usuário não estiver na lista, digite na caixa **Selecionar** para pesquisar no diretório os nomes de exibição e os endereços de email.

1. Clique em **Salvar** para atribuir a função.

   Após alguns instantes, o usuário é atribuído à função Proprietário no escopo da assinatura.

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

No Azure RBAC, para remover o acesso de um recurso do Azure, você remove uma atribuição de função. Siga estas etapas para remover uma atribuição de função.

1. Abra **Controle de acesso (IAM)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja remover o acesso.

1. Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.

1. Na lista de atribuições de função, marque a caixa de seleção ao lado de objeto com a atribuição de função de segurança que você deseja remover.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Clique em **Remover**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem para remover a atribuição de função exibida, clique em **Sim**.

    Se você vir uma mensagem que as atribuições de função herdadas não podem ser removidas, você está tentando remover uma atribuição de função em um escopo de criança. Você deve abrir o controle de acesso (IAM) no escopo onde a função foi atribuída e tentar novamente. Uma maneira rápida de abrir o controle de acesso (IAM) no escopo correto é olhar para a coluna **Escopo** e clicar no link ao lado **de (Herdado)**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Próximas etapas

- [Listar atribuições de função usando o Azure RBAC e o portal Azure](role-assignments-list-portal.md)
- [Tutorial: Conceda a um usuário acesso aos recursos do Azure usando o RBAC e o portal Azure](quickstart-assign-role-user-portal.md)
- [Solução de problemas com o RBAC para recursos do Azure](troubleshooting.md)
- [Organize seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)
