---
title: Azure Monitor Network Insights
description: デプロイされたすべてのネットワーク リソースの正常性とメトリックを構成なしで包括的に把握できる、Azure Monitor Network Insights の概要。
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: f401c143f1f5a9352b56b80f9e473bd7fa819245
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968256"
---
# <a name="azure-monitor-network-insights"></a>Azure Monitor Network Insights

Azure Monitor Network Insights を使用すると、デプロイされたすべてのネットワーク リソースの[正常性](../../service-health/resource-health-checks-resource-types.md)と[メトリック](../essentials/metrics-supported.md)を構成なしで包括的に把握できます。 また、[接続モニター](../../network-watcher/connection-monitor-overview.md)、[ネットワーク セキュリティ グループ (NSG) のフロー ログ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)、および [Traffic Analytics](../../network-watcher/traffic-analytics.md) などのネットワーク監視機能にアクセスすることもできます。 さらには、他のネットワーク[診断](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics)機能を提供します。

Azure Monitor Network Insights は、監視のこれらの主要コンポーネントを中心に構成されています。
- [ネットワークの正常性とメトリック](#networkhealth)
- [接続](#connectivity)
- [トラフィック](#traffic)
- [診断ツールキット](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>ネットワークの正常性とメトリック

Azure Monitor Network Insights の **[概要]** ページでは、ネットワーク リソースのインベントリを、リソースの正常性やアラートと共に簡単に視覚化できます。 これは、検索とフィルター処理、リソースの正常性とメトリック、アラート、および依存関係ビューの 4 つの主要な機能領域に分かれています。

[![[概要] ページを示すスクリーンショット](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>検索とフィルター処理
リソースの正常性とアラートのビューは、 **[サブスクリプション]** 、 **[リソース グループ]** 、および **[種類]** などのフィルターを使用してカスタマイズできます。

検索ボックスを使用して、リソースとそれに関連付けられているリソースを検索できます。 たとえば、パブリック IP がアプリケーション ゲートウェイに関連付けられているとします。 パブリック IP の DNS 名を検索すると、パブリック IP と、関連付けられているアプリケーション ゲートウェイの両方が返されます。

[![Azure Monitor Network Insights の検索結果を示すスクリーンショット。](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>リソースの正常性とメトリック
次の例の各タイルは、リソースの種類を表しています。 タイルには、選択したすべてのサブスクリプションにデプロイされた、特定のリソースの種類のインスタンスの数が表示されます。 また、リソースの正常性状態も表示されます。 この例では、デプロイされている 105 個の ER および VPN 接続があります。 103 個は正常で、2 個は使用不可です。

![Azure Monitor Network Insights のリソースの正常性とメトリックを示すスクリーンショット。](media/network-insights-overview/resource-health.png)

利用不可の ER および VPN 接続を選択すると、メトリック ビューが表示されます。 

![Azure Monitor Network Insights のメトリック ビューを示すスクリーンショット。](media/network-insights-overview/metric-view.png)

グリッド ビューでは、任意の項目を選択できます。 **[正常性]** 列のアイコンを選択して、その接続のリソースの正常性を取得します。 **[アラート]** 列の値を選択して、接続のアラートとメトリックのページにアクセスします。 

### <a name="alerts"></a>警告
ページの右側にある **[アラート]** ボックスには、すべてのサブスクリプションで選択されたリソースに対して生成されたすべてのアラートのビューが表示されます。 アラートの数を選択して、アラートの詳細ページにアクセスします。

### <a name="dependency-view"></a>[依存関係] ビュー
依存関係ビューは、リソースがどのように構成されているかを視覚化するのに役立ちます。 現在、依存関係ビューは、Azure Application Gateway、Azure Virtual WAN、および Azure Load Balancer で使用できます。 たとえば、Application Gateway の場合、メトリックのグリッド ビューで Application Gateway リソース名を選択すると、依存関係ビューにアクセスできます。 Virtual WAN と Load Balancer についても同様に行うことができます。

![Azure Monitor Network Insights の Application Gateway ビューを示すスクリーンショット。](media/network-insights-overview/application-gateway.png)

Application Gateway の依存関係ビューには、フロントエンド IP がリスナー、ルール、バックエンド プールにどのように接続されているかが、簡略化されたビューで示されます。 接続している線は色分けされ、バックエンド プールの正常性に基づいて追加の詳細が表示されます。 このビューには、Application Gateway メトリックと、仮想マシン スケール セットと VM インスタンスなどのすべての関連するバックエンド プールのメトリックの詳しいビューも表示されます。

[![Azure Monitor Network Insights の依存関係ビューを示すスクリーンショット。](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

依存関係グラフを使用すると、構成設定に簡単に移動できます。 他の情報にアクセスするには、バックエンド プールを右クリックします。 たとえば、バックエンド プールが VM である場合、VM Insights と Azure Network Watcher 接続のトラブルシューティングに直接アクセスして、接続の問題を特定できます。

![Azure Monitor Network Insights の依存関係ビュー メニューを示すスクリーンショット。](media/network-insights-overview/dependency-view-menu.png)

依存関係ビューの検索とフィルター バーを使用すると、グラフ全体を簡単に検索することができます。 たとえば、前の例で **AppGWTestRule** を検索した場合、ビューは AppGWTestRule を介して接続されているすべてのノードにスケールダウンします。

![Azure Monitor Network Insights の検索の例を示すスクリーンショット。](media/network-insights-overview/search-example.png)

さまざまなフィルターを使用して、特定のパスと状態にスケールダウンすることができます。 たとえば、状態が異常なすべてのエッジを表示するには、 **[正常性状態]** の一覧から **[異常]** のみを選択します。

**[詳細なメトリックを表示する]** を選択すると、アプリケーション ゲートウェイ、すべてのバックエンド プール リソース、フロントエンド IP に関する詳しいメトリックが含まれる、事前構成されたブックを開くことができます。 

## <a name="connectivity"></a><a name="connectivity"></a>接続

**[接続]** タブを使用すると、選択したサブスクリプションのセットについて、[接続モニター](../../network-watcher/connection-monitor-overview.md)と接続モニター (クラシック) を使用して構成されたすべてのテストを簡単に視覚化できます。

![Azure Monitor Network Insights の [接続] タブを示すスクリーンショット。](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

テストは **[ソース]** と **[ターゲット]** のタイルごとにグループ化され、各テストの到達可能性の状態が表示されます。 [到達可能] の設定を使用すると、チェックの失敗率 (%) と RTT (ミリ秒) に基づいた到達可能性条件の構成に簡単にアクセスできます。 値を設定すると、選択条件に基づいて各テストの状態が更新されます。

[![Azure Monitor Network Insights の接続テストを示すスクリーンショット。](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

任意のソースまたはターゲットのタイルを選択して、メトリック ビューを開くことができます。

[![Azure Monitor Network Insights の接続メトリックを示すスクリーンショット。](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


グリッド ビューでは、任意の項目を選択できます。 **[Reachability]\(到達可能性\)** 列のアイコンを選択して [接続モニター] ポータル ページに移動すると、特定された問題に影響を与えるホップごとのトポロジと接続が表示されます。 **[アラート]** 列の値を選択して、アラートにアクセスします。 **[チェック失敗の割合]** および **[ラウンド トリップ時間 (ミリ秒)]** 列のグラフを選択して、選択した接続モニターの [メトリック] ページにアクセスします。

ページの右側にある  **[アラート]** ボックスには、すべてのサブスクリプションにわたって構成された接続テストに対して生成されたすべてのアラートのビューが表示されます。 アラートの数を選択して、アラートの詳細ページにアクセスします。

## <a name="traffic"></a><a name="traffic"></a>トラフィック
**[トラフィック]** タブでは、選択したサブスクリプションのセットの [NSG フロー ログ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)と [Traffic Analytics](../../network-watcher/traffic-analytics.md) に対して構成され、場所ごとにグループ化されたすべての NSG にアクセスできます。 このタブで利用できる検索機能を使用すると、ユーザーは検索した IP アドレスに対して構成された NSG を特定できます。 環境内の任意の IP アドレスを検索できます。 並べて表示されているリージョン ビューには、すべての NSG と、NSG フロー ログと Traffic Analytics の構成状態が表示されます。

[![Azure Monitor Network Insights の [トラフィック] タブを示すスクリーンショット。](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

いずれかのリージョン タイルを選択すると、グリッド ビューが表示されます。 このグリッドによって、NSG フロー ログと Traffic Analytics が見やすく構成しやすいビューで表示されます。  

[![Azure Monitor Network Insights のトラフィックのリージョン ビューを示すスクリーンショット。](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

グリッド ビューでは、任意の項目を選択できます。 **[Flowlog Configuration Status]\(フローログの構成状態\)** 列にあるアイコンを選択して、NSG フロー ログと Traffic Analytics 構成を編集します。 **[アラート]** 列の値を選択して、選択した NSG 用に構成されているトラフィック アラートにアクセスします。 同様に、 **[Traffic Analytics ワークスペース]** を選択して Traffic Analytics ビューにアクセスできます。  

ページの右側にある  **[アラート]** ボックスには、すべてのサブスクリプションにわたるすべての Traffic Analytics ワークスペース ベースのアラートのビューが表示されます。 アラートの数を選択して、アラートの詳細ページにアクセスします。

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> 診断ツールキット
診断ツールキットからは、ネットワークのトラブルシューティングに使用できるすべての診断機能にアクセスできます。 このドロップダウン リストを使用して、[[パケット キャプチャ]](../../network-watcher/network-watcher-packet-capture-overview.md)、[[VPN トラブルシューティング]](../../network-watcher/network-watcher-troubleshoot-overview.md)、[[接続のトラブルシューティング]](../../network-watcher/network-watcher-connectivity-overview.md)、[[次ホップ]](../../network-watcher/network-watcher-next-hop-overview.md)、[[IP フロー検証]](../../network-watcher/network-watcher-ip-flow-verify-overview.md) などの機能にアクセスできます。

![[診断ツールキット] タブを示すスクリーンショット。](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="onboarded-resources"></a>オンボード リソース 

オンボード リソースには、組み込みのブックと依存関係ビューがあります。 現在、オンボード リソースは、Virtual WAN、Application Gateway、Load Balancer、ExpressRoute です。

## <a name="troubleshooting"></a>トラブルシューティング 
一般的なトラブルシューティングのガイダンスについては、専用のブックベースの分析情報の[トラブルシューティングに関する記事](troubleshoot-workbooks.md)を参照してください。
このセクションでは、Azure Monitor Network Insights を使用するときに発生する可能性のある一般的な問題の診断とトラブルシューティングについて説明します。 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>パフォーマンスの問題や障害を解決するにはどうすればよいですか?

Azure Monitor Network Insights でネットワーク関連の問題が見つかったとき、それを解決するには、不具合のあるリソース用のトラブルシューティング ドキュメントを参照してください。 

よく使用されるサービスのトラブルシューティングの記事へのリンクを次に示します。 これらのサービスに関するその他のトラブルシューティングの記事については、サービスの目次にある「トラブルシューティング」セクションの他の記事を参照してください。
* [Azure Virtual Network](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Azure Application Gateway](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure Load Balancer](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>選択したサブスクリプションの一部のリソースが表示されないのはなぜですか?

Azure Monitor Network Insights では、一度に 5 つのサブスクリプションのリソースのみを表示できます。 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-network-insights"></a>Azure Monitor Network Insights に変更を加えたり、視覚化を追加したりするにはどうすればよいですか?

変更するには、 **[編集モード]** を選択してブックを変更します。 その後、指定したサブスクリプションとリソース グループに紐づけられている新しいブックとして、変更内容を保存できます。

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>ブックの一部をピン留めした後の時間グレインはどうなりますか?

Azure Monitor Network Insights では **[自動]** 時間グレインが使用されるため、時間グレインは選択した時間範囲によって異なります。

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>ブックの一部分をピン留めしたときの時間範囲はどうなりますか?

時間範囲は、ダッシュボードの設定によって異なります。

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-network-insights"></a>他のデータを表示したり、独自の視覚化を作成したりするにはどうすればよいですか。 Azure Monitor Network Insights に変更を加えるにはどうすればよいですか?

編集モードを使用すると、サイドパネルまたは詳細なメトリック ビューに表示されるブックを編集できます。 その後、変更内容を新しいブックとして保存できます。

## <a name="next-steps"></a>次の手順

- ネットワークの監視に関する詳細情報:[Azure Network Watcher とは](../../network-watcher/network-watcher-monitoring-overview.md)
- ブックでサポートされるように設計されているシナリオ、レポートの作成方法、および既存のレポートのカスタマイズ方法などについて説明します。[Azure Monitor ブックを使用した対話型レポートの作成](../visualize/workbooks-overview.md)
