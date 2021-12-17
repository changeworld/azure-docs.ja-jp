---
title: Azure Application Gateway の監視
description: Azure Application Gateway を監視する方法は、こちらでご確認ください
author: vhorne
ms.author: victorh
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 7789590a2d13e6811e0ad8e7d2fa53cea9e0069a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295980"
---
<!-- VERSION 2.2
Template for the main monitoring article for Azure services. 
Keep the required sections and add/modify any content for any information specific to your service. 
This article should be in your TOC with the name *monitor-[Azure Application Gateway].md* and the TOC title "Monitor Azure Application Gateway". 
Put accompanying reference information into an article in the Reference section of your TOC with the name *monitor-[service-name]-reference.md* and the TOC title "Monitoring data". 
Keep the headings in this order. 
-->

# <a name="monitoring-azure-application-gateway"></a>Azure Application Gateway の監視
<!-- REQUIRED. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Add to it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 

この記事では、Azure Application Gateway によって生成される監視データについて説明します。 Azure Application Gateway では、[Azure Monitor](../azure-monitor/overview.md) が使用されます。 Azure Monitor を使用するすべての Azure サービスにとって共通する機能に詳しくない場合は、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。


<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact robb@microsoft.com -->

## <a name="monitoring-overview-page-in-azure-portal"></a>Azure portal の [監視の概要] ページ
<!-- OPTIONAL. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Edit it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

各 Application Gateway の Azure portal 内の **[概要]** ページには、次のメトリックが含まれます。

- 合計要求の総数
- 失敗した要求の合計
- HttpStatus 別の応答状態の合計
- 合計スループット
- CurrentConnections の合計
- BackendPool HttpSettings 別の正常なホスト数の平均
- BackendPool HttpSettings 別の正常ではないホスト数の平均

これは、Application Gateway で利用可能なメトリックのサブセットに過ぎません。 詳細については、[Azure Application Gateway の監視データのリファレンス](monitor-application-gateway-reference.md)に関する記事をご覧ください。


## <a name="azure-monitor-network-insights"></a>Azure Monitor Network Insights

<!-- OPTIONAL SECTION.  Only include if your service has an "insight" associated with it. Examples of insights include
  - CosmosDB https://docs.microsoft.com/azure/azure-monitor/insights/cosmosdb-insights-overview
  - If you still aren't sure, contact azmondocs@microsoft.com.>
-->

Azure の一部のサービスには、サービスを監視する開始点を提供する Azure portal の特別な事前構築済みの監視ダッシュボードがあります。 これらの特別なダッシュボードは、"分析情報" と呼ばれます。

<!-- Give a quick outline of what your "insight page" provides and refer to another article that gives details -->

Azure Monitor Network Insights を使用すると、デプロイされたすべてのネットワーク リソース (Application Gateway を含む) の正常性とメトリックを、構成なしで包括的に把握できます。 詳細については、「[Azure Monitor Network Insights](../azure-monitor/insights/network-insights-overview.md)」を参照してください。

## <a name="monitoring-data"></a>データの監視 

<!-- REQUIRED. Please keep headings in this order -->
Azure Application Gateway では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページをご覧ください。 

Azure Application Gateway によって作成されるメトリックとログ メトリックの詳細については、「[Azure Application Gateway の監視データのリファレンス](monitor-application-gateway-reference.md)」を参照してください。

<!-- If your service has additional non-Azure Monitor monitoring data then outline and refer to that here. Also include that information in the data reference as appropriate. -->

## <a name="collection-and-routing"></a>収集とルーティング

<!-- REQUIRED. Please keep headings in this order -->

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

<!-- Include any additional information on collecting logs.  The number of things that diagnostics settings control is expanding -->

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。 Azure Application Gateway のカテゴリは、「[Azure Application Gateway の監視データのリファレンス](monitor-application-gateway-reference.md#resource-logs)」に記載されています。

<!-- OPTIONAL: Add specific examples of configuration for this service. For example, CLI and PowerShell commands for creating diagnostic setting. Ideally, customers should set up a policy to automatically turn on collection for services. Azure monitor has Resource Manager template examples you can point to. See https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings.  Contact azmondocs@microsoft.com if you have questions.   -->

収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics"></a>メトリックの分析

<!-- REQUIRED. Please keep headings in this order 
If you don't support metrics, say so. Some services may be only onboarded to logs -->

メトリックス エクスプローラーを使用して、Azure Application Gateway のメトリックを他の Azure サービスからのメトリックと一緒に分析するには、**Azure Monitor** のメニューから **[メトリック]** を開きます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。 

<!-- Point to the list of metrics available in your monitor-service-reference article. -->
Azure Application Gateway 用に収集されるプラットフォーム メトリックの一覧については、[「Azure Application Gateway の監視データのリファレンス」の「メトリック」](monitor-application-gateway-reference.md#metrics)を参照してください。  


参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を確認できます。

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about how to use metrics explorer specifically for your service. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

## <a name="analyzing-logs"></a>ログの分析

<!-- REQUIRED. Please keep headings in this order
If you don't support resource logs, say so. Some services may be only onboarded to metrics and the activity log. -->

Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。  

Azure Monitor 内のすべてのリソース ログには、同じフィールドの後にサービス固有のフィールドがあります。 「[Azure リソース ログの共通およびサービス固有のスキーマ](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)」には、共通スキーマの概要が記載されています。 

[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)は、サブスクリプション レベルのイベントの分析情報を提供するプラットフォーム ログイン Azure です。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。  

Azure Application Gateway 用に収集されるリソース ログの種類の一覧については、「[Azure Application Gateway の監視データのリファレンス](monitor-application-gateway-reference.md#resource-logs)」を参照してください。

Azure Monitor ログによって使用され、Log Analytics によってクエリ可能なテーブルの一覧については、「[Azure Application Gateway の監視データのリファレンス](monitor-application-gateway-reference.md#azure-monitor-logs-tables)」を参照してください。  

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about log usage or what logs are most important. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

<!-- REQUIRED if you support logs. Please keep headings in this order -->
<!-- Add sample Log Analytics Kusto queries for your service. -->

> [!IMPORTANT]
> Application Gateway メニューから **[ログ]** を選択すると、Log Analytics が開き、クエリ スコープは現在の Application Gateway に設定されています。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他の Application Gateway のデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope/)」を参照してください。

<!-- REQUIRED: Include queries that are helpful for figuring out the health and state of your service. Ideally, use some of these queries in the alerts section. It's possible that some of your queries may be in the Log Analytics UI (sample or example queries). Check if so.  -->

次のクエリを使用すると、ご自身の Application Gateway リソースを監視することができます。 

<!-- Put in a code section here. -->  
```Kusto
// Requests per hour 
// Count of the incoming requests on the Application Gateway. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart 
```

```kusto
// Failed requests per hour 
// Count of requests to which Application Gateway responded with an error. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart
```

```kusto
// Top 10 Client IPs 
// Count of requests per client IP. 
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by clientIP_s
| top 10 by AggregatedValue
```

```kusto
// Errors by user agent 
// Number of errors by user agent. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by userAgent_s, _ResourceId
| sort by AggregatedValue desc
```

## <a name="alerts"></a>警告

<!-- SUGGESTED: Include useful alerts on metrics, logs, log conditions or activity log. Ask your PMs if you don't know. 
This information is the BIGGEST request we get in Azure Monitor so do not avoid it long term. People don't know what to monitor for best results. Be prescriptive  
-->

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 アラートの種類に応じて、さまざまな利点と欠点があります。

<!-- only include next line if applications run on your service and work with App Insights. --> 

Application Gateway が使用されたいるアプリケーションを作成または実行している場合、[Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) によって、アラートの種類が追加で提供される場合があります。
<!-- end -->

次の表に、Application Gateway の一般的な警告ルールと推奨される警告ルールを示します。

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->

**Application Gateway v1**

| アラートの種類 | 条件 | 説明  |
|:---|:---|:---|
|メトリック|CPU 使用率が 80% を超える|通常の状況では、CPU 使用率が 90% を超えることは望ましくありません。これは Application Gateway の背後でホストされている Web サイトで待機時間が発生し、クライアント エクスペリエンスを乱す可能性があるためです。|
|メトリック|異常なホストの数がしきい値を超える|Application Gateway で正常にプローブできないバックエンド サーバーの数を示します。 これにより、Application Gateway のインスタンスをバックエンドに接続できない問題が検出されます。 この数値がバックエンド容量の 20% を超えたらアラートを出します。|
|メトリック|応答の状態 (4xx、5xx) がしきい値を超える|Application Gateway の応答の状態が 4xx または 5xx の場合。 一時的な問題が原因で 4xx または 5xx の応答が時々発生することがあります。 運用環境を観察して静的しきい値を判別するか、アラートに動的しきい値を使用してください。|
|メトリック|失敗した要求の数がしきい値を超える|失敗した要求のメトリックがしきい値を超えた場合。 運用環境を観察して静的しきい値を判別するか、アラートに動的しきい値を使用してください。|


**Application Gateway v2**

| アラートの種類 | 条件 | 説明  |
|:---|:---|:---|
|メトリック|コンピューティング ユニット使用率が平均使用量の 75% を超える|コンピューティング ユニットは、Application Gateway のコンピューティング使用率の測定単位です。 過去 1 か月間の平均コンピューティング ユニット使用率を確認し、その 75% を超えた場合にアラートを設定します。|
|メトリック|容量ユニットの使用率がピーク時の使用量の 75% を超える|容量ユニットは、スループット、コンピューティング、接続数の観点から、全体的なゲートウェイ使用率を表します。 過去 1 か月間の最大容量ユニット使用量を確認し、その 75% を超えた場合にアラートを設定します。|
|メトリック|異常なホストの数がしきい値を超える|Application Gateway で正常にプローブできないバックエンド サーバーの数を示します。 これにより、アプリケーション ゲートウェイのインスタンスをバックエンドに接続できない問題が検出されます。 この数値がバックエンド容量の 20% を超えたらアラートを出します。|
|メトリック|応答の状態 (4xx、5xx) がしきい値を超える|Application Gateway の応答の状態が 4xx または 5xx の場合。 一時的な問題が原因で 4xx または 5xx の応答が時々発生することがあります。 運用環境を観察して静的しきい値を判別するか、アラートに動的しきい値を使用してください。|
|メトリック|失敗した要求の数がしきい値を超える|失敗した要求のメトリックがしきい値を超えた場合。 運用環境を観察して静的しきい値を判別するか、アラートに動的しきい値を使用してください。|
|メトリック|バックエンドの最後のバイトの応答時間がしきい値を超える|バックエンド サーバーへの接続の確立を開始してから応答本文の最後のバイトを受信するまでの時間間隔を示します。 バックエンドの応答待機時間が、通常の特定のしきい値を超えた場合にアラートを作成します。|
|メトリック|Application Gateway の合計時間がしきい値を超える|これは、Application Gateway が HTTP 要求の最初のバイトを受信してから、最後の応答バイトがクライアントに送信されるまでの間隔です。 バックエンドの応答待機時間が、通常の特定のしきい値を超えた場合にアラートを作成します。|

## <a name="next-steps"></a>次のステップ

<!-- Add additional links. You can change the wording of these and add more if useful.   -->

- Application Gateway によって作成されるメトリック、ログなどの重要な値のリファレンスについては、「[Application Gateway の監視データのリファレンス](monitor-application-gateway-reference.md)」を参照してください。

- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。