---
title: Azure VPN Gateway メトリックにアラートを設定する
description: Azure portal を使用し、仮想ネットワーク VPN ゲートウェイのメトリックに基づいて Azure Monitor のアラートを設定する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e17f86d08b5892c7df0a761e53e1f16dd43f127a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158298"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>VPN Gateway メトリックにアラートを設定する

この記事では、Azure VPN Gateway メトリックにアラートを設定する方法について説明します。 Azure Monitor では、Azure リソースのアラートを設定するための機能を提供しています。 "VPN" 型の仮想ネットワーク ゲートウェイにアラートを設定できます。

| **メトリック**                                 | **単位**     | **細分性**     | **説明**                                                                         |
| -------------------------------------------| ------------ | ------------------- | --------------------------------------------------------------------------------------- |
| **BGP ピアの状態**                        | Count        | 5 分           | ピアごとおよびインスタンスごとの平均の BGP 接続状態。                              |
| **アドバタイズされた BGP ルート**                  | Count        | 5 分           | ピアごとおよびインスタンスごとのアドバタイズされたルートの数。                                  |
| **学習された BGP ルート**                     | Count        | 5 分           | ピアごとおよびインスタンスごとの学習されたルートの数。                                     |
| **ゲートウェイの P2S 帯域幅**                  | バイト/秒      | 1 分            | ゲートウェイ上のすべてのポイント対サイト接続の帯域幅合計使用率の平均。 |
| **ゲートウェイの S2S 帯域幅**                  | バイト/秒      | 5 分           | ゲートウェイ上のすべてのサイト対サイト接続の帯域幅合計使用率の平均。  |
| **P2S 接続数**                   | Count        | 1 分            | ゲートウェイ上のポイント対サイト接続の数。                                      |
| **Tunnel Bandwidth (トンネル帯域幅)**                       | バイト/秒      | 5 分           | ゲートウェイに作成されたトンネルの帯域幅使用率の平均。                        |
| **Tunnel Egress Bytes (トンネル エグレス バイト数)**                    | バイト        | 5 分           | トンネルからの送信バイト数。                                                 |
| **トンネル エグレス パケット破棄数**        | Count        | 5 分           | トンネルによって破棄された送信パケットの数。                                         |
| **Tunnel Egress Packets (トンネル エグレス パケット数)**                  | Count        | 5 分           | トンネルからの送信パケットの数。                                               |
| **Tunnel Egress TS Mismatch Packet Drop (トンネル エグレス TS 不一致パケット破棄数)**  | Count        | 5 分           | トラフィック セレクターの不一致に原因があるトンネルでドロップされた送信パケットの数。      |
| **Tunnel Ingress Bytes (トンネル イングレス バイト数)**                   | バイト        | 5 分           | トンネルへの受信バイト数。                                                   |
| **トンネル イングレス パケット破棄数**       | Count        | 5 分           | トンネルによって破棄された受信パケットの数。                                         |
| **Tunnel Ingress Packets (トンネル イングレス パケット数)**                 | Count        | 5 分           | トンネルへの受信パケット数。                                                 |
| **Tunnel Ingress TS Mismatch Packet Drop (トンネル イングレス TS 不一致パケット破棄数)** | Count        | 5 分           | トラフィック セレクターの不一致に原因があるトンネルでドロップされた受信パケットの数。      |
| **トンネル MMSA 数**                      | Count        | 5 分           | 存在するメイン モードのセキュリティ関連付けの数。                                      |
| **トンネル ピーク PPS**                        | Count        | 5 分           | トンネルごとの 1 秒あたりのパケットの最大数。                                            |
| **トンネル QMSA 数**                      | Count        | 5 分           | 存在するクイック モードのセキュリティ関連付けの数。                                     |
| **トンネルの合計フロー数**                | Count        | 5 分           | トンネルごとに作成された個別のフローの数。                                            |
| **ユーザー VPN ルート数**                   | Count        | 5 分           | VPN Gateway に構成されているユーザー VPN ルートの数。                                |
| **VNet アドレス プレフィックス数**              | Count        | 5 分           | ゲートウェイによって使用またはアドバタイズされた VNet アドレス プレフィックスの数。                |

## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Azure portal を使用して、メトリックに基づいた Azure Monitor のアラートを設定します

次の手順の例では、ゲートウェイに次のアラートを作成します。

- **メトリック:** TunnelAverageBandwidth
- **条件:** 帯域幅 > 10 バイト/秒
- **ウィンドウ:** 5 分
- **アラート アクション:** Email



1. 仮想ネットワーク ゲートウェイのリソースに移動し、 **[監視]** タブから **[アラート]** を選択します。次に新しいアラート ルールを作成するか、または既存のアラート ルールを編集します。

   ![アラート ルールを作成するための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "作成")

2. リソースとして VPN ゲートウェイを選択します。

   ![[選択] ボタンとリソースの一覧内の VPN ゲートウェイ](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Select")

3. アラート用に構成するメトリックを選択します。

   ![メトリックの一覧内の選択されたメトリック](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Select")
4. シグナル ロジックを構成します。 これには 3 つのコンポーネントがあります。

    a. **ディメンション**: メトリックにディメンションがある場合、特定のディメンション値を選択して、アラートがそのディメンションのデータのみを評価するようにできます。 これらは省略可能です。

    b. **条件**: これは、メトリック値を評価する操作です。

    c. **Time**: メトリック データの細分性と、アラートを評価する期間を指定します。

   ![シグナル ロジックの構成の詳細](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. 構成済みのルールを表示するには、 **[アラート ルールの管理]** を選択します。

   ![アラート ルールを管理するためのボタン](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Select")

## <a name="next-steps"></a>次のステップ

トンネル リソース ログにアラートを構成するには、[VPN Gateway リソース ログのアラートの設定](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)に関するページを参照してください。
