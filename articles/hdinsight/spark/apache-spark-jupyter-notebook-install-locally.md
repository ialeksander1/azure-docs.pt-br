---
title: Instalar o Jupyter localmente e conectar-se a um cluster do Spark do Azure HDInsight
description: Saiba mais sobre como instalar o bloco de anotações do Jupyter localmente em seu computador e se conectar a um cluster Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/23/2020
ms.openlocfilehash: d68019a499b81ffbe40ba42e0b3edb7dab562844
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133568"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalar o bloco de anotações do Jupyter em seu computador e conectar-se ao Apache Spark no HDInsight

Neste artigo, você aprende a instalar o Jupyter notebook com os kernels personalizados PySpark (para Python) e Apache Spark (para escalabilidade) com o Spark Magic. Em seguida, conecte o bloco de anotações a um cluster HDInsight.

Há quatro etapas principais envolvidas na instalação do Jupyter e na conexão com o Apache Spark no HDInsight.

* Configure o cluster Spark.
* Instalar Jupyter notebook.
* Instale os kernels PySpark e Spark com a mágica do Spark.
* Configure a mágica do Spark para acessar o cluster Spark no HDInsight.

Para obter mais informações sobre kernels personalizados e a mágica do Spark, consulte [kernels disponíveis para notebooks Jupyter com clusters do Linux Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md). O bloco de anotações local se conecta ao cluster HDInsight.

* Familiaridade com o uso de anotações do Jupyter com Spark no HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar bloco de notas Jupyter em seu computador

Instale o Python antes de instalar o Jupyter notebooks. A [distribuição Anaconda](https://www.anaconda.com/download/) instalará o, o Python e o Jupyter notebook.

Baixe o [instalador do Anaconda](https://www.anaconda.com/download/) para sua plataforma e execute a instalação. Ao executar o assistente de instalação, não deixe de selecionar a opção de adicionar o Anaconda à variável PATH.  Consulte também, [instalando o Jupyter usando o Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Instalar a mágica do Spark

1. Insira um dos comandos abaixo para instalar o Spark Magic. Consulte também a [documentação do sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Versão do cluster | Comando de instalação |
    |---|---|
    |v 3.6 e v 3.5 |`pip install sparkmagic==0.13.1`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Verifique `ipywidgets` se o está instalado corretamente executando o seguinte comando:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalar kernels PySpark e Spark

1. Identifique onde `sparkmagic` o é instalado digitando o seguinte comando:

    ```cmd
    pip show sparkmagic
    ```

    Em seguida, altere seu diretório de trabalho para o **local** identificado com o comando acima.

1. No seu novo diretório de trabalho, insira um ou mais dos comandos abaixo para instalar os kernels desejados:

    |Kernel | Comando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Opcional. Digite o comando a seguir para habilitar a extensão do servidor:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurar a mágica do Spark para se conectar ao cluster do HDInsight Spark

Nesta seção, você configura a mágica do Spark que você instalou anteriormente para se conectar a um cluster Apache Spark.

1. Inicie o Shell do Python com o seguinte comando:

    ```cmd
    python
    ```

2. As informações de configuração do Jupyter normalmente são armazenadas no diretório base dos usuários. Digite o seguinte comando para identificar o diretório base e crie uma pasta chamada ** \.sparkmagic**.  O caminho completo será disparado.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Dentro da pasta `.sparkmagic`, crie um arquivo chamado **config. JSON** e adicione o trecho JSON a seguir dentro dele.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Faça as seguintes edições no arquivo:

    |Valor do modelo | Novo valor |
    |---|---|
    |USU|Logon do cluster, o `admin`padrão é.|
    |CLUSTERDNSNAME|Nome do cluster|
    |{BASE64ENCODEDPASSWORD}|Uma senha codificada em base64 para sua senha real.  Você pode gerar uma senha base64 em [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Mantenha se estiver `sparkmagic 0.12.7` usando (clusters v 3.5 e v 3.6).  Se estiver `sparkmagic 0.2.3` usando (clusters v 3.4), substitua `"should_heartbeat": true`por.|

    Você pode ver um arquivo de exemplo completo em [Sample config. JSON](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > As pulsações são enviadas para garantir que as sessões não sejam perdidas. Quando um computador entra em suspensão ou está desligado, a pulsação não é enviada e, como resultado, a sessão é limpa. Para clusters v3.4, se desejar desabilitar esse comportamento, você poderá definir a configuração Livy `livy.server.interactive.heartbeat.timeout` para `0` da interface do usuário do Ambari. Para clusters v3.5, se você não definir a configuração de 3.5 ou acima, a sessão não será excluída.

5. Inicie o Jupyter. Use o comando do prompt de comando a seguir.

    ```cmd
    jupyter notebook
    ```

6. Verifique se você pode usar a mágica do Spark disponível com os kernels. Conclua as etapas a seguir.

    a. Crie um novo bloco de anotações. No canto direito, selecione **novo**. Você deve ver o kernel padrão **Python 2** ou **Python 3** e os kernels que você instalou. Os valores reais podem variar dependendo das suas opções de instalação.  Selecione **PySpark**.

    ![Kernels disponíveis no notebook Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kernels no notebook Jupyter")

    > [!IMPORTANT]  
    > Depois de selecionar **nova** revisão, seu shell para quaisquer erros.  Se você vir o erro `TypeError: __init__() got an unexpected keyword argument 'io_loop'` , talvez esteja ocorrendo um problema conhecido com determinadas versões do tornado.  Nesse caso, pare o kernel e, em seguida, faça o downgrade da instalação do `pip install tornado==4.5.3`tornado com o seguinte comando:.

    b. Execute o snippet de código a seguir.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Se você puder recuperar a saída com êxito, a conexão com o cluster HDInsight será testada.

    Se você quiser atualizar a configuração do bloco de anotações para se conectar a um cluster diferente, atualize o config. JSON com o novo conjunto de valores, conforme mostrado na etapa 3, acima.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que devo instalar o Jupyter no meu computador?

Motivos para instalar o Jupyter em seu computador e, em seguida, conectá-lo a um cluster Apache Spark no HDInsight:

* Fornece a opção de criar seus blocos de anotações localmente, testar seu aplicativo em um cluster em execução e, em seguida, carregar os blocos de anotações no cluster. Para carregar os blocos de anotações no cluster, você pode carregá-los usando o notebook Jupyter que está executando o ou o cluster, ou salvá-los `/HdiNotebooks` na pasta na conta de armazenamento associada ao cluster. Para saber mais sobre como os blocos de notas são armazenados no cluster, confira [Onde os blocos de notas Jupyter são armazenados](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Com os blocos de notas disponíveis localmente, você pode se conectar a clusters Spark diferentes com base nos requisitos do aplicativo.
* É possível usar o GitHub para implementar um sistema de controle de origem e ter o controle de versão para os blocos de notas. Você também pode ter um ambiente colaborativo, onde vários usuários podem trabalhar com o mesmo bloco de notas.
* Você pode trabalhar com blocos de notas localmente sem sequer ter um cluster em operação. É preciso apenas um cluster no qual testar seus blocos de notas, e não para gerenciar manualmente os blocos de notas ou um ambiente de desenvolvimento.
* Pode ser mais fácil configurar seu próprio ambiente de desenvolvimento local do que configurar a instalação do Jupyter no cluster.  Você pode aproveitar todos os softwares que você instalou localmente sem configurar um ou mais clusters remotos.

> [!WARNING]  
> Com o Jupyter instalado no computador local, vários usuários podem executar o mesmo bloco de notas no mesmo cluster Spark ao mesmo tempo. Nessa situação, várias sessões Livy são criadas. Se você tiver um problema e desejar depurá-lo, será uma tarefa complexa rastrear qual sessão Livy pertence a qual usuário.  

## <a name="next-steps"></a>Próximas etapas

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Kernels para o Jupyter Notebook no Apache Spark](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de anotações do Jupyter no Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
