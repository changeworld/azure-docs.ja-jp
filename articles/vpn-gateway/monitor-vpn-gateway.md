---
title: Azure VPN Gateway を監視する
description: Azure Monitor を使用して VPN Gateway メトリックを表示する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/23/2021
ms.author: alzam
ms.openlocfilehash: a55c4ac84f218b4fe657f02cac9f10a4e9dc4a68
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2021
ms.locfileid: "112583967"
---
# <a name="monitoring-vpn-gateway"></a>VPN Gateway の監視

Azure Monitor を使用して、Azure VPN ゲートウェイを監視できます。 この記事では、ポータル経由で利用できるメトリックについて説明します。 メトリックは軽量で、ほぼリアルタイムのシナリオをサポートできるため、アラートや迅速な問題の検出に役立ちます。


|**メトリック**   | **単位** | **細分性** | **説明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| バイト/秒  | 5 分| ゲートウェイ上のすべてのサイト対サイト接続の帯域幅合計使用率の平均。     |
|**P2SBandwidth**| バイト/秒  | 1 分  | ゲートウェイ上のすべてのポイント対サイト接続の帯域幅合計使用率の平均。    |
|**P2SConnectionCount**| Count  | 1 分  | ゲートウェイ上のポイント対サイト接続の数。   |
|**TunnelAverageBandwidth** | バイト/秒    | 5 分  | ゲートウェイに作成されたトンネルの帯域幅使用率の平均。 |
|**TunnelEgressBytes** | バイト | 5 分 | ゲートウェイに作成されたトンネルの発信トラフィック。   |
|**TunnelEgressPackets** | Count | 5 分 | ゲートウェイに作成されたトンネルの発信パケット数。   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 分 | トラフィック セレクターの不一致に原因があるトンネルでドロップされた発信パケットの数。 |
|**TunnelIngressBytes** | バイト | 5 分 | ゲートウェイに作成されたトンネルの着信トラフィック。   |
|**TunnelIngressPackets** | Count | 5 分 | ゲートウェイに作成されたトンネルの着信パケットの数。   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 分 | トラフィック セレクターの不一致に原因があるトンネルでドロップされた着信パケットの数。 |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>次の手順に従い、メトリックを検索して表示できます。

1. ポータルで仮想ネットワーク ゲートウェイ リソースに移動します
2. **[概要]** を選択して、トンネル イングレスおよびエグレス総数の各メトリックを表示します。

   ![アラート ルールを作成するための選択](./media/monitor-vpn-gateway/overview.png "表示")

3. 上記の一覧に示されている他のメトリックのいずれかを表示するには、 仮想ネットワーク ゲートウェイ リソースの下の **[メトリック]** セクションをクリックし、ドロップダウン リストからメトリックを選択します。

   ![[選択] ボタンとリソースの一覧内の VPN ゲートウェイ](./media/monitor-vpn-gateway/metrics.png "Select")

## <a name="next-steps"></a>次のステップ

トンネル メトリックにアラートを構成するには、「[Set up alerts on VPN Gateway metrics](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)」(VPN Gateway メトリックにアラートを設定する) を参照してください。

トンネル リソース ログにアラートを構成するには、[VPN Gateway リソース ログのアラートの設定](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)に関するページを参照してください。
