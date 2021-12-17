---
title: Azure Monitor を使用して App Service を監視する
description: App Services を監視する方法について説明します
author: msangapu-msft
ms.author: msangapu
ms.topic: article
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: d0bef29fc9e91d847046bc5d8157a70bd2fdfd9a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470986"
---
# <a name="monitoring-app-service"></a>App Service の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、App Service によって生成され、[Azure Monitor](../azure-monitor/overview.md) に配布される監視データについて説明します。 [リソースを監視する組み込みの診断機能](troubleshoot-diagnostic-logs.md)を使用して、App Service アプリのデバッグに役立てることもできます。 すべての Azure サービスに共通する Azure Monitor の機能に詳しくない場合は、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。

## <a name="monitoring-data"></a>データの監視 

App Service では、[Azure リソースの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページで説明されている他の Azure リソースと同じ種類の監視データが収集されます。 

App Service のメトリックとログの詳細については、「[*App Service* データの監視のリファレンス](monitor-app-service-reference.md)」を参照してください。

App Service では、組み込みの診断機能により、アプリのデバッグに役立てることができます。 組み込みログを有効にする方法の詳細については、[診断ログの有効化](troubleshoot-diagnostic-logs.md)に関するページを参照してください。 App Service インスタンスを監視するには、「[正常性チェックを使用して App Service インスタンスを監視する](monitor-instances-health-check.md)」を参照してください。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。 *App Service* のカテゴリは、[App Service 監視データのリファレンス](monitor-app-service-reference.md#resource-logs)に関するページに一覧表示されています。

収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics"></a>メトリックの分析

**Azure Monitor** のメニューから **[メトリック]** を開き、メトリックス エクスプローラーを使用して、*App Service* のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。 

App Service について収集されるプラットフォーム メトリックの一覧については、[App Service の監視データのリファレンス メトリック](monitor-app-service-reference.md#metrics)に関するページを参照してください  

参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を確認できます。

## <a name="analyzing-logs"></a>ログの分析

Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。  

Azure Monitor 内のすべてのリソース ログには、同じフィールドの後にサービス固有のフィールドがあります。 共通のスキーマの概要については、[Azure Monitor リソース ログのスキーマ](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor)に関する記事をご覧ください。

[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)は、サブスクリプションレベルのイベントの分析情報を提供するプラットフォーム ログの 1 つの種類です。 個別に表示できるほか、Azure Monitor ログにルーティングできます。 Azure Monitor ログへのルーティングにより、Log Analytics を使用して複雑なクエリを実行する利点が得られます。

App Service について収集されるリソース ログの種類の一覧については、「[App Service の監視データ リファレンス](monitor-app-service-reference.md#resource-logs)」を参照してください  

Azure Monitor ログおよび Log Analytics によって使用されるクエリ可能なテーブルの一覧については、「[App Service の監視データのリファレンス](monitor-app-service-reference.md#azure-monitor-logs-tables)」を参照してください。  

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

> [!IMPORTANT]
> App Service メニューから **[ログ]** を選択すると、クエリのスコープが現在のリソースに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他の [resource] からのデータや他の Azure サービスからのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope/)」を参照してください。

次のサンプル クエリは、`AppServiceAppLogs` を使用して、アプリ ログを監視するのに役立つ可能性があります。

```Kusto
AppServiceAppLogs 
| project CustomLevel, _ResourceId
| summarize count() by CustomLevel, _ResourceId
```

次のサンプル クエリは `HTTP response code` が `500` 以上の場合に `AppServiceHTTPLogs` を使用して、HTTP ログを監視するのに役立つ可能性があります。

```Kusto
AppServiceHTTPLogs 
//| where ResourceId = "MyResourceId" // Uncomment to get results for a specific resource Id when querying over a group of Apps
| where ScStatus >= 500
| reduce by strcat(CsMethod, ':\\', CsUriStem)
```

次のサンプル クエリは、`AppServiceConsoleLogs` と `AppserviceHTTPLogs` を結合することによって HTTP 500 エラーを監視するのに役立つ可能性があります。

```Kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;   
```

その他のサンプル クエリについては、[App Service の Azure Monitor クエリ](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Azure%20Services/App%20Services/Queries)に関するページを参照してください。

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。

App Service でアプリケーションを実行している場合は、[Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) によってより多くの種類のアラートが提供されます。

次の表に、App Service の一般的および推奨されるアラートのルールを示します。

| アラートの種類 | 条件 | 例  |
|:---|:---|:---|
| メトリック | 平均接続数| 接続数が設定値を超えた場合|
| メトリック | HTTP 404| HTTP 404 応答が設定値を超えた場合|
| メトリック | HTTP サーバー エラー数| HTTP 5xx エラーが設定値を超えた場合|
| アクティビティ ログ | Web アプリの作成または更新 | アプリが作成または更新されたとき|
| アクティビティ ログ | Web アプリの削除 | アプリが削除されたとき|
| アクティビティ ログ | Web アプリの再起動| アプリが再起動されたとき|
| アクティビティ ログ | Web アプリの停止| アプリが停止したとき|

## <a name="next-steps"></a>次のステップ

- App Service によって作成されるメトリック、ログ、その他の重要な値のリファレンスについては、「[App Service の監視データのリファレンス](monitor-app-service-reference.md)」を参照してください。

- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
