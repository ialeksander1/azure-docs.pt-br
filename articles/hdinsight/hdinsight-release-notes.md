---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: f8331b64792781a89428f8c5070375c31c0da48d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084882"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes corporativos para análise de código aberto no Azure.

## <a name="release-date-01092020"></a>Data de lançamento: 01/09/2020

Esta versão se aplica tanto ao HDInsight 3.6 quanto ao 4.0. A versão HDInsight é disponibilizada para todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se você não ver as mudanças abaixo, por favor, aguarde a liberação sendo ao vivo em sua região em vários dias.

> [!IMPORTANT]  
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, confira o [artigo sobre controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Novos recursos
### <a name="tls-12-enforcement"></a>Imposição do TLS 1.2
Os protocolos TLS e SSL são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores. Saiba mais sobre [o TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). O HDInsight usa o TLS 1.2 em pontos finais de HTTPs públicos, mas o TLS 1.1 ainda é suportado para compatibilidade retrógrada. 

Com esta versão, os clientes podem optar pelo TLS 1.2 apenas para todas as conexões através do ponto final do cluster público. Para suportar isso, a nova propriedade **minSupportedTlsVersion** é introduzida e pode ser especificada durante a criação de clusters. Se a propriedade não for definida, o cluster ainda suporta TLS 1.0, 1.1 e 1.2, o que é o mesmo que o comportamento de hoje. Os clientes podem definir o valor para esta propriedade como "1.2", o que significa que o cluster só suporta TLS 1.2 ou superior. Para obter mais informações, consulte [Transport Layer Security](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Traga sua própria chave para criptografia de disco
Todos os discos gerenciados no HDInsight são protegidos com o SSE (Criptografia do Serviço de Armazenamento) do Azure. Os dados nesses discos são criptografados por chaves gerenciadas pela Microsoft por padrão. A partir desta versão, você pode trazer sua própria chave (BYOK) para criptografia de disco e gerenciá-la usando o Azure Key Vault. A criptografia BYOK é uma configuração de uma etapa durante a criação de clusters sem custo adicional. Basta registrar o HDInsight como uma identidade gerenciada com o Azure Key Vault e adicionar a chave de criptografia ao criar seu cluster. Para obter mais informações, consulte [a criptografia de disco de chave gerenciada pelo cliente](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Reprovação
Sem depreciações para esta liberação. Para se preparar para as próximas deprecções, consulte [as próximas mudanças](#upcoming-changes).

## <a name="behavior-changes"></a>Alterações de comportamento
Nenhuma mudança de comportamento para esta liberação. Para se preparar para as próximas mudanças, consulte [As próximas mudanças](#upcoming-changes).

## <a name="upcoming-changes"></a>Alterações futuras
As seguintes mudanças acontecerão nos próximos lançamentos. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Um VM mínimo de 4 núcleos é necessário para o nó da cabeça 
Um VM mínimo de 4 núcleos é necessário para o Head Node para garantir a alta disponibilidade e confiabilidade dos clusters HDInsight. A partir de 6 de abril de 2020, os clientes só podem escolher 4 núcleos ou acima da VM como Head Node para os novos clusters HDInsight. Os clusters existentes continuarão a funcionar como esperado. 

### <a name="esp-spark-cluster-node-size-change"></a>Alteração do tamanho do nó do cluster de faísca eSP 
Na próxima versão, o tamanho mínimo do nó permitido para o cluster ESP Spark será alterado para Standard_D13_V2. As VMs da série A podem causar problemas de cluster ESP devido à capacidade relativamente baixa de CPU e memória. As VMs da série A serão depreciadas para a criação de novos clusters ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Movendo-se para conjuntos de escala de máquinas virtuais do Azure
O HDInsight agora usa máquinas virtuais do Azure para fornecer o cluster. Na próxima versão, o HDInsight usará os conjuntos de escala da máquina virtual do Azure. Veja mais sobre os conjuntos de escalas de máquinas virtuais do Azure.

### <a name="hbase-20-to-21"></a>HBase 2.0 a 2.1
Na próxima versão do HDInsight 4.0, a versão HBase será atualizada da versão 2.0 para a 2.1.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer melhorias de confiabilidade e desempenho de cluster. 

## <a name="component-version-change"></a>Mudança de versão do componente
Nenhuma mudança de versão do componente para esta versão. Você pode encontrar as versões atuais do componente para HDInsight 4.0 ad HDInsight 3.6 aqui.

