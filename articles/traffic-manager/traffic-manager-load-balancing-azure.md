---
title: Usando os serviços de balanceamento de carga no Azure | Microsoft Docs
description: 'Este tutorial mostra como criar um cenário usando o portfólio de balanceamento de carga do Azure: Gerenciador de Tráfego, Gateway de Aplicativo e Balanceador de Carga.'
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: rohink
ms.openlocfilehash: cccd4a6b0b52608a6a17b73688e18f27088df5b0
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757194"
---
# <a name="using-load-balancing-services-in-azure"></a>Usando os serviços de balanceamento de carga no Azure

## <a name="introduction"></a>Introdução

O Microsoft Azure fornece vários serviços para gerenciar a forma como o tráfego de rede é distribuído e como sua carga é balanceada. Com base nas suas necessidades específicas, você pode usar esses serviços individualmente ou combinar seus métodos para criar a solução ideal.

Neste tutorial, primeiro definimos um caso de uso do cliente e vemos como ele pode se tornar mais robusto e eficaz usando este portfólio de balanceamento de carga do Azure: Gerenciador de Tráfego, Gateway de Aplicativo e Balanceador de Carga. Em seguida, fornecemos instruções passo a passo para criar uma implantação que seja geograficamente redundante, que distribua o tráfego para VMs e ajuda você a gerenciar diferentes tipos de solicitações.

Em um nível conceitual, cada um desses serviços desempenha uma função distinta na hierarquia de balanceamento de carga.

* **O Gerenciador de Tráfego** fornece balanceamento de carga DNS global. Ele analisa as solicitações de entrada DNS e responde com um ponto de extremidade íntegro e que esteja de acordo com a política de roteamento selecionada pelo cliente. Opções de métodos de roteamento são:
  * Roteamento de desempenho para enviar o solicitante ao ponto de extremidade mais próximo em termos de latência.
  * Roteamento de prioridade para direcionar todo o tráfego para um ponto de extremidade com outros pontos de extremidade como backup.
  * Roteamento ponderado round-robin, que distribui o tráfego com base no peso atribuído a cada ponto de extremidade.
  * Roteamento baseado na geografia para distribuir o tráfego para os pontos de extremidade do aplicativo com base na localização geográfica do usuário.
  * Roteamento baseado na sub-rede para distribuir o tráfego para os pontos de extremidade do aplicativo com base na sub-rede (intervalo de endereços IP) do usuário.
  * Roteamento de vários valores que permite enviar endereços IP de mais de um ponto de extremidade do aplicativo em uma única resposta DNS.

  O cliente se conecta diretamente ao ponto de extremidade retornado pelo Gerenciador de Tráfego. O Gerenciador de Tráfego do Azure detecta quando um ponto de extremidade não está íntegro e redireciona os clientes para outra instância íntegra. Consulte a [documentação do Gerenciador de Tráfego do Azure](traffic-manager-overview.md) para saber mais sobre o serviço.
* O **Gateway de Aplicativo** fornece o ADC (Controlador de Entrega de Aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para o aplicativo. Ele permite que os clientes otimizem a produtividade da fazenda web descarregando a terminação TLS intensiva em CPU para o gateway de aplicativo. Outros recursos de roteamento de camada 7 incluem distribuição round robin do tráfego de entrada, afinidade de sessão, roteamento com base no caminho de URL e capacidade de hospedar vários sites com um único Gateway de Aplicativo baseado em cookie. O Gateway de Aplicativo pode ser configurado como um gateway voltado para a Internet, um gateway apenas interno ou uma combinação de ambos. O Gateway de Aplicativo é totalmente gerenciado pelo Azure, escalonável e altamente disponível. Ele fornece um conjunto avançado de recursos de log e diagnósticos para melhor capacidade de gerenciamento.
* **O Balancer** de carga é parte integrante da pilha SDN do Azure, fornecendo serviços de balanceamento de carga de alta latência e baixa latência para todos os protocolos UDP e TCP. Ele gerencia conexões de entrada e saída. Você pode configurar pontos de extremidade públicos e internos com balanceamento de carga e definir regras para mapear as conexões de entrada para destinos de pool de back-end usando opções TCP e HTTP de investigação de integridade para gerenciar a disponibilidade do serviço.

## <a name="scenario"></a>Cenário

Nesse cenário de exemplo, usamos um site simples com dois tipos de conteúdo: imagens e páginas da Web processadas dinamicamente. O site deve ser geograficamente redundante e deve atender a seus usuários da localização mais próxima (menor latência) para eles. O desenvolvedor do aplicativo decidiu que todas as URLs que correspondam o padrão /images/* são atendidas de um pool de VMs dedicado que é diferente do restante do web farm.

Além disso, o pool de VMs padrão que atende o conteúdo dinâmico precisa se comunicar com um banco de dados de back-end hospedado em um cluster de alta disponibilidade. Toda a implantação é configurada por meio do Azure Resource Manager.

A utilização do Gerenciador de Tráfego, do Gateway de Aplicativo e do Balanceador de Carga permite que este site atinja essas metas de design:

* **Redundância de várias áreas geográficas**: se uma região ficar inativa, o Gerenciador de Tráfego roteia o tráfego para a melhor região próxima sem nenhuma intervenção do proprietário do aplicativo.
* **Latência reduzida**: como o Gerenciador de Tráfego direciona o cliente automaticamente para a região mais próxima, o cliente experimenta menor latência ao solicitar o conteúdo da página da Web.
* **Escalabilidade independente**: como a carga de trabalho de aplicativos Web separada por tipo de conteúdo, o proprietário do aplicativo pode dimensionar as cargas de trabalho de solicitação de forma independente. O Gateway de Aplicativo garante que o tráfego seja roteado para os pools corretos com base nas regras especificadas e na integridade da integridade do aplicativo.
* **Balanceamento de carga interno**: como balanceador de carga a frente do cluster de alta disponibilidade, somente o ponto de extremidade íntegro e ativo para um banco de dados é exposto ao aplicativo. Além disso, um administrador de banco de dados pode otimizar a carga de trabalho distribuindo réplicas ativas e passivas no cluster, independentemente do aplicativo front-end. O balanceador de carga fornece conexões para o cluster de alta disponibilidade e garante que apenas bancos de dados íntegros recebam solicitações de conexão.

O diagrama a seguir mostra a arquitetura desse cenário:

![Diagrama de arquitetura de balanceamento de carga](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Este exemplo é apenas uma das muitas configurações possíveis dos serviços de balanceamento de carga oferecidos pelo Azure. O Gateway de Aplicativo, o Balanceador de Carga e o Gerenciador de Tráfego podem ser associados e combinados para melhor atender às necessidades de balanceamento de carga. Por exemplo, se a descarga tls ou o processamento da camada 7 não fornecessário, o Balancer de carga pode ser usado no lugar do Gateway de aplicativo.

## <a name="setting-up-the-load-balancing-stack"></a>Configurando a pilha de balanceamento de carga

### <a name="step-1-create-a-traffic-manager-profile"></a>Etapa 1: Criar um perfil do Gerenciador de Tráfego

1. No portal Azure, clique em Criar um perfil de**gerenciador de** > tráfego de **rede de** > **recursos** > **Criar**.
2. Insira as seguintes informações básicas:

   * **Nome**: forneça ao seu perfil de Gerenciador de Tráfego um nome de prefixo DNS.
   * **Método de roteamento**: selecione a política de método de roteamento de tráfego. Para obter mais informações sobre os métodos, consulte [Sobre os métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).
   * **Assinatura**: selecione a assinatura que contém o perfil.
   * **Grupo de recursos**: selecione o grupo de recursos que contém o perfil. Pode ser um grupo de recursos novo ou existente.
   * **Local do grupo de recursos**: serviço do Gerenciador de Tráfego é global e não está associado a um local. No entanto, você deve especificar uma região para o grupo onde residem os metadados associados com o perfil do Gerenciador de Tráfego. Essa localização não tem impacto sobre a disponibilidade de runtime do perfil.

3. Clique **em Criar** para gerar o perfil do Gerenciador de tráfego.

   ![Criar folha do Gerenciador de Tráfego](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Etapa 2: Criar os Gateways de Aplicativo

1. No portal Azure, no painel esquerdo, clique em **Criar um gateway** > de aplicativo**de** > **rede de**recursos .
2. Insira as seguintes informações básicas sobre o gateway de aplicativo:

   * **Name**: o nome do gateway de aplicativo.
   * **Tamanho do SKU**: esse é o tamanho do gateway de aplicativo, as opções disponíveis são Pequeno, Médio e Grande.
   * **Contagem de instâncias**: O número de instâncias, um valor de 2 a 10.
   * **Grupo de recursos**: o grupo de recursos que mantém o gateway de aplicativo. Pode ser um grupo de recursos existente ou um novo.
   * **Localização**: a região do gateway de aplicativo, que é a mesma localização no grupo de recursos. O local é importante porque a rede virtual e o IP público devem estar na mesma localização do que o gateway.
3. Clique em **OK**.
4. Defina a rede virtual, a sub-rede, o IP de front-end e as configurações do ouvinte para o Gateway de Aplicativo. Nesse cenário, o endereço IP de front-end é **Público**, o que permite que ele seja adicionado, mais tarde, como um ponto de extremidade para o perfil do Gerenciador de Tráfego.
5. Configure o ouvinte com uma das seguintes opções:
    * Se você usar HTTP, não há nada a ser configurado. Clique em **OK**.
    * Se você usar HTTPS, será necessária configuração adicional. Consulte [Criar um gateway de aplicativo](../application-gateway/application-gateway-create-gateway-portal.md), começando na etapa 9. Quando tiver concluído a configuração, clique em **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Configurar o roteamento de URL para os gateways de aplicativo

Quando você escolhe um pool de back-end, um Gateway de Aplicativo configurado com uma regra com base em caminho toma um padrão de caminho da URL da solicitação, além da distribuição round robin. Nesse cenário, estamos adicionando uma regra com base em caminho para direcionar qualquer URL com "/images/\*" ao pool de servidor de imagem. Para obter mais informações sobre roteamento com base em caminho de configuração de URL para um Gateway de Aplicativo, consulte [Criar uma regra com base em caminho para um gateway de aplicativo](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagrama de camada da web de Gateway de Aplicativo](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. A partir do grupo de recursos, navegue até a instância do gateway de aplicativo criada na seção anterior.
2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, selecione **Adicionar** para adicionar as VMs que você desejar associar ao pool de back-end de camada da Web.
3. Digite o nome do pool de back-end e todos os endereços IP dos computadores que residem no pool. Nesse cenário, estamos conectando dois pools de servidores de back-end de máquinas virtuais.

   !["Adicionar pool de back-end" do Gateway de Aplicativo](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Em **Configurações** de Gateway de Aplicativo, selecione **Regras** e, em seguida, clique no botão **Com base em caminho** para adicionar uma regra.

   ![Botão de "Caminho com base em" de regras de Gateway de Aplicativo](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Configure a regra fornecendo as informações a seguir.

   Configurações básicas:

   + **Nome**: um nome amigável para a regra que está acessível no portal.
   + **Ouvinte**: ouvinte usado para a regra.
   + **Pool de back-end padrão**: pool de back-end a ser usado com a regra padrão.
   + **Configurações HTTP padrão**: as configurações HTTP a serem usadas para a regra padrão.

   Regras com base no caminho:

   + **Nome**: um nome amigável da regra com base em caminho.
   + **Caminhos**: a regra de caminho usada para encaminhar o tráfego.
   + **Pool de back-end**: pool de back-end a ser usado com esta regra.
   + **Configuração HTTP**: as configurações HTTP a serem usadas com esta regra.

   > [!IMPORTANT]
   > Caminhos: caminhos válidos devem começar com "/". O caractere curinga "\*" só é permitido no final. Exemplos válidos são /xyz, /xyz \*, ou /xyz/\*.

   ![Folha "Adicionar regra com base no caminho" do Gateway de Aplicativo](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Etapa 3: Adicionar Gateways de Aplicativo aos pontos de extremidade do Gerenciador de Tráfego

Nesse cenário, o Gerenciador de Tráfego está conectado a instâncias do gateway de aplicativo (conforme definido nas etapas acima) que residem em diferentes regiões. Agora que os gateways de aplicativo estão configurados, a próxima etapa é conectar-se ao nosso perfil do Gerenciador de Tráfego.

1. Abra o seu perfil do Gerenciador de Tráfego. Para fazer isso, procure no seu grupo de recursos ou procure o nome do perfil do Gerenciador de Tráfego em **Todos os Recursos**.
2. No painel esquerdo, selecione **Pontos de Extremidade**e, em seguida, clique em **Adicionar** para adicionar um ponto de extremidade.

   ![Botão “Adicionar” de Pontos de extremidade do Gerenciador de Tráfego](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Crie um ponto de extremidade inserindo as seguintes informações:

   * **Tipo**: selecione o tipo de ponto de extremidade para o balanceamento de carga. Nesse cenário, selecione **ponto de extremidade do Azure**, já que estamos nos conectando com as instâncias de gateway de aplicativo que foram configuradas anteriormente.
   * **Nome**: insira o nome do ponto de extremidade.
   * **Tipo de recurso de destino**: selecione o **endereço IP público** e, na configuração do **Recurso de destino**, selecione o IP público do gateway de aplicativo configurado anteriormente.

   ![“Adicionar ponto de extremidade” do Gerenciador de Tráfego](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Agora, você pode testar a instalação, acessando-a com o DNS do seu perfil de Gerenciador de Tráfego (neste exemplo: TrafficManagerScenario.trafficmanager.net). Você pode reenviar solicitações, colocar para cima/baixo servidores Web e VMs que foram criados em regiões diferentes e alterar as configurações do perfil de Gerenciador de Tráfego para testar sua instalação.

### <a name="step-4-create-a-load-balancer"></a>Etapa 4: criar um balanceador de carga

Neste cenário, o balanceador de carga distribui conexões entre a camada da Web e os bancos de dados em um cluster de alta disponibilidade.

Se o cluster de banco de dados de alta disponibilidade estiver usando o AlwaysOn do SQL Server, consulte Configurar um ou mais [Sempre em ouvintes de grupo de disponibilidade](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) para obter instruções passo a passo.

Para obter mais informações sobre como configurar um balanceador de carga interno, consulte [Criar um balanceador de carga interno no portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. No portal Azure, no painel esquerdo, clique em **Criar um balanceador** > de carga**de** > **rede de recursos**.
2. Escolha um nome para o balanceador de carga.
3. Defina o **Tipo** **como Interno**e escolha a rede e a sub-rede virtuais apropriadas para que o balanceador de carga resida.
4. Em **atribuição de endereço IP,** selecione **Dinâmica** ou **Estática**.
5. Em **Grupo de recursos**, escolha o grupo de recursos para o balanceador de carga.
6. Em **Localização,** escolha a região apropriada para o balanceador de carga.
7. Clique em **Criar** para gerar o balanceador de carga.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Conectar uma camada de banco de dados de back-end ao balanceador de carga

1. A partir do grupo de recursos, encontre o balanceador de carga que foi criado nas etapas anteriores.
2. Em **Configurações**, clique em **Pools de back-end** e clique em **Adicionar** para adicionar um pool de back-end.

   !["Adicionar pool de back-end" do Load Balancer](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Digite o nome do pool de back-end.
4. Adicione máquinas individuais ou uma conjunto de disponibilidade para o pool de back-end.

#### <a name="configure-a-probe"></a>Configurar uma investigação

1. No seu balanceador de carga, abaixo de **Configurações**, selecione **Investigações** e, em seguida, clique em **Adicionar** para adicionar uma investigação.

   !["Adicionar teste" do Load Balancer](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Digite o nome para a investigação.
3. Selecione o **Protocolo** para a investigação. Para um banco de dados, é ideal utilizar uma investigação TCP em vez de uma investigação HTTP. Para saber mais sobre investigações de balanceador de carga, consulte [Entender investigações do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md).
4. Digite a **Porta** do banco de dados a ser utilizada ao acessar a investigação.
5. Em **Intervalo** , especifique a frequência de investigação do aplicativo.
6. Em **limiar insalubre,** especifique o número de falhas contínuas do teste que devem ocorrer para que a VM back-end seja considerada insalubre.
7. Clique em **OK** para criar a sonda.

#### <a name="configure-the-load-balancing-rules"></a>Configuração de regras de balanceamento de carga

1. Em **Configurações **do balanceador de carga, selecione **Regras de balanceamento de carga** e, em seguida, clique em **Adicionar** para criar uma regra.
2. Insira o **Nome** da regra de balanceamento de carga.
3. Escolha o **endereço IP frontend** do balanceador de carga, **protocolo**e **porta**.
4. Em **Porta Backend,** especifique a porta a ser usada no pool back-end.
5. Selecione o **Pool de back-end** e a **Investigação** criados nas etapas anteriores para aplicar a regra.
6. Em **Sessão persistência,** escolha como deseja que as sessões persistam.
7. Em **tempos de ocioso,** especifique o número de minutos antes de um intervalo ocioso.
8. Em **IP flutuante**, selecione **Desabilitado** ou **Habilitado**.
9. Clique em **OK** para criar a regra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Etapa 5: Conectar VMs de camada da Web ao balanceador de carga

Agora podemos configurar o endereço IP e a porta de front-end do balanceador de carga dos aplicativos em execução em suas VMs de camada da Web para qualquer conexão de banco de dados. Essa configuração é específica para o aplicativo que é rodado nessas VMs. Para configurar o endereço IP de destino e a porta, consulte a documentação do aplicativo. Para localizar o endereço IP de front-end, navegue até o pool de IP de front-end nas **Configurações do balanceador de carga** no portal do Azure.

![Painel de navegação de "Pool Frontend IP" do balanceador de carga](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Próximas etapas

* [Visão Geral do Gerenciador de Tráfego](traffic-manager-overview.md)
* [Visão geral do Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md)
* [Visão geral do Balancer de carga do Azure](../load-balancer/load-balancer-overview.md)
