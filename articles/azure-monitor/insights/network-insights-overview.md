---
title: Azure Monitor for Networks (プレビュー)
description: デプロイされたすべてのネットワーク リソースの正常性とメトリックを構成なしで包括的に把握できる、Azure Monitor for Network の簡単な概要。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/22/2020
ms.openlocfilehash: 88e69ac4156bb2bdfb217a4fc83ac2d798d08211
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987863"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor for Networks (プレビュー)
Azure Monitor for Network では、デプロイされたすべてのネットワーク リソースの[正常性](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types)と[メトリック](../platform/metrics-supported.md)を構成なしで包括的に把握できます。  また、[接続モニター](../../network-watcher/connection-monitor-preview.md)、[ネットワーク セキュリティ グループ (NSG) のフロー ログ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)、[Traffic Analytics](../../network-watcher/traffic-analytics.md)、その他のネットワークの[診断](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics)など、すべてのネットワーク監視機能にアクセスすることもできます。

Azure Monitor for Networks は、次に示す監視の主要コンポーネントを中心に構成されています。
- [ネットワークの正常性とメトリック](#networkhealth)
- [接続](#connectivity)
- [トラフィック](#traffic)
- [診断ツールキット](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>ネットワークの正常性とメトリック

Azure Monitor for Networks の **[概要]** ページでは、ネットワーク リソースのインベントリと、リソースの正常性やアラートを簡単に視覚化できます。 4 つの主要な機能領域に分かれています。検索とフィルター処理、リソースの正常性とメトリック、アラート。 そして依存関係ビューです

![[概要] ページ](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>検索とフィルター処理
リソース正常性とアラートのビューは、 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リソースの種類]** などのフィルターを使用してカスタマイズできます。 検索ボックスでは、リソース プロパティを使用して検索することができます。

検索ボックスは、リソースや関連するリソースを検索するために使用できます。 たとえば、パブリック IP がアプリケーション ゲートウェイに関連付けられているとします。 このパブリック IP の DNS 名を検索すると、パブリック IP と、関連付けられているアプリケーション ゲートウェイが特定されます。

![Azure Monitor for Networks - 検索](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>リソース正常性とメトリック
各タイルには、リソースの種類と、選択されたすべてのサブスクリプション全体でデプロイされているインスタンスの数、およびリソース正常性の状態が表示されます。 次の例では、デプロイされている 45 個の ER および VPN 接続があり、44 個は正常で、1 個は利用不可です。

![Azure Monitor for Networks - リソースの正常性](media/network-insights-overview/resource-health.png)

利用不可の ER および VPN 接続をクリックすると、メトリック ビューが起動します。 

![Azure Monitor for Networks - メトリック ビュー](media/network-insights-overview/metric-view.png)

グリッド ビューの各要素をクリックできます。 [正常性] アイコンをクリックすると、その接続のリソース正常性にリダイレクトされます。 [アラート] をクリックすると、その接続のアラートとメトリックのそれぞれのページにリダイレクトされます。 

### <a name="alerts"></a>警告
右側の **[アラート]** グリッドには、すべてのサブスクリプションで選択されたリソースに対して生成されたすべてのアラートのビューが表示されます。 アラート数をクリックすると、アラートの詳細に関するページに移動します。

### <a name="dependency-view"></a>[依存関係] ビュー
**[依存関係]** ビューは、リソースがどのように構成されているかを視覚化するのに役立ちます。 現在、[依存関係] ビューは Application Gateway、Virtual WAN、Load Balancer に対してサポートされるようになっています。 たとえば、Application Gateway の場合、[依存関係] ビューにアクセスするには、[メトリック] グリッド ビューで Application Gateway のリソース名をクリックします。 これは、Virtual WAN および Load Balancer にも適用されます。

![Azure Monitor for Networks - Application Gateway ビュー](media/network-insights-overview/application-gateway.png)

Application Gateway の **[依存関係]** ビューは、フロントエンド IP がリスナー、ルール、バックエンド プールにどのように接続されているかを、簡略化されたビューで示します。 接続しているエッジは色分けされ、バックエンド プールの正常性に基づいて追加の詳細が表示されます。 このビューでは、Application Gateway メトリックと、仮想マシン スケール セットと VM インスタンスなどのすべての関連するバックエンド プールのメトリックの詳しいビューも表示されます。

![Azure Monitor for Networks - 依存関係ビュー](media/network-insights-overview/dependency-view.png)

依存関係グラフでは、構成設定に簡単に移動できます。 他の機能にアクセスするには、バックエンド プールを右クリックします。 たとえば、バックエンド プールが VM である場合、VM Insights と Network Watcher 接続のトラブルシューティングに直接アクセスして、接続の問題を特定できます。

![Azure Monitor for Networks - 依存関係ビューのメニュー](media/network-insights-overview/dependency-view-menu.png)

[依存関係] ビューの検索バーとフィルター バーを使用すると、グラフ全体を簡単に検索することができます。 たとえば、次の例で *AppGWTestRule* を検索すると、グラフィック表示が絞り込まれて、*AppGWTestRule* に接続されているすべてのノードが表示されます。

![Azure Monitor for Networks - 検索例](media/network-insights-overview/search-example.png)

様々なフィルターを使用すると、特定のパスや状態に絞り込むのに役立ちます。 たとえば、 **[正常性状態]** ドロップダウンから *[異常]* だけを選択すると、状態が *[異常]* であるすべてのエッジを表示することができます。

**[Detailed Metric View]\(詳細メトリック ビュー\)** をクリックすると、アプリケーション ゲートウェイ、すべてのバックエンド プール リソース、フロントエンド IP に関する詳しいメトリックを含む事前構成されたブックを起動できます。 

## <a name="connectivity"></a><a name="connectivity"></a>接続

**[接続]** タブを使用すると、選択したサブスクリプションのセットに対して接続モニターと[接続モニター (プレビュー)](../../network-watcher/connection-monitor-preview.md) を使用して、構成されたすべてのテストを簡単に視覚化することができます。

![Azure Monitor for Networks の [接続] タブ](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

テストは [ソース] と [ターゲット] のタイルごとにグループ化され、各テストの到達可能性の状態が表示されます。 [到達可能] の設定からは、チェックの失敗率 (%) と RTT (ミリ秒) に基づいた到達可能性条件の構成に簡単にアクセスできます。 これらの値が設定されると、各テストの状態が選択条件に基づいて更新されます。

![Azure Monitor for Networks での接続テスト](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

ソースまたはターゲットのタイルをクリックすると、メトリック ビューが起動します。

![Azure Monitor for Networks の接続メトリック](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


グリッド ビューの各要素をクリックできます。 **[Reachability]\(到達可能性\)** アイコンをクリックすると、 **[接続モニター]** ポータル ページにリダイレクトされ、特定された問題に影響を与えるホップごとのトポロジと接続が表示されます。 **[アラート]** をクリックしてアラートにリダイレクトし、 **[Checks Failed Percent/Round-Trip Time]\(失敗率およびラウンドトリップ時間のチェック\)** をクリックして、選択した接続モニターのメトリック ページにリダイレクトします。

右側の  **[アラート]**   グリッドには、すべてのサブスクリプションにわたって構成された接続テストに対して生成されたすべてのアラートのビューが表示されます。 アラート数をクリックすると、アラートの詳細に関するページに移動します。

## <a name="traffic"></a><a name="traffic"></a>トラフィック
[トラフィック] タブでは、選択したサブスクリプションのセットの [NSG フロー ログ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)と [Traffic Analytics](../../network-watcher/traffic-analytics.md) に対して構成され、場所ごとにグループ化されたすべての NSG にアクセスできます。 このタブで提供される検索機能を使用すると、検索された IP アドレスに対して構成された NSG を特定できます。 環境内の任意の IP アドレスを検索できます。タイル化されたリージョン ビューには、すべての NSG と、NSG フロー ログと Traffic Analytics の構成状態が表示されます。

![Azure Monitor for Networks のトラフィック ビュー](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

いずれかのリージョン タイルをクリックすると、グリッド ビューが起動します。ここで、NSG フロー ログと Traffic Analytics を簡単に表示および構成できます。  

![Azure Monitor for Networks のトラフィック リージョン ビュー](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

グリッド ビューの各要素をクリックできます。 NSG フロー ログと Traffic Analytics の構成を編集するには、構成状態をクリックします。 選択した NSG 用に構成されたトラフィック アラートにリダイレクトするには、アラートをクリックします。 同様に、ワークスペースをクリックすると、Traffic Analytics ビューに移動できます。  

右側の  **[アラート]**   グリッドには、すべてのサブスクリプションにわたるすべての Traffic Analytics ワークスペース ベースのアラートのビューが表示されます。 アラート数をクリックすると、アラートの詳細に関するページに移動します。

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> 診断ツールキット
診断ツールキットからは、ネットワークのトラブルシューティングに使用できるすべての診断機能にアクセスできます。 このドロップダウンから、[[パケット キャプチャ]](../../network-watcher/network-watcher-packet-capture-overview.md)、[[VPN のトラブルシューティング]](../../network-watcher/network-watcher-troubleshoot-overview.md)、[[接続のトラブルシューティング]](../../network-watcher/network-watcher-connectivity-overview.md)、[[次ホップ]](../../network-watcher/network-watcher-next-hop-overview.md)、[[IP フローの確認]](../../network-watcher/network-watcher-ip-flow-verify-overview.md) などの機能にアクセスできます。

![[診断ツールキット] タブ](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="next-steps"></a>次のステップ

- ネットワーク監視の詳細については、「[Azure Network Watcher とは](../../network-watcher/network-watcher-monitoring-overview.md)」を参照してください。
