---
title: Introdução ao log de fluxo para NSGs
titleSuffix: Azure Network Watcher
description: Este artigo explica como usar o recurso dos logs de fluxo NSG do Observador de Rede do Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: a1674f51d5b877a1296e9a457c6acf61a507c82e
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131365"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução ao log de fluxo dos grupos de segurança da rede

## <a name="introduction"></a>Introdução

Os logs de fluxo do NSG ( [grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) ) são um recurso do observador de rede do Azure que permite registrar informações sobre o tráfego IP que flui através de um NSG. Os dados de fluxo são enviados para contas de armazenamento do Azure de onde você pode acessá-los, bem como exportá-los para qualquer ferramenta de visualização, SIEM ou IDS de sua escolha.

![visão geral dos logs de fluxo](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Por que usar logs de fluxo?

É essencial monitorar, gerenciar e conhecer sua própria rede para um desempenho, conformidade e segurança incomparáveis. Conhecer o seu próprio ambiente é de suma importância para protegê-lo e otimizá-lo. Geralmente é necessário saber o estado atual da rede, quem está se conectando, de onde eles estão se conectando, quais portas estão abertas para a Internet, o comportamento esperado da rede, o comportamento irregular da rede e os aumentos repentinos no tráfego.

Os logs de fluxo são a fonte de verdade para todas as atividades de rede em seu ambiente de nuvem. Quer você seja uma próxima inicialização tentando otimizar recursos ou grandes empresas tentando detectar invasões, os logs de fluxo são sua melhor aposta. Você pode usá-lo para otimizar os fluxos de rede, monitorar a taxa de transferência, verificar a conformidade, detectar invasões e muito mais.

## <a name="common-use-cases"></a>Casos de uso comuns

**Monitoramento de rede**: identificar tráfego desconhecido ou indesejado. Monitore os níveis de tráfego e o consumo de largura de banda. Filtrar logs de fluxo por IP e porta para entender o comportamento do aplicativo. Exportar logs de fluxo para ferramentas de análise e visualização de sua escolha para configurar painéis de monitoramento.

**Monitoramento e otimização de uso:** Identifique os principais palestrantes em sua rede. Combine com os dados do GeoIP para identificar o tráfego entre regiões. Entenda o crescimento do tráfego para a previsão de capacidade. Use dados para remover regras de tráfego overtly restritivas.

**Conformidade**: Use os dados de fluxo para verificar o isolamento de rede e a conformidade com as regras de acesso corporativo

**Análise de segurança & de perícia de rede**: analise fluxos de rede de IPS e interfaces de rede comprometidos. Exporte os logs de fluxo para qualquer ferramenta SIEM ou IDS de sua escolha.

## <a name="how-logging-works"></a>Como o log funciona

**Propriedades da chave**

- Os logs de fluxo operam na [camada 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) e registram todos os fluxos de IP que entram e saem de um NSG
- Os logs são coletados por meio da plataforma do Azure e não afetam os recursos do cliente ou o desempenho da rede de alguma forma.
- Os logs são gravados no formato JSON e mostram os fluxos de entrada e saída em uma base regra por NSG.
- Cada registro de log contém a NIC (interface de rede) que o fluxo aplica a informações de 5 tuplas, a decisão de tráfego & (versão 2 somente) informações de taxa de transferência. Veja o _formato de log_ abaixo para obter detalhes completos.
- Os logs de fluxo têm um recurso de retenção que permite excluir automaticamente os logs de até um ano após sua criação

**Conceitos principais**

- Redes definidas por software são organizadas em redes virtuais (VNETs) e sub-redes. A segurança dessas VNets e sub-redes pode ser gerenciada usando um NSG.
- Um NSG (grupo de segurança de rede) contém uma lista de _regras de segurança_ que permitem ou negam o tráfego de rede em recursos aos quais ele está conectado. NSGs pode ser associado a sub-redes, VMs individuais ou adaptadores de rede (NIC) individuais conectados às VMs (Gerenciador de recursos). Para obter mais informações, confira [Visão geral do Grupo de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview?toc=%2Fazure%2Fnetwork-watcher%2Ftoc.json).
- Todos os fluxos de tráfego em sua rede são avaliados usando as regras no NSG aplicável.
- O resultado dessas avaliações são os logs de fluxo NSG. Os logs de fluxo são coletados por meio da plataforma Azure e não exigem nenhuma alteração nos recursos do cliente.
- Os logs de fluxo NSG são gravados em contas de armazenamento de onde podem ser acessados.
- Você pode exportar, processar, analisar e Visualizar logs de fluxo usando ferramentas como TA, Splunk, Grafana, Stealthwatch, etc.

## <a name="log-format"></a>Formato de log

Logs de fluxo incluem as seguintes propriedades:

* **time** - a hora em que o evento foi registrado
* **SystemId** -ID de recurso do grupo de segurança de rede.
* **category** - a categoria do evento. A categoria é sempre **NetworkSecurityGroupFlowEvent**
* **ResourceId** -a ID de recurso do NSG
* **operationName** - é sempre NetworkSecurityGroupFlowEvents
* **properties** - uma coleção de propriedades do fluxo
    * **Version** - o número de versão do esquema do evento Log de Fluxo
    * **fluxos** – uma coleção de fluxos. Essa propriedade tem várias entradas para diferentes regras
        * **rule** - a regra para a qual os fluxos são listados
            * **flows** - uma coleção de fluxos
                * **mac** - o endereço MAC da NIC para a VM na qual o fluxo foi coletado
                * **flowTuples** - uma cadeia de caracteres que contém várias propriedades para a tupla de fluxo no formato separado por vírgulas
                    * **Carimbo de data/hora** -esse valor é o carimbo de data/hora de quando o fluxo ocorreu no formato de época do UNIX
                    * **Source IP** - o IP de origem
                    * **Destination IP** - o IP de destino
                    * **Source Port** - a porta de origem
                    * **Destination Port** - a porta de destino
                    * **Protocol** - o protocolo do fluxo. Os valores válidos são **T** para TCP e **U** para UDP
                    * **Traffic Flow** - a direção do fluxo do tráfego. Os valores válidos são **I** para entrada e **O** para saída.
                    * **Decisão de tráfego** - se o tráfego foi permitido ou negado. Os valores válidos são **A** para permitido e **D** para negado.
                    * **Estado de fluxo - versão 2 somente** - captura o estado do fluxo. Os estados possíveis são **B**: iniciar, quando um fluxo for criado. As estatísticas não são fornecidas. **C**: continuando um fluxo contínuo. As estatísticas são fornecidas em intervalos de 5 minutos. **E**: final, quando um fluxo é encerrado. Estatísticas são fornecidas.
                    * **Pacotes - Fonte para destino - Versão 2 Apenas** A quantidade total de pacotes TCP ou UDP enviados da origem ao destino desde a última atualização.
                    * **Bytes enviados - Fonte para destino - Versão 2 Apenas** A quantidade total de bytes de pacotes TCP ou UDP enviados da origem ao destino desde a última atualização. O número total de bytes de pacote TCP e UDP enviados da origem para o destino desde os últimos bytes updatePacket inclui o cabeçalho e a carga útil do pacote.
                    * **Pacotes - Destino para fonte - Versão 2 Apenas** A quantidade total de pacotes TCP ou UDP enviados do destino para a fonte desde a última atualização.
                    * **Bytes enviados - Destino para fonte - Versão 2 Apenas** A quantidade total de bytes de pacotes TCP ou UDP enviados do destino para a fonte desde a última atualização. Os bytes de pacote incluem cabeçalho de pacote e carga útil.


**Logs de fluxo NSG versão 2 (vs versão 1)** 

A versão 2 dos logs apresenta o conceito de estado de fluxo. Você pode configurar qual versão de logs de fluxo receber.

O estado de fluxo _B_ é registrado quando um fluxo é iniciado. Estado de fluxo _C_ e o estado de fluxo _E_ são os estados que marcam a continuação de um fluxo e encerramento de fluxo, respectivamente. Os estados _C_ e _E_ contêm informações de largura de banda de tráfego.

### <a name="sample-log-records"></a>Registros de log de exemplo

O texto que segue é um exemplo de um log de fluxo. Como você pode ver, há vários registros que seguem a lista de propriedades descrita na seção anterior.

> [!NOTE]
> Os valores na propriedade **flowTuples* são uma lista separada por vírgulas.
 
**Amostra de formato de log do fluxo de NSG Versão 1**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        
        
```
**Amostra de formato de log do fluxo de NSG Versão 2**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**Tupla de log explicada**

![visão geral dos logs de fluxo](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Cálculo de largura de banda de exemplo**

Fluxo de tuplas de uma conversa TCP entre 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Para os estados de fluxo de continuação _C_ e final _E_, as contagens de bytes e pacotes são contagens agregadas do tempo do registro da tupla de fluxo anterior. Referenciando a conversação de exemplo anterior, o número total de pacotes transferidos é 1021 + 52 + 8005 + 47 = 9125. O número total de bytes transferidos é 588096 + 29952 + 4610880 + 27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Habilitando logs de fluxo do NSG

Use o link relevante abaixo para obter guias sobre como habilitar logs de fluxo.

- [Azure portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)
- [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-powershell)
- [CLI](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-cli)
- [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-rest)
- [Azure Resource Manager](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)

## <a name="updating-parameters"></a>Atualizando parâmetros

**Azure portal**

Na portal do Azure, navegue até a seção de logs de fluxo NSG no observador de rede. Em seguida, clique no nome do NSG. Isso abrirá o painel configurações para o log de fluxo. Altere os parâmetros desejados e clique em **salvar** para implantar as alterações.

**PS/CLI/REST/ARM**

Para atualizar parâmetros por meio de ferramentas de linha de comando, use o mesmo comando usado para habilitar logs de fluxo (acima), mas com parâmetros atualizados que você deseja alterar.

## <a name="working-with-flow-logs"></a>Trabalhando com logs de fluxo

*Ler e exportar logs de fluxo*

- [Baixar &amp; logs de fluxo de exibição do portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log)
- [Ler logs de fluxo usando funções do PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs)
- [Exportar logs de fluxo NSG para Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Embora os logs de fluxo sejam destinados aos NSGs, eles não são exibidos como os outros logs. Os logs de fluxo são armazenados apenas em uma conta de armazenamento e seguem o caminho do log mostrado no exemplo a seguir:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Visualizar logs de fluxo*

- A [análise de tráfego do Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) é um serviço nativo do Azure para processar logs de fluxo, extrai informações e visualiza logs de fluxo. 
- [Destina Visualizar logs de fluxo NSG com Power BI](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-power-bi)
- [Destina Visualizar logs de fluxo NSG com pilha elástica](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-open-source-tools)
- [Destina Gerenciar e analisar logs de fluxo de NSG usando o Grafana](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-grafana)
- [Destina Gerenciar e analisar logs de fluxo de NSG usando o Graylog](https://docs.microsoft.com/azure/network-watcher/network-watcher-analyze-nsg-flow-logs-graylog)


## <a name="nsg-flow-logging-considerations"></a>Considerações de log de fluxo NSG

**Considerações sobre a conta de armazenamento**: 

- Local: a conta de armazenamento usada deve estar na mesma região que o NSG.
- Rotação de chaves de autogerenciamento: se você alterar/girar as chaves de acesso para sua conta de armazenamento, os logs de fluxo do NSG deixarão de funcionar. Para corrigir esse problema, você deve desabilitar e, em seguida, reabilitar os logs de fluxo do NSG.

**Custos de log de fluxo**: o log de fluxo de NSG é cobrado no volume de logs produzidos. Um alto volume de tráfego pode resultar em um volume grande de log de fluxo e nos custos associados. Os preços do log de fluxo de NSG não incluem os custos de armazenamento subjacentes. Usar o recurso de política de retenção com log de fluxo NSG significa incorrer em custos de armazenamento separados por longos períodos de tempo. Se você não precisa do recurso de política de retenção, é recomendável que você defina esse valor como 0. Para obter mais informações, consulte [preços do observador de rede](https://azure.microsoft.com/pricing/details/network-watcher/) e preços do [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter detalhes adicionais.

**Fluxos de entrada registrados de IPS de Internet para VMs sem IPS públicos**: VMs que não têm um endereço IP público atribuído por meio de um endereço IP público associado à NIC como um IP público em nível de instância, ou que fazem parte de um pool de back-end do Load Balancer básico, usam [SNAT padrão](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) e têm um endereço IP atribuído pelo Azure para facilitar a conectividade de saída. Como resultado, você poderá ver entradas de log de fluxo de fluxos de endereços IP da Internet, se o fluxo for destinado a uma porta no intervalo de portas atribuídas para SNAT. Embora o Azure não permita esses fluxos para a VM, a tentativa é registrada e aparece no log de fluxo do NSG do observador de rede por design. Recomendamos que o tráfego de Internet de entrada indesejado seja explicitamente bloqueado com NSG.

**Contagens incorretas de bytes e pacotes para fluxos sem estado**: [NSGs (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/security-overview) são implementados como um [Firewall com estado](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). No entanto, muitas regras padrão/internas que controlam o fluxo de tráfego são implementadas de maneira sem monitoração de estado. Devido a limitações da plataforma, os bytes e as contagens de pacotes não são registrados para fluxos sem monitoração de estado (ou seja, fluxos de tráfego que passam por regras sem estado), eles são registrados apenas para fluxos com estado. Consequentemente, o número de bytes e pacotes relatados nos logs de fluxo NSG (e Análise de Tráfego) podem ser diferentes dos fluxos reais. Essa limitação está agendada para ser corrigida em junho de 2020.

## <a name="best-practices"></a>Práticas recomendadas

**Habilitar em VNETs/sub-redes críticas**: os logs de fluxo devem ser habilitados em todas as VNETs/sub-redes críticas em sua assinatura como uma prática recomendada de auditoria e de segurança. 

**Habilitar o log de fluxo de NSG em todos os NSGs anexados a um recurso**: o log de fluxo no Azure está configurado no recurso NSG. Um fluxo só será associado a uma regra de NSG. Em cenários em que vários NSGs são utilizados, é recomendável Habilitar logs de fluxo de NSG em todos os NSGs aplicados à sub-rede ou interface de rede de um recurso para garantir que todo o tráfego seja registrado. Para obter mais informações, consulte [como o tráfego é avaliado](../virtual-network/security-overview.md#how-traffic-is-evaluated) em grupos de segurança de rede.

**Provisionamento de armazenamento**: o armazenamento deve ser provisionado em sintonia com o volume de log de fluxo esperado.

## <a name="troubleshooting-common-issues"></a>Solução de problemas comuns

### <a name="i-could-not-enable-nsg-flow-logs"></a>**Não consegui habilitar os logs de fluxo do NSG**

- O provedor de recursos **Microsoft. insights** não está registrado

Se você recebeu um erro _AuthorizationFailed_ ou _GatewayAuthenticationFailed_, talvez não tenha habilitado o provedor de recursos do Microsoft Insights em sua assinatura. [Siga as instruções](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#register-insights-provider) para habilitar o provedor do Microsoft insights.

### <a name="i-have-enabled-nsg-flow-logs-but-do-not-see-data-in-my-storage-account"></a>**Habilitei os logs de fluxo do NSG, mas não vejo nenhum dado em minha conta de armazenamento**

- **Hora da configuração**

Os logs de fluxo do NSG podem levar até 5 minutos para aparecer em sua conta de armazenamento (se configurados corretamente). Um PT1H.json será exibido, que pode ser acessado [conforme descrito aqui](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log).

- **Nenhum tráfego em seus NSGs**

Às vezes, você não verá logs porque suas VMs não estão ativas ou há filtros upstream em um Gateway de Aplicativo ou outros dispositivos que estão bloqueando o tráfego para seus NSGs.

### <a name="i-want-to-automate-nsg-flow-logs"></a>**Quero automatizar os logs de fluxo do NSG**

O suporte para automação por meio de modelos do Resource Manager não está disponível atualmente para logs de fluxo NSG. Leia o [anúncio do recurso](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) para obter mais informações.

## <a name="faq"></a>Perguntas frequentes

### <a name="what-does-nsg-flow-logs-do"></a>**O que os logs de fluxo do NSG fazem?**

Os recursos de rede do Azure podem ser combinados e gerenciados por meio [de NSGs (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/security-overview). Os logs de fluxo do NSG permitem que você registre informações de fluxo de 5 tuplas sobre todo o tráfego por meio de seu NSGs. Os logs de fluxo brutos são gravados em uma conta de armazenamento do Azure de onde eles podem ser processados, analisados, consultados ou exportados conforme necessário.

### <a name="does-using-flow-logs-impact-my-network-latency-or-performance"></a>**O uso de logs de fluxo afeta a latência ou o desempenho da rede?**

Os dados de logs de fluxo são coletados fora do caminho do tráfego de rede e, portanto, não afetam a taxa de transferência ou latência da rede. Você pode criar ou excluir logs de fluxo sem nenhum risco de impacto no desempenho da rede.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>**Como fazer usar logs de fluxo NSG com uma conta de armazenamento por trás de um firewall?**

Para usar uma conta de armazenamento por trás de um firewall, você precisa fornecer uma exceção para que os serviços confiáveis da Microsoft acessem sua conta de armazenamento:

- Navegue até a conta de armazenamento digitando o nome da conta de armazenamento na pesquisa global no portal ou na [página contas de armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- Na seção **configurações** , selecione **firewalls e redes virtuais**
- Em **permitir acesso de**, selecione **redes selecionadas**. Em **exceções**, marque a caixa ao lado de * * * * permitir que serviços confiáveis da Microsoft acessem esta conta de armazenamento * * * *
- Se já estiver selecionada, nenhuma alteração será necessária.
- Localize o NSG de destino na [página de visão geral dos logs de fluxo do NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e habilite os logs de fluxo do NSG com a conta de armazenamento acima selecionada.

Você pode verificar os logs de armazenamento após alguns minutos, você deve ver um carimbo de data/hora atualizado ou um novo arquivo JSON criado.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>**Como fazer usar logs de fluxo NSG com uma conta de armazenamento por trás de um ponto de extremidade de serviço?**

Os logs de fluxo NSG são compatíveis com pontos de extremidade de serviço sem a necessidade de nenhuma configuração extra. Consulte o [tutorial sobre como habilitar pontos de extremidade de serviço](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) em sua rede virtual.

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>**Qual é a diferença entre as versões 1 & 2 dos logs de fluxo?**

Os logs de fluxo versão 2 apresentam o conceito de _estado de fluxo_ & armazena informações sobre bytes e pacotes transmitidos. [Leia mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)

## <a name="pricing"></a>Preços

Os logs de fluxo NSG são cobrados por GB de logs coletados e vêm com uma camada gratuita de 5 GB/mês por assinatura. Para obter os preços atuais em sua região, consulte a [página de preços do observador de rede](https://azure.microsoft.com/pricing/details/network-watcher/).

O armazenamento de logs é cobrado separadamente, consulte a [página de preços do blob de blocos de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) para obter os preços relevantes.
 
