---
title: Personalize os clusters Do Azure HDInsight usando ações de script
description: Adicione componentes personalizados aos clusters HDInsight usando ações de script. As ações de script são scripts Bash que podem ser usados para personalizar a configuração do cluster. Ou adicionar serviços adicionais e utilitários como Hue, Solr ou R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 434d4adb763fd0e0a29c065ce051bfd4fa461180
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770747"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Personalize os clusters Do Azure HDInsight usando ações de script

O Azure HDInsight fornece um método de configuração chamado **script actions** que invocam scripts personalizados para personalizar o cluster. Esses scripts são usados para instalar componentes adicionais e alterar definições de configuração. Ações de script podem ser usadas durante ou após a criação do cluster.

Ações de script também podem ser publicadas no Azure Marketplace como um aplicativo do HDInsight. Para saber mais sobre aplicativos do HDInsight, confira [Publicar um aplicativo do HDInsight no Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Permissões

Para um cluster do HDInsight associado a domínio, há duas permissões Apache Ambari necessárias ao usar ações de script com o cluster:

* **Ambari, ambari. EXECUTE\_\_O COMANDO CUSTOM**. Função de administrador de Ambari tem essa permissão por padrão.
* **O CLUSTER. EXECUTE\_\_O COMANDO CUSTOM**. O administrador de Cluster HDInsight e administrador de Ambari têm essa permissão por padrão.

Para saber mais sobre como trabalhar com permissões com o HDInsight associado a um domínio, confira [Gerenciar clusters do HDInsight com o Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Controle de acesso

Se você não é o proprietário ou administrador da sua assinatura do Azure, sua conta deve ter pelo menos acesso de Colaborador ao grupo de recursos que contém o cluster do HDInsight.

Alguém com pelo menos acesso do Contribuinte à assinatura do Azure deve ter registrado anteriormente o provedor. O registro do provedor acontece quando um usuário com acesso contribuinte à assinatura cria um recurso. Para sem criar um recurso, consulte [registrar um provedor usando REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Saiba mais sobre como trabalhar com o gerenciamento de acesso:

* [Introdução ao gerenciamento de acesso no portal do Azure](../role-based-access-control/overview.md)
* [Usar as atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Noções básicas sobre ações de script

Uma ação de script é Bash script executado em nós em um cluster HDInsight. Estas são as características e os recursos das ações de script:

* Devem estar armazenados em um URI que possa ser acessado do cluster do HDInsight. Estes são os possíveis locais de armazenamento:

    * Para clusters regulares:

      * ADLS Gen1: O serviço principal que o HDInsight usa para acessar o Data Lake Storage deve ter lido acesso ao script. O formato de URI dos scripts armazenados no Data Lake Storage Gen1 é `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Um blob em uma conta do Armazenamento do Azure que seja a conta de armazenamento principal ou adicional para o cluster do HDInsight. O HDInsight recebe acesso a ambos esses tipos de contas de armazenamento durante a criação do cluster.

        > [!IMPORTANT]  
        > Não gire a chave de armazenamento nesta conta do Azure Storage, pois isso fará com que as ações de script subseqüentes com scripts armazenados lá falhem.

      * Um serviço público de compartilhamento de arquivos acessível através de caminhos http://. Exemplos são Azure Blob, GitHub, OneDrive. Para obter exemplos de URIs, confira [Exemplos de ações de script](#example-script-action-scripts).

     * Para clusters com ESP, são suportados os URIs wasb:// ou wasbs:// ou http[s]://.

* Isso pode estar restrito à execução somente em determinados tipos de nó. Por exemplo, nós de cabeçalho ou nós de trabalho.

* Pode ser persistido ou `ad hoc`.

    As ações de script persistentes devem ter um nome exclusivo. Os scripts persistentes são usados para personalizar novos nós de trabalho adicionados ao cluster por meio de operações de escalonamento. Um script persistente também pode aplicar alterações a outro tipo de nó quando ocorrem operações de escalonamento. Um exemplo é um nó de cabeçalho.

    `Ad hoc`scripts não são persistidos. As ações de script usadas durante a criação do cluster são mantidas automaticamente. Eles não são aplicados a nós de trabalho adicionados ao cluster após o script ter sido executado. Então você pode `ad hoc` promover um script para um script persistente ou `ad hoc` rebaixar um script persistente para um script. Os scripts que falham não são persistentes, mesmo que você indique especificamente que eles devem ser.

* Eles podem aceitar parâmetros usados pelo script durante a execução.

* São executados com os privilégios no nível de raiz nos nós de cluster.

* Pode ser usado através do portal Azure, Azure PowerShell, Azure CLI ou HDInsight .NET SDK.

O cluster mantém um histórico de todos os scripts que foram executados. O histórico é útil quando você precisa localizar a ID de um script para operações de promoção ou rebaixamento.

> [!IMPORTANT]  
> Não há nenhuma forma automática de desfazer as alterações feitas por uma ação de script. Reverta manualmente as alterações ou forneça um script que as reverta.

### <a name="script-action-in-the-cluster-creation-process"></a>Ação de script no processo de criação do cluster

As ações de script usadas durante a criação do cluster são ligeiramente diferentes das ações de script executadas em um cluster existente:

* O script é automaticamente persistente.

* Uma falha no script pode causar falha no processo de criação do cluster.

O seguinte diagrama ilustra quando a ação de script é executada durante o processo de criação:

![Personalização do cluster HDInsight e estágios durante a criação de cluster][img-hdi-cluster-states]

O script é executado enquanto o HDInsight está sendo configurado. O script é executado em paralelo com todos os nós especificados no cluster. Ele é executado com privilégios de raiz nos nós.

Você pode fazer operações como parar e iniciar serviços, incluindo serviços relacionados ao Apache Hadoop. Se você interromper os serviços, certifique-se de que a Ambari e outros serviços relacionados ao Hadoop estejam sendo executados antes que o script termine. Esses serviços necessários determinam a saúde e o estado do cluster enquanto ele está sendo criado.

Durante a criação do cluster, você pode usar várias ações de script simultaneamente. Esses scripts são invocados na ordem em que eles foram especificados.

> [!IMPORTANT]  
> As ações de script devem terminar dentro de 60 minutos, ou eles tempo para fora. Durante o provisionamento de clusters, o script é executado simultaneamente com outros processos de configuração e configuração. A competição por recursos, como tempo de CPU ou largura de banda da rede, pode fazer com que o script demore mais para concluir em comparação ao tempo de conclusão no seu ambiente de desenvolvimento.
>
> Para minimizar o tempo necessário de execução do script, evite tarefas como baixar e compilar aplicativos da fonte. Pré-compile aplicativos e armazene o binário no Armazenamento do Azure.

### <a name="script-action-on-a-running-cluster"></a>Ação de script em um cluster em execução

Uma falha de script em um cluster já em execução não faz com que o cluster mude automaticamente para um estado com falha. Quando um script é concluído, o cluster deve retornar a um estado em execução. Mesmo que o cluster esteja em um estado em execução, o script com falha pode apresentar problemas. Por exemplo, um script pode excluir arquivos necessários para o cluster.

Ações de scripts executados com privilégios de raiz. Certifique-se de entender o que um script faz antes de aplicá-lo ao seu cluster.

Quando você aplica um script a um cluster, o estado do cluster muda de **Em execução** para **Aceito**. Em seguida, ele é alterado para **Configuração do HDInsight** e, por fim, de volta para **Em execução**, no caso de scripts bem-sucedidos. O status do script é registrado no histórico de ação de script. Essa informação indica se o script teve êxito ou falha. Por exemplo, o cmdlet `Get-AzHDInsightScriptActionHistory` do PowerShell exibe o status de um script. Isso retorna informações semelhantes ao seguinte texto:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Se você alterar a senha do usuário (admin) do cluster após sua criação, as ações de script executadas nesse cluster podem falhar. Se houver ações de script persistente direcionadas para nós de trabalho, esses scripts poderão falhar quando você dimensionar o cluster.

## <a name="example-script-action-scripts"></a>Scripts de ação do de exemplo de script

Scripts de ação de script podem ser usados por meio dos utilitários a seguir:

* Portal do Azure
* Azure PowerShell
* CLI do Azure
* SDK do .NET do HDInsight

O HDInsight fornece scripts para instalar os seguintes componentes nos clusters do HDInsight:

| Nome | script |
| --- | --- |
| Adicionar uma conta de Armazenamento do Azure |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Confira [Adicionar outras contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md). |
| Instalar o Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Confira [Instalar e usar o Hue em clusters Hadoop do HDInsight](hdinsight-hadoop-hue-linux.md). |
| Pré-carregar bibliotecas Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Confira [Adicionar bibliotecas Apache Hive personalizadas ao criar seu cluster do HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Ação de script durante a criação de clusters

Esta seção explica as diferentes maneiras de usar ações de script ao criar um cluster do HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Usar uma ação de script durante a criação do cluster no portal do Azure

1. Comece a criar um cluster conforme descrito no [Create Linux clusters no HDInsight usando o portal Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Na guia **Configuração + preços,** selecione **+ Adicione ação de script**.

    ![Ação de script de cluster do portal Azure](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Use a entrada __Selecionar um script__ para escolher um script criado previamente. Para usar um script personalizado, selecione __Personalizado__. Em seguida, forneça o __Nome__ e o __URI do script de bash__ para o seu script.

    ![Adicionar um script no formulário selecionar script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    A tabela a seguir descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecionar um script | Para usar seu próprio script, selecione __Personalizado__. Caso contrário, selecione um dos scripts fornecidos. |
    | Nome |Especifique um nome para a ação de script. |
    | URI do script Bash |Especificar o URI do script. |
    | Chefe/Trabalhador/Zookeeper |Especifique os nós nos quais o script é executado: **Cabeça**, **Trabalhador**ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se exigido pelo script. |

    Use a entrada __Persista essa ação de script__ para garantir que o script seja aplicado durante operações de escalonamento.

1. Selecione __Criar__ para salvar o script. Posteriormente, você pode usar __+ Enviar novo__ para adicionar outro script.

    ![HDInsight múltiplas ações de script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Quando terminar de adicionar scripts, você retorna à guia **Configuração + preços.**

1. Complete as etapas restantes de criação de cluster, como de costume.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Usar uma ação de script em modelos do Azure Resource Manager

Ações de script podem ser usadas com modelos do Azure Resource Manager. Por exemplo, confira [Criar cluster Linux do HDInsight e executar uma ação de script](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

Neste exemplo, a ação de script é adicionada usando o seguinte código:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Saiba mais sobre como implantar um modelo:

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Usar uma ação de script durante a criação do cluster no Azure PowerShell

Nesta seção, você usa o [cmdlet Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) para invocar scripts para personalizar um cluster. Antes de começar, instale e configure o Azure PowerShell. Para usar esses comandos PowerShell, você precisa do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview).

O seguinte script mostra como aplicar uma ação de script ao criar um cluster usando o PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Pode levar alguns minutos até que o cluster seja criado.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Usar uma ação de script durante a criação do cluster no SDK do .NET do HDInsight

O SDK .NET do HDInsight fornece bibliotecas de cliente que facilitam o trabalho com o HDInsight por meio de um aplicativo .NET. Para obter uma amostra de código, consulte [Ações de script](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Ação de script para um cluster em execução

Esta seção explica como aplicar ações de script a um cluster em execução.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Aplicar uma ação de script em um cluster em execução no portal do Azure

1. Faça login no [portal Azure](https://portal.azure.com) e localize seu cluster.

1. Na exibição padrão, sob **Configurações**, selecione **Ações de script**.

1. Na parte superior da página **Ações de script**, selecione **+ Enviar novas**.

    ![Adicionar um script a um cluster em execução](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Use a entrada __Selecionar um script__ para escolher um script criado previamente. Para usar um script personalizado, selecione __Personalizado__. Em seguida, forneça o __Nome__ e o __URI do script de bash__ para o seu script.

    ![Adicionar um script no formulário selecionar script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    A tabela a seguir descreve os elementos no formulário:

    | Propriedade | Valor |
    | --- | --- |
    | Selecionar um script | Para usar seu próprio script, selecione __personalizado__. Caso contrário, selecione um script fornecido. |
    | Nome |Especifique um nome para a ação de script. |
    | URI do script Bash |Especificar o URI do script. |
    | Cabeçalho/Trabalho/Zookeeper |Especifique os nós nos quais o script é executado: **Cabeça**, **Trabalhador**ou **ZooKeeper**. |
    | Parâmetros |Especifique os parâmetros, se exigido pelo script. |

    Use a entrada __Persistir essa ação de script__ para garantir que o script seja aplicado durante operações de colocação em escala.

1. Por fim, selecione o botão **Criar** para aplicar o script ao cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Aplicar uma ação de script em um cluster em execução no Azure PowerShell

Para usar esses comandos PowerShell, você precisa do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview). O seguinte exemplo mostra como aplicar uma ação de script a um cluster em execução:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Após a conclusão da operação, você receberá informações semelhantes ao seguinte texto:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Aplicar uma ação de script em um cluster em execução da CLI do Azure

Antes de começar, instale e configure a CLI do Azure. Certifique-se de ter a versão mais recente. Para obter mais informações, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Autentique-se na assinatura do Azure:

    ```azurecli
    az login
    ```

1. Aplique uma ação de script a um cluster em execução:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Os papéis `workernode` `zookeepernode`válidos são, `edgenode` `headnode`, , . Se o script deve ser aplicado a vários tipos de nó, separe as funções por um espaço. Por exemplo, `--roles headnode workernode`.

    Para persistir o script, adicione `--persist-on-success`. Também é possível persistir o script posteriormente usando `az hdinsight script-action promote`.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Aplicar uma ação de script a um cluster em execução usando a API REST

Confira [API REST de cluster no Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Aplicar uma ação de script a um cluster em execução no SDK do .NET do HDInsight

Para obter um exemplo de como usar o SDK .NET para aplicar scripts a um cluster, confira [Aplicar uma ação de script a um cluster do HDInsight baseado em Linux em execução](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Exibir o histórico, promover e rebaixar ações de script

### <a name="the-azure-portal"></a>O Portal do Azure

1. Faça login no [portal Azure](https://portal.azure.com) e localize seu cluster.

1. Na exibição padrão, sob **Configurações**, selecione **Ações de script**.

1. Um histórico de scripts para esse cluster é exibido na seção de ações de script. Essas informações incluem uma lista de scripts persistentes. A captura de tela a seguir mostra que o script Solr foi executado nesse cluster. A captura de tela não mostra scripts persistentes.

    ![Ações de script do portal enviam histórico](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Selecione um script do histórico para exibir a seção **Propriedades** desse script. Na parte superior da tela, é possível executar novamente o script ou promovê-lo.

    ![As propriedades de ações de script promovem](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Você também pode selecionar a elipse, **...**... à direita de entradas na seção de ações de script para fazer ações.

    ![Ações de script persistidas excluem](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Função |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Recuperar informações sobre as ações de script persistentes. Este cmdlet não desfaz as ações feitas por um script, apenas remove a bandeira persistida.|
| `Get-AzHDInsightScriptActionHistory` |Recuperar um histórico das ações de script aplicadas no cluster ou detalhes de um script específico. |
| `Set-AzHDInsightPersistedScriptAction` |Promova `ad hoc` uma ação de script para uma ação de script persistente. |
| `Remove-AzHDInsightPersistedScriptAction` |Rebaixar uma ação de script `ad hoc` persistente para uma ação. |

O exemplo de script a seguir demonstra como usar os cmdlets para promover e depois rebaixar um script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>CLI do Azure

| Comando | Descrição |
| --- | --- |
| [`az hdinsight script-action delete`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Exclui uma ação de script persistida especificada do cluster. Este comando não desfaz as ações feitas por um script, apenas remove a bandeira persistida.|
|[`az hdinsight script-action execute`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Execute ações de script no cluster HDInsight especificado.|
| [`az hdinsight script-action list`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Lista todas as ações de script persistidas para o cluster especificado. |
|[`az hdinsight script-action list-execution-history`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Lista o histórico de execução de todos os scripts para o cluster especificado.|
|[`az hdinsight script-action promote`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Promove a execução de script ad-hoc especificada para um script persistido.|
|[`az hdinsight script-action show-execution-details`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Obtém os detalhes de execução do script para o iD de execução de script dado.|

### <a name="hdinsight-net-sdk"></a>SDK do .NET do HDInsight

Para obter um exemplo de como usar o SDK .NET para recuperar o histórico de scripts de um cluster, promover ou rebaixar scripts, confira [Aplicar uma ação de script em um cluster do HDInsight baseado em Linux em execução](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Este exemplo também demonstra como instalar um aplicativo do HDInsight usando o SDK .NET.

## <a name="next-steps"></a>Próximas etapas

* [Desenvolver scripts de ação de script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Acrescentar armazenamento adicional a um cluster HDInsight](hdinsight-hadoop-add-storage.md)
* [Solucionar problemas em ações de script](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Estágios durante a criação de cluster"
