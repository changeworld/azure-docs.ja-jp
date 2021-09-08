---
title: 'Azure ExpressRoute: 監視、メトリック、およびアラート'
description: Azure 全体のすべてのメトリック、アラート、診断ログを一元的に管理できる Azure Monitor を使用した、Azure ExpressRoute の監視、メトリクス、およびアラートについて説明します。
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: abcec496f6bf3fdcd42dcffa66ecfb67533c7052
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449497"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute の監視、メトリック、およびアラート

この記事では、Azure Monitor を使用した ExpressRoute の監視、メトリック、およびアラートについて説明します。 Azure Monitor を使用すると、Azure 全体のすべてのメトリック、アラート、診断ログを一元的に管理できます。
 
>[!NOTE]
>**クラシック メトリック** の使用はお勧めしません。
>

## <a name="expressroute-metrics"></a>ExpressRoute メトリック

**[メトリック]** を表示するには、 *[Azure Monitor]* ページに移動して *[メトリック]* を選択します。 **[ExpressRoute]** メトリックを表示するには、リソースの種類を *[ExpressRoute 回線]* でフィルターを適用します。 **[Global Reach]** メトリックを表示するには、リソースの種類を *[ExpressRoute 回線]* でフィルターを適用し、Global Reach が有効になっている ExpressRoute 回線リソースを選択します。 **ExpressRoute Direct** メトリックを表示するには、 *[ExpressRoute ポート]* でリソースの種類をフィルターします。 

メトリックを選択すると、既定の集計が適用されます。 必要に応じて、さまざまなディメンションでメトリックを表示する分割を適用できます。

### <a name="aggregation-types"></a>集計の種類:

メトリックス エクスプローラーでは、[集計の種類](../azure-monitor/essentials/metrics-charts.md#aggregation)として SUM、MAX、MIN、AVG、COUNT がサポートされています。 各 ExpressRoute メトリックの分析情報を確認するときは、推奨される集計の種類を使用する必要があります。

* Sum:集計間隔でキャプチャされたすべての値の合計。 
* Count:集計間隔でキャプチャされた測定値の数。 
* Average: 集計間隔でキャプチャされたメトリック値の平均。 
* Min:集計間隔でキャプチャされた値の最小値。 
* Max:集計間隔でキャプチャされた値の最大値。 

### <a name="available-metrics"></a>使用可能なメトリック

|**メトリック**|**カテゴリ**|**ディメンション**|**機能**|
| --- | --- | --- | --- |
|ARP の可用性|可用性|<ul><li>ピア (プライマリ/セカンダリの ExpressRoute ルーター)</li><li> ピアリングの種類 (プライベート/パブリック/Microsoft)</li></ul>|ExpressRoute|
|BGP の可用性|可用性|<ul><li> ピア (プライマリ/セカンダリの ExpressRoute ルーター)</li><li> ピアリングの種類</li></ul>|ExpressRoute|
|BitsInPerSecond (受信ビット数/秒)|トラフィック|<ul><li> ピアリングの種類 (ExpressRoute)</li><li>リンク (ExpressRoute Direct)</li></ul>|<ul><li>ExpressRoute</li><li>ExpressRoute Direct</li><li>ExpressRoute ゲートウェイの接続</li></ul>|
|BitsOutPerSecond (送信ビット数/秒)|トラフィック| <ul><li>ピアリングの種類 (ExpressRoute)</li><li> リンク (ExpressRoute Direct)</li></ul> |<ul><li>ExpressRoute</li><li>ExpressRoute Direct</li><li>ExpressRoute ゲートウェイの接続</li></ul>|
|CPU 使用率|パフォーマンス| <ul><li>インスタンス</li></ul>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|1 秒あたりのパケット数|パフォーマンス| <ul><li>インスタンス</li></ul>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|ピアにアドバタイズされたルートの数 |可用性| <ul><li>インスタンス</li></ul>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|ピアから学習したルートの数 |可用性| <ul><li>インスタンス</li></ul>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|ルート変更の頻度 |可用性| <ul><li>インスタンス</li></ul>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|仮想ネットワーク内の VM の数 |可用性| 該当なし |ExpressRoute 仮想ネットワーク ゲートウェイ|
|GlobalReachBitsInPerSecond|トラフィック|<ul><li>ピアリングされた回線 Skey (サービス キー)</li></ul>|Global Reach|
|GlobalReachBitsOutPerSecond|トラフィック|<ul><li>ピアリングされた回線 Skey (サービス キー)</li></ul>|Global Reach|
|AdminState|物理的な接続性|Link|ExpressRoute Direct|
|LineProtocol|物理的な接続性|Link|ExpressRoute Direct|
|RxLightLevel|物理的な接続性|<ul><li>Link</li><li>レーン</li></ul>|ExpressRoute Direct|
|TxLightLevel|物理的な接続性|<ul><li>Link</li><li>レーン</li></ul>|ExpressRoute Direct|
>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* と *GlobalGlobalReachBitsOutPerSecond* の使用は、少なくとも 1 つの Global Reach 接続が確立されている場合にのみ表示されます。
>

## <a name="circuits-metrics"></a>回線のメトリック

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>ビットのインとアウト - すべてのピアリング全体でのメトリック

集計の種類: *Avg*

特定の ExpressRoute 回線上のすべてのピアリングを超えてメトリックを表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="回線のメトリック":::

### <a name="bits-in-and-out---metrics-per-peering"></a>ビットのインとアウト - ピアリングごとのメトリック

集計の種類: *Avg*

プライベート、パブリック、Microsoft ピアリングのメトリック (ビット/秒) を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="ピアリングごとのメトリック":::

### <a name="bgp-availability---split-by-peer"></a>BGP の可用性 - ピアによる分割  

集計の種類: *Avg*

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、BGP (レイヤー 3 接続) の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、プライベート ピアリング用のプライマリ BGP セッション状態はアップ状態であり、プライベート ピアリング用の 2 番目の BGP セッション状態はダウン状態であると表示されます。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="ピアごとの BGP の可用性":::

### <a name="arp-availability---split-by-peering"></a>ARP の可用性 - ピアリングによる分割  

集計の種類: *Avg*

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、[ARP](./expressroute-troubleshooting-arp-resource-manager.md) (レイヤー 3 接続) の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、両方のピア間のプライベート ピアリング ARP セッションはアップ状態だがピア間の Microsoft ピアリングはダウン状態であることが表示されます。 既定の集計 (平均) は、両方のピアとの間に使用されていました。  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ピアごとの ARP の可用性":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct メトリック

### <a name="admin-state---split-by-link"></a>管理状態 - リンクによる分割

集計の種類: *Avg*

ExpressRoute Direct ポート ペアのリンクごとに管理状態を表示できます。 管理状態は、物理ポートがオンかオフかを示します。 この状態は、ExpressRoute Direct 接続を介してトラフィックを通過させるために必要です。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct 管理状態":::

### <a name="bits-in-per-second---split-by-link"></a>1 秒あたりのビット イン - リンクで分割

集計の種類: *Avg*

ExpressRoute Direct ポート ペアの両方のリンクを対象に、1 秒あたりのビット インを表示できます。 このダッシュボードを監視して、両方のリンクの着信帯域幅を比較します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct の 1 秒あたりのビット イン":::

### <a name="bits-out-per-second---split-by-link"></a>1 秒あたりのビット アウト - リンクで分割

集計の種類: *Avg*

ExpressRoute Direct ポート ペアの両方のリンクを対象に、1 秒あたりのビット アウトも表示できます。 このダッシュボードを監視して、両方のリンクの発信帯域幅を比較します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct の 1 秒あたりのビット アウト":::

### <a name="line-protocol---split-by-link"></a>回線プロトコル - リンクによる分割

集計の種類: *Avg*

ExpressRoute Direct ポート ペアのリンクごとに回線プロトコルを表示できます。 回線プロトコルは、物理リンクが ExpressRoute Direct 経由で稼働しているかどうかを示します。 このダッシュボードを監視し、物理接続が停止したことを通知するアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct 回線プロトコル":::

### <a name="rx-light-level---split-by-link"></a>Rx ライト レベル - リンクによる分割

集計の種類: *Avg*

ポート別の Rx ライト レベル (ExpressRoute Direct ポートで **受信** するライト レベル) を表示できます。 正常な Rx ライト レベルは通常、-10 dBm から 0 dBm までの範囲内です。 Rx ライト レベルが正常範囲外になった場合に通知されるようにアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct 回線 Rx ライト レベル":::

### <a name="tx-light-level---split-by-link"></a>Tx ライト レベル - リンクによる分割

集計の種類: *Avg*

ポート別の Tx ライト レベル (ExpressRoute Direct ポートで **送信** するライト レベル) を表示できます。 正常な Tx ライト レベルは一般に、-10 dBm から 0 dBm までの範囲内です。 Tx ライト レベルが正常範囲外になった場合に通知されるようにアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct 回線 Tx ライト レベル":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 仮想ネットワーク ゲートウェイ メトリック

集計の種類: *Avg*

ExpressRoute ゲートウェイをデプロイすると、Azure でゲートウェイのコンピューティングと機能が管理されます。 ゲートウェイのパフォーマンスをより深く理解するために、6 つのゲートウェイ メトリックが用意されています。

* CPU 使用率
* 1 秒あたりのパケット数
* ピアにアドバタイズされたルートの数
* ピアから学習したルートの数
* ルートが変更された頻度
* 仮想ネットワーク内の VM の数  

これらのメトリックごとにアラートを設定することを強くお勧めします。これにより、ゲートウェイでパフォーマンスの問題が発生した場合に把握できます。

### <a name="cpu-utilization---split-instance"></a>CPU 使用率 - インスタンスの分割

集計の種類: *Avg*

各ゲートウェイ インスタンスの CPU 使用率を表示できます。 定期的なホスト メンテナンス中に CPU 使用率が急増することがありますが、CPU 使用率が高い状態が長く続く場合は、ゲートウェイがパフォーマンスのボトルネックに達していることを示している可能性があります。 ExpressRoute ゲートウェイのサイズを増やすと、この問題が解決することがあります。 CPU 使用率が特定のしきい値を超えた頻度について、アラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 使用率 - 分割メトリックのスクリーンショット。":::

### <a name="packets-per-second---split-by-instance"></a>1 秒あたりのパケット数 - インスタンスで分割

集計の種類: *Avg*

このメトリックは、ExpressRoute ゲートウェイを通過する受信パケットの数をキャプチャします。 ゲートウェイがオンプレミス ネットワークからのトラフィックを受信している場合は、一貫したデータ ストリームがここに表示されることが想定されます。 1 秒あたりのパケット数がしきい値を下回り、ゲートウェイがトラフィックを受信しなくなったことを示している場合のアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="1 秒あたりのパケット数 - 分割メトリックのスクリーンショット。":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>ピアにアドバタイズされたルートの数 - インスタンスで分割

集計の種類: *Count*

このメトリックは、ExpressRoute ゲートウェイで回線にアドバタイズされているルートの数です。 アドレス空間には、VNet ピアリングを使用して接続され、リモート ExpressRoute ゲートウェイが使用される仮想ネットワークが含まれる場合があります。 仮想ネットワークのアドレス空間に頻繁に変更が加えられない限り、ルートの数は一定であると想定されます。 認識している仮想ネットワーク アドレス空間の数に対して、アドバタイズされたルートの数がしきい値を下回った場合のアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="ピアにアドバタイズされたルートの数のスクリーンショット。":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>ピアから学習したルートの数 - インスタンスで分割

集計の種類: *Max*

このメトリックは、ExpressRoute ゲートウェイで ExpressRoute 回線に接続されているピアから学習しているルートの数を示します。 これらのルートは、同じ回線に接続されている別の仮想ネットワークのものでも、オンプレミスから学習したものでもかまいません。 学習したルートの数が特定のしきい値を下回った場合のアラートを設定します。 これは、ゲートウェイでパフォーマンスの問題が発生しているか、リモート ピアで ExpressRoute 回線へのルートがアドバタイズされていないことを示している可能性があります。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="ピアから学習したルートの数のスクリーンショット。":::

### <a name="frequency-of-routes-change---split-by-instance"></a>ルート変更の頻度 - インスタンスで分割

集計の種類: *Sum*

このメトリックは、リモート ピアから学習した、またはアドバタイズされたルートの頻度を示します。 まず、オンプレミスのデバイスを調査して、ネットワークが頻繁に変更されている理由を把握しておく必要があります。 ルート変更の頻度が高い場合は、ExpressRoute ゲートウェイでパフォーマンスの問題が発生している可能性があり、この問題はゲートウェイ SKU のスケール アップによって解決することがあります。 ExpressRoute ゲートウェイで異常なルート変更が観察されるとき、認識すべき頻度のしきい値のアラートを設定します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="ルートが変更された頻度のメトリックのスクリーンショット。":::

### <a name="number-of-vms-in-the-virtual-network"></a>仮想ネットワーク内の VM の数

集計の種類: *Max*

このメトリックは、ExpressRoute ゲートウェイが使用されている仮想マシンの数を示します。 仮想マシンの数には、同じ ExpressRoute ゲートウェイが使用されているピアリングされた仮想ネットワークの VM が含まれる場合があります。 VM の数が特定のしきい値を超え、ゲートウェイのパフォーマンスに影響する可能性がある場合に、このメトリックのアラートを設定します。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="仮想ネットワーク内の仮想マシンの数を示すメトリックのスクリーンショット。":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute ゲートウェイの接続 (ビット/秒)

集計の種類: *Avg*

このメトリックは、ExpressRoute 回線への特定の接続における帯域幅の使用量を示します。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="ゲートウェイ接続の帯域幅の使用量を示すメトリックのスクリーンショット。":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute ゲートウェイの接続のアラート

1. アラートを構成するために、**Azure Monitor** に移動して、 **[アラート]** を選択します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. **[+ ターゲットの選択]** を選択し、ExpressRoute ゲートウェイの接続リソースを選択します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="target":::
3. アラートの詳細を定義します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="アクション グループ":::
4. アクション グループを定義して追加します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="アクション グループの追加":::

## <a name="alerts-based-on-each-peering"></a>各ピアリングに基づくアラート

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="各ピアリング":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>回線でアクティビティ ログのアラートを構成する

**[アラートの条件]** で、[シグナルの種類] に **[アクティビティ ログ]** を選択し、[シグナル] を選択することができます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="アクティビティ ログ":::

## <a name="more-metrics-in-log-analytics"></a>Log Analytics のその他のメトリック

ExpressRoute メトリックは、ExpressRoute 回線に移動し、 *[ログ]* タブを選択する方法でも表示できます。クエリを実行するメトリックについては、出力に以下の列が含まれます。

|**列**|**Type**|**説明**|
| --- | --- | --- |
|TimeGrain|string|PT1M (メトリック値が 1 分ごとにプッシュされます)|
|Count|real|通常は 2 に等しい (各 MSEE により、1 分ごとにメトリック値が 1 つプッシュされます)|
|最小値|real|2 つの MSEE によってプッシュされる 2 つのメトリック値の最小値|
|最大値|real|2 つの MSEE によってプッシュされる 2 つのメトリック値の最大値|
|Average|real|(最小値 + 最大値)/2 に等しい|
|合計|real|両方の MSEE からの 2 つのメトリック値の合計 (クエリが実行されたメトリックの場合、メインの値に焦点が当たる)|
  
## <a name="next-steps"></a>次のステップ

ExpressRoute 接続を構成します。
  
* [回線の作成と変更](expressroute-howto-circuit-arm.md)
* [ピアリング構成の作成と変更](expressroute-howto-routing-arm.md)
* [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)
