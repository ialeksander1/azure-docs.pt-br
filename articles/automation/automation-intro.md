---
title: Visão geral da Automação do Azure
description: Saiba como usar a Automação do Azure para automatizar o ciclo de vida de infraestrutura e de aplicativos.
services: automation
ms.subservice: process-automation
keywords: azure automation, DSC, powershell, state configuration, update management, change tracking, DSC, inventory, runbooks, python, graphical
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010232"
---
# <a name="an-introduction-to-azure-automation"></a>Uma introdução à Automação do Azure

Este artigo fornece uma visão geral da Automação do Azure e responde algumas perguntas comuns. Para obter mais informações sobre os diferentes recursos, acesse os links em toda esta visão geral.

## <a name="about-azure-automation"></a>Sobre a Automação do Azure

A Automação do Azure oferece um serviço de configuração e de automação baseado em nuvem que compatível com o gerenciamento consistente em seus ambientes Azure e não Azure. Ela é composta pela automação de processos, pelo gerenciamento de configuração, pelo gerenciamento de atualizações, por funcionalidades compartilhadas e por recursos heterogêneos. A Automação oferece controle completo durante a implantação, operações e o encerramento de cargas de trabalho e de recursos.

![Funcionalidades da Automação](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automação de processos

A automação de processos na Automação do Azure permite automatizar tarefas de gerenciamento de nuvem frequentes, demoradas e propensas a erros. Esse serviço ajuda você a se concentrar em trabalhos que agregam valor. Ao reduzir erros e melhorar a eficiência, também ajuda a diminuir os custos operacionais. O ambiente operacional da automação de processos é detalhado na [Execução de runbook na Automação do Azure](automation-runbook-execution.md).

A automação de processos dá suporte à integração de serviços do Azure e outros sistemas públicos necessários para implantar, configurar e gerenciar seus processos de ponta a ponta. O serviço permite criar [runbooks](automation-runbook-types.md) graficamente, no PowerShell ou usando o Python. Usando um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), é possível unificar o gerenciamento por meio da orquestração entre ambientes locais. [Webhooks](automation-webhooks.md) permitem que você atenda a solicitações e garanta a entrega e operações contínuas disparando automação do ITSM, DevOps e sistemas de monitoramento. 

## <a name="configuration-management"></a>Gerenciamento de configuração

A [configuração de estado](automation-dsc-overview.md) da Automação do Azure é uma solução baseada em nuvem para DSC (Desired State Configuration) do PowerShell que fornece serviços necessários para ambientes empresariais. Usando esse recurso, é possível gerenciar recursos de DSC na Automação do Azure e aplicar as configurações a máquinas virtuais ou físicas de um servidor de pull de DSC na nuvem do Azure. É possível monitorar e atualizar automaticamente as configurações do computador em máquinas virtuais e físicas, no Windows ou Linux, na nuvem ou locais. O suporte a inventário permite consultar recursos no convidado para obter visibilidade sobre aplicativos instalados e outros itens de configuração.
 
O serviço de State Configuration da Automação do Azure fornece funcionalidades avançadas de relatórios e pesquisa. É possível usar esses recursos para localizar informações detalhadas sobre o que é configurado dentro de um sistema operacional. O serviço dá suporte ao controle de alterações entre serviços, daemons, software, Registro e arquivos em seu ambiente para ajudar a diagnosticar alterações indesejadas e a gerar alertas. Um recurso relacionado importante é o relatório de eventos principais, por exemplo, eventos emitidos quando nós se desviam das configurações atribuídas a eles. 

## <a name="update-management"></a>Gerenciamento de atualizações

A Automação do Azure inclui a solução de [gerenciamento de atualizações](automation-update-management.md) para sistemas Windows e Linux em ambientes híbridos. Com essa solução, você obtém visibilidade sobre a conformidade de atualizações local e no Azure e em outras nuvens. O gerenciamento de atualizações permite criar implantações agendadas para orquestrar a instalação de atualizações em uma janela de manutenção definida. Se uma atualização não deve ser instalada em um computador, você pode usar os recursos de gerenciamento de atualizações para excluí-la de uma implantação.

## <a name="shared-capabilities"></a>Funcionalidades compartilhadas

A Automação do Azure oferece uma série de funcionalidades compartilhadas, incluindo recursos compartilhados, controle de acesso baseado em função, agendamento flexível, integração de controle do código-fonte, auditoria e marcação.

### <a name="shared-resources"></a><a name="shared-resources"></a>Recursos compartilhados

A Automação do Azure é composta por um conjunto de recursos compartilhados que facilitam a automatização e configurar seu ambiente em escala.

* **[Agendas](automation-schedules.md)** – disparam operações da Automação em horários predefinidos.
* **[Módulos](automation-integration-modules.md)** – gerenciam o Azure e outros sistemas. É possível importar módulos na conta de Automação da Microsoft, de terceiros, da comunidade ou de cmdlets definidos personalizados e recursos de DSC.
* **[Galeria de módulos](automation-runbook-gallery.md)** – dá suporte à integração nativa à Galeria do PowerShell para exibir runbooks e importá-los para a conta de Automação. Com a galeria, é possível começar rapidamente a integrar e criar seus processos da galeria do PowerShell e do Microsoft Script Center.
* **[Pacotes do Python 2](python-packages.md)** – dão suporte a runbooks do Python 2 para sua conta de Automação.
* **[Credenciais](automation-credentials.md)** – armazenam com segurança informações confidenciais que podem ser usadas por runbooks e configurações em runtime.
* **[Conexões](automation-connections.md)** – armazenam pares nome-valor de informações comuns para conexões com sistemas. O autor do módulo define conexões em runbooks e configurações para uso em runtime.
* **[Certificados](automation-certificates.md)** – definem informações a serem usadas na autenticação e na proteção de recursos implantados quando acessados por runbooks ou configurações de DSC em runtime. 
* **[Variáveis](automation-variables.md)** – mantêm o conteúdo que pode ser usado em runbooks e configurações. É possível alterar valores de variável sem precisar modificar nenhum runbook ou nenhuma configuração que os referencia.

### <a name="role-based-access-control"></a>Controle de acesso baseado em função

A Automação do Azure dá suporte ao RBAC (controle de acesso baseado em função) para regular o acesso à conta de Automação e aos recursos dela. Para saber mais sobre como configurar o RBAC na conta, nos runbooks e nos trabalhos de Automação, confira [Controle de acesso baseado em função para a Automação do Azure](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integração de controle do código-fonte

A Automação do Azure permite a [integração do controle do código-fonte](source-control-integration.md). Esse recurso promove a configuração como código em que o check-in de runbooks ou configurações pode ser feito em um sistema de controle do código-fonte.

## <a name="heterogeneous-support-windows-and-linux"></a>Suporte heterogêneo (Windows e Linux)

A Automação foi criada para funcionar em seu ambiente de nuvem híbrida e também em sistemas Windows e Linux. Ela oferece uma forma consistente de automatizar e configurar cargas de trabalho implantadas e os sistemas operacionais que as executam.

## <a name="common-scenarios-for-automation"></a>Cenários comuns de Automação

A Automação do Azure dá suporte ao gerenciamento durante todo o ciclo de vida de sua infraestrutura e de seus aplicativos. Cenários comuns incluem:

* **Escrever runbooks** – crie runbooks do PowerShell, Fluxo de Trabalho do PowerShell, gráficos, Python 2 e DSC em linguagens comuns. 
* **Criar e implantar recursos** – implante máquinas virtuais em um ambiente híbrido usando runbooks e modelos do Azure Resource Manager. Integre-se a ferramentas de desenvolvimento, como Jenkins e Azure DevOps.
* **Configurar VMs** – avalie e configure computadores Windows e Linux com as configurações para a infraestrutura e o aplicativo.
* **Compartilhar conhecimento** – transfira conhecimento para o sistema sobre como a organização oferece e mantém as cargas de trabalho. 
* **Recuperar inventário** – obtenha um inventário completo de recursos implantados para direcionamento, relatórios e conformidade. 
* **Localizar alterações** – identifique alterações que podem causar erros de configuração e aprimore a conformidade operacional.
* **Monitorar** – isole alterações no computador que estão causando problemas e corrija-as ou escalone-as para sistemas de gerenciamento.
* **Proteger** – coloque computadores em quarentena se forem gerados alertas de segurança. Defina requisitos no convidado.
* **Administrar** – configure o RBAC para equipes. Recupere recursos não utilizados.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Preço da Automação

É possível examinar o preço associado à Automação do Azure na página [preços](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma conta de Automação](automation-quickstart-create-account.md)

