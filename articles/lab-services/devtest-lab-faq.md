---
title: Perguntas frequentes sobre o Azure DevTest Labs | Microsoft Docs
description: Este artigo fornece respostas para algumas das perguntas mais frequentes (FAQ) sobre o Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270778"
---
# <a name="azure-devtest-labs-faq"></a>Perguntas frequentes sobre o Azure DevTest Labs
Obtenha respostas para algumas das perguntas mais comuns sobre o Azure DevTest Labs.

## <a name="blog-post"></a>Postagem no blog
Nosso blog DevTest Labs Team foi aposentado a partir de 20 de março de 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Onde posso acompanhar as atualizações de recursos de agora em diante?
A partir de agora, estaremos postando atualizações de recursos e postagens informativas no blog azure e atualizações do Azure. Esses posts no blog também se vincularão à nossa documentação sempre que necessário.

Inscreva-se nas atualizações do [DevTest Labs Azure Blog](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) e [do DevTest Labs Azure](https://azure.microsoft.com/updates/?product=devtest-lab) para ficar informado sobre novos recursos no DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>O que acontece com os posts existentes no blog?
No momento, estamos trabalhando na migração de posts existentes (excluindo atualizações de paralisação) para nossa [documentação do DevTest Labs](devtest-lab-overview.md). Quando o blog do MSDN for preterido, ele será redirecionado para a visão geral da documentação para o DevTest Labs. Uma vez redirecionado, você pode procurar o artigo que você está procurando no título 'Filtrar por'. Ainda não migramos todos os postos, mas deve ser feito até o final deste mês. 


### <a name="where-do-i-see-outage-updates"></a>Onde vejo atualizações de paralisação?
Estaremos postando atualizações de paralisação usando nossa alça do Twitter a partir de agora. Siga-nos no Twitter para obter as últimas atualizações sobre paralisações e bugs conhecidos.

### <a name="twitter"></a>Twitter
Nossa alça do Twitter:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Geral
### <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?
Se sua pergunta não estiver listada aqui, avise-nos, para que possamos ajudá-lo a encontrar uma resposta.

- Poste uma pergunta no final destas Perguntas Frequentes.
- Para alcançar um público maior, poste uma pergunta no [Fórum do MSDN do Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Envolva-se com a equipe do Azure DevTest Labs e outros membros da comunidade.
- Para solicitações de recurso, envie solicitações e ideias para o [User Voice do Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>O que é uma conta da Microsoft?
Uma conta da Microsoft é uma conta que você utiliza para quase tudo o que faz com os serviços e dispositivos da Microsoft. É um endereço de e-mail e senha que você usa para entrar no Skype, Outlook.com, OneDrive, Windows phone, Azure e Xbox Live. Uma conta única significa que seus arquivos, fotos, contatos e configurações, podem seguir você em qualquer dispositivo.

> [!NOTE]
> A conta da Microsoft costumava ser chamada de Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Por que devo usar o Azure DevTest Labs?
O Azure DevTest Labs pode economizar dinheiro e tempo da equipe. Os desenvolvedores podem criar seus próprios ambientes usando várias bases diferentes. Eles também podem usar artefatos para implantar e configurar aplicativos rapidamente. Usando fórmulas e imagens personalizadas, você pode salvar VMs (máquinas virtuais) como modelos e reproduzi-los facilmente entre a equipe. O DevTest Labs também oferece várias políticas configuráveis que permitem aos administradores do laboratório reduzir o desperdício e gerenciar os ambientes de uma equipe. Essas políticas incluem desligamento automático, limite de custo, máximo de VMs por usuário e tamanhos máximos de VM. Para obter uma explicação mais detalhada sobre o DevTest Labs, veja a [visão geral](devtest-lab-overview.md) ou o [vídeo introdutório](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>O que significa "autoatendimento sem preocupações"?
Autoatendimento sem preocupações significa que os desenvolvedores e testadores criam seus próprios ambientes conforme necessário. Os administradores têm a segurança de saber que o DevTest Labs pode ajudar a definir o acesso adequado, minimizar os custos de desperdício e controle. Os administradores podem especificar quais tamanhos de VM são permitidos, o número máximo de VMs e quando as VMs são inicializadas e desligadas. O DevTest Labs também torna mais fácil monitorar os custos e definir alertas, para ajudar você a saber sempre como os recursos do laboratório estão sendo usados.

### <a name="how-can-i-use-devtest-labs"></a>Como posso usar o Azure DevTest Labs?
O DevTest Labs é útil sempre que você precisa de ambientes de teste ou de vérmes e deseja reproduzi-los rapidamente ou gerenciá-los usando políticas de economia de custos.
Aqui estão alguns cenários em que nossos clientes usam o DevTest Labs:

- Gerenciar ambientes de desenvolvimento e teste em um único local. Usar políticas para reduzir custos e criar imagens personalizadas para compartilhar builds entre a equipe.
- Desenvolver um aplicativo usando imagens personalizadas para salvar o estado do disco em todas as fases de desenvolvimento.
- Acompanhar o custo em relação ao andamento.
- Criar ambientes de teste em massa para testes de garantia de qualidade.
- Usar fórmulas e artefatos para configurar e reproduzir um aplicativo em ambientes diversos rapidamente.
- Distribuir VMs para hackathons (trabalho colaborativo de desenvolvimento ou teste) e desprovisioná-las com facilidade quando o evento termina.

### <a name="how-am-i-billed-for-devtest-labs"></a>Como eu sou cobrado pelo DevTest Labs?
O DevTest Labs é um serviço gratuito. Criar e configurar políticas, modelos e artefatos no DevTest Labs é gratuito. Você paga apenas pelos recursos do Azure usados em seus laboratórios, assim como VMs, contas de armazenamento e redes virtuais. Para obter mais informações sobre o custo de recursos de laboratório, veja [preço do Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Segurança

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quais são os diferentes níveis de segurança no DevTest Labs?
O acesso de segurança é determinado pelo RBAC (controle de acesso baseado em função). Para entender como o acesso funciona, é importante entender as diferenças entre um escopo, uma permissão e uma função, conforme definido pelo RBAC.

- **Permissão**: uma permissão é um acesso definido para uma ação específica. Por exemplo, uma permissão pode ser de acesso de leitura a todas as VMs.
- **Função**: uma função é um conjunto de permissões que podem ser agrupadas e atribuídas a um usuário. Por exemplo, um usuário com uma função de proprietário da assinatura tem acesso a todos os recursos dentro de uma assinatura.
- **Escopo**: um escopo é um nível dentro da hierarquia de um recurso do Azure. Por exemplo, um escopo pode ser um grupo de recursos, um único laboratório ou toda a assinatura.

Dentro do escopo do DevTest Labs, há dois tipos de funções para definir permissões de usuário:

- **Proprietário de laboratório**: um proprietário de laboratório tem acesso a qualquer recurso no laboratório. Um proprietário de laboratório pode modificar políticas, ler e gravar para quaisquer VMs, alterar a rede virtual e assim por diante.
- **Usuário de laboratório**: um usuário de laboratório pode exibir todos os recursos de laboratório, tais como VMs, políticas e redes virtuais. No entanto, um usuário de laboratório não pode modificar políticas ou quaisquer VMs que foram criadas por outros usuários.

Você também pode criar funções personalizadas no DevTest Labs. Para saber como criar funções personalizadas no DevTest Labs, consulte [Conceder permissões de usuário para políticas específicas do laboratório](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Já que os escopos são hierárquicos, quando um usuário tem permissões em um determinado escopo, o usuário recebe essas permissões automaticamente em cada escopo de nível inferior que está no escopo. Por exemplo, se a função de proprietário da assinatura é atribuída a um usuário, ele tem acesso a todos os recursos em uma assinatura. Esses recursos incluem VMs, redes virtuais e laboratórios. Um proprietário de assinatura herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdadeiro. O proprietário de um laboratório tem acesso a um laboratório, que é um escopo menor que o nível de assinatura. Então, um dono de laboratório não pode ver VMs, redes virtuais, ou quaisquer outros recursos que estão fora do laboratório.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Como faço para definir o controle de acesso baseado em função para meus ambientes de DevTest Labs e garantir que a TI possa ter controle enquanto os desenvolvedores/teste realizam seu trabalho?
Há um padrão amplo, no entanto, o detalhe depende de sua organização.

A Central DE TI deve possuir apenas o necessário e permitir que as equipes de projeto e aplicativo tenham o nível de controle necessário. Normalmente, isso significa que a TI central controla a assinatura e lida com as principais funções de TI, como configurações de rede. O conjunto de **proprietários** de uma assinatura deve ser pequeno. Esses proprietários podem nomear proprietários adicionais quando houver necessidade ou aplicar políticas de nível de assinatura, por exemplo "Sem IP público".

Pode haver um subconjunto de usuários que exija acesso por meio de uma assinatura, como o suporte à Camada 1 ou à Camada 2. Nesse caso, recomendamos que você conceda a esses usuários o acesso de **colaborador** para que eles possam gerenciar os recursos sem fornecer acesso a usuários nem ajustar políticas.

O recurso DevTest Labs deve pertencer a proprietários próximos à equipe do projeto/aplicativo. É porque eles entendem seus requisitos para máquinas, e software necessário. Na maioria das organizações, o proprietário do recurso DevTest Labs geralmente é o líder do projeto/desenvolvimento. Esse proprietário pode gerenciar usuários e políticas no ambiente de laboratório e gerenciar todas as VMs no ambiente de DevTest Labs.

Os membros da equipe de projeto/aplicativo devem ser adicionados à função **DevTest Labs Users.** Esses usuários podem criar máquinas virtuais (alinhadas com políticas do laboratório e do nível da assinatura). Eles também podem gerenciar suas próprias máquinas virtuais. Eles não podem gerenciar máquinas virtuais que pertencem a outros usuários.

Para obter mais informações, consulte [o andaime corporativo do Azure – documentação prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold)de governança de assinatura .


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Como criar uma função para permitir que os usuários façam uma tarefa específica?
Para um artigo abrangente sobre como criar funções personalizadas e atribuir permissões a uma função, consulte [Conceder permissões de usuário para políticas específicas do laboratório](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Eis um exemplo de um script que cria a função **usuário Advanced do DevTest Labs**, que tem permissão para inicializar e desligar todas as VMs no laboratório:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Como uma organização pode assegurar que as políticas de segurança corporativa estão em vigor?
Uma organização pode alcançar esse objetivo realizando as seguintes ações:

- Desenvolver e publicar uma política de segurança abrangente. A política articula as regras de uso aceitável associadas ao uso de software e de ativos de nuvem. Ele também define o que claramente viola a política.
- Desenvolva uma imagem personalizada, artefatos personalizados e um processo de implantação que permite a orquestração no realm de segurança que é definido com o Active Directory. Essa abordagem impõe o limite corporativo e define um conjunto comum de controles ambientais. Esses controles determinam o ambiente que um desenvolvedor pode considerar conforme desenvolve e segue um ciclo de vida de desenvolvimento seguro como parte de seu processo geral. O objetivo também é fornecer um ambiente que não seja excessivamente restritivo que possa dificultar o desenvolvimento, mas um conjunto razoável de controles. As políticas de grupo na UO (unidade organizacional) que contém máquinas virtuais do laboratório pode ser um subconjunto das políticas de grupo totais que são encontrados na produção. Como alternativa, elas podem ser um conjunto adicional para mitigar adequadamente todos os riscos identificados.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Como uma organização pode assegurar a integridade de dados para garantir que os desenvolvedores de comunicação remota não possam remover o código ou introduzir malware ou software não aprovado?
Há várias camadas de controle para atenuar a ameaça de consultores externos, prestadores de serviços ou funcionários que estão acessando remotamente para colaborar no DevTest Labs.

Conforme mencionado anteriormente, a primeira etapa precisa ter uma política de uso aceitável esboçada e definida que descreva claramente as consequências de quando alguém viola a política.

A primeira camada de controles para acesso remoto é aplicar uma política de acesso remoto através de uma conexão VPN que não está diretamente conectada ao laboratório.

A segunda camada de controles é aplicar um conjunto de objetos de política de grupo que evita copiar e colar por meio da Área de Trabalho Remota. Uma política de rede pode ser implementada para não permitir a saída de serviços do ambiente, tais como serviços de protocolo RDP e FTP fora do ambiente. O roteamento definido pelo usuário poderia forçar todo o tráfego de rede do Azure de volta ao local, mas o roteamento não poderia explicar todas as URLs que poderiam permitir o upload de dados, a menos que seja controlado por um proxy para escanear conteúdo e sessões. IPs públicos poderiam ser restritos dentro da rede virtual que dá suporte ao DevTest Labs para não permitir a passagem de um recurso de rede externo.

Em última análise, o mesmo tipo de restrições precisa ser aplicado em toda a organização, o que explicaria todos os métodos possíveis de mídia removível ou URLs externos que pudessem aceitar uma postagem de conteúdo. Confira seu profissional de segurança para examinar e implementar uma política de segurança. Para obter mais recomendações, confira [Segurança cibernética da Microsoft](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Configuração do laboratório

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Como criar um laboratório de um modelo do Resource Manager?
Nós oferecemos um [repositório GitHub dos modelos do laboratório do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) que você pode implantar como são ou modificar para criar modelos personalizados para seus laboratórios. Cada modelo tem um link para implantar o laboratório, pois está em sua própria assinatura do Azure. Ou então você pode personalizar o modelo e [implantar usando o PowerShell ou a CLI do Azure](../azure-resource-manager/templates/deploy-powershell.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Posso ter todas as máquinas virtuais para serem criadas em um grupo de recursos comuns, em vez de ter cada máquina em seu próprio grupo de recursos?
Sim, como proprietário de laboratório, você pode deixar o laboratório lidar com a alocação de grupos de recursos para você ou ter todas as máquinas virtuais criadas em um grupo de recursos comum que você especifica.

Cenário de grupo de recursos separado:
-   DevTest Labs cria um novo grupo de recursos para cada máquina virtual IP pública/privada que você gira
-   O DevTest Labs cria um grupo de recursos para máquinas IP compartilhadas que pertencem ao mesmo tamanho.

Cenário de grupo de recursos comum:
-   Todas as máquinas virtuais são giradas no grupo de recursos comum especificado. Saiba mais [alocação de grupo de recursos para o laboratório](https://aka.ms/RGControl).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Como manter a uma convenção de nomenclatura em meu ambiente do DevTest Labs?
Talvez você queira estender as convenções de nomenclatura empresariais atuais para operações do Azure e torná-las consistentes em todo o ambiente do DevTest Labs. Ao implantar o DevTest Labs, é recomendável que você tenha políticas iniciais específicas. Você implanta essas políticas por um script central e modelos JSON para impor consistência. Políticas de nomenclatura podem ser implementadas por meio de políticas do Azure aplicadas no nível da assinatura. Para obter exemplos de JSON para o Azure Policy, confira [Exemplos do Azure Policy](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quantos laboratórios posso criar na mesma assinatura?
Não há um limite específico quanto ao número de laboratórios que podem ser criados por assinatura. No entanto, a quantidade de recursos usados por assinatura é limitada. Você pode ler sobre os [limites e cotas para assinaturas do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) e sobre [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Quantas VMs posso criar por laboratório?
Não há nenhum limite específico quanto ao número de VMs que podem ser criadas por laboratório. No entanto, os recursos usados (núcleos de VMs, endereços IP públicos, etc.) são limitados por assinatura. Você pode ler sobre os [limites e cotas para assinaturas do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) e sobre [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Como determino a proporção de usuários por laboratório e o número total de laboratórios necessários em uma organização?
É recomendável que as unidades de negócios e os grupos de desenvolvimento associados com o mesmo projeto de desenvolvimento sejam associados com o mesmo laboratório. Isso permite que os mesmos tipos de políticas, imagens e políticas de desligamento sejam aplicadas a ambos os grupos.

Você também precisará considerar os limites geográficos. Por exemplo, desenvolvedores no Nordeste dos Estados Unidos (EUA) podem usar um laboratório provisionado no Leste dos EUA2. E os desenvolvedores em Dallas, Texas e em Denver, Colorado podem ser direcionados para usar um recurso no Centro-Sul dos EUA. Se houver um esforço colaborativo com terceiros, eles poderão ser atribuídos a um laboratório não usado por desenvolvedores internos.

Você também pode usar um laboratório para um projeto específico dentro do Azure DevOps Projects. Em seguida, você aplica a segurança por meio de um grupo especificado do Azure Active Directory, o que permite o acesso a ambos os conjunto de recursos. A rede virtual atribuída ao laboratório pode ser outro limite para consolidar os usuários.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Como podemos evitar a exclusão de recursos dentro de um laboratório?
É recomendável que você defina permissões apropriadas no nível do laboratório, para que somente usuários autorizados possam excluir recursos ou alterar políticas de laboratório. Os desenvolvedores devem ser colocados dentro do grupo **Usuários do DevTest Labs**. O desenvolvedor líder ou o líder de infraestrutura deve ser o **Proprietário do DevTest Labs**. Recomendamos que você tenha apenas dois donos de laboratório. Essa política se estende ao repositório de código para evitar corrupção. Os usuários de laboratório têm o direito de usar recursos, mas não podem atualizar as políticas do laboratório. Confira o artigo a seguir, que lista as funções e os direitos de cada grupo interno em um laboratório: [Adicionar proprietários e usuários no Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como faço para compartilhar um link direto para o meu laboratório?

1. No [portal Azure,](https://portal.azure.com)vá para o laboratório.
2. Copie a URL do **laboratório** do seu navegador e compartilhe-a com os usuários do seu laboratório.

> [!NOTE]
> Se um usuário do laboratório for um usuário externo que tenha uma conta da Microsoft, mas que não seja um membro da instância do Active Directory da sua organização, o usuário poderá ver uma mensagem de erro ao tentar acessar o link compartilhado. Se um usuário externo vê uma mensagem de erro, peça ao usuário para primeiro selecionar o nome dele no canto superior direito do Portal do Azure. Em seguida, na seção Diretório do menu, o usuário pode selecionar o diretório em que o laboratório existe.

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Por que não posso ver VMs na página Máquinas Virtuais que vejo no DevTest Labs?
Quando você cria uma VM no DevTest Labs, você tem permissão para acessar essa VM. Você pode ver a VM tanto na página de laboratórios quanto na página **Máquinas Virtuais.** Os usuários atribuídos à função **DevTest Labs Owner** podem ver todas as VMs que foram criadas no laboratório na página **All Virtual Machines** do laboratório. No entanto, os usuários que têm a função **de usuário do DevTest Labs** não recebem acesso de leitura automaticamente aos recursos de VM que outros usuários criaram. Então, essas VMs não são exibidas na página **Máquinas Virtuais.**


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como criar várias VMs do mesmo modelo simultaneamente?
Você tem duas opções para criar simultaneamente várias VMs do mesmo modelo:

- Você pode usar a [extensão de tarefas do Azure DevOps Services](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Você pode [gerar um modelo de Gerenciador de recursos](devtest-lab-add-vm.md#save-azure-resource-manager-template) enquanto cria uma VM e implantar o modelo resource manager do Windows [PowerShell](../azure-resource-manager/templates/deploy-powershell.md).
- Você também pode especificar mais de uma instância de uma máquina a ser criada durante a criação de máquinas virtuais. Para saber mais sobre como criar várias instâncias de máquinas virtuais, consulte o doutor sobre [a criação de uma máquina virtual de laboratório](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Como mover minhas VMs do Azure existentes para meu laboratório do DevTest Labs?
Para copiar suas VMs existentes para o DevTest Labs:

1.  Copie o arquivo VHD da VM existente usando um [script do Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Crie a [imagem personalizada](devtest-lab-create-template.md) dentro do laboratório DevTest Labs.
3.  Crie uma VM no laboratório com sua imagem personalizada.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Posso anexar vários discos às minhas VMs?

Sim, você pode anexar vários discos a suas VMs.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Se eu quiser usar uma imagem do sistema operacional Windows para o meu teste, preciso comprar uma assinatura do MSDN?
Para usar as imagens do Sistema Operacional do Cliente Windows (Windows 7 ou uma versão posterior) para o seu desenvolvimento ou teste no Azure, tome uma das seguintes etapas:

- [Comprar uma assinatura do MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Se você tiver um Contrato Enterprise, crie uma assinatura do Azure com a [oferta de Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p).

Para obter mais informações sobre os créditos Azure para cada oferta do MSDN, consulte [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como posso automatizar o processo de exclusão de todas as VMs em meu laboratório?
Como dono de laboratório, você pode excluir VMs do seu laboratório no portal Azure. Você também pode excluir todas as VMs no laboratório usando um script do PowerShell. No exemplo a seguir, os **valores para alterar** o comentário, modifique os valores do parâmetro. Você pode recuperar os valores de SubscriptionID, labResourceGroup e labName do painel de laboratório no portal Azure.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Ambientes

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Como posso usar modelos do Resource Manager no meu ambiente de Laboratórios de Desenvolvimento/Teste?
Você implanta seus modelos de Gerenciador de recursos em um ambiente DevTest Labs usando etapas mencionadas no recurso Ambientes no artigo [DovTest Labs.](devtest-lab-test-env.md) Basicamente, você verifica seus modelos do Gerenciador de Recursos em um Repositório Git (Azure Repos ou GitHub) e adiciona um [repositório privado para seus modelos](devtest-lab-test-env.md) ao laboratório. Este cenário pode não ser útil se você estiver usando o DevTest Labs para hospedar máquinas de desenvolvimento, mas pode ser útil se você estiver construindo um ambiente de preparação, que é representativo da produção.

Também vale a pena notar que o número de máquinas virtuais por laboratório ou por opção de usuário limita apenas o número de máquinas criadas nativamente no próprio laboratório, e não por quaisquer ambientes (modelos de Gerenciador de Recursos).

## <a name="custom-images"></a>Imagens personalizadas

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Como posso configurar um processo facilmente repetível para trazer minhas imagens organizacionais personalizadas para um ambiente do DevTest Labs?
Veja este [vídeo no padrão Image Factory](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Esse cenário é um cenário avançado e os scripts fornecidos são apenas scripts de exemplo. Caso alguma alteração seja necessária, você deve gerenciar e manter os scripts usados em seu ambiente.

Para obter informações detalhadas sobre a criação de uma fábrica de imagens, consulte [Criar uma fábrica de imagens personalizada no Azure DevTest Labs](image-factory-create.md).

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Qual é a diferença entre uma imagem personalizada e uma fórmula?
Uma imagem personalizada é uma imagem gerenciada. Uma fórmula é uma imagem que você pode definir com configurações adicionais e, em seguida, salvar e reproduzir. Uma imagem personalizada poderá ser preferível se você quer criar rapidamente vários ambientes usando a mesma imagem básica e imutável. Uma fórmula pode ser melhor se você deseja reproduzir a configuração da VM de bits mais recentes, como parte de uma sub-rede ou rede virtual, ou como uma VM de um tamanho específico. Para uma explicação mais detalhada, veja [Comparando imagens personalizadas e fórmulas no DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Quando devo usar uma fórmula versus a imagem personalizada?
Normalmente, o fator decisivo neste cenário é o custo e a reutilização. Se você tem um cenário onde muitos usuários/laboratórios requerem uma imagem com um monte de software em cima da imagem base, então você pode reduzir custos criando uma imagem personalizada. Significa que a imagem é criada uma vez. Ela reduz o tempo de configuração da máquina virtual e o custo incorrido uma vez que a máquina virtual encontra-se em execução durante a instalação.

No entanto, um fator adicional a observar é a frequência das alterações ao seu pacote de software. Se você executa compilações diariamente e necessita que o software esteja nas máquinas virtuais dos seus usuários, considere usar uma fórmula em vez de uma imagem personalizada.

Para obter uma explicação mais detalhada, consulte [Comparar imagens e fórmulas personalizadas](devtest-lab-comparing-vm-base-image-types.md) no DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como automatizar o processo de carregamento de arquivos VHD para criar imagens personalizadas?

Para automatizar o upload de arquivos VHD para criar imagens personalizadas, você tem duas opções:

- Usar o [AzCopy](../storage/common/storage-use-azcopy-v10.md) para copiar ou carregar arquivos VHD na conta de armazenamento associada ao laboratório.
- Use [o Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). O Gerenciador de Armazenamento é um aplicativo autônomo que é executado no Windows, no OS X e no Linux.

Para localizar a conta de armazenamento de destino associada a seu laboratório:

1.  Faça login no [portal Azure](https://portal.azure.com).
2.  Selecione **Grupos de Recursos** no menu esquerdo.
3.  Localize e selecione o grupo de recursos associado ao seu laboratório.
4.  Em **Visão geral**, selecione uma das contas de armazenamento.
5.  Selecione **Blobs**.
6.  Procure os carregamentos na lista. Se não houver nenhuma, retorne para a etapa 4 e tente escolher outra conta de armazenamento.
7.  Use a **URL** como o destino no comando AzCopy.

Quando devo usar uma imagem do Microsoft Azure Marketplace versus minha própria imagem organizacional personalizada?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Quando devo usar uma imagem do Microsoft Azure Marketplace versus minha própria imagem organizacional personalizada?
O Microsoft Azure Marketplace deve ser usado por padrão, a menos que você tenha preocupações específicas ou requisitos organizacionais. Alguns exemplos comuns incluem:

- Configuração de softwares complexos que requerem um aplicativo para serem incluídos como parte da imagem base.
- A instalação e a configuração de um aplicativo podem levar muitas horas, o que não é um uso eficiente do tempo de computação para ser adicionado em uma imagem do Azure Marketplace.
- Desenvolvedores e testadores necessitam de acesso rápido a uma máquina virtual e querem minimizar o tempo de configuração de uma nova máquina virtual.
- Condições de conformidade ou regulamentares (por exemplo, políticas de segurança) que devem estar em vigor para todas as máquinas.
- O uso de imagens personalizadas não deve ser considerado de ânimo leve. Eles introduzem complexidade extra, como você agora deve gerenciar arquivos VHD para essas imagens base subjacentes. Também será preciso aplicar patches frequentemente nas imagens base com atualizações de software. Essas atualizações incluem novas atualizações do sistema operacional (SO) e todas as alterações de atualizações ou configurações necessárias para o pacote de software.

## <a name="artifacts"></a>Artefatos

### <a name="what-are-artifacts"></a>O que são os artefatos?
Artefatos são elementos personalizáveis que podem ser usados para implantar os bits mais recentes ou as ferramentas de desenvolvimento em uma VM. Anexe artefatos à VM quando criá-la. Depois que a VM é provisionada, os artefatos a implantam e configuram. Vários artefatos pré-existentes estão disponíveis em nosso [repositório público do GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) Você também pode [criar seus próprios artefatos](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Meu artefato falhou durante a criação da VM. Como solucionar isso?
Para saber como obter logs relacionados ao artefato com falha, veja [Como diagnosticar falhas de artefato no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Quando uma organização deve usar um repositório de artefatos público ou usar um repositório de artefatos privado no DevTest Labs?
O [repositório de artefatos público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) fornece um conjunto inicial de pacotes de software que são mais comumente usados. Ele ajuda na implantação rápida sem ter que investir tempo para reproduzir ferramentas e complementos comuns de desenvolvedores. Você pode optar por implantar seu próprio repositório privado. Você pode usar um repositório público e um privado em paralelo. Você também pode optar por desabilitar o repositório público. Os critérios para implantar um repositório privado devem ser orientados pelas seguintes perguntas e considerações:

- A organização tem um requisito de ter software corporativo licenciado como parte de sua oferta do DevTest Labs? Se a resposta for sim, um repositório privado deverá ser criado.
- A organização desenvolve software personalizado que fornece uma operação específica, que é exigida como parte do processo geral de provisionamento? Se a resposta for sim, um repositório privado deverá ser implantado.
- Se a política de governança da organização exigir isolamento e os repositórios externos não estarem gerenciamento direto de configuração pela organização, um repositório de artefatos privados deve ser implantado. Como parte desse processo, uma cópia inicial do repositório público pode ser copiada e integrada com o repositório privado. Em seguida, o repositório público pode ser desabilitado para que ninguém na organização possa continuar acessando-o. Essa abordagem força todos os usuários dentro da organização a ter apenas um único repositório que é aprovado pela organização e minimizar os descompassos de configuração.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Uma organização deve planejar ter um único repositório ou permitir vários repositórios?
Como parte da estratégia de gerenciamento de configuração e de governança de geral da sua organização, é recomendável que você use um repositório centralizado. Quando você usa vários repositórios, eles podem se tornar silos de software não gerenciado ao longo do tempo. Com um repositório central, várias equipes podem consumir artefatos desse repositório para seus projetos. Ele impõe a padronização, a segurança, a facilidade de gerenciamento e elimina a duplicação de esforços. Como parte a centralização, as seguintes ações são práticas recomendadas para gerenciamento a longo prazo e de sustentabilidade:

- Associe o Azure Repos ao mesmo locatário do Azure Active Directory que a assinatura do Azure está usando para autenticação e autorização.
- Crie um `All DevTest Labs Developers` grupo nomeado no Azure Active Directory que seja gerenciado centralmente. Qualquer desenvolvedor que contribui para o desenvolvimento de artefatos deve ser colocado nesse grupo.
- O mesmo grupo do Azure Active Directory pode ser usado para fornecer acesso ao repositório do Azure Repos e ao laboratório.
- No Azure Repos, a ramificação ou bifurcação deve ser usada para um repositório em desenvolvimento separado do repositório de produção primário. O conteúdo só é adicionado ao branch mestre com uma solicitação de pull após uma revisão de código apropriada. Depois que o revisor de código aprova a alteração, um desenvolvedor-chefe, que é responsável pela manutenção do branch mestre, mescla o código atualizado.

## <a name="cicd-integration"></a>Integração CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>O DevTest Labs se integra à minha cadeia de ferramentas de CI/CD?
Se você estiver usando o Azure DevOps, você pode usar uma [extensão DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) para automatizar seu pipeline de liberação no DevTest Labs. Algumas das tarefas que você pode fazer com essa extensão incluem:

- Criar e implantar uma VM automaticamente. Você também pode configurar a VM com a versão mais recente usando tarefas do Azure File Copy ou do PowerShell Azure DevOps Services.
- Capture o estado de uma VM automaticamente após os testes para reproduzir um bug na mesma VM a fim de investigar melhor.
- Exclua a VM no final do pipeline de liberação, quando não for mais necessário.

As postagens de blog a seguir oferecem diretrizes e informações sobre como usar a extensão do Azure DevOps Services:

- [Laboratórios de desenvolvimento/teste e a extensão de DevOps do Azure](integrate-environments-devops-pipeline.md)
- [Implantar uma nova máquina virtual em um laboratório do DevTest Labs existente Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Usando o gerenciamento de versão do Azure DevOps Services para implantações contínuas no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Para outras cadeias de ferramentas de CI (integração contínua)/CD (entrega contínua), você pode obter os mesmos cenários implantando [modelos do Azure Resource Manager](https://azure.microsoft.com/resources/templates/) usando [cmdlets do Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [SDKs do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Você também pode usar [APIs REST para DevTest Labs](https://aka.ms/dtlrestapis) a fim de integrar sua cadeia de ferramentas.

## <a name="networking"></a>Rede

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Quando devo criar uma nova rede virtual para meu ambiente de Laboratórios de Desenvolvimento/Teste versus o uso de uma rede virtual existente?
Se suas VMs precisarem interagir com a infra-estrutura existente, então considere usar uma rede virtual existente dentro do ambiente DevTest Labs. Se você usar o ExpressRoute, você pode querer minimizar a quantidade de VNets / Subnets para que você não fragmente o espaço de endereço IP que é atribuído para uso nas assinaturas.

Considere usar o padrão de peering VNet aqui[(modelo Hub-Spoke)](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)também. Essa abordagem permite a comunicação vnet/sub-net entre assinaturas. Caso contrário, cada ambiente de Laboratórios de Desenvolvimento/Teste pode ter sua própria rede virtual.

Há [limites](../azure-resource-manager/management/azure-subscription-service-limits.md) no número de redes virtuais por assinatura. O valor padrão é 50, embora esse limite possa ser aumentado para 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Quando devo usar um IP compartilhado, um IP público ou um IP privado?

Se você usar uma VPN site a site ou Rota Expressa, considere o uso de IPs privados para que suas máquinas sejam acessíveis por meio de sua rede interna e inacessíveis pela internet pública.

> [!NOTE]
> Os proprietários de laboratórios podem alterar essa política de sub-rede para garantir que ninguém acidentalmente crie endereços IP públicos para suas VMs. O proprietário da assinatura deve criar uma política de assinatura impedindo a criação de IPs públicos.

Ao usar IPs públicos compartilhados, as máquinas virtuais em um laboratório compartilham um endereço IP público. Essa abordagem pode ser útil quando você precisa evitar que violem os limites em endereços IP públicos para uma determinada assinatura.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Como faço para garantir que máquinas virtuais de desenvolvimento e teste não tenham acesso à internet pública? Há padrões recomendados para definir a configuração de rede?

Sim. Há dois aspectos a considerar: tráfego de entrada e de saída.

- **Tráfego de entrada** – Se a máquina virtual não tiver um endereço IP público, ele não poderá ser alcançado pela internet. Uma abordagem comum é garantir que uma política de nível de assinatura seja definida, de forma que nenhum usuário possa criar um endereço IP público.
- **Tráfego de saída** – Se você quiser impedir que máquinas virtuais acessem a internet pública diretamente e force maqueo o tráfego através de um firewall corporativo, então você pode direcionar o tráfego no local via via rota expressa ou VPN, usando roteamento forçado.

> [!NOTE]
> Se você tiver um servidor proxy que bloqueia o tráfego sem configurações de proxy, não se esqueça de adicionar exceções à conta de armazenamento de artefatos do laboratório.

Você também pode usar grupos de segurança de rede para máquinas virtuais ou sub-redes. Esta etapa adiciona uma camada adicional de proteção para permitir/bloquear o tráfego.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Por que a minha rede virtual existente não está salvando corretamente?
Uma das possibilidades é que o nome da rede virtual contém pontos. Nesse caso, tente remover os pontos ou substituí-los por hifens. Em seguida, tente novamente salvar a rede virtual.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Por que recebo um erro "Recurso pai não encontrado" ao provisionar uma VM do PowerShell?
Quando um recurso é pai de outro recurso, o recurso pai deve existir antes de você criar o recurso filho. Se o recurso pai não existir, você verá uma mensagem **ParentResourceNotFound.** Se você não especificar uma dependência no recurso pai, o recurso filho poderá obter implantado antes do pai.

As VMs são recursos filhos em um laboratório em um grupo de recursos. Quando você usa modelos do Resource Manager para implantar VMs pelo uso do PowerShell, o nome do grupo de recursos fornecido no script do PowerShell deve ser o nome do grupo de recursos do laboratório. Para obter mais informações, consulte [Solução de erros comuns de implantação do Azure](../azure-resource-manager/templates/common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Onde posso encontrar mais informações sobre erros se há falha na implantação de uma VM?
Os erros de implantação de VM são capturados nos logs de atividades. Você pode encontrar registros de atividades de VM de laboratório em **registros de auditoria** ou **diagnósticos de máquinas virtuais** no menu de recursos na página VM do laboratório (a página aparece depois de selecionar a VM na lista Minhas máquinas virtuais).

Às vezes, o erro de implantação ocorre antes do início da implantação da VM. Um exemplo é quando o limite de assinatura para um recurso que foi criado com a VM é excedido. Nesse caso, os detalhes do erro são capturados nos logs de atividades de nível de laboratório. Os logs de atividades estão localizados na parte inferior das configurações **Políticas e configurações**. Para obter mais informações sobre como usar os logs de atividades no Azure, consulte [Exibir logs de atividades para auditar ações em recursos](../azure-resource-manager/management/view-activity-logs.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Por que eu recebo erro de "localização não está disponível para tipo de recurso" ao tentar criar um laboratório?
Você pode ver uma mensagem de erro semelhante à seguinte quando você tenta criar um laboratório:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Você pode resolver esse erro tomando uma das seguintes etapas:

#### <a name="option-1"></a>Opção 1
Verifique a disponibilidade do tipo de recurso nas regiões do Azure na [página Produtos disponíveis pela](https://azure.microsoft.com/global-infrastructure/services/) região. Se o tipo de recurso não estiver disponível em uma determinada região, o DevTest Labs não suporta a criação de um laboratório nessa região. Selecione outra região ao criar seu laboratório.

#### <a name="option-2"></a>Opção 2
Se o tipo de recurso estiver disponível em sua região, verifique se ele está registrado com sua assinatura. Pode ser feito no nível do proprietário da assinatura, conforme mostrado [neste artigo](../azure-resource-manager/management/resource-providers-and-types.md).
