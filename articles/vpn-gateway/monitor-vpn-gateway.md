---
title: Azure VPN Gateway を監視する
description: Azure Monitor を使用して VPN Gateway メトリックを表示する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/08/2021
ms.author: alzam
ms.openlocfilehash: 8ae6519881dd0e41cde8ed0fa4d7ffc64f35bdf2
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058791"
---
# <a name="monitoring-vpn-gateway"></a>VPN Gateway の監視

Azure Monitor を使用して、Azure VPN ゲートウェイを監視できます。 この記事では、ポータル経由で利用できるメトリックについて説明します。 メトリックは軽量で、ほぼリアルタイムのシナリオをサポートできるため、アラートや迅速な問題の検出に役立ちます。


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

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>次の手順に従い、メトリックを検索して表示できます。

1. ポータルで仮想ネットワーク ゲートウェイ リソースに移動します
2. **[概要]** を選択して、トンネル イングレスおよびエグレス総数の各メトリックを表示します。

   ![アラート ルールを作成するための選択](./media/monitor-vpn-gateway/overview.png "表示")

3. 上記の一覧に示されている他のメトリックのいずれかを表示するには、 仮想ネットワーク ゲートウェイ リソースの下の **[メトリック]** セクションをクリックし、ドロップダウン リストからメトリックを選択します。

   ![[選択] ボタンとリソースの一覧内の VPN ゲートウェイ](./media/monitor-vpn-gateway/metrics.png "Select")

## <a name="next-steps"></a>次のステップ

トンネル メトリックにアラートを構成するには、「[Set up alerts on VPN Gateway metrics](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)」(VPN Gateway メトリックにアラートを設定する) を参照してください。

トンネル リソース ログにアラートを構成するには、[VPN Gateway リソース ログのアラートの設定](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)に関するページを参照してください。
