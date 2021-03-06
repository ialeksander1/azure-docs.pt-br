---
title: Adicionar ou remover membros do grupo – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar ou remover membros de um grupo usando o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c83c57be63ae9e2a4d4113accaefe8a2c2b525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561960"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Adicionar ou remover membros do grupo usando o Azure Active Directory
Usando o Azure Active Directory, você pode continuar adicionando ou removendo membros do grupo.

## <a name="to-add-group-members"></a>Para adicionar membros do grupo

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador Global para o diretório.

2. Selecione **Azure Active Directory** e, em seguida, selecione **Grupos**.

3. Na página **Grupos - Todos os grupos**, pesquise e selecione o grupo em que você quer adicionar o membro. Nesse caso, use o grupo criado anteriormente, **Política de MDM - Oeste**.

    ![Grupos - Todos os grupos, o nome do grupo realçado](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na página **Política de MDM - Visão geral do Oeste**, selecione **Membros** na área **Gerenciar**.

    ![Política de MDM - Página de Visão geral do Oeste, com a opção Membros realçada](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Selecione **Adicionar membros** e, em seguida, pesquise e selecione cada um dos membros que você quer adicionar ao grupo e, em seguida, escolha **Selecionar**.

    Você receberá uma mensagem informando que os membros foram adicionados com êxito.

    ![Adicionar página de membros, com o membro pesquisado mostrado](media/active-directory-groups-members-azure-portal/update-members.png)

6. Atualize a tela para ver todos os nomes de membros adicionados ao grupo.

## <a name="to-remove-group-members"></a>Para remover membros do grupo

1. Na página **Grupos - Todos os grupos**, pesquise e selecione o grupo do qual você quer remover o membro. Usaremos novamente **Política de MDM - Oeste**.

2. Selecione **Membros** na área **Gerenciar**, pesquise e selecione o nome do membro a ser removido e, em seguida, selecione **Remover**.

    ![Página de informações do membro, com a opção Remover](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Próximas etapas

- [Exibir grupos e membros](active-directory-groups-view-azure-portal.md)

- [Editar as configurações de grupo](active-directory-groups-settings-azure-portal.md)

- [Gerenciar acesso a recursos usando grupos](active-directory-manage-groups.md)

- [Gerenciar regras dinâmicas para usuários em um grupo](../users-groups-roles/groups-create-rule.md)

- [Associar ou adicionar uma assinatura do Azure ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
