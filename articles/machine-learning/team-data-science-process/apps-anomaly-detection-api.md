---
title: API de Detecção de Anomalias do Azure Machine Learning – Processo de Ciência de Dados da Equipe
description: A API de detecção de anomalias é um exemplo criado com o Microsoft Azure Machine Learning que detecta anomalias nos dados de série temporal com valores numéricos que são espaçados uniformemente no tempo.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: a09094cf0d1bd3c2e299e968d7de8410dcd9c3cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721873"
---
# <a name="machine-learning-anomaly-detection-api"></a>API de detecção de anomalias do Machine Learning

> [!NOTE]
> Este item está em manutenção. Nós encorajamos você a usar o [serviço de API do Detector de Anomalias](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) alimentado por uma galeria de algoritmos de Machine Learning os Serviços Cognitivos Azure para detectar anomalias de métricas de negócios, operacionais e IoT.

## <a name="overview"></a>Visão geral
A [API de detecção de anomalias](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) é um exemplo criado com o Azure Machine Learning que detecta anomalias nos dados de série temporal com valores numéricos que são espaçados uniformemente no tempo.

Esta API pode detectar os seguintes tipos de padrão anômalo nos dados de série temporal:

* **Tendências positivas e negativas**: por exemplo, ao monitorar o uso da memória na computação, uma tendência de aumento pode ser interessante, pois ela pode indicar um vazamento de memória,
* **Alterações no intervalo dinâmico dos valores**: por exemplo, ao monitorar as exceções geradas por um serviço de nuvem, as alterações no intervalo dinâmico dos valores pode indicar instabilidade na integridade do serviço e
* **Picos e quedas**: por exemplo, ao monitorar o número de falhas de logon em um serviço ou o número de check-outs em um site de comércio eletrônico, os picos ou as quedas podem indicar um comportamento anormal.

Esses detectores de aprendizado da máquina rastreiam as alterações nos valores ao longo do tempo e informam as mudanças contínuas em seus valores como pontuações de anomalia. Eles não precisam de ajuste de limite ad hoc e suas pontuações podem ser usadas para controlar a taxa de falsos positivos. A API de detecção de anomalias é útil em vários cenários, como o monitoramento do serviço ao controlar KPIs ao longo do tempo, monitoramento do uso por meio de métricas, como o número de pesquisas, número de cliques, monitoramento do desempenho por meio de contadores, como memória, CPU, leituras de arquivos etc. ao longo do tempo.

A oferta da Detecção de Anomalias vem com ferramentas úteis para você começar.

* O [aplicativo Web](https://anomalydetection-aml.azurewebsites.net/) ajuda a avaliar e visualizar os resultados das APIs de detecção de anomalias em seus dados.

> [!NOTE]
> Tente a **solução IT Anomaly Insights** fornecida por [essa API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Implantação da API
Para usar a API, você deve implantá-la na sua assinatura do Azure, onde ela será hospedada como um serviço Web do Azure Machine Learning.  Você pode fazer isso na [Galeria de IA do Azure](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Isso implantará dois Web Services (clássicos) do Azure Machine Learning Studio (e seus recursos relacionados) para sua assinatura do Azure - um para detecção de anomalias com detecção de sazonalidade e outro sem detecção de sazonalidade.  Uma vez concluída a implantação, você poderá gerenciar suas APIs a partir da página [de serviços web do Azure Machine Learning Studio (clássico).](https://services.azureml.net/webservices/)  Nessa página, você poderá encontrar a localização do seu ponto de extremidade, chaves de API, bem como um código de amostra para chamar a API.  Instruções mais detalhadas estão disponíveis [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Dimensionando a API
Por padrão, sua implantação terá um plano de faturamento de Dev/Teste gratuito que inclui 1.000 transações/mês e 2 horas de cálculo/mês.  Você pode atualizar para outro plano de acordo com suas necessidades.  Os detalhes sobre o preço de diferentes planos estão disponíveis [aqui](https://azure.microsoft.com/pricing/details/machine-learning/), em "Preço da API Web de produção".

## <a name="managing-aml-plans"></a>Gerenciando planos do AML
Você pode gerenciar seu plano de cobrança [aqui](https://services.azureml.net/plans/).  O nome do plano será baseado no nome do grupo de recursos que você escolheu durante a implantação da API, além de uma cadeia de caracteres que é exclusiva à sua assinatura.  As instruções sobre como atualizar seu plano estão disponíveis [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice), na seção "Gerenciando planos de cobranças".

## <a name="api-definition"></a>Definição da API
O serviço web fornece uma API baseada em REST sobre HTTPS que pode ser consumida de diferentes maneiras, incluindo um aplicativo web ou móvel, R, Python, Excel, etc.  Você envia seus dados da série temporal para este serviço através de uma chamada de API REST, e ele executa uma combinação dos três tipos de anomalias descritos abaixo.

## <a name="calling-the-api"></a>Chamando a API
Para chamar a API, você precisará conhecer o local do ponto de extremidade e chave de API.  Esses dois requisitos, juntamente com o código de exemplo para chamar a API, estão disponíveis na página de [serviços web do Azure Machine Learning Studio (clássico).](https://services.azureml.net/webservices/)  Navegue até a API desejada e clique na guia "Consume" para encontrá-la.  Você pode chamar a API como uma API De `format=swagger`Swagger (ou seja, com o parâmetro URL) ou como uma API não-Swagger (ou seja, sem o `format` parâmetro URL).  O código de amostra usa o formato Swagger.  Veja abaixo um exemplo de solicitação e resposta em um formato não Swagger.  Esses exemplos são o ponto de extremidade de sazonalidade.  O ponto de extremidade de não sazonalidade é semelhante.

### <a name="sample-request-body"></a>Exemplo de corpo da solicitação
A solicitação contém dois objetos: `Inputs` e `GlobalParameters`.  No exemplo de solicitação abaixo, alguns parâmetros são enviados explicitamente, enquanto outros, não (role para baixo para ver uma lista completa de parâmetros para cada ponto de extremidade).  Os parâmetros que não são enviados explicitamente na solicitação usarão os valores padrão fornecidos abaixo.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Exemplo de Resposta
Para ver o `ColumnNames` campo, você `details=true` deve incluir como parâmetro url em sua solicitação.  Confira as tabelas abaixo para ver o significado por trás de cada um desses campos.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>API de Pontuação
A API de Pontuação é usada para executar a detecção de anomalias nos dados de série temporal não sazonais. A API executa vários detectores de anomalias nos dados e retorna suas pontuações de anomalias.
A figura abaixo mostra um exemplo de anomalias que a API de Pontuação pode detectar. Esta série tem duas mudanças de nível distintas, e três picos. Os pontos vermelhos mostram a hora em que a alteração no nível é detectada, enquanto as setas pretas mostram os picos detectados.
![API de Pontuação][1]

### <a name="detectors"></a>Detectores
A API de detecção de anomalias suporta detectores em três grandes categorias. Os detalhes sobre determinados parâmetros de entrada e saídas para cada detector podem ser encontrados na tabela a seguir.

| Categoria do Detector | Detector | Descrição | Parâmetros de Entrada | outputs |
| --- | --- | --- | --- | --- |
| Detectores de Pico |Detector TSpike |Detectar picos e quedas com base na distância dos valores em relação ao primeiro e terceiro quartis |*tspikedetector.sensitivity:* usa o valor inteiro no intervalo de 1 a 10, padrão: 3; valores mais altos capturam valores mais extremos, tornando-o menos sensível |TSpike: valores binários – '1' se um pico/queda for detectado, '0' do contrário |
| Detectores de Pico | Detector ZSpike |Detecta picos e quedas com base na distância dos pontos de dados em relação à média |*zspikedetector.sensitivity:* usa o valor inteiro no intervalo de 1 a 10, padrão: 3; valores mais altos capturam valores mais extremos, tornando-o menos sensível |ZSpike: valores binários – '1' se um pico/queda for detectado, '0' do contrário |
| Detector de Tendências Lentas |Detector de Tendências Lentas |Detectar uma tendência positiva lenta de acordo com a sensibilidade definida |*trenddetector.sensitivity:* limiar na pontuação do detector (padrão: 3.25, 3.25 – 5 é um intervalo razoável para selecionar; Quanto mais alto, menos sensível) |tscore: número flutuante que representa a pontuação de anomalias na tendência |
| Detectores de Alteração no Nível | Detector de Alteração no Nível Bidirecional |Detectar uma alteração de aumento e diminuição no nível de acordo com a sensibilidade definida |*bileveldetector.sensitivity:* limiar na pontuação do detector (padrão: 3.25, 3.25 – 5 é um intervalo razoável para selecionar; Quanto mais alto, menos sensível) |rpscore: número flutuante que representa a pontuação de anomalias na alteração de aumento e diminuição no nível |

### <a name="parameters"></a>Parâmetros
Informações mais detalhadas sobre esses parâmetros de entrada são listadas na tabela a seguir:

| Parâmetros de Entrada | Descrição | Configuração padrão | Type | Intervalo Válido | Intervalo Sugerido |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Histórico (no número de pontos de dados) usado para o cálculo da pontuação de anomalias |500 |inteiro |10-2.000 |Dependente da série temporal |
| detectors.spikesdips | Se apenas picos, apenas quedas, ou ambos devem ser detectados |Ambos |enumeração |Ambos, Picos, Quedas |Ambos |
| bileveldetector.sensitivity |Sensibilidade para o detector de alteração no nível bidirecional. |3.25 |double |Nenhum |3.25-5 (valores mais baixos significam mais sensibilidade) |
| trenddetector.sensitivity |Sensibilidade para o detector de tendência positiva. |3.25 |double |Nenhum |3.25-5 (valores mais baixos significam mais sensibilidade) |
| tspikedetector.sensitivity |Sensibilidade para o Detector TSpike |3 |inteiro |1-10 |3-5 (valores mais baixos significam mais sensibilidade) |
| zspikedetector.sensitivity |Sensibilidade para o Detector ZSpike |3 |inteiro |1-10 |3-5 (valores mais baixos significam mais sensibilidade) |
| postprocess.tailRows |Número de pontos de dados mais recentes a serem mantidos nos resultados da saída |0 |inteiro |0 (manter todos os pontos de dados) ou especificar o número de pontos de nos resultados |N/D |

### <a name="output"></a>Saída
A API executa esses detectores em seus dados da série temporal e retorna as pontuações de anomalias e os indicadores de picos binários para cada ponto no tempo. A tabela abaixo lista as saídas da API.

| outputs | Descrição |
| --- | --- |
| Hora |Carimbos de data/hora dos dados brutos ou dos dados atribuídos (e/ou) agregados se a atribuição dos dados ausentes (e/ou) de agregação for aplicada |
| Dados |Valores dos dados brutos ou dos dados atribuídos (e/ou) agregados se a atribuição dos dados ausentes (e/ou) de agregação for aplicada |
| TSpike |Indicador binário para indicar se um pico é detectado pelo Detector TSpike |
| ZSpike |Indicador binário para indicar se um pico é detectado pelo Detector ZSpike |
| rpscore |Um número flutuante que representa a pontuação de anomalias na alteração bidirecional no nível |
| rpalert |Valor 1/0 indicando que há uma anomalia na alteração de nível bidirecional baseada na sensibilidade de entrada |
| tscore |um número flutuante que representa a pontuação de anomalias na tendência positiva |
| talert |Valor 1/0 indicando que há uma anomalia de tendência positiva baseada na sensibilidade de entrada |

## <a name="scorewithseasonality-api"></a>API ScoreWithSeasonality
A API ScoreWithSeasonality é usada para executar a detecção de anomalias na série temporal que têm padrões sazonais. Essa API é útil para detectar desvios nos padrões sazonais.
A figura a seguir mostra um exemplo de anomalias detectadas em uma série temporal sazonal. A série temporal tem um pico (o primeiro dot preto), dois mergulhos (o segundo dot preto e um no final) e uma mudança de nível (vermelho). Tanto o mergulho no meio da série temporal quanto a mudança de nível só são perceptíveis depois que componentes sazonais são removidos da série.
![API de Sazonalidade][2]

### <a name="detectors"></a>Detectores
Os detectores no ponto de extremidade de sazonalidade são semelhantes aos do ponto de extremidade de não sazonalidade, mas com nomes de parâmetro ligeiramente diferentes (listados abaixo).

### <a name="parameters"></a>Parâmetros

Informações mais detalhadas sobre esses parâmetros de entrada são listadas na tabela a seguir:

| Parâmetros de Entrada | Descrição | Configuração padrão | Type | Intervalo Válido | Intervalo Sugerido |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Intervalo de agregação em segundos para agregar a série temporal de entrada |0 (nenhuma agregação é realizada) |inteiro |0: ignorar a agregação, > 0 do contrário |5 minutos para 1 dia, dependente da série temporal |
| preprocess.aggregationFunc |Função usada para agregar dados para o AggregationInterval especificado |média |enumeração |média, soma, comprimento |N/D |
| preprocess.replaceMissing |Valores usados para atribuir os dados ausentes |lkv (último valor conhecido) |enumeração |zero, lkv, média |N/D |
| detectors.historywindow |Histórico (no número de pontos de dados) usado para o cálculo da pontuação de anomalias |500 |inteiro |10-2.000 |Dependente da série temporal |
| detectors.spikesdips | Se apenas picos, apenas quedas, ou ambos devem ser detectados |Ambos |enumeração |Ambos, Picos, Quedas |Ambos |
| bileveldetector.sensitivity |Sensibilidade para o detector de alteração no nível bidirecional. |3.25 |double |Nenhum |3.25-5 (valores mais baixos significam mais sensibilidade) |
| postrenddetector.sensitivity |Sensibilidade para o detector de tendência positiva. |3.25 |double |Nenhum |3.25-5 (valores mais baixos significam mais sensibilidade) |
| negtrenddetector.sensitivity |Sensibilidade para o detector de tendência negativa. |3.25 |double |Nenhum |3.25-5 (valores mais baixos significam mais sensibilidade) |
| tspikedetector.sensitivity |Sensibilidade para o Detector TSpike |3 |inteiro |1-10 |3-5 (valores mais baixos significam mais sensibilidade) |
| zspikedetector.sensitivity |Sensibilidade para o Detector ZSpike |3 |inteiro |1-10 |3-5 (valores mais baixos significam mais sensibilidade) |
| seasonality.enable |Caso a análise da sazonalidade precise ser executada |true |booleano |verdadeiro, falso |Dependente da série temporal |
| seasonality.numSeasonality |Número máximo de ciclos periódicos a serem detectados |1 |inteiro |1, 2 |1-2 |
| seasonality.transform |Caso os componentes sazonais (e) de tendência devam ser removidos antes de aplicar a detecção de anomalias |sem sazonalidade |enumeração |nenhum, sem sazonalidade, sem sazonalidade e tendência |N/D |
| postprocess.tailRows |Número de pontos de dados mais recentes a serem mantidos nos resultados da saída |0 |inteiro |0 (manter todos os pontos de dados) ou especificar o número de pontos de nos resultados |N/D |

### <a name="output"></a>Saída
A API executa esses detectores em seus dados da série temporal e retorna as pontuações de anomalias e os indicadores de picos binários para cada ponto no tempo. A tabela abaixo lista as saídas da API.

| outputs | Descrição |
| --- | --- |
| Hora |Carimbos de data/hora dos dados brutos ou dos dados atribuídos (e/ou) agregados se a atribuição dos dados ausentes (e/ou) de agregação for aplicada |
| OriginalData |Valores dos dados brutos ou dos dados atribuídos (e/ou) agregados se a atribuição dos dados ausentes (e/ou) de agregação for aplicada |
| ProcessedData |Qualquer uma das opções a seguir: <ul><li>Série temporal ajustada periodicamente caso uma sazonalidade significativa tenha sido detectada e opção sem sazonalidade foi selecionada;</li><li>séries temporais e sem tendência e ajustadas periodicamente caso uma sazonalidade significativa tenha sido detectada e a opção sem sazonalidade e tendência foi selecionada</li><li>caso contrário, esta opção é a mesma que OriginalData</li> |
| TSpike |Indicador binário para indicar se um pico é detectado pelo Detector TSpike |
| ZSpike |Indicador binário para indicar se um pico é detectado pelo Detector ZSpike |
| BiLevelChangeScore |Um número flutuante que representa a pontuação de anomalias na alteração de nível |
| BiLevelChangeAlert |Valor 1/0 indicando que há uma anomalia na alteração de nível baseada na sensibilidade de entrada |
| PosTrendScore |um número flutuante que representa a pontuação de anomalias na tendência positiva |
| PosTrendAlert |Valor 1/0 indicando que há uma anomalia de tendência positiva baseada na sensibilidade de entrada |
| NegTrendScore |Um número flutuante que representa a pontuação de anomalias na tendência negativa |
| NegTrendAlert |Valor 1/0 indicando que há uma anomalia de tendência negativa baseada na sensibilidade de entrada |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

