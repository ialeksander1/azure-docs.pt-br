---
title: Afinação automática de notificações de e-mail como orientar
description: Habilite notificações por email para o ajuste automático de consulta do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: b3b235833e794e48ae655d184bf938effc0d7ac0
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768381"
---
# <a name="email-notifications-for-automatic-tuning"></a>Notificações por email para ajuste automático

Recomendações de ajuste do Banco de Dados SQL são geradas pelo [ajuste automático](sql-database-automatic-tuning.md) do Banco de Dados SQL do Azure. Essa solução monitora e analisa continuamente as cargas de trabalho de Bancos de Dados SQL fornecendo recomendações personalizadas para cada banco de dados individual relacionadas à criação de índice, exclusão de índice e otimização dos planos de execução de consulta.

As recomendações de ajuste automático do Banco de Dados SQL podem ser exibidas no [portal do Azure](sql-database-advisor-portal.md), recuperados com chamadas da [API REST](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) ou usando comandos de [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [ PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction). Este artigo se baseia no uso de um script de PowerShell para recuperar as recomendações de ajuste automático.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para obter esses cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizar as notificações por email para as recomendações de ajuste automático

A seguinte solução automatiza o envio de notificações por email que contêm as recomendações de ajuste automático. A solução descrita consiste em automatizar a execução de um script de PowerShell para recuperar as recomendações de ajuste usando a [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) e automação de agendamento de trabalho de entrega de email usando o [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Criar conta de Automação do Azure

Para usar a Automação do Azure, a primeira etapa é criar uma conta de automação e configurá-la com os recursos do Azure a serem usados para a execução do script de PowerShell. Para saber mais sobre a Automação do Azure e seus recursos, consulte [Introdução à Automação do Azure](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Siga estas etapas para criar a conta de Automação do Azure por meio do método de seleção e configuração do aplicativo Automação do Marketplace:

1. Faça logon no portal do Azure.
1. Clique em "**+ Crie um recurso**" no canto superior esquerdo.
1. Procure por "**Automação**" (press enter).
1. Clique no aplicativo Automação nos resultados da pesquisa.

    ![Adicionar Automação do Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

1. Uma vez dentro do painel "Criar uma conta de automação", clique em "**Criar**".
1. Preencha as informações necessárias: digite um nome para esta conta de automação, selecione os recursos de ID de assinatura do Azure e do Azure para serem usados na execução do script PowerShell.
1. Para a opção "**Criar conta azure executar como**", selecione **Sim** para configurar o tipo de conta sob a qual o script PowerShell é executado com a ajuda do Azure Automation. Para saber mais sobre os tipos de conta, consulte [Executar como conta](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
1. Concluir a criação da conta de automação clicando em **Criar**.

> [!TIP]
> Registre o nome da conta de Automação do Azure, ID da assinatura e recursos (como copiar e colar em um bloco de notas) exatamente como digitado ao criar o aplicativo Automação. Você precisará dessas informações mais adiante.
>

Se tiver várias assinaturas do Azure para as quais você deseja criar a mesma automação, você precisa repetir este processo para as outras assinaturas.

## <a name="update-azure-automation-modules"></a>Atualizar Módulos de Automação do Azure

O script PowerShell para recuperar a recomendação de ajuste automático usa os comandos [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) e [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) para os quais a versão 4 e acima do Módulo Azure é necessária.

- Caso os módulos Azure precisem ser atualizados, consulte [o suporte ao módulo Az no Azure Automation](../automation/shared-resources/modules.md).

## <a name="create-azure-automation-runbook"></a>Criar o Runbook de Automação do Azure

A próxima etapa é criar um Runbook na Automação do Azure no qual reside o script de PowerShell para recuperação de recomendações de ajuste.

Siga as etapas a seguir para criar um novo runbook da Automação do Azure:

1. Acesse a conta azure Automação que você criou na etapa anterior.
1. Uma vez no painel da conta de automação, clique no item de menu "**Runbooks**" no lado esquerdo para criar um novo runbook de Automação Do Azure com o script PowerShell. Para saber mais sobre a criação de runbooks de automação, consulte [Criar um novo runbook](../automation/manage-runbooks.md#creating-a-runbook).
1. Para adicionar um novo runbook, clique na opção de menu "**+Add a runbook**" e clique em "**Criar rápido – Crie um novo runbook**"..
1. No painel Runbook, digite o nome do seu runbook (para o propósito deste exemplo, "**AutomaticTuningEmailAutomation**" é usado), selecione o tipo de runbook como **PowerShell** e escreva uma descrição deste runbook para descrever seu propósito.
1. Clique no botão **Criar** para terminar de criar um novo runbook.

    ![Adicionar o Runbook de Automação do Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Siga estas etapas para carregar um script de PowerShell dentro do runbook criado:

1. Dentro do painel "**Editar PowerShell Runbook**", selecione "**RUNBOOKS**" na árvore do menu e expanda a exibição até ver o nome do seu runbook (neste exemplo "**AutomaticTuningEmailAutomation**"). Selecione este runbook.
1. Na primeira linha do "Edit PowerShell Runbook" (começando com o número 1), cole o seguinte código de script PowerShell. Este script do PowerShell é fornecido no estado em que se encontra para você começar. Modifique o script para que se adeque às suas necessidades.

No cabeçalho do script do PowerShell fornecido, você precisa substituir `<SUBSCRIPTION_ID_WITH_DATABASES>` por sua ID de assinatura do Azure. Para saber como recuperar a ID de assinatura do Azure, consulte [Obter a GUID de assinatura do Azure](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

No caso de várias assinaturas, você pode adicioná-las como vírgula delimitada à propriedade "$subscriptions" no cabeçalho do script.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Clique no botão "**Salvar**" no canto superior direito para salvar o script. Quando estiver satisfeito com o script, clique no botão "**Publicar**" para publicar este runbook.

No painel principal do runbook, você pode optar por clicar no botão "**Iniciar**" para **testar** o script. Clique em "**Saída**" para ver os resultados do script executado. Essa saída será o conteúdo do seu email. O exemplo de saída do script pode ser visto na captura de tela a seguir.

![Executar a exibição de recomendações de ajuste automático da Automação do Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Certifique-se de ajustar o conteúdo personalizando o script de PowerShell para suas necessidades.

Com as etapas acima, o script de PowerShell para recuperar as recomendações de ajuste automático são carregadas na Automação do Azure. A próxima etapa é automatizar e agendar o trabalho de entrega de email.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizar os trabalhos de email com o Microsoft Flow

Para concluir a solução, como etapa final, crie um fluxo de automação no Microsoft Flow que consista em três ações (trabalhos):

 - "**Azure Automation - Create job**" – usado para executar o script PowerShell para recuperar recomendações de ajuste automático dentro do runbook de automação do Azure.
 - "**Azure Automation - Obter saída de trabalho**" – usado para recuperar a saída do script PowerShell executado.
 - "**Office 365 Outlook – Envie um e-mail**" – usado para enviar e-mails. Emails são enviados usando a conta do Office 365 da pessoa que está criando o fluxo.

Para saber mais sobre os recursos do Microsoft Flow, consulte [Introdução ao Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

O pré-requisito para essa etapa é se inscrever-se em uma conta do [Microsoft Flow](https://flow.microsoft.com) e fazer logon. Uma vez dentro da solução, siga estas etapas para configurar um **novo fluxo**:

1. Acesse o item do menu "**Meus fluxos**".
1. Dentro dos meus fluxos, selecione o link "**+Create from blank**" na parte superior da página.
1. Clique no link "**Procurar centenas de conectores e gatilhos**" na parte inferior da página.
1. No tipo de campo de pesquisa "**recurrence**", e selecione "**Agenda - Recorrência**" dos resultados da pesquisa para agendar o trabalho de entrega de e-mail para executar.
1. No painel Recorrência no campo Frequência, selecione a frequência de agendamento para este fluxo de execução, como enviar email automatizado a cada Minuto, Hora, Dia, Semana, etc.

A próxima etapa é adicionar três trabalhos (criar, obter saída e enviar email) ao fluxo recorrente recém-criado. Para realizar a adição dos trabalhos necessários para o fluxo, siga estas etapas:

1. Crie uma ação para executar o script de PowerShell para recuperar as recomendações de ajuste

   - Selecione "**+Novo passo**", seguido por "**Adicionar uma ação**" dentro do painel de fluxo de recorrência.
   - No tipo de campo de pesquisa "**automação**" e selecione "**Azure Automation – Crie trabalho**" a partir dos resultados de pesquisa.
   - No painel de trabalho Criar, configure as propriedades do trabalho. Para essa configuração, você precisará de detalhes da sua ID de assinatura do Azure, grupo de recursos e conta de automação **registrada anteriormente** no **painel da conta de automação**. Para saber mais sobre as opções disponíveis nesta seção, consulte [Automação do Azure – criar trabalho](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Complete a criação desta ação clicando em "**Salvar fluxo**".

2. Criar ação para recuperar a saída do script de PowerShell executado

   - Selecione "**+Novo passo**", seguido por "**Adicionar uma ação**" dentro do painel de fluxo de recorrência
   - Na pesquisa filed tipo "**automação**" e selecione "**Azure Automation – Obtenha saída**de emprego " a partir dos resultados da pesquisa. Para saber mais sobre as opções disponíveis nesta seção, consulte [Automação do Azure – obter saída do trabalho](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Preencha os campos necessários (semelhante à criação do trabalho anterior) - preencha seu ID de assinatura do Azure, Grupo de Recursos e Conta de Automação (conforme inserido no painel conta de automação).
   - Clique no campo "**Job ID**" para que o menu "**Dynamic content**" apareça. Dentro deste menu, selecione a opção "**Job ID**".
   - Complete a criação desta ação clicando em "**Salvar fluxo**".

3. Criar ação para enviar email usando a integração do Office 365

   - Selecione "**+Novo passo**", seguido por "**Adicionar uma ação**" dentro do painel de fluxo de recorrência.
   - No tipo de pesquisa arquivada "**envie um e-mail**" e selecione "**Office 365 Outlook – Envie um e-mail**" dos resultados da pesquisa.
   - No tipo de campo "**To**" no endereço de e-mail para o qual você precisa enviar o e-mail de notificação.
   - No tipo de campo "**Assunto**" no assunto do seu e-mail, por exemplo "Notificação automática de recomendações de ajuste".
   - Clique no campo "**Body**" para que o menu "**Dynamic content**" apareça. A partir deste menu, em "**Obter saída de trabalho**", selecione "**Conteúdo**".
   - Complete a criação desta ação clicando em "**Salvar fluxo**".

> [!TIP]
> Para enviar emails automatizados para diferentes destinatários, crie fluxos separados. Nesses fluxos adicionais, altere o endereço de e-mail do destinatário no campo "Para" e a linha de assunto de e-mail no campo "Assunto". Criar novos runbooks na Automação do Azure com scripts de PowerShell personalizados (como na alteração da ID de assinatura do Azure) permite maior personalização de cenários automatizados, como, por exemplo, enviar emails para destinatários separados sobre recomendações de ajuste automatizado para assinaturas separadas.
>

As instruções acima concluem as etapas necessárias para configurar o fluxo de trabalho de entrega de email. O fluxo inteiro que consiste na criação de três ações é mostrado na imagem a seguir.

![Exibir o fluxo de notificações por email para ajuste automático](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Para testar o fluxo, clique em "**Run Now**" no canto superior direito dentro do painel de fluxo.

Estatísticas da execução das tarefas automatizadas, mostrando o êxito de notificações por email enviadas, podem ser vistas no painel de análise do Flow.

![Executar o fluxo de notificações por email para ajuste automático](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

A análise de fluxo é útil para monitorar o êxito das execuções de trabalho e, se necessário, para solução de problemas.  No caso de solução de problemas, também convém examinar o log de execução de script do PowerShell, acessível por meio do aplicativo Automação do Azure.

A saída final do email automatizado é semelhante ao seguinte email recebido depois de criar e executar essa solução:

![Amostra de saída de email de notificações por email para ajuste automático](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Ajustando o script do PowerShell, você pode ajustar a saída e a formatação do email automatizado às suas necessidades.

Você pode personalizar ainda mais a solução para criar notificações por email com base em um evento de ajuste específico e para vários destinatários, várias assinaturas ou bancos de dados, dependendo de seus cenários personalizados.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como o ajuste automático pode ajudar a melhorar o desempenho do banco de dados, consulte [Ajuste automático no Banco de Dados SQL do Azure](sql-database-automatic-tuning.md).
- Para habilitar o ajuste automático no Banco de Dados SQL do Azure para gerenciar a carga de trabalho, consulte [Habilitar ajuste automático](sql-database-automatic-tuning-enable.md).
- Para examinar e aplicar recomendações de Ajuste automático manualmente, consulte [Localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).
