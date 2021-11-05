---
title: Azure Monitor による SSIS 操作の監視
description: Azure Monitor を使用して Azure Data Factory の SSIS 操作を監視する方法について説明します。
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 91e291b27301cee8b7ec8f65e248e09c4f79799c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842741"
---
# <a name="monitor-ssis-operations-with-azure-monitor"></a>Azure Monitor による SSIS 操作の監視

SSIS ワークロードをリフト アンド シフトするには、次をサポートする [ADF で SSIS IR をプロビジョニング](./tutorial-deploy-ssis-packages-azure.md)できます。

- Azure SQL Database サーバーまたは Managed Instance をホストとする SSIS カタログ (SSISDB) にデプロイされたパッケージを実行する (プロジェクト デプロイ モデル)
- Azure SQL Managed Instance をホストとするファイル システム、Azure Files、SQL Server データベース (MSDB) のいずれかにデプロイされたパッケージを実行する (パッケージ デプロイ モデル)

プロビジョニングが完了すると、[Azure PowerShell を使うか、ADF ポータルの **[監視]** ハブで、SSIS IR の動作状態をチェック](./monitor-integration-runtime.md#azure-ssis-integration-runtime)できるようになります。 プロジェクト デプロイ モデルでは、SSIS パッケージ実行ログは SSISDB 内部テーブルまたはビューに格納されるため、SSMS などの指定されたツールを使用して、クエリ、分析、および視覚的な表示を実行できます。 パッケージ デプロイ モデルでは、SSIS パッケージ実行ログをファイル システムまたは Azure Files に CSV ファイルとして保存できますが、クエリ、分析、および視覚的な表示を実行する前に、指定された他のツールを使用して解析と処理を行う必要があります。

[Azure Monitor](../azure-monitor/data-platform.md) の統合により、SSIS IR 操作と SSIS パッケージ実行で生成されるすべてのメトリックとログを、Azure portal でクエリ、分析、および視覚的に表示できるようになりました。 さらに、それらに関するアラートを生成することもできます。

## <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>SSIS 操作の診断設定とワークスペースを構成する

SSIS IR 操作と SSIS パッケージ実行で生成されるすべてのメトリックとログを Azure Monitor に送信するには、[ADF の診断設定とワークスペースの構成](monitor-configure-diagnostics.md)を実行する必要があります。

## <a name="ssis-operational-metrics"></a>SSIS 操作のメトリック

SSIS 操作の[メトリック](../azure-monitor/essentials/data-platform-metrics.md)は、SSIS IR の開始および停止操作の状態と特定の時点での SSIS パッケージ実行の状態を示すパフォーマンス カウンターまたは数値です。 それらは、[Azure Monitor の ADF メトリック](monitor-metrics-alerts.md)の一部です。

Azure Monitor で ADF の診断設定とワークスペースを構成するときに、 _[AllMetrics]_ チェック ボックスをオンにすると、[Azure メトリックス エクスプローラーを使用した対話型分析](../azure-monitor/essentials/metrics-getting-started.md)、[Azure ダッシュボードでの表示](../azure-monitor/app/tutorial-app-dashboards.md)、および [準リアルタイムのアラート](../azure-monitor/alerts/alerts-metric.md)のために SSIS 操作のメトリックを使用できるようになります。

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="設定に名前を付けてログ分析ワークスペースを選択する":::

## <a name="ssis-operational-alerts"></a>SSIS 操作のアラート

ADF ポータルから SSIS 操作のメトリックに関するアラートを生成するには、[ADF の **[監視]** ハブの **[アラートとメトリック]** ページを選択し、示される詳細な手順に従います](./monitor-visually.md#alerts)。

:::image type="content" source="media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png" alt-text="ADF ポータルから SSIS 操作のアラートを生成":::

Azure portal から SSIS 操作のメトリックに関するアラートを生成するには、[Azure の **[監視]** ハブの **[アラート]** ページを選択し、示される詳細な手順に従います](monitor-metrics-alerts.md)。

:::image type="content" source="media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png" alt-text="Azure portal から SSIS 操作のアラートを生成":::

## <a name="ssis-operational-logs"></a>SSIS 操作のログ

SSIS 操作の[ログ](../azure-monitor/logs/data-platform-logs.md)は、SSIS IR 操作と SSIS パッケージ実行によって生成されるイベントであり、特定された任意の問題に関する十分なコンテキストを提供するため、根本原因の分析に役立ちます。 

Azure Monitor で ADF の診断設定やワークスペースを構成するとき、関連する SSIS 操作ログを選択して、Azure Data Explorer に基づく Log Analytics にそれらを送信できます。 そこで、[豊富なクエリ言語を使用した分析](../azure-monitor/logs/log-query-overview.md)、[Azure ダッシュボードでの表示](../azure-monitor/app/tutorial-app-dashboards.md)、および[準リアルタイムのアラート](../azure-monitor/alerts/alerts-log.md)のためにそれらを使用できます。

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="設定に名前を付けてログ分析ワークスペースを選択する":::

Azure Monitor と Log Analytics の SSIS パッケージ実行ログのスキーマと内容は、SSISDB 内部テーブルまたはビューのスキーマに似ています。

| Azure Monitor のログのカテゴリ          | Log Analytics のテーブル                     | SSISDB 内部テーブルまたはビュー              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

SSIS 操作のログの属性とプロパティの詳細については、[ADF のための Azure Monitor と Log Analytics のスキーマ](monitor-schema-logs-events.md)に関するページを参照してください。

選択された SSIS パッケージ実行ログは、呼び出しメソッドに関係なく、常に Log Analytics に送信されます。 たとえば、SSMS、SQL Server エージェント、またはその他の指定されたツールで T-SQL を使用して、ADF パイプラインでの Execute SSIS Package アクティビティのトリガーされたまたはデバッグの実行として、Azure 対応 SSDT のパッケージ実行を呼び出すことができます。

Logs Analytics で SSIS IR 操作ログのクエリを実行するとき、それぞれが `Start/Stop/Maintenance/Heartbeat` と `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` に設定された **OperationName** プロパティと **ResultType** プロパティを使用できます。

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query.png" alt-text="Log Analytics での SSIS IR パッケージ操作ログのクエリの実行":::

SSIS IR ノードの状態に対してクエリを実行するには、**OperationName** プロパティを `Heartbeat` に設定します。 各ノードは通常、1 分あたり 1 つの `Heartbeat` レコードを Log Analytics に送信します。このとき **ResultType** プロパティにはその状態が反映されており、パッケージの実行に使用できる場合は `Healthy`、そうでない場合は `Unhealthy` になっています。 たとえば、お使いの SSIS IR に利用可能なノードが 2 つある場合は、どの 1 分間にも 2 つの `Heartbeat` レコードが必ず表示され、その **ResultType** プロパティは `Healthy` に設定されています。

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query-3.png" alt-text="Log Analytics 上での SSIS IR ハートビートに対するクエリの実行":::

次のパターンに対してクエリを実行して、SSIS IR ノードが使用できないことを検出できます。

* SSIS IR がまだ実行されているときに、`Heartbeat` レコードが見つからない 1 分間が多数存在する。
* SSIS IR がまだ実行されているとき、多くの 1 分間に、**ResultType** プロパティが `Unhealthy` に設定されている `Heartbeat` レコードが存在する。

上記のクエリを[アラート](../azure-monitor/alerts/alerts-unified-log.md)に変更し、[SSIS IR 監視ページ](monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)に移動して、それらのアラートをいつ受け取るかを確認することができます。

Logs Analytics で SSIS パッケージ実行ログのクエリを実行する場合、**OperationId**/**ExecutionId**/**CorrelationId** プロパティを使用してそれらを結合できます。 SSISDB への格納/T-SQL を使用した呼び出しが行われて **いない** パッケージに関連するすべての操作/実行の場合、**OperationId**/**ExecutionId** には常に `1` が設定されます。

:::image type="content" source="media/data-factory-monitor-oms/log-analytics-query2.png" alt-text="SSIS パッケージ実行ログに対する Log Analytics でのクエリの実行":::

## <a name="next-steps"></a>次の手順

[ログとイベントのスキーマ](monitor-schema-logs-events.md)
