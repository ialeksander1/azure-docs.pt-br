---
title: Mostrar dados de tráfego no mapa do Android | Mapas do Microsoft Azure
description: Neste artigo você aprenderá como exibir dados de tráfego em um mapa usando o Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335386"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Mostrar dados de tráfego no mapa usando o Azure Maps Android SDK

Dados de fluxo e dados de incidentes são os dois tipos de dados de tráfego que podem ser exibidos no mapa. Este guia mostra como exibir os dois tipos de dados de tráfego. Os dados de incidentes consistem em dados baseados em pontos e linhas para coisas como construções, fechamentos de estradas e acidentes. Os dados de fluxo mostram métricas sobre o fluxo de tráfego na estrada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de mostrar o tráfego no mapa, você precisa [fazer uma conta do Azure](quick-demo-map-app.md#create-an-account-with-azure-maps)e obter uma chave de [assinatura](quick-demo-map-app.md#get-the-primary-key-for-your-account). Em seguida, você precisa instalar o [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) e carregar um mapa.

## <a name="incidents-traffic-data"></a>Dados de tráfego de incidentes 

Você precisará importar as seguintes bibliotecas para chamar `setTraffic` e: `incidents`

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 O trecho de código a seguir mostra como exibir dados de tráfego no mapa. Passamos um valor booleano para o `incidents` `setTraffic` método, e passamos isso para o método. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Dados de tráfego de fluxo

Primeiro você precisará importar as seguintes `setTraffic` bibliotecas para chamar e: `flow`

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Use o seguinte trecho de código para definir os dados de fluxo de tráfego. Semelhante ao código na seção anterior, passamos `flow` o `setTraffic` valor de retorno do método para o método. Existem quatro valores que `flow`podem ser repassados , e cada valor seria acionado `flow` para devolver o respectivo valor. O valor `flow` de retorno será então `setTraffic`passado como o argumento para . Veja a tabela abaixo para estes quatro valores:

| | |
| :-- | :-- |
| TrafficFlow.NONE | Não exibe dados de tráfego no mapa |
| TrafficFlow.RELATIVE | Mostra dados de tráfego relativos à velocidade de fluxo livre da estrada |
| TrafficFlow.RELATIVE_DELAY | Exibe áreas mais lentas do que o atraso médio esperado |
| TrafficFlow.ABSOLUTE | Mostra a velocidade absoluta de todos os veículos na estrada |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Mostrar dados de tráfego de incidentes clicando em um recurso

Para obter os incidentes para um recurso específico, você pode usar o código abaixo. Quando um recurso é clicado, a lógica de código verifica se há incidentes e cria uma mensagem sobre o incidente. Uma mensagem aparece na parte inferior da tela com os detalhes.

1. Primeiro, você precisa editar **res > layout > activity_main.xml,** para que pareça com o abaixo. Você pode `mapcontrol_centerLat`substituir `mapcontrol_centerLng`os `mapcontrol_zoom` valores desejados e os valores desejados. Lembre-se, o nível de zoom é um valor entre 0 e 22. No nível de zoom 0, o mundo inteiro se encaixa em um único azulejo.

   ```XML
   <?xml version="1.0" encoding="utf-8"?>
   <FrameLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       >
    
       <com.microsoft.azure.maps.mapcontrol.MapControl
           android:id="@+id/mapcontrol"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Adicione o seguinte código ao seu arquivo **MainActivity.java.** O pacote está incluído por padrão, por isso certifique-se de manter seu pacote no topo.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
       }

       @Override
       public void onPause() {
           super.onPause();
           mapControl.onPause();
       }

       @Override
       public void onStop() {
           super.onStop();
           mapControl.onStop();
       }

       @Override
       public void onLowMemory() {
           super.onLowMemory();
           mapControl.onLowMemory();
       }

       @Override
       protected void onDestroy() {
           super.onDestroy();
           mapControl.onDestroy();
       }

       @Override
       protected void onSaveInstanceState(Bundle outState) {
           super.onSaveInstanceState(outState);
           mapControl.onSaveInstanceState(outState);
       }
   }
   ```

3. Depois de incorporar o código acima em seu aplicativo, você poderá clicar em um recurso e ver os detalhes dos incidentes de trânsito. Dependendo da latitude, longitude e dos valores de nível de zoom que você usou no seu arquivo **activity_main.xml,** você verá resultados semelhantes à seguinte imagem:

   <center>

   ![Incidente-tráfego no mapa](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Próximas etapas

Veja os seguintes guias para saber como adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar formas ao mapa android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Exibir informações do recurso](display-feature-information-android.md)
