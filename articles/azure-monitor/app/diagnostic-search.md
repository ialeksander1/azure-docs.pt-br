---
title: Usar a Pesquisa no Azure Application Insights | Microsoft Docs
description: Pesquise e filtre telemetria bruta enviada pelo seu aplicativo Web.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275978"
---
# <a name="using-search-in-application-insights"></a>Usar a Pesquisa no Application Insights

A Pesquisa é um recurso do [Application Insights](../../azure-monitor/app/app-insights-overview.md) que você usa para localizar e explorar itens individuais de telemetria, como exibições de página, exceções ou solicitações da Web. Você também pode exibir rastreamentos de log e eventos que você tenha codificado.

(Para consultas mais complexas sobre os dados, use o [Analytics](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Onde você vê o Search?

### <a name="in-the-azure-portal"></a>No portal do Azure

Você pode abrir pesquisa de diagnóstico a partir da guia Visão Geral do Aplicativo Insights do seu aplicativo (localizado na barra superior) ou sob investigação à esquerda.

![Guia Pesquisar](./media/diagnostic-search/view-custom-events.png)

Vá ao menu suspenso dos tipos de eventos para ver uma lista de itens de telemetria: solicitações de servidor, exibições de página, eventos personalizados que você codificou e assim por diante. No topo da lista de resultados, está um gráfico de resumo mostrando a contagem de eventos ao longo do tempo.

Clique para fora do menu suspenso ou Atualize para obter novos eventos.

### <a name="in-visual-studio"></a>No Visual Studio

No Visual Studio, há também uma janela da Pesquisa do Application Insights. É mais útil exibir eventos de telemetria gerados pelo aplicativo que você está depurando. Contudo, ele também pode mostrar os eventos coletados do seu aplicativo publicado no Portal do Azure.

Abra a janela Pesquisar no Visual Studio:

![O Visual Studio abre a pesquisa do Application Insights](./media/diagnostic-search/32.png)

A janela Pesquisar tem os mesmos recursos que o portal da Web:

![Janela de pesquisa do Visual Studio Application Insights](./media/diagnostic-search/34.png)

A guia Controlar Operação está disponível ao abrir uma solicitação ou uma exibição de página. Uma “operação” é uma sequência de eventos associada a uma única solicitação ou exibição de página. Por exemplo, chamadas de dependência, exceções, logs de rastreamento e eventos personalizados podem fazer parte de uma única operação. A guia Controlar Operação mostra graficamente o tempo e a duração desses eventos em relação à exibição de solicitação ou página.

## <a name="inspect-individual-items"></a>Inspecionar itens individuais

Selecione qualquer item de telemetria para ver os campos-chave e itens relacionados.

![Captura de tela de uma solicitação de dependência individual](./media/diagnostic-search/telemetry-item.png)

Isso iniciará a exibição de detalhes da transação completa.

## <a name="filter-event-types"></a>Filtrar tipos de evento

Abra o menu suspenso dos tipos de evento e escolha os tipos de eventos que deseja ver. (Se, mais tarde, você quiser restaurar os filtros, clique em Redefinir.)

Os tipos de evento são:

* **Rastreie** - [registros de diagnóstico,](../../azure-monitor/app/asp-net-trace-logs.md) incluindo chamadas TrackTrace, log4Net, NLog e System.Diagnostic.Trace.
* **Solicitar** -Solicitações HTTP recebidas pelo seu aplicativo para servidores, incluindo páginas, scripts, imagens, arquivos de estilo e dados. Esses eventos são usados para criar os gráficos de visão geral de solicitação e de resposta.
* **Página Exibir** - [Telemetria enviada pelo cliente web](../../azure-monitor/app/javascript.md), usada para criar relatórios de exibição de página.
* **Evento Personalizado** ‑ Se você tiver inserido chamadas TrackEvent() para [monitorar o uso](../../azure-monitor/app/api-custom-events-metrics.md), você poderá pesquisá-las aqui.
* **Exceção** - Exceções não [capturadas no servidor](../../azure-monitor/app/asp-net-exceptions.md)e aquelas que você registra usando TrackException().
* **Chamadas de dependência** - [do aplicativo do servidor](../../azure-monitor/app/asp-net-dependencies.md) para outros serviços, como APIs ou bancos de dados REST, e chamadas AJAX do código do [cliente](../../azure-monitor/app/javascript.md).
* **Disponibilidade** ‑ Resultados de [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrar pelos valores de propriedade

Você pode filtrar eventos pelos valores de suas propriedades. As propriedades disponíveis dependem dos tipos de evento que você selecionou. Clique no ícone do filtro ![Ícone do filtro](./media/diagnostic-search/filter-icon.png) para começar.

Não escolher nenhum valor para uma determinada propriedade tem o mesmo efeito que escolher todos os valores. Ele desativará a filtragem nessa propriedade.

Observe que as contagens à direita dos valores de filtro mostram quantas ocorrências existem no atual conjunto filtrado.

## <a name="find-events-with-the-same-property"></a>Encontrar eventos com a mesma propriedade

Para encontrar todos os itens com o mesmo valor de propriedade, digite-o na barra de pesquisa ou clique na caixa de seleção ao olhar através de propriedades na guia do filtro.

![Clique na caixa de seleção de uma propriedade na guia filtro](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Pesquisar os dados

> [!NOTE]
> Para escrever consultas mais complexas, abra [**Logs (Analytics)**](../../azure-monitor/log-query/get-started-portal.md) na parte superior da lâmina pesquisar.
>

Você pode pesquisar termos em qualquer um dos valores de propriedade. Isso é útil se você tiver escrito [eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md) com valores de propriedade.

Você talvez queira definir um tempo de intervalo, já que pesquisas em um intervalo mais curto são mais rápidas.

![Abra a pesquisa de diagnóstico](./media/diagnostic-search/search-property.png)

Pesquisar por palavras inteiras, não subcadeias de caracteres. Use aspas para delimitar caracteres especiais.

| String | *Não* encontrado | Encontrado |
| --- | --- | --- |
| ControladorInicial.Sobre |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Estados Unidos|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Estas são algumas expressões de pesquisa que você pode usar:

| Exemplo de consulta | Efeito |
| --- | --- |
| `apple` |Encontrar todos os eventos no período cujos campos incluem a palavra “maçã” |
| `apple AND banana` <br/>`apple banana` |Encontrar eventos que contêm as duas palavras. Use "AND” em letras maiúsculas, e não "and". <br/>Forma abreviada. |
| `apple OR banana` |Encontrar eventos que contêm uma das duas palavras. Use "OR", e não "or". |
| `apple NOT banana` |Encontre eventos que contêm uma das palavras, mas não a outra. |

## <a name="sampling"></a>amostragem

Se o seu aplicativo gerar uma grande quantidade de telemetria (e você estiver usando o ASP.NET versão SDK 2.0.0-beta3 ou posterior), o módulo de amostragem adaptativa reduz automaticamente o volume que é enviado ao portal enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados à mesma solicitação serão selecionadas ou desmarcadas como um grupo, para que você possa navegar entre os eventos relacionados.

[Saiba mais sobre amostragem](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Criar um item de trabalho

Você pode criar um bug no GitHub ou Azure DevOps com os detalhes de qualquer item de telemetria.

Vá para a exibição de detalhes de transação de ponta a ponta clicando em qualquer item de telemetria e selecione **Criar item de trabalho**.

![Clique em Novo Item de Trabalho, edite os campos e, em seguida, clique em OK.](./media/diagnostic-search/work-item.png)

Na primeira vez que fizer isso, será solicitado que você configure um link para sua conta e projeto do Azure DevOps.

(Você também pode configurar o link na guia Itens de trabalho.)

## <a name="send-more-telemetry-to-application-insights"></a>Enviar mais telemetria para o Application Insights

Além de telemetria da caixa enviada pelo SDK do Application Insights, você pode:

* Capturar rastreamentos de log da sua estrutura de registros favorita no [.NET](../../azure-monitor/app/asp-net-trace-logs.md) ou [Java](../../azure-monitor/app/java-trace-logs.md). Isso significa que você pode pesquisar os rastreamentos de log e correlacioná-los com outros eventos, exceções e visualizações de página.
* [Escrever código](../../azure-monitor/app/api-custom-events-metrics.md) para enviar eventos personalizados, visualizações de página e exceções.

[Saiba como enviar logs e telemetria personalizada para o Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>Perguntas e respostas

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Que quantidade de dados é mantida?

Veja o [Resumo de limites](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como consultar dados de POSTAGEM nas minhas solicitações de servidor?

Nós não registramos os dados de POST automaticamente, mas você pode usar [chamadas de log ou TrackTrace](../../azure-monitor/app/asp-net-trace-logs.md). Coloque os dados de POSTAGEM no parâmetro de mensagem. Não é possível filtrar a mensagem da mesma maneira que as propriedades, mas o limite de tamanho é maior.

## <a name="next-steps"></a><a name="add"></a>Próximas etapas

* [Escrever consultas complexas no Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Enviar logs e telemetria personalizada para o Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Configurar testes de disponibilidade e capacidade de resposta](../../azure-monitor/app/monitor-web-app-availability.md)
* [Solução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
