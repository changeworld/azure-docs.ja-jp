---
title: Azure Virtual WAN の監視
description: Azure Monitor を使用した Azure Virtual WAN のログとメトリックについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: fd0eeb94bb35248aababa3f3a8d9fe820d4abe7f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002873"
---
# <a name="monitoring-virtual-wan"></a>Virtual WAN の監視

Azure Monitor を使用して、Azure Virtual WAN を監視できます。 Virtual WAN は、ネットワーク、セキュリティ、およびルーティングのさまざまな機能をまとめて、1 つの運用インターフェイスを提供するネットワーク サービスです。 Virtual WAN VPN ゲートウェイ、ExpressRoute ゲートウェイ、および Azure Firewall には、Azure Monitor 経由で利用可能なログ記録とメトリックがあります。 

この記事では、ポータル経由で利用可能なメトリックと診断について取り上げます。 メトリックは軽量で、ほぼリアルタイムのシナリオをサポートできるため、アラートや迅速な問題の検出に役立ちます。

### <a name="monitoring-secured-hub-azure-firewall"></a>セキュリティ保護付きハブの監視 (Azure Firewall) 

Azure Firewall を使用して仮想ハブをセキュリティで保護することを選択した場合、関連するログとメトリックはこちらで入手できます。[Azure Firewall のログとメトリック](../firewall/logs-and-metrics.md)。
Azure Firewall のログとメトリックを使用して、セキュリティ保護付きハブを監視できます。 また、アクティビティ ログを使用して、Azure Firewall リソースに対する操作を監査することもできます。
セキュリティ保護してセキュリティ保護付きハブに変換する Azure 仮想 WAN ごとに、ハブがあるリソース グループに明示的なファイアウォール リソース オブジェクトが作成されます。 

:::image type="content" source="./media/monitor-virtual-wan/firewall-resources-portal.png" alt-text="スクリーンショットは、vWAN ハブ リソース グループ内のファイアウォール リソースを示しています。":::

診断とログ記録の構成は、そこから **[診断設定]** タブにアクセスして行う必要があります。

:::image type="content" source="./media/monitor-virtual-wan/firewall-diagnostic-settings.png" alt-text="スクリーンショットは、ファイアウォール診断設定を示しています。":::


## <a name="metrics"></a>メトリック

Azure Monitor において、メトリックは特定の時点におけるシステムの何らかの側面を表す数値です。 メトリックは 1 分ごとに収集され、頻繁にサンプリングできるためアラート発信に役立ちます。 アラートは比較的シンプルなロジックで迅速に発生させることができます。

### <a name="site-to-site-vpn-gateways"></a>サイト間 VPN ゲートウェイ

Azure サイト間 VPN ゲートウェイでは、次のメトリックを利用できます。

#### <a name="tunnel-packet-drop-metrics"></a>トンネル パケット破棄メトリック
| メトリック | 説明|
| --- | --- |
| **トンネル エグレス パケット破棄数** | トンネルによって破棄された送信パケットの数。|
| **トンネル イングレス パケット破棄数** | トンネルによって破棄された受信パケットの数。|
| **トンネルでの NAT パケットの切断** | 破棄の種類と NAT 規則別のトンネルで破棄された NAT 処理パケットの数。|
| **Tunnel Egress TS Mismatch Packet Drop (トンネル エグレス TS 不一致パケット破棄数)** | トンネルのトラフィック セレクター不一致からの送信パケット破棄数。|
| **Tunnel Ingress TS Mismatch Packet Drop (トンネル イングレス TS 不一致パケット破棄数)** | トンネルのトラフィック セレクター不一致からの受信パケット破棄数。|

#### <a name="ipsec-metrics"></a>IPSEC メトリック
| メトリック | 説明|
| --- | --- |
| **トンネル MMSA 数** | 作成または削除された MMSA の数。|
| **トンネル QMSA 数** | 作成または削除された IPSEC QMSA の数。|

#### <a name="routing-metrics"></a>ルーティング メトリック
| メトリック | 説明|
| --- | --- |
| **BGP ピアの状態** | ピアごとおよびインスタンスごとの BGP 接続状態。|
| **アドバタイズされた BGP ルート** | ピアごとおよびインスタンスごとのアドバタイズされたルートの数。|
| **学習された BGP ルート** | ピアごとおよびインスタンスごとの学習されたルートの数。|
| **VNET アドレス プレフィックス数** | ゲートウェイによって使用またはアドバタイズされた VNET アドレス プレフィックスの数。|

**[分割を適用する]** を選択し、推奨値を選択することで、ピアとインスタンスごとのメトリックを確認できます。 

#### <a name="traffic-flow-metrics"></a>トラフィック フロー メトリック
| メトリック | 説明|
| --- | --- |
| **ゲートウェイの帯域幅** | ゲートウェイのサイト間での平均総帯域幅 (バイト/秒)。|
| **Tunnel Bandwidth (トンネル帯域幅)** | 平均トンネル帯域幅 (バイト/秒)。|
| **Tunnel Egress Bytes (トンネル エグレス バイト数)** | トンネルの送信バイト数。 |
| **Tunnel Egress Packets (トンネル エグレス パケット数)** | トンネルの送信パケット数。 |
| **Tunnel Ingress Bytes (トンネル イングレス バイト数)** | トンネルの受信バイト数。|
| **Tunnel Ingress Packet (トンネル イングレス パケット数)** | トンネルの受信パケット数。|
| **トンネル ピーク PPS** | 最後の 1 分間のリンク接続あたりの 1 秒あたりのパケット数。|
| **トンネル フロー数** | リンク接続ごとに作成された個別のフローの数。|

### <a name="point-to-site-vpn-gateways"></a>ポイント対サイト VPN ゲートウェイ

Azure ポイント対サイト VPN ゲートウェイでは、次のメトリックを利用できます。

| メトリック | 説明|
| --- | --- |
| **ゲートウェイの P2S 帯域幅** | ゲートウェイのポイント対サイトでの平均総帯域幅 (バイト/秒)。 |
| **P2S 接続数** |ゲートウェイのポイント対サイト接続の数。 ゲートウェイのポイント対サイト接続の数。 Azure Monitor で正確なメトリックが表示されるように、 **[P2S 接続数]** の **[集計の種類]** として **[合計]** を選択してください。 また、 **[インスタンス]** ごとにも分割する場合は、 **[最大]** を選択することもできます。 |
| **ユーザー VPN ルート数** | VPN ゲートウェイに構成されているユーザー VPN ルートの数。 このメトリックは、**静的** ルートと **動的** ルートに分けることができます。

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute ゲートウェイ

Azure ExpressRoute ゲートウェイでは、次のメトリックを利用できます。

| メトリック | 説明|
| --- | --- |
| **BitsInPerSecond (受信ビット数/秒)** | 1 秒あたりの Azure へのイングレス ビット数。|
| **BitsOutPerSecond (送信ビット数/秒)** | 1 秒あたりの Azure からのエグレス ビット数。 |
| **CPU 使用率** | ExpressRoute ゲートウェイの CPU 使用率。|
| **1 秒あたりのパケット数** | ExpressRoute ゲートウェイのパケット数。|
| **ピアにアドバタイズされたルートの数**| ExpressRoute ゲートウェイによってピアにアドバタイズされたルートの数。 | 
| **ピアから学習したルートの数**| ExpressRoute ゲートウェイによってピアから学習したルートの数。|
| **ルートが変更された頻度** | ExpressRoute ゲートウェイでのルート変更の頻度。|
| **仮想ネットワーク内の VM の数**| この ExpressRoute ゲートウェイを使用する VM の数。|

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>ゲートウェイ メトリックを表示する

次の手順に従い、メトリックを検索して表示できます。

1. ポータル上で、ゲートウェイがある仮想ハブに移動します。

2. サイト間ゲートウェイを検索するには **[VPN (サイト間)]** を、ExpressRoute ゲートウェイを検索するには **[ExpressRoute]** を、ポイント対サイト ゲートウェイを検索するには **[ユーザー VPN (ポイントからサイトへ)]** を選択します。

3. **[メトリック]** を選びます。

   :::image type="content" source="./media/monitor-virtual-wan/view-metrics.png" alt-text="スクリーンショットは、[Azure Monitor で表示] が選択されたサイト間 VPN ペインを示しています。":::

4. **[メトリック]** ページ上で、関心のあるメトリックを閲覧できます。

   :::image type="content" source="./media/monitor-virtual-wan/metrics-page.png" alt-text="カテゴリが強調表示されている [メトリック] ページを示すスクリーンショット。":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>診断ログ

### <a name="site-to-site-vpn-gateways"></a>サイト間 VPN ゲートウェイ

Azure サイト間 VPN ゲートウェイでは、次の診断を利用できます。

| メトリック | 説明|
| --- | --- |
| **ゲートウェイ診断ログ** | 正常性、構成、サービス更新、追加の診断など、ゲートウェイ固有の診断。|
| **トンネル診断ログ** | サイト間 IPsec トンネルの接続および切断イベント、ネゴシエートされた SA、切断の理由、追加の診断など、IPsec トンネル関連のログです。|
| **ルート診断ログ** | 静的ルートのイベント、BGP、ルート更新、追加の診断に関連するログです。 |
| **IKE 診断ログ** | IPsec 接続に対する IKE 固有の診断。 |

### <a name="point-to-site-vpn-gateways"></a>ポイント対サイト VPN ゲートウェイ

Azure ポイント対サイト VPN ゲートウェイでは、次の診断を利用できます。

| メトリック | 説明|
| --- | --- |
| **ゲートウェイ診断ログ** | 正常性、構成、サービス更新、その他の診断など、ゲートウェイ固有の診断。 |
| **IKE 診断ログ** | IPsec 接続に対する IKE 固有の診断。|
| **P2S 診断ログ** | ユーザー VPN (ポイント対サイト) P2S の構成とクライアント イベントです。 クライアントの接続または切断、VPN クライアント アドレスの割り当て、その他の診断が含まれます。|

### <a name="express-route-gateways"></a>Express Route ゲートウェイ

Azure Virtual WAN 内の Express Route ゲートウェイの診断ログはサポートされていません。

### <a name="view-diagnostic-logs-configuration"></a><a name="diagnostic-steps"></a>診断ログの構成の表示

次の手順を使用すると、診断設定の作成、編集、表示を行うことができます。

1. ポータルで、Virtual WAN リソースに移動し、 **[接続]** グループで **[ハブ]** を選択します。 

   :::image type="content" source="./media/monitor-virtual-wan/select-hub.png" alt-text="vWAN ポータルでのハブの選択を示すスクリーンショット。":::

2. 左側の **[接続]** グループで、診断を調べるゲートウェイを選択します。

   :::image type="content" source="./media/monitor-virtual-wan/select-hub-gateway.png" alt-text="ハブの [接続] セクションを示すスクリーンショット。":::

3. ページの右側の **[ログ]** の右にある **[Azure Monitor で表示]** リンクをクリックして、オプションを選択します。 Log Analytics への送信、イベント ハブへのストリーム、またはストレージ アカウントへの単純なアーカイブから選ぶことができます。

   :::image type="content" source="./media/monitor-virtual-wan/view-hub-gateway-logs.png" alt-text="[ログ] の [Azure Monitor で表示] の選択を示すスクリーンショット。":::

4. このページで、新しい診断設定の作成 ( **[+診断設定を追加する]** ) や既存の診断設定の編集 ( **[設定の編集]** ) を行えます。 診断ログを Log Analytics に送信するか (次の例を参照)、イベント ハブにストリーム配信するか、サード パーティのソリューションに送信するか、ストレージ アカウントにアーカイブするかを選択できます。

    :::image type="content" source="./media/monitor-virtual-wan/select-gateway-settings.png" alt-text="[診断ログの設定] の選択を示すスクリーンショット。":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics サンプル クエリ

Log Analytics ワークスペースに診断データを送信することを選択した場合は、次の例のような SQL 系のクエリを使用してデータを調べることができます。 詳細については、「[Log Analytics クエリ言語](/services-hub/health/log_analytics_query_language)」を参照してください。

次の例には、サイト間でのルート診断を取得するためのクエリが含まれています。

`AzureDiagnostics | where Category == "RouteDiagnosticLog"`

この記事の前のセクションに示した表に基づいて、必要に応じて、 **= =** の後の以下の値を置き換えます。

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

クエリを実行するには、診断ログを受信するように構成した Log Analytics リソースを開き、ペインの左側の **[全般]** タブで **[ログ]** を選択する必要があります。

:::image type="content" source="./media/monitor-virtual-wan/log-analytics-query-samples.png" alt-text="Log Analytics クエリの例。":::

サイト間およびポイント対サイトの Azure VPN Gateway の追加の Log Analytics クエリ サンプルについては、「[診断ログを使用した Azure VPN Gateway のトラブルシューティング](../vpn-gateway/troubleshoot-vpn-with-azure-diagnostics.md)」のページを参照してください。 Azure Firewall では、ログ分析を容易にする[ブック](../firewall/firewall-workbook.md)が用意されています。 そのグラフィカル インターフェイスを使用すると、Log Analytics クエリを手動で作成しなくても、診断データを調べることができます。 

## <a name="activity-logs"></a><a name="activity-logs"></a>アクティビティ ログ

**アクティビティ ログ** エントリは既定で収集され、また Azure portal に表示できます。 Azure アクティビティ ログ (以前は "*操作ログ*" と "*監査ログ*" と呼ばれていた) を使用して、Azure サブスクリプションに送信されるすべての操作を表示できます。

## <a name="next-steps"></a>次のステップ

* Azure Firewall のログとメトリックを監視する方法については、[Azure Firewall のログを監視する方法に関するチュートリアル](../firewall/firewall-diagnostics.md)を参照してください。
* Azure Monitor のメトリックの詳細については、「[Azure Monitor のメトリック](../azure-monitor/essentials/data-platform-metrics.md)」を参照してください。
