---
title: Excel & Apache Hadoop com Driver de Conectividade de Banco de Dados Aberto (ODBC) - Azure HDInsight
description: Saiba como configurar e usar o driver ODBC do Microsoft Hive para Excel para consultar dados em clusters HDInsight no Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/22/2020
ms.openlocfilehash: 5aefd2c344565bf6dcb384996c42c1bc30e7291d
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024924"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Conecte o Excel ao Apache Hadoop no HDInsight do Azure com o driver ODBC do Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A solução de Big Data da Microsoft integra os componentes do Microsoft Business Intelligence (BI) com os clusters Apache Hadoop implantados no HDInsight. Um exemplo é a capacidade de conectar o Excel ao depósito de dados Hive de um cluster Hadoop. Conecte-se usando o Driver de conectividade de banco de dados aberto da Microsoft Hive (ODBC).

Você pode conectar os dados associados a um cluster HDInsight do Excel com o complemento do Microsoft Power Query para excel. Para obter mais informações, consulte [Conecte o Excel ao HDInsight com a Consulta de Energia](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter os seguintes itens:

* Cluster Hadoop do HDInsight. Para criar um, confira [Introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho com o Office 2010 Professional Plus ou posterior ou o Excel 2010 ou posterior.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o driver ODBC do Microsoft Hive

Baixe e [instale o Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886). Escolha a versão que corresponde à versão do aplicativo onde você estará usando o driver ODBC.  Para este artigo, o driver é usado para o Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Criar fonte de dados ODBC do Apache Hive

As etapas a seguir mostram como criar uma fonte de dados ODBC do Hive.

1. A partir do Windows, navegue para **Iniciar > Ferramentas Administrativas do Windows > Fontes de Dados ODBC (32 bits)/(64 bits)**.  Esta ação abre a janela **Administrador a Origem de Dados oDBC.**

    ![Administrador de fonte de dados ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Configurar um DSN usando o administrador de fonte de dados ODBC")

1. Na guia **DSN de Usuário**, selecione **Adicionar** para abrir a janela **Criar Nova Fonte de Dados**.

1. Selecione **Driver ODBC do Microsoft Hive** e selecione **Concluir** para abrir a janela **Configuração de DNS do Driver ODBC do Microsoft Hive**.

1. Digite ou selecione os valores a seguir:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Fonte de Dados |Forneça um nome para a sua fonte de dados |
   |  Hosts |Digite `HDInsightClusterName.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net`. Nota: `HDInsightClusterName-int.azurehdinsight.net` é suportado desde que a VM do cliente seja espiada para a mesma rede virtual. |
   |  Porta |Use **443**. (Essa porta foi alterada de 563 para 443.) |
   |  Banco de dados |Use **padrão**. |
   |  Mecanismo |Selecione **Serviço do Microsoft Azure HDInsight** |
   |  Nome do Usuário |Insira o nome de usuário HTTP do cluster HDInsight. O nome de usuário padrão é **admin**. |
   |  Senha |Insira a senha do usuário do cluster HDInsight. Marque a caixa de seleção **Salvar Senha (Criptografada)**.|

1. Opcional: Selecione **opções avançadas...**  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Use Consulta Nativa |Quando selecionado, o driver ODBC NÃO tenta converter TSQL em HiveQL. Você deve usá-lo apenas se você tiver 100% de certeza de que está enviando declarações puras de HiveQL. Ao conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure, deixe-a desmarcada. |
   |  Linhas buscadas por bloco |Ao buscar uma grande quantidade de registros, o ajuste desse parâmetro poderá ser necessário para garantir o desempenho ideal. |
   |  Comprimento de coluna de cadeia de caracteres padrão, Comprimento da coluna binária e Escala da coluna decimal |Os tamanhos e as precisões dos tipos de dados podem afetar a maneira como os dados são retornados. Eles fazem com que informações incorretas sejam devolvidas por perda de precisão e, ou truncação. |

    ![Opções de configuração avançada de DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Opções de configuração avançada de DSN")

1. Selecione **Testar** para testar a fonte de dados. Quando a fonte de dados é configurada corretamente, o resultado do teste mostra **SUCESSO!**

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela **Configuração de DNS do Driver ODBC do Microsoft Hive**.  

1. Selecione **OK** para fechar a janela **Administrador de Fonte de Dados ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importar dados do HDInsight para o Excel

As etapas a seguir descrevem a maneira de importar dados de uma tabela Hive em uma pasta de trabalho do Excel usando a fonte de dados ODBC que você criou na seção anterior.

1. Abra uma pasta de trabalho nova ou existente no Excel.

2. Na guia **Dados**, navegue até **Obter Dados** > **De Outras Fontes** > **Do ODBC** para abrir a janela **Do ODBC**.

    ![Abra o assistente de conexão de dados do Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Abra o assistente de conexão de dados do Excel")

3. Na lista de paradas, selecione o nome de origem de dados que você criou na última seção e, em seguida, selecione **OK**.

4. Para o primeiro uso, uma caixa de diálogo **de driver ODBC** será aberta. Selecione **Windows** no menu esquerdo. Em seguida, **selecione Conectar** para abrir a janela **Navegador.**

5. De **Navegador**, navegue até **HIVE** > **padrão** > **hivesampletable** e selecione **Carregar**. Leva alguns minutos para que os dados sejam importados para o Excel.

    ![Navegador HDInsight Excel Hive ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Navegador HDInsight Excel Hive ODBC")

## <a name="next-steps"></a>Próximas etapas

Neste artigo você aprendeu a usar o driver ODBC do Microsoft Hive para recuperar dados do Serviço do HDInsight no Excel. Da mesma forma, você pode recuperar dados do Serviço do HDInsight no Banco de Dados SQL. Também é possível carregar dados em um Serviço HDInsight. Para obter mais informações, consulte:

* [Visualize os dados da Colmeia Apache com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize dados da Colmeia de Consulta Interativa com power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Conecte o Excel ao Apache Hadoop usando o Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
