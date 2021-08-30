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
ms.openlocfilehash: 4f07659381c852e35585b5127d97c932a05088c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735270"
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

### <a name="aggregation-types"></a>集計タイプ

メトリックス エクスプローラーでは、[集計の種類](../azure-monitor/essentials/metrics-charts.md#aggregation)として SUM、MAX、MIN、AVG、COUNT がサポートされています。 各 ExpressRoute メトリックの分析情報を確認するときは、推奨される集計の種類を使用します。

* Sum:集計間隔でキャプチャされたすべての値の合計。 
* Count:集計間隔でキャプチャされた測定値の数。 
* Average: 集計間隔でキャプチャされたメトリック値の平均。 
* Min:集計間隔でキャプチャされた値の最小値。 
* Max:集計間隔でキャプチャされた値の最大値。

>[!NOTE]
>**クラシック メトリック** の使用はお勧めしません。
>

### <a name="circuits-metrics"></a>回線のメトリック

#### <a name="bits-in-and-out---metrics-across-all-peerings"></a>ビットのインとアウト - すべてのピアリング全体でのメトリック

集計の種類: *Avg*

特定の ExpressRoute 回線上のすべてのピアリングを超えてメトリックを表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="回線のメトリック":::

#### <a name="bits-in-and-out---metrics-per-peering"></a>ビットのインとアウト - ピアリングごとのメトリック

集計の種類: *Avg*

プライベート、パブリック、Microsoft ピアリングのメトリック (ビット/秒) を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="ピアリングごとのメトリック":::

#### <a name="bgp-availability---split-by-peer"></a>BGP の可用性 - ピアによる分割  

集計の種類: *Avg*

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、BGP (レイヤー 3 接続) の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、プライベート ピアリング用のプライマリ BGP セッション状態はアップ状態であり、プライベート ピアリング用の 2 番目の BGP セッション状態はダウン状態であると表示されます。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="ピアごとの BGP の可用性":::

### <a name="arp-availability---split-by-peering"></a>ARP の可用性 - ピアリングによる分割  

集計の種類: *Avg*

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、[ARP](./expressroute-troubleshooting-arp-resource-manager.md) (レイヤー 3 接続) の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、両方のピア間のプライベート ピアリング ARP セッションはアップ状態だがピア間の Microsoft ピアリングはダウン状態であることが表示されます。 既定の集計 (平均) は、両方のピアとの間に使用されていました。  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ピアごとの ARP の可用性":::

### <a name="expressroute-direct-metrics"></a>ExpressRoute Direct メトリック

#### <a name="admin-state---split-by-link"></a>管理状態 - リンクによる分割

集計の種類: *Avg*

ExpressRoute Direct ポート ペアのリンクごとに管理状態を表示できます。 管理状態は、物理ポートがオンかオフかを示します。 この状態は、ExpressRoute Direct 接続を介してトラフィックを通過させるために必要です。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct 管理状態":::

#### <a name="bits-in-per-second---split-by-link"></a>1 秒あたりのビット イン - リンクで分割

集計の種類: *Avg*

ExpressRoute Direct ポート ペアの両方のリンクを対象に、1 秒あたりのビット インを表示できます。 このダッシュボードを監視して、両方のリンクの着信帯域幅を比較します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct の 1 秒あたりのビット イン":::

#### <a name="bits-out-per-second---split-by-link"></a>1 秒あたりのビット アウト - リンクで分割

集計の種類: *Avg*

ExpressRoute Direct ポート ペアの両方のリンクを対象に、1 秒あたりのビット アウトも表示できます。 このダッシュボードを監視して、両方のリンクの発信帯域幅を比較します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct の 1 秒あたりのビット アウト":::

#### <a name="line-protocol---split-by-link"></a>回線プロトコル - リンクによる分割

集計の種類: *Avg*

ExpressRoute Direct ポート ペアのリンクごとに回線プロトコルを表示できます。 回線プロトコルは、物理リンクが ExpressRoute Direct 経由で稼働しているかどうかを示します。 このダッシュボードを監視し、物理接続が停止したことを通知するアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct 回線プロトコル":::

#### <a name="rx-light-level---split-by-link"></a>Rx ライト レベル - リンクによる分割

集計の種類: *Avg*

ポート別の Rx ライト レベル (ExpressRoute Direct ポートで **受信** するライト レベル) を表示できます。 正常な Rx ライト レベルは通常、-10 dBm から 0 dBm までの範囲内です。 Rx ライト レベルが正常範囲外になった場合に通知されるようにアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct 回線 Rx ライト レベル":::

#### <a name="tx-light-level---split-by-link"></a>Tx ライト レベル - リンクによる分割

集計の種類: *Avg*

ポート別の Tx ライト レベル (ExpressRoute Direct ポートで **送信** するライト レベル) を表示できます。 正常な Tx ライト レベルは一般に、-10 dBm から 0 dBm までの範囲内です。 Tx ライト レベルが正常範囲外になった場合に通知されるようにアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct 回線 Tx ライト レベル":::

### <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 仮想ネットワーク ゲートウェイ メトリック

集計の種類: *Avg*

ExpressRoute ゲートウェイをデプロイすると、Azure でゲートウェイのコンピューティングと機能が管理されます。 ゲートウェイのパフォーマンスをより深く理解するために、6 つのゲートウェイ メトリックが用意されています。

* CPU 使用率
* 1 秒あたりのパケット数
* ピアにアドバタイズされたルートの数
* ピアから学習したルートの数
* ルートが変更された頻度
* 仮想ネットワーク内の VM の数  

これらのメトリックごとにアラートを設定することを強くお勧めします。これにより、ゲートウェイでパフォーマンスの問題が発生した場合に把握できます。

#### <a name="cpu-utilization---split-instance"></a>CPU 使用率 - インスタンスの分割

集計の種類: *Avg*

各ゲートウェイ インスタンスの CPU 使用率を表示できます。 定期的なホスト メンテナンス中に CPU 使用率が急増することがありますが、CPU 使用率が高い状態が長く続く場合は、ゲートウェイがパフォーマンスのボトルネックに達していることを示している可能性があります。 ExpressRoute ゲートウェイのサイズを増やすと、この問題が解決することがあります。 CPU 使用率が特定のしきい値を超えた頻度について、アラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 使用率 - 分割メトリックのスクリーンショット。":::

#### <a name="packets-per-second---split-by-instance"></a>1 秒あたりのパケット数 - インスタンスで分割

集計の種類: *Avg*

このメトリックは、ExpressRoute ゲートウェイを通過する受信パケットの数をキャプチャします。 ゲートウェイがオンプレミス ネットワークからのトラフィックを受信している場合は、一貫したデータ ストリームがここに表示されることが想定されます。 1 秒あたりのパケット数がしきい値を下回り、ゲートウェイがトラフィックを受信しなくなったことを示している場合のアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="1 秒あたりのパケット数 - 分割メトリックのスクリーンショット。":::

#### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>ピアにアドバタイズされたルートの数 - インスタンスで分割

集計の種類: *Count*

このメトリックは、ExpressRoute ゲートウェイで回線にアドバタイズされているルートの数です。 アドレス空間には、VNet ピアリングを使用して接続され、リモート ExpressRoute ゲートウェイが使用される仮想ネットワークが含まれる場合があります。 仮想ネットワークのアドレス空間に頻繁に変更が加えられない限り、ルートの数は一定であると想定されます。 認識している仮想ネットワーク アドレス空間の数に対して、アドバタイズされたルートの数がしきい値を下回った場合のアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="ピアにアドバタイズされたルートの数のスクリーンショット。":::

#### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>ピアから学習したルートの数 - インスタンスで分割

集計の種類: *Max*

このメトリックは、ExpressRoute ゲートウェイで ExpressRoute 回線に接続されているピアから学習しているルートの数を示します。 これらのルートは、同じ回線に接続されている別の仮想ネットワークのものでも、オンプレミスから学習したものでもかまいません。 学習したルートの数が特定のしきい値を下回った場合のアラートを設定します。 ルートが多数破棄される現象は、ゲートウェイでパフォーマンスの問題が発生しているか、リモート ピアで ExpressRoute 回線へのルートがアドバタイズされていないことを示している可能性があります。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="ピアから学習したルートの数のスクリーンショット。":::

#### <a name="frequency-of-routes-change---split-by-instance"></a>ルート変更の頻度 - インスタンスで分割

集計の種類: *Sum*

このメトリックは、リモート ピアから学習した、またはアドバタイズされたルートの頻度を示します。 まず、オンプレミスのデバイスを調査して、ネットワークが頻繁に変更されている理由を把握しておく必要があります。 ルート変更の頻度が高い場合は、ExpressRoute ゲートウェイでパフォーマンスの問題が発生している可能性があり、この問題はゲートウェイ SKU のスケール アップによって解決することがあります。 ExpressRoute ゲートウェイで異常なルート変更が観察されるとき、認識すべき頻度のしきい値のアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="ルートが変更された頻度のメトリックのスクリーンショット。":::

#### <a name="number-of-vms-in-the-virtual-network"></a>仮想ネットワーク内の VM の数

集計の種類: *Max*

このメトリックは、ExpressRoute ゲートウェイが使用されている仮想マシンの数を示します。 仮想マシンの数には、同じ ExpressRoute ゲートウェイが使用されているピアリングされた仮想ネットワークの VM が含まれる場合があります。 VM の数が特定のしきい値を超え、ゲートウェイのパフォーマンスに影響する可能性がある場合に、このメトリックのアラートを設定します。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="仮想ネットワーク内の仮想マシンの数を示すメトリックのスクリーンショット。":::

#### <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute ゲートウェイの接続 (ビット/秒)

集計の種類: *Avg*

このメトリックは、ExpressRoute 回線への特定の接続における帯域幅の使用量を示します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="ゲートウェイ接続の帯域幅の使用量を示すメトリックのスクリーンショット。":::

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
