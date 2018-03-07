---
title: "Azure Log Analytics のネットワーク パフォーマンス モニター ソリューション | Microsoft Docs"
description: "Network Performance Monitor の ExpressRoute Manager 機能では、ブランチ オフィスと Azure 間のエンド ツー エンド接続とパフォーマンスを Azure ExpressRoute 経由で監視することができます。"
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 405bcd7d69e93f838d35b61be489464fcf55ee88
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2018
---
# <a name="expressroute-manager"></a>ExpressRoute Manager

[Network Performance Monitor](log-analytics-network-performance-monitor.md) の ExpressRoute Manager 機能では、ブランチ オフィスと Azure 間のエンド ツー エンド接続とパフォーマンスを Azure ExpressRoute 経由で監視することができます。 主な利点は次のとおりです。 

- サブスクリプションに関連付けられている ExpressRoute 回線の自動検出 
- ExpressRoute の帯域幅使用率、回線での損失と待ち時間、ピアリングと VNet のレベルの追跡 
- ExpressRoute 回線のネットワーク トポロジの検出 

![ExpressRoute Monitor](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>構成 
Network Performance Monitor の構成を開くには、[Network Performance Monitor ソリューション](log-analytics-network-performance-monitor.md)を開き、**[構成]** をクリックします。

### <a name="configure-nsg-rules"></a>NSG 規則の構成 
NPM を介した監視に使用される Azure 内のサーバーについては、NPM の代理トランザクションに使用されるポートの TCP トラフィックを許可するようにネットワーク セキュリティ グループ (NSG) 規則を構成する必要があります。 既定のポートは 8084 です。 これにより、Azure VM にインストールされている OMS エージェントが、オンプレミスの監視エージェントと通信できるようになります。 

NSG について詳しくは、 [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)に関するページをご覧ください。 

>[!NOTE]
> この手順に進む前に、エージェント (オンプレミス サーバー エージェントと Azure サーバー エージェントの両方) がインストール済みであること、また EnableRules.ps1 PowerShell スクリプトを実行済みであることをご確認ください。 

 
### <a name="discover-expressroute-peering-connections"></a>ExpressRoute ピアリング接続の検出 
 
1. **[ExpressRoute ピアリング]** ビューをクリックします。  
2. **[今すぐ検出する]** をクリックして、この Log Analytics ワークスペースにリンクされている Azure サブスクリプション内の VNET に接続されているすべての ExpressRoute プライベート ピアリングを検出します。  

>[!NOTE]  
> ソリューションは、現在、ExpressRoute のプライベート ピアリングのみを検出します。 

>[!NOTE]  
> この Log Analytics ワークスペースにリンクされているサブスクリプションに関連付けられた VNET に接続されているプライベート ピアリングのみが検出されます。 ExpressRoute が、このワークスペースにリンクされているサブスクリプションの外部の VNET に 接続されている場合は、これらのサブスクリプションに Log Analytics ワークスペースを作成し、NPM を使用してこれらのピアリングを監視する必要があります。 

 ![ExpressRoute Monitor 構成](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 検出が完了したら、検出されたプライベート ピアリング接続がテーブルに一覧表示されます。 これらのピアリングの監視は、最初は無効状態になっています。 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>ExpressRoute ピアリング接続の監視を有効にする 

1. 監視対象のプライベート ピアリング接続をクリックします。  
2. RHS ウィンドウで、**[このピアリングを監視する]** チェックボックスをクリックします。 
3. この接続の正常性イベントを作成する場合は、**[このピアリングの正常性監視を有効にする]** をオンにします。 
4. 監視条件を選択します。 しきい値を入力して、正常性イベントの生成に関するカスタムしきい値を設定できます。 ピアリング接続に対して選択したしきい値を条件の値が上回ると、正常性イベントが生成されます。 
5. **[エージェントの追加]** をクリックして、このピアリング接続を監視するために使用する監視エージェントを選択します。 接続の両側にエージェント (このピアリングに接続されている Azure VNET 内の少なくとも 1 つのエージェントと、このピアリングに接続されている少なくとも 1 つのオンプレミスのエージェント) を追加する必要があります。 
6. **[保存]** をクリックして構成を保存します。 

![ExpressRoute Monitor 構成](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


規則を有効にして監視対象の値とエージェントを選択した後、30 ～ 60 分ほど待つと、値が反映され始め、**[ExpressRoute の監視]** タイルが利用できる状態になります。 監視中のタイルが表示されていれば、ExpressRoute 回線と接続リソースが NPM によって監視されています。 

>[!NOTE]
> この機能は、新しいクエリ言語にアップグレードされたワークスペースで確実に機能します。  

## <a name="walkthrough"></a>チュートリアル 

ネットワーク パフォーマンス監視ダッシュ ボードには、ExpressRoute 回線とピアリング接続の正常性の概要が表示されます。 

![NPM ダッシュ ボードの ExpressRoute](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>回線の一覧 

監視対象の全 ExpressRoute 回線の一覧を表示するには、[ExpressRoute 回線] タイルをクリックします。 いずれかの回線を選択すると、その正常性状態のほか、パケット損失、帯域幅使用率、待ち時間の各トレンド グラフを表示できます。 これらのグラフは対話操作が可能です。 グラフのプロット対象となる時間枠を自分で選んでカスタマイズすることができます。 グラフ上の領域でマウスをドラッグすることによってデータ ポイントを拡大し、細かく表示することができます。 

![ExpressRoute 回線の一覧](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trend-of-loss-latency-and-throughput"></a>パケット損失、待ち時間、スループットのトレンド 

帯域幅、待ち時間、損失の各グラフは対話操作が可能です。 これらのグラフの任意のセクションをマウス操作で拡大することができます。 また、左上の [アクション] の下にある [日付/時刻] をクリックすることによって、帯域幅、待ち時間、損失データの表示対象間隔を変更することもできます。 

![ExpressRoute の待ち時間](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>ピアリング一覧 

ダッシュボードの **[プライベート ピアリング]** タイルをクリックすると、プライベート ピアリング上の仮想ネットワークに対するすべての接続の一覧が表示されます。 ここでいずれかの仮想ネットワークの接続を選択すると、その正常性状態のほか、パケット損失、帯域幅使用率、待ち時間の各トレンド グラフを表示できます。 

![ExpressRoute ピアリング](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>回線トポロジ 

回線トポロジを表示するには、**[トポロジ]** タイルをクリックします。 この操作により、選択した回線またはピアリングのトポロジ ビューが表示されます。 トポロジ ダイアグラムには、ネットワーク上の各セグメントの待ち時間が表示され、各レイヤー 3 ホップがダイアグラムのノードで表現されます。 いずれかのホップをクリックすると、そのホップについてのさらに詳しい情報が表示されます。 **[フィルター]** の下のスライダー バーを動かすことで、視認性を高めて表示範囲をオンプレミスのホップにまで広げることができます。 スライダー バーを左右に動かすと、トポロジ グラフに表示されるホップ数が増減します。 各セグメントの待ち時間が視覚的に確認できるので、ネットワーク上のセグメントの中で、待ち時間の長いセグメントを短時間で切り分けることができます。 

![ExpressRoute のトポロジ](media/log-analytics-network-performance-monitor/expressroute-topology.png)  

### <a name="detailed-topology-view-of-a-circuit"></a>回線の詳細なトポロジ ビュー 

このビューは、VNet 接続を示しています。 

![ExpressRoute VNet](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>診断 

Network Performance Monitor は、いくつかの回線接続の問題を診断するのに役立ちます。 問題の一部を次に示します 

**回線がダウンしている。** NPM は、オンプレミスのリソースと Azure Vnet 間の接続が失われるとすぐに通知します。 これにより、ユーザーからエスカレーションを受ける前に予防的なアクションを実行し、ダウンタイムを短縮できます 

![ExpressRoute 回線のダウン](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**トラフィックが目的の回線経由で流れていない。** NPM は、トラフィックが予期せず目的の ExpressRoute 回線経由で流れていない場合に通知します。 これは、回路がダウンし、トラフィックがバックアップ ルート経由で流れている場合、またはルーティングの問題がある場合に発生することがあります。 この情報は、ルーティング ポリシーの構成の問題を予防的に管理し、最適かつ最も安全なルートが使用されるようにするために役立ちます。 

 

**トラフィックがプライマリ回線経由で流れていない。** この機能は、トラフィックがセカンダリの ExpressRoute 回線経由で流れている場合に通知します。 この場合は接続性の問題は発生しませんが、プライマリ回線の問題の予防的なトラブルシューティングを行うと、より入念に準備を整えることができます。 

 
![ExpressRoute トラフィック フロー](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**ピーク時の使用率によるパフォーマンス低下。** 帯域幅使用率の傾向を待ち時間の傾向と関連付けて、Azure ワークロードのパフォーマンス低下が帯域幅使用率のピークによるものかどうかを識別し、それに応じてアクションを実行することができます。  

![ExpressRoute Monitor](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>次の手順
* 詳細なネットワーク パフォーマンスのデータ レコードを表示するために、[ログを検索](log-analytics-log-searches.md)します。
