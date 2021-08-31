---
title: Azure Virtual WAN の監視
description: Azure Monitor を使用した Azure Virtual WAN のログとメトリックについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: 25e12ce4fd361cb053eae8b0d9992031a91d4616
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469018"
---
# <a name="monitoring-virtual-wan"></a>Virtual WAN の監視

Azure Monitor を使用して、Azure Virtual WAN を監視できます。 Virtual WAN は、ネットワーク、セキュリティ、およびルーティングのさまざまな機能をまとめて、1 つの運用インターフェイスを提供するネットワーク サービスです。 Virtual WAN VPN ゲートウェイ、ExpressRoute ゲートウェイ、および Azure Firewall には、Azure Monitor 経由で利用可能なログ記録とメトリックがあります。

この記事では、ポータル経由で利用可能なメトリックと診断について取り上げます。 メトリックは軽量で、ほぼリアルタイムのシナリオをサポートできるため、アラートや迅速な問題の検出に役立ちます。

### <a name="monitoring-secured-hub-azure-firewall"></a>セキュリティ保護付きハブの監視 (Azure Firewall) 

Azure Firewall のログを使用して、セキュリティ保護付きハブを監視できます。 また、アクティビティ ログを使用して、Azure Firewall リソースに対する操作を監査することもできます。

Azure Firewall を使用して仮想ハブをセキュリティで保護することを選択した場合、関連するログとメトリックはこちらで入手できます。[Azure Firewall のログとメトリック](../firewall/logs-and-metrics.md)。

## <a name="metrics"></a>メトリック

Azure Monitor において、メトリックは特定の時点におけるシステムの何らかの側面を表す数値です。 メトリックは 1 分ごとに収集され、頻繁にサンプリングできるためアラート発信に役立ちます。 アラートは比較的シンプルなロジックで迅速に発生させることができます。

### <a name="site-to-site-vpn-gateways"></a>サイト間 VPN ゲートウェイ

Azure サイト間 VPN ゲートウェイでは、次のメトリックを利用できます。

| メトリック | 説明|
| --- | --- |
| **ゲートウェイの帯域幅** | ゲートウェイのサイト間での平均総帯域幅 (バイト/秒)。|
| **Tunnel Bandwidth (トンネル帯域幅)** | 平均トンネル帯域幅 (バイト/秒)。|
| **Tunnel Egress Bytes (トンネル エグレス バイト数)** | トンネルの送信バイト数。 |
| **Tunnel Egress Packets (トンネル エグレス パケット数)** | トンネルの送信パケット数。 |
| **Tunnel Egress TS Mismatch Packet Drop (トンネル エグレス TS 不一致パケット破棄数)** | トンネルのトラフィック セレクター不一致からの送信パケット破棄数。|
| **Tunnel Ingress Bytes (トンネル イングレス バイト数)** | トンネルの受信バイト数。|
| **Tunnel Ingress Packet (トンネル イングレス パケット数)** | トンネルの受信パケット数。|
| **Tunnel Ingress TS Mismatch Packet Drop (トンネル イングレス TS 不一致パケット破棄数)** | トンネルのトラフィック セレクター不一致からの受信パケット破棄数。|

### <a name="point-to-site-vpn-gateways"></a>ポイント対サイト VPN ゲートウェイ

Azure ポイント対サイト VPN ゲートウェイでは、次のメトリックを利用できます。

| メトリック | 説明|
| --- | --- |
| **ゲートウェイの P2S 帯域幅** | ゲートウェイのポイント対サイトでの平均総帯域幅 (バイト/秒)。 |
| **P2S 接続数** |ゲートウェイのポイント対サイト接続の数。 ゲートウェイのポイント対サイト接続の数。 Azure Monitor で正確なメトリックを表示していることを確認するには、 **[P2S 接続数]** の **[集計の種類]** として **[合計]** を選択してください。 また、 **[インスタンス]** ごとにも分割する場合は、 **[最大]** を選択することもできます。 |

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute ゲートウェイ

Azure ExpressRoute ゲートウェイでは、次のメトリックを利用できます。

| メトリック | 説明|
| --- | --- |
| **BitsInPerSecond (受信ビット数/秒)** | 1 秒あたりの Azure へのイングレス ビット数。|
| **BitsOutPerSecond (送信ビット数/秒)** | 1 秒あたりの Azure からのエグレス ビット数。 |

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>ゲートウェイ メトリックを表示する

次の手順に従い、メトリックを検索して表示できます。

1. ポータル上で、ゲートウェイがある仮想ハブに移動します。

2. サイト間ゲートウェイを検索するには **[VPN (サイト間)]** を、ExpressRoute ゲートウェイを検索するには **[ExpressRoute]** を、ポイント対サイト ゲートウェイを検索するには **[ユーザー VPN (ポイントからサイトへ)]** を選択します。 ページ上で、ゲートウェイの情報を確認できます。 この情報をコピーします。 Azure Monitor を使用して診断を表示するために、後で使用します。

3. **[メトリック]** を選びます。

   :::image type="content" source="./media/monitor-virtual-wan/metrics.png" alt-text="スクリーンショットには、Azure Monitor で [表示] が選択されたサイト間 VPN ペインが示されています。":::

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
| **P2S 診断ログ** | ユーザー VPN (ポイント対サイト) P2S の構成とクライアント イベントです。 クライアントの接続/切断、VPN クライアント アドレスの割り当て、およびその他の診断が含まれます。|

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>診断ログを表示する

次の手順に従い、診断を検索して表示できます。

1. ポータル上で、Virtual WAN リソースに移動します。 ポータル上の [Virtual WAN] ページの **[概要]** セクションで、 **[Essentials]** を選択してビューを展開し、リソース グループ情報を取得します。 リソース グループ情報をコピーします。

   :::image type="content" source="./media/monitor-virtual-wan/3.png" alt-text="[コピー] ボタンを指す矢印の付いた [概要] セクションを示すスクリーンショット。":::

2. 検索バーから **[モニター]** に移動し、[設定] セクションで **[診断設定]** を選択した後、リソース グループ、リソースの種類、リソース情報を入力します。 これは、この記事で前述した「[ゲートウェイ メトリックを表示する](#metrics-steps)」の手順 2 でコピーしたリソース グループ情報です。

   :::image type="content" source="./media/monitor-virtual-wan/4.png" alt-text="[リソース] ドロップダウンを指す矢印の付いた [監視] セクションを示すスクリーンショット。":::

3. [結果] ページで、 **[+ 診断設定を追加する]** を選択してから、オプションを選択します。 Log Analytics への送信、イベント ハブへのストリーム、またはストレージ アカウントへの単純なアーカイブから選ぶことができます。

   :::image type="content" source="./media/monitor-virtual-wan/5.png" alt-text="メトリック ページ":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics サンプル クエリ

ログは、 **[Azure Log Analytics ワークスペース]** で検索されます。 Log Analytics 内でクエリを設定できます。 次の例には、サイト間でのルート診断を取得するためのクエリが含まれています。

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

必要に応じて、 **= =** の後ろにある以下の値を置き換えます。

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog”
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>アクティビティ ログ

**アクティビティ ログ** エントリは既定で収集され、また Azure portal に表示できます。 Azure アクティビティ ログ (以前は "*操作ログ*" と "*監査ログ*" と呼ばれていた) を使用して、Azure サブスクリプションに送信されるすべての操作を表示できます。

## <a name="next-steps"></a>次のステップ

* Azure Firewall のログとメトリックを監視する方法については、[Azure Firewall のログを監視する方法に関するチュートリアル](../firewall/firewall-diagnostics.md)を参照してください。
* Azure Monitor のメトリックの詳細については、「[Azure Monitor のメトリック](../azure-monitor/essentials/data-platform-metrics.md)」を参照してください。
