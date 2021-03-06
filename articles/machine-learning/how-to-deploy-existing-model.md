---
title: Use e implante modelos existentes
titleSuffix: Azure Machine Learning
description: Saiba como usar o Azure Machine Learning com modelos que foram treinados fora do serviço. Você pode registrar modelos criados fora do Azure Machine Learning e, em seguida, implantá-los como um serviço web ou módulo Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472368"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Use um modelo existente com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a usar um modelo de aprendizado de máquina existente com o Azure Machine Learning.

Se você tem um modelo de aprendizado de máquina que foi treinado fora do Azure Machine Learning, você ainda pode usar o serviço para implantar o modelo como um serviço web ou para um dispositivo IoT Edge. 

> [!TIP]
> Este artigo fornece informações básicas sobre o registro e a implantação de um modelo existente. Uma vez implantado, o Azure Machine Learning fornece monitoramento para o seu modelo. Ele também permite armazenar dados de entrada enviados para a implantação, que podem ser usados para análise de deriva de dados ou treinamento de novas versões do modelo.
>
> Para obter mais informações sobre os conceitos e termos utilizados aqui, consulte [Gerenciar, implantar e monitorar modelos de aprendizado de máquina](concept-model-management-and-deployment.md).
>
> Para obter informações gerais sobre o processo de implantação, consulte [Implantar modelos com o Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho](how-to-manage-workspace.md).

    > [!TIP]
    > Os exemplos python neste artigo `ws` assumem que a variável está definida para o espaço de trabalho do Azure Machine Learning.
    >
    > Os exemplos da CLI usam `myworkspace` `myresourcegroup`um espaço reservado e . Substitua-os pelo nome do seu espaço de trabalho e pelo grupo de recursos que o contém.

* O [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* A [extensão CLI](reference-azure-machine-learning-cli.md)e Machine Learning CLI do [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .

* Um modelo treinado. O modelo deve ser persistido em um ou mais arquivos em seu ambiente de desenvolvimento.

    > [!NOTE]
    > Para demonstrar o registro de um modelo treinado fora do Azure Machine Learning, os exemplos de trechos de código [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)neste artigo usam os modelos criados pelo projeto de análise de sentimento do Twitter de Paolo Ripamonti: .

## <a name="register-the-models"></a>Registre os modelos

O registro de um modelo permite que você armazene, versão e rastreie metadados sobre modelos em seu espaço de trabalho. Nos exemplos de Python e `models` CLI a `model.h5`seguir, `encoder.pkl`o diretório contém os `tokenizer.pkl` `model.w2v`arquivos e arquivos. Este exemplo carrega os arquivos `models` contidos no diretório `sentiment`como um novo registro de modelo chamado :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Para obter mais informações, consulte a referência [Model.register().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Você também pode `tags` `properties` definir objetos de adicionar e dicionário para o modelo registrado. Esses valores podem ser usados posteriormente para ajudar a identificar um modelo específico. Por exemplo, a estrutura utilizada, parâmetros de treinamento, etc.

Para obter mais informações, consulte a referência [de registro do modelo az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)


Para obter mais informações sobre o registro do modelo em geral, consulte [Gerenciar, implantar e monitorar modelos de aprendizado de máquina](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Definir configuração de inferência

A configuração de inferência define o ambiente usado para executar o modelo implantado. A configuração de inferência faz referência às seguintes entidades, que são usadas para executar o modelo quando ele é implantado:

* Um script de entrada. Este arquivo `score.py`(nomeado) carrega o modelo quando o serviço implantado é iniciado. Também é responsável por receber dados, passá-los para o modelo e, em seguida, retornar uma resposta.
* Um ambiente de aprendizado de máquina [do Azure.](how-to-use-environments.md) Um ambiente define as dependências de software necessárias para executar o modelo e o script de entrada.

O exemplo a seguir mostra como usar o SDK para criar um ambiente e, em seguida, usá-lo com uma configuração de inferência:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Para obter mais informações, consulte os seguintes artigos:

+ [Como usar ambientes.](how-to-use-environments.md)
+ [Referência inferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)


A CLI carrega a configuração de inferência a partir de um arquivo YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Com a CLI, o ambiente conda é definido no `myenv.yml` arquivo referenciado pela configuração de inferência. O seguinte YAML é o conteúdo deste arquivo:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Para obter mais informações sobre a configuração de inferência, consulte [Implantar modelos com o Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Script de entrada

O script de entrada tem `init()` apenas `run(data)`duas funções necessárias, e . Essas funções são usadas para inicializar o serviço na inicialização e executar o modelo usando dados de solicitação passados por um cliente. O resto do script lida com o carregamento e execução do modelo( s).

> [!IMPORTANT]
> Não há um script de entrada genérico que funcione para todos os modelos. É sempre específico para o modelo que é usado. Ele deve entender como carregar o modelo, o formato de dados que o modelo espera e como pontuar dados usando o modelo.

O código Python a seguir`score.py`é um script de entrada de exemplo ( ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Para obter mais informações sobre scripts de entrada, consulte [Implantar modelos com O Zure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definir implantação

O pacote [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) contém as classes usadas para implantação. A classe que você usa determina onde o modelo é implantado. Por exemplo, para implantar como um serviço web no Azure Kubernetes Service, use [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) para criar a configuração de implantação.

O código Python a seguir define uma configuração de implantação para uma implantação local. Esta configuração implanta o modelo como um serviço web para o seu computador local.

> [!IMPORTANT]
> Uma implantação local requer uma instalação de trabalho do [Docker](https://www.docker.com/) em seu computador local:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Para obter mais informações, consulte a referência [LocalWebservice.deploy_configuration().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-)

A CLI carrega a configuração de implantação a partir de um arquivo YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Implantar em um alvo de computação diferente, como o Azure Kubernetes Service na nuvem Azure, é tão fácil quanto alterar a configuração de implantação. Para obter mais informações, consulte [Como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Implantar o modelo

O exemplo a seguir carrega informações `sentiment`sobre o modelo registrado nomeado `sentiment`e, em seguida, implanta-as como um serviço chamado . Durante a implantação, a configuração de configuração e implantação de inferência são usadas para criar e configurar o ambiente de serviço:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Para obter mais informações, consulte a referência [Model.deploy().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Para implantar o modelo a partir do CLI, use o seguinte comando. Este comando implanta a versão 1`sentiment:1`do modelo registrado ( ) usando `inferenceConfig.json` `deploymentConfig.json` a configuração de inferência e implantação armazenada nos arquivos:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Para obter mais informações, consulte [o modelo az ml implantar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) referência.

Para obter mais informações sobre a implantação, consulte [Como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Consumo de solicitação-resposta

Após a implantação, o URI de pontuação é exibido. Este URI pode ser usado pelos clientes para enviar solicitações ao serviço. O exemplo a seguir é um cliente Python básico que envia dados para o serviço e exibe a resposta:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Para obter mais informações sobre como consumir o serviço implantado, consulte [Criar um cliente](how-to-consume-web-service.md).

## <a name="next-steps"></a>Próximas etapas

* [Monitore seus modelos de Aprendizado de Máquina do Azure com insights de aplicativos](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Como criar um cliente para um modelo implantado](how-to-consume-web-service.md)
