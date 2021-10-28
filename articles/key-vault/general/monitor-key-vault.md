---
title: Azure Key Vault の監視
description: Azure Key Vault を監視する方法については、こちらでご確認ください
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/21/2021
ms.author: mbaldwin
ms.custom: subject-monitoring
ms.openlocfilehash: 229b7ace69c3b91f38f0cfb6e764f7771b4ff495
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234550"
---
# <a name="monitoring-azure-key-vault"></a>Azure Key Vault の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 Azure Key Vault の場合、スケーリングを開始するとキー コンテナーに送信される要求の数が増えるので、サービスを監視することが重要です。 これにより要求の待機時間が長くなる可能性があり、極端な場合には要求がスロットルされてサービスのパフォーマンスに影響を与えます。

この記事では、Key Vault によって生成される監視データについて説明します。 Key Vault では [Azure Monitor](../../azure-monitor/overview.md) が使用されます。 Azure Monitor を使用するすべての Azure サービスにとって共通する機能に詳しくない場合は、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。

## <a name="monitoring-overview-page-in-azure-portal"></a>Azure portal の [監視の概要] ページ

各キー コンテナーの Azure portal 内の **[概要]** ページには、[監視] タブに次のメトリックが含まれます。

- 要求の合計
- 平均待機時間
- 成功率

[追加のメトリック] (または左側のサイドバーの [監視] の下にある [メトリック] タブ) を選択して、これらのメトリックも表示できます。

- サービス API の全体的な待機時間
- コンテナーの全体的な可用性
- コンテナーの全体的な飽和度
- サービス API ヒット数の合計
- サービス API 結果数の合計

## <a name="key-vault-insights"></a>Key Vault 分析情報

Azure の一部のサービスには、サービスを監視する開始点を提供する Azure portal の特別な事前構築済みの監視ダッシュボードがあります。 これらの特別なダッシュボードは、"分析情報" と呼ばれます。

Key Vault 分析情報ではキー コンテナーの要求、パフォーマンス、エラー、待機時間の統合ビューにより、キー コンテナーの包括的な監視が提供されます。 詳細については、「[Key Vault 分析情報を使用したキー コンテナー サービスの監視](../../azure-monitor/insights/key-vault-insights-overview.md)」を参照してください。

## <a name="monitoring-data"></a>データの監視

Key Vault では、[Azure リソースの監視データ](../../azure-monitor/essentials/monitor-azure-resource.md)に関するページで説明されている他の Azure リソースと同じ種類の監視データが収集されます。

Key Vault によって作成されるメトリックとログ メトリックの詳細については、「[*Key Vault* 監視データのリファレンス](monitor-key-vault-reference.md)」を参照してください。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。 *Key Vault* のカテゴリは、[Key Vault 監視データのリファレンス](monitor-key-vault-reference.md#resource-logs)に関するページに一覧表示されています。

キー コンテナーの診断設定を作成するには、「[Key Vault のログ記録を有効にする](howto-logging.md)」を参照してください。  収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics"></a>メトリックの分析

**Azure Monitor** のメニューから **[メトリック]** を開き、メトリックス エクスプローラーを使用して、Key Vault のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。

Key Vault のために収集されるプラットフォーム メトリックの一覧については、[Key Vault 監視データのリファレンスのメトリック](monitor-key-vault-reference.md#metrics)に関するページを参照してください  

## <a name="analyzing-logs"></a>ログの分析

Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。  

Azure Monitor 内のすべてのリソース ログには、同じフィールドの後にサービス固有のフィールドがあります。 共通のスキーマの概要については、[Azure Monitor リソース ログのスキーマ](../../azure-monitor/essentials/resource-logs-schema.md)に関する記事をご覧ください 

[アクティビティ ログ](../../azure-monitor/essentials/activity-log.md)は、サブスクリプションレベルのイベントに関する分析情報を提供する Azure のプラットフォーム ログの一種です。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。  

Key Vault 用に収集されるリソース ログの種類の一覧については、「[Key Vault 監視データのリファレンス](monitor-key-vault-reference.md#resource-logs)」を参照してください  

Azure Monitor ログによって使用され、Log Analytics によってクエリ可能なテーブルの一覧については、「[Key Vault 監視データのリファレンス](monitor-key-vault-reference.md#azure-monitor-logs-tables)」を参照してください  

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

> [!IMPORTANT]
> Key Vault のメニューから **[ログ]** を選択すると、クエリのスコープが現在のキー コンテナーに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のキー コンテナーのデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope/)」を参照してください。

Key Vault リソースの監視に利用できる、 **[ログ検索]** バーに入力するクエリを紹介します。 これらのクエリは[新しい言語](../../azure-monitor/logs/log-query-overview.md)で使用できます。

* 低速な要求はあるか?

    ```Kusto
    // List of KeyVault requests that took longer than 1sec. 
    // To create an alert for this query, click '+ New alert rule'
    let threshold=1000; // let operator defines a constant that can be further used in the query

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where DurationMs > threshold
    | summarize count() by OperationName, _ResourceId
    ```

* エラーはあるか?

    ```Kusto
    // Count of failed KeyVault requests by status code. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where httpStatusCode_d >= 300 and not(OperationName == "Authentication" and httpStatusCode_d == 401)
    | summarize count() by requestUri_s, ResultSignature, _ResourceId
    // ResultSignature contains HTTP status, e.g. "OK" or "Forbidden"
    // httpStatusCode_d contains HTTP status code returned
    ```

* 逆シリアル化の入力エラー

    ```Kusto
    // Shows errors caused due to malformed events that could not be deserialized by the job. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.KEYVAULT" and parse_json(properties_s).DataErrorType in ("InputDeserializerError.InvalidData", "InputDeserializerError.TypeConversionError", "InputDeserializerError.MissingColumns", "InputDeserializerError.InvalidHeader", "InputDeserializerError.InvalidCompressionType")
    | project TimeGenerated, Resource, Region_s, OperationName, properties_s, Level, _ResourceId
    ```

* この KeyVault はどのくらいアクティブだったか?

    ```Kusto
    // Line chart showing trend of KeyVault requests volume, per operation over time. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize count() by bin(TimeGenerated, 1h), OperationName // Aggregate by hour
    | render timechart

    ```

* だれがこの KeyVault を呼び出しているか? 

    ```Kusto
    // List of callers identified by their IP address with their request count.  
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT"
    | summarize count() by CallerIPAddress
    ```

* この KeyVault ではどれくらいの速度で要求が処理されているか? 

    ```Kusto
    // Line chart showing trend of request duration over time using different aggregations. 
 
    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize avg(DurationMs) by requestUri_s, bin(TimeGenerated, 1h) // requestUri_s contains the URI of the request
    | render timechart
    ```

* 先月どのような変更が発生したか? 

    ```Kusto
    // Lists all update and patch requests from the last 30 days. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where TimeGenerated > ago(30d) // Time range specified in the query. Overrides time picker in portal.
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where OperationName == "VaultPut" or OperationName = "VaultPatch"
    | sort by TimeGenerated desc
    ```


## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、先回りして問題を識別して対処できます。 アラートは[メトリック](../../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。  

Azure Key Vault 上で動作するアプリケーションを作成または実行している場合、[Azure Monitor Application Insights](../../azure-monitor/overview.md#application-insights) に、追加の種類のアラートがある場合があります。

Azure Key Vault の一般的な推奨されるアラート ルールを次に示します。

- Key Vault の可用性が 100% を下回る (静的しきい値)
- Key Vault の待機時間が 500 ミリ秒を超える (静的しきい値)
- コンテナーの全体的な飽和度が 75% を超える (静的しきい値)
- コンテナーの全体的な飽和度が平均を超える (動的しきい値)
- エラー コードの総数が平均を超える (動的しきい値)

詳細については、「[Azure Key Vault の監視とアラート](alert.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Key Vault によって作成されるメトリック、ログ、その他の重要な値のリファレンスについては、[Azure Key Vault 監視データのリファレンス](monitor-key-vault-reference.md)に関する記事を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
- [Azure Key Vault のアラート](alert.md)に関するページを参照してください。
