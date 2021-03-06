---
title: Solucionar falhas de criação de recursos no Azure HDInsight
description: Os erros comuns de problema de capacidade e técnicas de mitigação são fornecidos neste artigo.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.openlocfilehash: 871e6b1253b0fc9a5b6ae5d3389982cfdd6af3bd
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103172"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Solucionar falhas de criação de recursos no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com o Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Erro: a implantação excederia a cota de ' 800 '

O Azure tem um limite de cota de 800 implantações por grupo de recursos. Cotas diferentes são aplicadas por grupo de recursos, assinatura, conta ou outros escopos. Por exemplo, sua assinatura pode estar configurada para limitar o número de núcleos de uma região. Tentar implantar uma máquina virtual com mais núcleos do que o permitido resulta na mensagem de erro informando que a cota foi excedida.

Para resolver esse problema, exclua as implantações que não são mais necessárias usando o portal do Azure, a CLI ou o PowerShell.

Para obter mais informações, consulte [Solucionar erros de cotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Erro: o nó máximo excedeu os núcleos disponíveis nesta região

Sua assinatura pode estar configurada para limitar o número de núcleos de uma região. A tentativa de implantar um recurso com mais núcleos do que o permitido resulta na mensagem de erro informando que a cota foi excedida.

Para solicitar um aumento de cota, siga essas etapas:

1. Vá para a [portal do Azure](https://portal.azure.com)e selecione **ajuda + suporte**.

1. Selecione **Nova solicitação de suporte**.

1. Na guia **noções básicas** da página **nova solicitação de suporte** , forneça as seguintes informações:

   * **Tipo de problema:** Selecione **limites de serviço e assinatura (cotas)**.
   * **Assinatura:** Selecione a assinatura que você deseja modificar.
   * **Tipo de cota:** Selecione **HDInsight**.

Para obter mais informações, consulte [Criar um tíquete de suporte para aumentar o núcleo](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
