---
title: Introdução aos dispositivos gêmeos do Hub IoT do Azure (Python) | Microsoft Docs
description: Como usar dispositivos gêmeos do Hub IoT do Azure para adicionar marcas e usar uma consulta do Hub IoT. Use os SDKs do IoT do Azure para Python para implementar o aplicativo de dispositivo simulado e um aplicativo de serviço que adiciona as marcações e executa a consulta do Hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 0bb1371de827fbb68afd5d114f49afa4acec0deb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759727"
---
# <a name="get-started-with-device-twins-python"></a>Introdução aos dispositivos gêmeos (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ao final deste tutorial, você terá dois aplicativos de console do Python:

* **AddTagsAndQuery.py**, um aplicativo de back-end do Python, que adiciona marcações e consulta os dispositivos gêmeos.

* **ReportConnectivity.py**, um aplicativo do Python, que simula um dispositivo que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e relata sua condição de conectividade.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Verifique se a porta 8883 está aberta no firewall. A amostra do dispositivo neste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a seqüência de conexão do hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço

Nesta seção, você cria um aplicativo de console Python que adiciona metadados de localização ao dispositivo gêmeo associado ao seu **{ID do dispositivo}**. Em seguida, ele consulta os dispositivos gêmeos armazenados no hub IoT selecionando os dispositivos localizados em Redmond e, depois, aqueles que relatam uma conexão celular.

1. Em seu diretório de trabalho, abra um prompt de comando e instale o **SDK do Serviço de Hub Azure IoT para Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Usando um editor de texto, crie um novo arquivo **AddTagsAndQuery.py**.

3. Adicione o código a seguir para importar os módulos necessários do SDK de serviço:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Adicione o código seguinte: Substitua-a pela `[IoTHub Connection String]` seqüência de conexão de hub IoT que você copiou em [Obter a seqüência de conexão de hub IoT](#get-the-iot-hub-connection-string). Substitua pelo `[Device Id]` ID do dispositivo registrado no [Registre um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Adicione o seguinte código ao arquivo **AddTagsAndQuery.py**:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    O objeto **IoTHubRegistryManager** expõe todos os métodos necessários para interagir com gêmeos de dispositivos do serviço. O código primeiro inicializa o objeto **IoTHubRegistryManager,** depois atualiza o dispositivo duplo para **DEVICE_ID**e, finalmente, executa duas consultas. O primeiro seleciona apenas os dispositivos gêmeos de dispositivos localizados na planta **Redmond43,** e o segundo refina a consulta para selecionar apenas os dispositivos que também estão conectados através de uma rede celular.

6. Adicione o seguinte código ao final de **AddTagsAndQuery.py** para implementar a função **iothub_service_sample_run**:

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Execute o aplicativo com:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Você deve ver um dispositivo nos resultados da consulta que pergunta sobre todos os dispositivos localizados em **Redmond43**, e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.

    ![primeira consulta mostrando todos os dispositivos em Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

Na seção seguinte, você cria um aplicativo de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo

Nesta seção, você cria um aplicativo de console Python que se conecta ao seu hub como seu **{ID do dispositivo}** e, em seguida, atualiza as propriedades relatadas do seu dispositivo gêmeo para conter as informações de que ele está conectado usando uma rede celular.

1. A partir de um prompt de comando em seu diretório de trabalho, instale o **Azure IoT Hub Device SDK para Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Usando um editor de texto, crie um novo arquivo **ReportConnectivity.py**.

3. Adicione o seguinte código para importar os módulos necessários do SDK do dispositivo:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Adicione o código seguinte: Substitua `[IoTHub Device Connection String]` o valor do espaço reservado pela seqüência de conexão do dispositivo que você copiou em [Registrar um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Adicione o seguinte código ao arquivo **ReportConnectivity.py** para implementar a funcionalidade de dispositivo gêmeo:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    O objeto **IoTHubModuleClient** expõe todos os métodos necessários para interagir com gêmeos do dispositivo. O código anterior, depois de inicializar o objeto **IoTHubModuleClient,** recupera o dispositivo gêmeo para o seu dispositivo e atualiza sua propriedade relatada com as informações de conectividade.

6. Adicione o seguinte código ao final de **ReportConnectivity.py** para implementar a função **iothub_client_sample_run**:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Execute o aplicativo do dispositivo:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Você deve ver a confirmação de que as propriedades do dispositivo gêmeo relatada foram atualizadas.

    ![atualizar propriedades relatadas do aplicativo do dispositivo](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Agora que o dispositivo divulgou suas informações de conectividade, ele deve aparecer em ambas as consultas. Volte e execute as consultas novamente:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Desta vez, o **{ID do dispositivo)** deve aparecer em ambos os resultados da consulta.

    ![segunda consulta no aplicativo de serviço](./media/iot-hub-python-twin-getstarted/service-2.png)

    No aplicativo do seu dispositivo, você verá a confirmação de que as propriedades desejadas do patch duplo enviado pelo aplicativo de serviço foram recebidas.

    ![receber propriedades desejadas no aplicativo do dispositivo](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você adicionou os metadados do dispositivo como marcações de um aplicativo de back-end e criou um aplicativo de dispositivo simulado para relatar informações de conectividade no dispositivo gêmeo. Você também aprendeu a consultar essas informações usando o registro.

Veja os recursos a seguir para saber como:

* Envie telemetria de dispositivos com o tutorial [Get started with IoT Hub.](quickstart-send-telemetry-python.md)

* Configure dispositivos usando as propriedades desejadas do dispositivo twin com as [propriedades desejadas de Uso para configurar o](tutorial-device-twins.md) tutorial de dispositivos.

* Controlar dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário) com o tutorial [Usar métodos diretos](quickstart-control-device-python.md).
