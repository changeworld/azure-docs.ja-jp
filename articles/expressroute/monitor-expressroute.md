---
title: Azure ExpressRoute の監視
description: Azure ExpressRoute を監視する方法については、こちらでご確認ください。
services: expressroute
author: duongau
ms.author: duau
ms.service: expressroute
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: 2e54166f71071a21b84e5f58e27c4ebc8a7df03a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587860"
---
# <a name="monitoring-azure-expressroute"></a>Azure ExpressRoute の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 

この記事では、Azure ExpressRoute によって生成される監視データについて説明します。 Azure ExpressRoute は、[Azure Monitor](../azure-monitor/overview.md) を使用します。 Azure Monitor を使用するすべての Azure サービスにとって共通する機能に詳しくない場合は、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
 
## <a name="expressroute-insights"></a>ExpressRoute の分析情報

Azure の一部のサービスには、サービスを監視する開始点を提供する Azure portal の特別な事前構築済みの監視ダッシュボードがあります。 これらの特別なダッシュボードは、"分析情報" と呼ばれます。

ExpressRoute では、ネットワークの分析情報を使用して、すべての ExpressRoute コンポーネント (ピアリング、接続、ゲートウェイ) の相互関係の詳細なトポロジ マッピングが提供されます。 ExpressRoute のネットワークの分析情報には、可用性、スループット、パケットの破棄、ゲートウェイのメトリックに関する事前に読み込み済みのメトリック ダッシュボードもあります。 詳細については、「[ネットワークの分析情報を使用した Azure ExpressRoute の分析情報](expressroute-network-insights.md)」を参照してください。

## <a name="monitoring-data"></a>データの監視 

Azure ExpressRoute では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページをご覧ください。 

Azure ExpressRoute によって作成されるメトリックとログの詳細については、[Azure ExpressRoute 監視データのリファレンス](monitor-expressroute-reference.md)に関する記事を参照してください。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。 *Azure ExpressRoute* のカテゴリの一覧については、[Azure ExpressRoute 監視データのリファレンス](monitor-expressroute-reference.md#resource-logs)に記載されています。

> [!IMPORTANT]
> これらの設定を有効にするには、追加の Azure サービス (ストレージ アカウント、イベント ハブ、または Log Analytics) が必要であり、コストが増加する可能性があります。 推定コストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator)にアクセスしてください。

収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics"></a>メトリックの分析

**Azure Monitor** のメニューから **[メトリック]** を開き、メトリックス エクスプローラーを使用して、*Azure ExpressRoute* のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metrics-page.png" alt-text="ExpressRoute のメトリック ダッシュボードのスクリーンショット。":::

参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を確認できます。

* **[ExpressRoute]** メトリックを表示するには、リソースの種類を *[ExpressRoute 回線]* でフィルターを適用します。 
* **[Global Reach]** メトリックを表示するには、リソースの種類を *[ExpressRoute 回線]* でフィルターを適用し、Global Reach が有効になっている ExpressRoute 回線リソースを選択します。 
* **ExpressRoute Direct** メトリックを表示するには、 *[ExpressRoute ポート]* でリソースの種類をフィルターします。 

メトリックを選択すると、既定の集計が適用されます。 必要に応じて、さまざまなディメンションでメトリックを表示する分割を適用できます。

## <a name="analyzing-logs"></a>ログの分析

Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。  

Azure Monitor 内のすべてのリソース ログには、同じフィールドの後にサービス固有のフィールドがあります。 共通のスキーマの概要については、[Azure Monitor リソース ログのスキーマ](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)に関する記事をご覧ください。 ExpressRoute リソース ログのスキーマは、[Azure ExpressRoute のデータ リファレンス](monitor-expressroute-reference.md#schemas)に関する記事で確認できます。 

[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)は、サブスクリプション レベルのイベントの分析情報を提供するプラットフォーム ログインです。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。

Azure ExpressRoute は、次のテーブルにデータを格納します。

| テーブル | 説明 |
| ----- | ----------- |
| AzureDiagnostics | リソース ログを格納するために複数のサービスによって使用される共通テーブル。 ExpressRoute からのリソース ログは、`MICROSOFT.NETWORK` で識別できます。 |
| AzureMetrics | Azure ExpressRoute によって生成される、その正常性とパフォーマンスを測定したメトリック データ。 

これらのテーブルを表示するには、ExpressRoute 回線リソースに移動し、 *[監視]* で **[ログ]** を選択します。

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

[ログ検索] 検索バーに入力して Azure ExpressRoute リソースの監視に利用できるクエリを紹介します。 これらのクエリは[新しい言語](../azure-monitor/logs/log-query-overview.md)で使用できます。

* 過去 12 時間に学習した BGP ルート テーブルのクエリを実行する場合。

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceType , network_s, path_s, OperationName
    ```

* レベル、リソースの種類、およびネットワーク別に BGP 情報メッセージのクエリを実行する場合。

    ```Kusto
    AzureDiagnostics
    | where Level == "Informational"
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceId , Level, ResourceType , network_s, path_s
    ```

* 過去 1 時間のトラフィック グラフ BitInPerSeconds のクエリを実行する場合。
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsInPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* 過去 1 時間のトラフィック グラフ BitOutPerSeconds のクエリを実行する場合。
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsOutPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* 5 分間隔で ArpAvailability のグラフのクエリを実行する場合。
    
    ```Kusto
    AzureMetrics
    | where MetricName == "ArpAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

* 5 分間隔で BGP 可用性のグラフのクエリを実行する場合。

    ```Kusto
    AzureMetrics
    | where MetricName == "BGPAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 アラートの種類に応じて、さまざまな利点と欠点があります。

次の表に、ExpressRoute の一般的および推奨される警告ルールを示します。

| アラートの種類 | 条件 | 説明  |
|:---|:---|:---|
| ARP 可用性のダウン | ディメンション名: ピアリングの種類、集計の種類: 平均、演算子: より小さい、しきい値: 100% | ピアリングの種類の ARP 可用性がダウンである場合。 |
| BGP 可用性のダウン | ディメンション名: ピア、集計の種類: 平均、演算子: より小さい、しきい値: 100% | ピアの BGP 可用性がダウンである場合。 |

### <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute ゲートウェイの接続のアラート

1. アラートを構成するために、**Azure Monitor** に移動して、 **[アラート]** を選択します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. **[+ ターゲットの選択]** を選択し、ExpressRoute ゲートウェイの接続リソースを選択します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="target":::
3. アラートの詳細を定義します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="アクション グループ":::
4. アクション グループを定義して追加します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="アクション グループの追加":::


### <a name="alerts-based-on-each-peering"></a>各ピアリングに基づくアラート

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="各ピアリング":::

### <a name="configure-alerts-for-activity-logs-on-circuits"></a>回線でアクティビティ ログのアラートを構成する

**[アラートの条件]** で、[シグナルの種類] に **[アクティビティ ログ]** を選択し、[シグナル] を選択することができます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="アクティビティ ログ":::

## <a name="next-steps"></a>次のステップ

* Azure ExpressRoute によって作成されるメトリック、ログ、その他の重要な値のリファレンスについては、[Azure ExpressRoute 監視データのリファレンス](monitor-expressroute-reference.md)に関する記事を参照してください。
* Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/overview.md)」を参照してください。
