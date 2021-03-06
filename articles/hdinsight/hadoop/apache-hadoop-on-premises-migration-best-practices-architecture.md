---
title: 'Arquitetura: No local Apache Hadoop para Azure HDInsight'
description: Aprenda as melhores práticas de arquitetura para migrar clusters do Hadoop locais para o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 2d0d5bb871612bc5e16a26eb49808c39661ffb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934680"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrar clusters do Apache Hadoop locais para o Azure HDInsight – melhores práticas de arquitetura

Este artigo fornece recomendações para a arquitetura de sistemas do Azure HDInsight. Ele faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas locais do Apache Hadoop para o Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Usar vários clusters otimizados para carga de trabalho

Muitas implantações do Apache Hadoop locais consistem em um único cluster grande compatível com várias cargas de trabalho. Este cluster único pode ser complexo e pode exigir comprometimentos para os serviços individuais para fazer tudo funcionar junto. A migração de clusters do Hadoop locais para o Azure HDInsight requer uma mudança na abordagem.

Os clusters de Azure HDInsight são projetados para um tipo específico de uso de computação. Como o armazenamento pode ser compartilhado em vários clusters, é possível criar vários clusters de computação otimizados para carga de trabalho para atender às necessidades de diferentes trabalhos. Cada tipo de cluster tem a configuração ideal para essa carga de trabalho específica. A tabela a seguir lista os tipos de cluster compatíveis com o HDInsight e as cargas de trabalho correspondentes.

|Carga de trabalho|Tipo de cluster HDInsight|
|---|---|
|Processamento em lotes (ETL/ELT)|Hadoop, Spark|
|Data warehousing|Hadoop, Spark, Consulta Interativa|
|IoT/Streaming|Kafka, Storm, Spark|
|Processamento transacional do NoSQL|HBase|
|Consultas interativas e mais rápidas com o caching na memória|Consulta Interativa|
|Ciência de dados|ML Services, Spark|

A tabela a seguir mostra os diferentes métodos que podem ser usados para criar um cluster do HDInsight.

|Ferramenta|Baseado em navegador|Linha de Comando|API REST|.|
|---|---|---|---|---|
|[Portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Fábrica de Dados Azure](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[CLI do Azure (versão 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[Onda](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||X|
|[SDK do Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Modelos do Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Para obter mais informações, consulte os tipos de cluster do artigo [no HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Usar clusters de sob demanda transitórios

Os clusters do HDInsight podem ficar sem ser utilizados por longos períodos. Para ajudar a economizar nos custos de recurso, o HDInsight é compatível com clusters transitórios sob demanda, que podem ser excluídos depois que a carga de trabalho é concluída com êxito.

Quando você exclui um cluster, a conta de armazenamento associada e os metadados externos não são removidos. O cluster pode ser recriado posteriormente usando as mesmas contas de armazenamento e repositório de metadados.

O Azure Data Factory pode ser usado para agendar a criação de clusters do HDInsight sob demanda. Para obter mais informações, confira o artigo [Criar clusters Apache Hadoop sob demanda no HDInsight usando o Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Separar o armazenamento da computação

As implantações do Hadoop locais usam o mesmo conjunto de computadores para o armazenamento e para o processamento de dados. Por serem localizados, a computação e o armazenamento devem ser dimensionados juntos.

Nos clusters HDInsight, o armazenamento não precisa ser localizado com computação e pode estar no armazenamento Azure, no Azure Data Lake Storage ou ambos. Desacoplar o armazenamento da computação tem os seguintes benefícios:

- Compartilhamento de dados entre clusters.
- Uso de clusters transitórios, pois os dados não são dependentes do cluster.
- Redução dos custos de armazenamento.
- Dimensionamento do armazenamento e de computação separadamente.
- Replicação de dados entre regiões.

Os clusters de cálculo são criados próximos aos recursos da conta de armazenamento em uma região do Azure para reduzir o custo de desempenho da separação da computação e do armazenamento. As redes de alta velocidade fazem com que seja eficiente para os nós de computação acessar os dados dentro do armazenamento do Azure.

## <a name="use-external-metadata-stores"></a>Usar repositórios de metadados externos

Existem duas metalojas principais que trabalham com clusters HDInsight: [Apache Hive](https://hive.apache.org/) e [Apache Oozie](https://oozie.apache.org/). O metastore do Hive é o repositório de esquema central que pode ser usado por mecanismos de processamento de dados, incluindo Hadoop, Spark, LLAP, Presto e Apache Pig. O metastore do Oozie armazena os detalhes sobre o agendamento e o status de trabalhos do Hadoop em andamento e concluídos.

O HDInsight usa o Banco de Dados SQL do Azure para metastores do Hive e do Oozie. Há duas maneiras de configurar um metastore nos clusters do HDInsight:

1. Metastore padrão

    - Sem custo adicional.
    - O metastore é excluído quando o cluster é excluído.
    - O metastore não pode ser compartilhado entre clusters diferentes.
    - Usa o BD SQL do Azure, que tem um limite de cinco DTU.

1. Metastore externo personalizado

    - Especifique um Banco de Dados SQL do Azure externo como o metastore.
    - Clusters podem ser criados e excluídos sem perder metadados, incluindo detalhes de trabalho do Oozie de esquema do Hive.
    - O banco de dados do metastore único pode ser compartilhado com diferentes tipos de clusters.
    - O metastore pode ser escalado verticalmente conforme necessário.
    - Para obter mais informações, consulte [Usar armazenamentos de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Melhores práticas para metastore do Hive

Algumas melhores práticas do metastore Hive do HDInsight são as seguintes:

- Use um metastore externo personalizado para separar recursos de computação e metadados.
- Inicie com uma Instância do SQL do Azure de camada S2, que fornece 50 DTU e 250 GB de armazenamento. Se você vir um afunilamento, poderá expandir o banco de dados.
- Não compartilhe o metastore criado para uma versão de cluster do HDInsight com clusters de uma versão diferente. Versões diferentes do Hive usam esquemas diferentes. Por exemplo, um metastore não pode ser compartilhado com clusters do Hive 1.2 e do Hive 2.1.
- Faça backup do metastore personalizado periodicamente.
- Mantenha o metastore e o cluster HDInsight na mesma região.
- Monitore o metastore para desempenho e disponibilidade usando as ferramentas de monitoramento de banco de dados Do Azure SQL, como o portal Azure ou os logs do Monitor Azure.
- Execute `ANALYZE TABLE` o comando conforme necessário para gerar estatísticas para tabelas e colunas. Por exemplo, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Melhores práticas para diferentes cargas de trabalho

- Considere usar o cluster LLAP para consultas interativas da Colmeia com tempo de resposta melhorado [LlAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) é um novo recurso na Colmeia 2.0 que permite o cache na memória de consultas. O LLAP acelera as consultas de Hive em até [26 vezes mais rápido do que o Hive 1.x em alguns casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Considere o uso de trabalhos do Spark no lugar de trabalhos do Hive.
- Considere a substituição de consultas com base no impala por consultas LLAP.
- Considere a substituição de trabalhos do MapReduce por trabalhos do Spark.
- Considere a substituição de trabalhos em lotes do Spark de baixa latência usando trabalhos do Streaming estruturado do Spark.
- Considere o uso do Azure Data Factory (ADF) 2.0 para orquestração de dados.
- Considere o Ambari para gerenciamento de cluster.
- Altere o armazenamento de dados do HDFS local para WASB, ADLS ou ADFS para scripts de processamento.
- Considere o uso do Ranger RBAC em tabelas do Hive e auditorias.
- Considere o uso do CosmosDB no lugar do MongoDB ou do Cassandra.

## <a name="next-steps"></a>Próximas etapas

Leia o próximo artigo desta série:

- [Melhores práticas de infraestrutura para migração do local para o Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
