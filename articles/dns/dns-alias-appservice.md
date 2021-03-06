---
title: Hospedar aplicativos da web do Azure com carga balanceada no apex da zona
description: Use um registro de alias do DNS do Azure para hospedar aplicativos da Web com balanceamento de carga no ápice da zona
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 8ba96a028d51e6e5503bb4a8e6735b48033c9ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937360"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hospedar aplicativos da web do Azure com carga balanceada no apex da zona

O protocolo DNS impede a atribuição de algo diferente de um registro A ou AAAA no ápice da zona. Um vértice da zona de exemplo é contoso.com. Essa restrição apresenta um problema para os proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás do Gerenciador de Tráfego. Não é possível apontar para o perfil do Gerenciador de Tráfego no registro do ápice da zona. Como resultado, os proprietários do aplicativo devem usar uma solução alternativa. Um redirecionamento na camada de aplicativo deve redirecionar do ápice da zona para outro domínio. Um exemplo é um redirecionamento de contoso.com para www\.contoso.com. Esse arranjo apresenta um único ponto de falha para a função de redirecionamento.

Com registros de alias, esse problema não existe mais. Agora, os proprietários do aplicativo podem apontar seu registro do ápice da zona para um perfil do Gerenciador de Tráfego que tenha nós de extremidade externos. Os proprietários de aplicativos podem apontar para o mesmo perfil do Gerenciador de tráfego que é usado para qualquer outro domínio em sua zona DNS.

Por exemplo, contoso.com\.e www contoso.com podem apontar para o mesmo perfil de Gerenciador de Tráfego. Esse é o caso, desde que o perfil do Gerenciador de Tráfego tenha apenas endpoints externos configurados.

Neste artigo, você aprende a criar um registro de alias para o seu ápice de domínio e a configurar seu perfil de Gerenciador de Tráfego do Microsoft Azure para seus aplicativos da web.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter um nome de domínio disponível para hospedar no DNS do Azure para testar. Você deve ter controle total sobre esse domínio. Controle total inclui a capacidade de definir os registros NS (nomes de servidor) para o domínio.

Para obter instruções para hospedar seu domínio no DNS do Azure, consulte [Tutorial: hospede seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo usado neste tutorial é contoso.com, mas use seu próprio nome de domínio.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para armazenar todos os recursos usados neste artigo.

## <a name="create-app-service-plans"></a>Criar planos de serviço de aplicativo

Crie dois planos do Serviço de Aplicativo da Web em seu grupo de recursos usando a tabela a seguir para obter informações de configuração. Para obter mais informações sobre como criar um plano do Serviço de Aplicativo, consulte [Gerenciar um plano do Serviço de Aplicativo no Azure](../app-service/app-service-plan-manage.md).


|Nome  |Sistema operacional  |Location  |Camada de preços  |
|---------|---------|---------|---------|
|ASP-01     |Windows|Leste dos EUA|Dev / Teste D1-Compartilhado|
|ASP-02     |Windows|Centro dos EUA|Dev / Teste D1-Compartilhado|

## <a name="create-app-services"></a>Criar Serviços de Aplicativos

Crie dois aplicativos da web, um em cada plano do Serviço de Aplicativo.

1. No canto superior esquerdo da página do portal Azure, **selecione Criar um recurso**.
2. Digite **Web app** na barra de pesquisa e pressione Enter.
3. Selecione **web app**.
4. Selecione **Criar**.
5. Aceite os padrões e use a tabela a seguir para configurar os dois aplicativos da Web:

   |Nome<br>(deve ser exclusivo dentro do. azurewebsites.net)|Grupo de recursos |Pilha de runtime|Região|Plano do serviço de aplicativo/localização
   |---------|---------|-|-|-------|
   |App-01|Uso existente<br>Selecione o grupo de recursos|.NET Core 2.2|Leste dos EUA|ASP-01(D1)|
   |App-02|Uso existente<br>Selecione o grupo de recursos|.NET Core 2.2|Centro dos EUA|ASP-02(D1)|

### <a name="gather-some-details"></a>Reúna alguns detalhes

Agora você precisa anotar o endereço IP e o nome do host para os aplicativos da Web.

1. Abra seu grupo de recursos e selecione seu primeiro aplicativo web **(App-01** neste exemplo).
2. Na coluna esquerda, selecione **Propriedades**.
3. Anote o endereço em **URL** e, em **Endereços IP de saída**, observe o primeiro endereço IP na lista. Você usará essas informações mais tarde quando configurar os pontos finais do Gerenciador de Tráfego.
4. Repita a operação em **aplicativo-02**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

Crie um perfil do Gerenciador de Tráfego no seu grupo de recursos. Use os padrões e digite um nome exclusivo dentro do namespace trafficmanager.net.

Para obter informações sobre como criar um perfil do Gerenciador de Tráfego do Microsoft Azure, consulte [Guia de início rápido: crie um perfil do Gerenciador de Tráfego para um aplicativo da Web altamente disponível](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Criar pontos de extremidade

Agora você pode criar os pontos de extremidade para os dois aplicativos web.

1. Abra seu grupo de recursos e selecione o perfil do Gerenciador de Tráfego.
2. Na coluna esquerda, selecione **Endpoints**.
3. Selecione **Adicionar**.
4. Use a tabela a seguir para configurar os terminais:

   |Type  |Nome  |Destino  |Location  |Configurações personalizadas de cabeçalho|
   |---------|---------|---------|---------|---------|
   |Ponto de extremidade externo     |Final-01|Endereço IP que você registrou para o App-01|Leste dos EUA|host:\<a URL que você registrou para o aplicativo 01\><br>Exemplo: **: o aplicativo host-01.azurewebsites.net**|
   |Ponto de extremidade externo     |Final-02|Endereço IP que você registrou para o aplicativo-02|Centro dos EUA|host:\<a URL que você registrou para o aplicativo-02\><br>Exemplo: **: o aplicativo host-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Criar zona DNS

Você pode usar uma zona DNS existente para teste ou criar uma nova zona. Para criar e delegar uma nova zona DNS no Azure, consulte [Tutorial: hospede seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Adicione um registro TXT para validação de domínio personalizado

Quando você adiciona um nome de host personalizado aos seus aplicativos da Web, ele procurará um registro TXT específico para validar seu domínio.

1. Abra seu grupo de recursos e selecione a zona DNS.
2. Escolha **Conjunto de registros**.
3. Adicione o conjunto de registros usando a tabela a seguir. Para o valor, use a URL real do aplicativo web que você gravou anteriormente:

   |Nome  |Type  |Valor|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Adicione um domínio personalizado para ambos os aplicativos da Web.

1. Abra seu grupo de recursos e selecione seu primeiro aplicativo web.
2. Na coluna à esquerda, selecione **Domínios personalizados**.
3. Em **Domínios personalizados,** selecione **Adicionar domínio personalizado**.
4. Em **domínio personalizado,** digite seu nome de domínio personalizado. Por exemplo, contoso.com.
5. Selecione **Validar**.

   Seu domínio deve passar a validação e mostrar marcas de verificação verdes ao lado da **disponibilidade do Hostname** e **da propriedade do Domínio**.
5. Selecione **Adicionar domínio personalizado**.
6. Para ver o novo nome do host em **Hostnames atribuídos ao site**, atualize seu navegador. A atualização na página nem sempre mostra alterações imediatamente.
7. Repita este procedimento para seu segundo aplicativo web.

## <a name="add-the-alias-record-set"></a>Adicione o conjunto de registros de alias

Agora adicione um registro de alias para o ápice da região.

1. Abra seu grupo de recursos e selecione a zona DNS.
2. Escolha **Conjunto de registros**.
3. Adicione o registro definido usando a tabela a seguir:

   |Nome  |Type  |Conjunto de registros de alias  |Tipo de alias  |Recursos do Azure|
   |---------|---------|---------|---------|-----|
   |@     |Um|Sim|Recursos do Azure|Gerenciador de tráfego - seu perfil|


## <a name="test-your-web-apps"></a>Teste seus aplicativos web

Agora você pode testar para ter certeza de que você pode acessar o seu aplicativo web e que ele está sendo carregado equilibrado.

1. Abra um navegador da web e navegue até seu domínio. Por exemplo, contoso.com. Você deve ver a página de aplicativo web padrão.
2. Interrompa seu primeiro aplicativo web.
3. Feche seu navegador da web e aguarde alguns minutos.
4. Inicie seu navegador da Web e navegue até seu domínio. Você ainda deve ver a página do aplicativo da web padrão.
5. Interrompa seu segundo aplicativo web.
6. Feche seu navegador da web e aguarde alguns minutos.
7. Inicie seu navegador da Web e navegue até seu domínio. Você deve ver o erro 403, indicando que o aplicativo da Web está parado.
8. Inicie seu segundo aplicativo da web.
9. Feche seu navegador da web e aguarde alguns minutos.
10. Inicie seu navegador da Web e navegue até seu domínio. Novamente, você verá a página de aplicativo web padrão.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre registros de alias, consulte os seguintes artigos:

- [Tutorial: Configure um registro de alias para consultar um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio com o Gerenciador de tráfego do apex](tutorial-alias-tm.md)
- [PERGUNTAS FREQUENTES SOBRE O DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Para saber como migrar um nome DNS ativo, consulte [Migrar um nome DNS ativo para Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
