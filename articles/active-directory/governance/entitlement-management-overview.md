---
title: O que é gerenciamento de direitos? – Azure AD
description: Obtenha uma visão geral do gerenciamento de direitos Azure Active Directory e como você pode usá-lo para gerenciar o acesso a grupos, aplicativos e sites do SharePoint Online para usuários internos e externos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f4ce292ad507eb0208633db7743b881508a8e58
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144446"
---
# <a name="what-is-azure-ad-entitlement-management"></a>O que é o gerenciamento de direitos do Azure AD?

O gerenciamento de direitos do Azure Active Directory (AD do Azure) é um recurso de [governança de identidade](identity-governance-overview.md) que permite que as organizações gerenciem o ciclo de vida de identidade e acesso em escala, automatizando fluxos de trabalho de solicitação de acesso, atribuições de acesso, revisões e expiração.

Os funcionários em organizações precisam acessar vários grupos, aplicativos e sites para executar seu trabalho. O gerenciamento desse acesso é desafiador, conforme os requisitos mudam-novos aplicativos são adicionados ou os usuários precisam de direitos de acesso adicionais.  Esse cenário fica mais complicado quando você colabora com organizações externas – talvez não saiba quem na outra organização precisa de acesso aos recursos da sua organização e não saberá quais aplicativos, grupos ou sites sua organização está usando.

O gerenciamento de direitos do Azure AD pode ajudá-lo a gerenciar com mais eficiência o acesso a grupos, aplicativos e sites do SharePoint Online para usuários internos e também para usuários fora da sua organização que precisam de acesso a esses recursos.

## <a name="why-use-entitlement-management"></a>Por que usar o gerenciamento de direitos?

As organizações empresariais geralmente enfrentam desafios ao gerenciar o acesso de funcionários a recursos como:

- Os usuários podem não saber qual acesso eles devem ter e, mesmo se fizerem, podem ter dificuldade para localizar os indivíduos certos para aprovar o acesso deles
- Depois que os usuários localizarem e receberem acesso a um recurso, eles poderão manter o acesso mais longo do que o necessário para fins comerciais

Esses problemas são compostos por usuários que precisam de acesso de outra organização, como usuários externos que são de organizações de cadeia de fornecedores ou outros parceiros comerciais. Por exemplo:

- Ninguém pode conhecer todos os indivíduos específicos nos diretórios de outras organizações para poder convidá-los
- Mesmo que eles possam convidar esses usuários, ninguém nessa organização pode se lembrar de gerenciar todo o acesso do usuário de forma consistente

O gerenciamento de direitos do Azure AD pode ajudar a resolver esses desafios.  Para saber mais sobre como os clientes usam o gerenciamento de direitos do Azure AD, você pode ler o [estudo de caso do Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) e o [estudo de caso do Centrica](https://customers.microsoft.com/story/757467-centrica-energy-azure).  Este vídeo fornece uma visão geral do gerenciamento de direitos e seu valor:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>O que posso fazer com o gerenciamento de direitos?

Aqui estão alguns recursos de gerenciamento de direitos:

- Delegar a não-administradores a capacidade de criar pacotes de acesso. Esses pacotes de acesso contêm recursos que os usuários podem solicitar e os gerenciadores de pacotes de acesso delegados podem definir políticas com regras para as quais os usuários podem solicitar, quem deve aprovar o acesso e quando o acesso expira.
- Selecione as organizações conectadas cujos usuários podem solicitar acesso.  Quando um usuário que ainda não está em seu diretório solicita acesso e é aprovado, ele é automaticamente convidado para seu diretório e recebe acesso.  Quando o acesso expirar, se eles não tiverem nenhuma outra atribuição de pacote de acesso, sua conta B2B no diretório poderá ser removida automaticamente.

Você pode começar com nosso [tutorial para criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md). Você também pode ler os [cenários comuns](entitlement-management-scenarios.md)ou assistir a vídeos, incluindo

- [Como implantar o gerenciamento de direitos do Azure AD em sua organização](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Como monitorar e dimensionar o uso do gerenciamento de direitos do Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Como delegar o gerenciamento de direitos](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>O que são pacotes do Access e quais recursos posso gerenciar com eles?

O gerenciamento de direitos apresenta ao Azure AD o conceito de um *pacote de acesso*. Um pacote do Access é um pacote de todos os recursos com o acesso que um usuário precisa para trabalhar em um projeto ou executar sua tarefa. Os pacotes do Access são usados para controlar o acesso de seus funcionários internos e também usuários fora da sua organização.

 Estes são os tipos de recursos para os quais você pode gerenciar o acesso do usuário com o gerenciamento de direitos:

- Associação de grupos de segurança do Azure AD
- Associação de grupos e equipes do Office 365
- Atribuição para aplicativos corporativos do Azure AD, incluindo aplicativos SaaS e aplicativos personalizados que dão suporte a Federação/logon único e/ou provisionamento
- Associação de sites do SharePoint Online

Você também pode controlar o acesso a outros recursos que dependem de grupos de segurança do Azure AD ou grupos do Office 365.  Por exemplo:

- Você pode conceder aos usuários licenças para o Microsoft Office 365 usando um grupo de segurança do Azure AD em um pacote do Access e Configurando o [Licenciamento baseado em grupo](../users-groups-roles/licensing-groups-assign.md) para esse grupo
- Você pode conceder aos usuários acesso para gerenciar recursos do Azure usando um grupo de segurança do Azure AD em um pacote do Access e criando uma [atribuição de função do Azure](../../role-based-access-control/role-assignments-portal.md) para esse grupo

## <a name="how-do-i-control-who-gets-access"></a>Como fazer controle quem obtém acesso?

Com um pacote do Access, um administrador ou um Gerenciador de pacotes de acesso delegado lista os recursos (grupos, aplicativos e sites) e as funções que os usuários precisam para esses recursos.

Pacotes de acesso também incluem uma ou mais *políticas*. Uma política define as regras ou guardrails para atribuição para acessar o pacote. Cada política pode ser usada para garantir que apenas os usuários apropriados sejam capazes de solicitar acesso, que há aprovadores para sua solicitação, e que seu acesso a esses recursos é limitado por tempo e expirará se não for renovado.

![Acessar o pacote e as políticas](./media/entitlement-management-overview/elm-overview-access-package.png)

Dentro de cada política, um administrador ou um Gerenciador de pacotes de acesso define

- Os usuários já existentes (normalmente funcionários ou convidados já convidados) ou as organizações parceiras de usuários externos, que são elegíveis para solicitar acesso
- O processo de aprovação e os usuários que podem aprovar ou negar o acesso
- A duração da atribuição de acesso de um usuário, uma vez aprovada, antes de a atribuição expirar

O diagrama a seguir mostra um exemplo dos diferentes elementos no gerenciamento de direitos. Ele mostra um catálogo com dois pacotes de acesso de exemplo.

- O **pacote do Access 1** inclui um único grupo como um recurso. O acesso é definido com uma política que permite que um conjunto de usuários no diretório solicite acesso.
- O **pacote de acesso 2** inclui um grupo, um aplicativo e um site do SharePoint Online como recursos. O acesso é definido com duas políticas diferentes. A primeira política permite que um conjunto de usuários no diretório solicite acesso. A segunda política permite que os usuários em um diretório externo solicitem acesso.

![Visão geral do gerenciamento de direitos](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Quando devo usar pacotes do Access?

Os pacotes do Access não substituem outros mecanismos para atribuição de acesso.  Eles são mais apropriados em situações como as seguintes:

- Os funcionários precisam de acesso limitado a tempo para uma determinada tarefa.  Por exemplo, você pode usar o licenciamento baseado em grupo e um grupo dinâmico para garantir que todos os funcionários tenham uma caixa de correio do Exchange Online e, em seguida, usar pacotes do Access para situações em que os funcionários precisam de acesso adicional, como para ler recursos departamentais de outro departamento.
- O acesso precisa ser aprovado pelo gerente de um funcionário ou por outros indivíduos designados.
- Os departamentos desejam gerenciar suas próprias políticas de acesso para seus recursos sem envolvimento de ti.  
- Duas ou mais organizações estão colaborando em um projeto e, como resultado, vários usuários de uma organização precisarão ser trazidos por meio do Azure AD B2B para acessar os recursos de outra organização.

## <a name="how-do-i-delegate-access"></a>Como fazer delegar acesso?

 Pacotes de acesso são definidos em contêineres chamados *catálogos*.  Você pode ter um único catálogo para todos os seus pacotes de acesso ou pode designar indivíduos para criar e possuir seus próprios catálogos. Um administrador pode adicionar recursos a qualquer catálogo, mas um não administrador só pode adicionar a um catálogo os recursos que eles possuem. Um proprietário de catálogo pode adicionar outros usuários como coproprietários de catálogo ou como gerenciadores de pacotes de acesso.  Esses cenários são descritos mais detalhadamente no artigo [delegação e funções no gerenciamento de direitos do Azure ad](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Resumo da terminologia

Para entender melhor o gerenciamento de direitos e sua documentação, você pode consultar novamente a lista de termos a seguir.

| Termo | Descrição |
| --- | --- |
| pacote de acesso | Um pacote de recursos que uma equipe ou projeto precisa e é regido por políticas. Um pacote do Access sempre está contido em um catálogo. Você criaria um novo pacote de acesso para um cenário no qual os usuários precisam solicitar acesso.  |
| solicitação de acesso | Uma solicitação para acessar os recursos em um pacote do Access. Normalmente, uma solicitação passa por um fluxo de trabalho de aprovação.  Se aprovada, o usuário solicitante receberá uma atribuição de pacote de acesso. |
| atribuição | Uma atribuição de um pacote de acesso a um usuário garante que o usuário tenha todas as funções de recurso desse pacote de acesso.  As atribuições de pacote do Access normalmente têm um limite de tempo antes de expirarem. |
| catálogo | Um contêiner de recursos relacionados e pacotes de acesso.  Os catálogos são usados para delegação, para que não administradores possam criar seus próprios pacotes de acesso. Os proprietários do catálogo podem adicionar recursos que eles possuem a um catálogo. |
| criador do catálogo | Uma coleção de usuários que estão autorizados a criar novos catálogos.  Quando um usuário não administrador que está autorizado a ser um criador de catálogo cria um novo catálogo, ele se torna automaticamente o proprietário desse catálogo. |
| organização conectada | Um diretório ou domínio do Azure AD externo com o qual você tem uma relação. Os usuários de uma organização conectada podem ser especificados em uma política como tendo permissão para solicitar acesso. |
| policy | Um conjunto de regras que define o ciclo de vida do acesso, como a forma como os usuários obtêm acesso, quem pode aprovar e por quanto tempo os usuários têm acesso por meio de uma atribuição. Uma política está vinculada a um pacote do Access. Por exemplo, um pacote do Access pode ter duas políticas: uma para os funcionários solicitarem acesso e um segundo para que usuários externos solicitem acesso. |
| recurso | Um ativo, como um grupo do Office, um grupo de segurança, um aplicativo ou um site do SharePoint Online, com uma função à qual um usuário pode receber permissões. |
| diretório de recursos | Um diretório que tem um ou mais recursos a serem compartilhados. |
| função de recurso | Uma coleção de permissões associadas a e definidas por um recurso. Um grupo tem duas funções – membro e proprietário. Os sites do SharePoint normalmente têm 3 funções, mas podem ter funções personalizadas adicionais. Os aplicativos podem ter funções personalizadas. |


## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Nuvens especializadas, como o Azure Alemanha e o Azure China 21Vianet, não estão disponíveis para uso no momento.

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças você precisa ter?

Verifique se o diretório tem pelo menos quantas licenças Azure AD Premium P2 você tem:

- Usuários Membros que **podem** solicitar um pacote de acesso.
- Membros e usuários convidados que solicitam um pacote de acesso.
- Membros e usuários convidados que aprovam solicitações para um pacote de acesso.
- Membros e usuários convidados que têm uma atribuição direta a um pacote do Access.

As licenças do Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Nenhuma licença é necessária para usuários com a função de administrador global que configura os catálogos iniciais, pacotes de acesso e políticas e delega tarefas administrativas a outros usuários.
- Nenhuma licença é necessária para os usuários que foram delegados tarefas administrativas, como criador do catálogo, proprietário do catálogo e Gerenciador de pacotes de acesso.
- Nenhuma licença é necessária para convidados que **podem** solicitar pacotes de acesso, mas **não** solicitar um pacote de acesso.

Para cada licença paga do Azure AD Premium P2 adquirida para seus usuários Membros (funcionários), você pode usar o Azure AD B2B para convidar até 5 usuários convidados. Esses usuários convidados também poderão usar os recursos do Azure AD Premium P2. Para obter mais informações, consulte [diretrizes de licenciamento da colaboração B2B do Azure ad](../b2b/licensing-guidance.md).

Para obter mais informações sobre licenças, consulte [atribuir ou remover licenças usando o portal de Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Cenários de licença de exemplo

Aqui estão alguns exemplos de cenários de licença para ajudá-lo a determinar o número de licenças que você deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um administrador global no Banco Woodgrove cria catálogos iniciais e delega tarefas administrativas a 6 outros usuários. Uma das políticas especifica que **todos os funcionários** (2.000 funcionários) podem solicitar um conjunto específico de pacotes de acesso. 150 funcionários solicitam os pacotes de acesso. | 2.000 funcionários que **podem** solicitar os pacotes de acesso | 2\.000 |
| Um administrador global no Banco Woodgrove cria catálogos iniciais e delega tarefas administrativas a 6 outros usuários. Uma das políticas especifica que **todos os funcionários** (2.000 funcionários) podem solicitar um conjunto específico de pacotes de acesso. Outra política especifica que alguns usuários de **usuários do parceiro contoso** (convidados) podem solicitar os mesmos pacotes de acesso sujeitos à aprovação. A contoso tem 30.000 usuários. 150 funcionários solicitam os pacotes de acesso e 10.500 usuários do contoso solicitam acesso. | 2.000 funcionários + 500 usuários convidados da Contoso que excedem a taxa de 1:5 (10.500-(2.000 * 5)) | 2\.500 |

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: criar seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Cenários comuns](entitlement-management-scenarios.md)
