---
title: Monitorar carga de trabalho-portal do Azure
description: Monitorar o Synapse SQL usando o portal do Azure
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 327174974affb3b2511eac60755aa1bf047b3b5e
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133460"
---
# <a name="monitor-workload---azure-portal"></a>Monitorar carga de trabalho-portal do Azure

Este artigo descreve como usar o portal do Azure para monitorar sua carga de trabalho. Isso inclui a configuração de logs de Azure Monitor para investigar a execução da consulta e as tendências de carga de trabalho usando o log Analytics para [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure: se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Pool do SQL: coletaremos logs para um pool do SQL. Se você não tiver um pool do SQL provisionado, consulte as instruções em [criar um pool do SQL](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho do Log Analytics

Navegue até a folha procurar por Log Analytics espaços de trabalho e crie um espaço de trabalho

![Workspaces do Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Adicionar espaço de trabalho de análise](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Adicionar espaço de trabalho de análise](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Para obter mais detalhes sobre espaços de trabalho, visite a [documentação](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)a seguir.

## <a name="turn-on-resource-logs"></a>Ativar logs de recursos

Defina as configurações de diagnóstico para emitir logs do seu pool SQL. Os logs consistem em exibições de telemetria equivalentes às DMVs de solução de problemas de desempenho usadas com mais frequência. Atualmente, há suporte para as seguintes exibições:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Habilitando logs de recursos](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Os logs podem ser emitidos para o armazenamento do Azure, Stream Analytics ou Log Analytics. Para este tutorial, selecione Log Analytics.

![Especificar logs](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Executar consultas no Log Analytics

Navegue até o espaço de trabalho Log Analytics onde você pode fazer o seguinte:

- Analisar logs usando consultas de log e salvar consultas para reutilização
- Salvar consultas para reutilização
- Criar alertas de log
- Fixar resultados da consulta em um painel

Para obter detalhes sobre os recursos das consultas de log, visite a [documentação](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)a seguir.

![Editor de espaço de trabalho Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Log Analytics consultas de espaço de trabalho](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Consultas de log de exemplo

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou e configurou os logs do Azure monitor, [Personalize os painéis do Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para compartilhar em sua equipe.
