---
title: Azure Monitor for VMs (プレビュー) についてよく寄せられる質問 | Microsoft Docs
description: Azure Monitor for VMs (プレビュー) は、Azure VM オペレーティング システムの正常性とパフォーマンスの監視が組み合わされた Azure のソリューションです。 これによりアプリケーション コンポーネント、および他のリソースとの依存関係が自動的に検出され、その間の通信がマップされます。 この記事では、よく寄せられる質問に回答します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184393"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>Azure Monitor for VMs (プレビュー) に関する FAQ
この記事では、Azure Monitor for VMs についてよく寄せられる質問に回答します。 このソリューションについてほかに質問がある場合は、[Azure ディスカッション フォーラム](https://feedback.azure.com/forums/34192--general-feedback)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>VM を既存のワークスペースにデプロイできますか?
仮想マシンが Log Analytics ワークスペースに既に接続されている場合、その仮想マシンを Azure Monitor for VMs にデプロイするときに、そのワークスペースを引き続き使用できます。 ワークスペースは、[Azure Monitor for VMs のデプロイ (プレビュー)](vminsights-onboard.md#prerequisites) に関するページの「前提条件」に示されている、サポート対象リージョンのいずれかに存在する必要があります。

デプロイ中、ワークスペース用にパフォーマンス カウンターが構成されます。 この操作により、Azure Monitor for VMs で表示および分析するために、ワークスペースにデータを報告する VM で情報の収集が開始されます。 これにより、選択したワークスペースに接続されているすべての VM のパフォーマンス データを確認できます。 正常性機能とマップ機能は、デプロイの対象として指定した VM に対してのみ有効になります。

どのパフォーマンス カウンターが有効になっているかの詳細については、[Azure Monitor for VMs のデプロイ (プレビュー)](vminsights-onboard.md) に関するページをご覧ください。

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>新しいワークスペースに VM をデプロイできますか? 
現在、VM が既存の Log Analytics ワークスペースに接続されていない場合は、データを保存するために新しいワークスペースを作成する必要があります。 新しいワークスペースが自動的に作成されるようにするには、Azure portal で Azure Monitor for VMs 用に単一の VM を構成します。

スクリプトベースの方法を使用する場合は、[Azure Monitor for VMs のデプロイ (プレビュー)](vminsights-onboard.md) に関するページをご覧ください。 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>VM が既に既存のワークスペースに報告している場合はどのようにすればよいですか?
仮想マシンから既にデータを収集している場合、既存の Log Analytics ワークスペースにデータを報告するように仮想マシンを構成済みである可能性があります。 そのワークスペースがサポートされているリージョンのいずれかにあれば、その既存のワークスペースに対して Azure Monitor for VMs を有効にすることができます。 Microsoft では、その他のリージョンのサポートに積極的に取り組んでいます。

>[!NOTE]
>Azure Monitor for VMs へのデプロイを選択したかどうかに関係なく、ワークスペースのパフォーマンス カウンターが構成されます。これのカウンターは、ワークスペースに報告するすべての VM に影響を及ぼします。 ワークスペース用にパフォーマンス カウンターを構成する方法の詳細については、「[Log Analytics での Windows および Linux のパフォーマンス データ ソース](../../azure-monitor/platform/data-sources-performance-counters.md)」の「パフォーマンス カウンターの構成」を参照してください。 Azure Monitor for VMs 用に構成されるカウンターについては、[Azure Monitor for VMs のデプロイ (プレビュー)](vminsights-onboard.md) に関するページをご覧ください。 

## <a name="why-did-my-vm-deployment-fail"></a>VM のデプロイに失敗したのはなぜですか?
Azure portal から Azure VM をデプロイするとき、次のイベントが発生します。

* 既定の Log Analytics ワークスペースが作成されます (該当するオプションが選択されている場合)。
* 選択したワークスペースに対してパフォーマンス カウンターが構成されます。 この手順が失敗すると、一部のパフォーマンス グラフやテーブルに、デプロイした VM のデータが表示されません。 この問題を修正するには、[Azure Monitor for VMs のデプロイ (プレビュー)](vminsights-onboard.md#enable-with-powershell) に関するページの「PowerShell を使用した有効化」に記載されている PowerShell スクリプトを実行してください。
* Log Analytics エージェントは、必要に応じて、VM 拡張機能を使用して Azure VM にインストールされます。 
* Azure Monitor for VMs Map Dependency エージェントは、必要に応じて、拡張機能を使用して Azure VM にインストールされます。 
* 正常性機能をサポートする Azure Monitor コンポーネントが必要に応じて構成され、正常性データを報告するように VM が構成されます。

デプロイ中、上記の各手順で状態がチェックされ、ポータルで通知の状態が返されます。 ワークスペースとエージェントのインストールの構成には、通常 5 から 10 分かかります。 監視データと正常性データが Azure portal に表示されるまでに、さらに 5 から 10 分かかります。 

デプロイを開始したときに、VM をデプロイする必要があることを示すメッセージが表示された場合は、VM がプロセスを完了するまで最大 30 分余裕をとってください。 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>パフォーマンス グラフに VM のデータが表示されません
ディスク テーブルまたはパフォーマンス グラフにパフォーマンス データが表示されない場合、ワークスペースでご自身のパフォーマンス カウンターが構成されていない可能性があります。 この問題を解決するには、[Azure Monitor for VMs のデプロイ (プレビュー)](vminsights-onboard.md#enable-with-powershell) に関するページの「PowerShell を使用した有効化」に記載されている PowerShell スクリプトを実行してください。

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Azure Monitor for VMs のマップ機能は Service Map とどのように異なるのですか?
Azure Monitor for VMs のマップ機能は Service Map に基づいていますが、次の点が異なります。

* マップ ビューには、VM ウィンドウから、および Azure Monitor の Azure Monitor for VMs からアクセスできます。
* マップ内の接続がクリック可能になっており、サイド パネルに接続メトリック データが表示されます。
* より複雑なマップのサポートを強化するために、新しい API を使用してマップが作成されます。
* 監視対象の VM がクライアント グループ ノードに含まれるようになり、ドーナツ グラフに、監視対象の仮想マシンと監視対象外の仮想マシンの割合が示されます。 また、グループを展開したときに、マシンの一覧をフィルター処理することもできます。
* 監視対象の仮想マシンがサーバー ポート グループ ノードに含まれるようになり、ドーナツ グラフに、監視対象マシンと監視対象外マシンの割合が示されます。 また、グループを展開したときに、マシンの一覧をフィルター処理することもできます。
* Azure Application Insights のアプリケーション マップとの一貫性を向上させるために、マップのスタイルが更新されました。
* サイド パネルが更新されましたが、Service Map でサポートされていた Update Management、Change Tracking、Security、およびService Desk との統合はまだ含まれていません。 
* マップするグループとマシンを選択するオプションが更新され、 サブスクリプション、リソース グループ、Azure 仮想マシン スケール セット、クラウド サービスがサポートされるようになりました。
* Azure Monitor for VMs のマップ機能では、新しい Service Map コンピューター グループを作成できません。 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>パフォーマンス グラフに点線が表示されるのはなぜですか?

パフォーマンス グラフに実線ではなく点線が表示される理由はいくつかあります。
* データ収集にギャップがある可能性があります。 

* データ サンプリングの既定の設定は 60 秒ごとです。 グラフの時間範囲を狭めた場合、ご自身のサンプリング頻度が、グラフで使用されるバケット サイズよりも少ないと、点線が表示されることがあります。 たとえば、サンプリング頻度として 10 分を選択したとします。グラフの各バケットは 5 分です。 この場合、表示する時間範囲を広げると、グラフの線が点線ではなく実線で表示されます。

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Azure Monitor for VMs でグループはサポートされていますか?
はい。Dependency エージェントをインストールした後、VM から情報が収集され、サブスクリプション、リソース グループ、仮想マシン スケール セット、およびクラウド サービスに基づいてグループが表示されます。 これらのグループは、Service Map を使用して、マシン グループを作成した場合も表示されます。 表示中のワークスペースでコンピューター グループを作成している場合は、それらもグループ フィルターに表示されます。 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>集計パフォーマンス グラフで 95 百分位線を引き上げているものについて詳細を表示するにはどのようにすればよいですか?
既定では、選択されたメトリックで 95 パーセンタイルの最大値を持つ VM を示すためにリストが並べ替えられます。 ただし、5 パーセンタイルの最小値を持つマシンが表示される、**使用可能なメモリ**のグラフを除きます。 グラフを選択すると、適切なメトリックが選択された**上位 N のリスト** ビューが開きます。

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>マップ機能では、さまざまな仮想ネットワークおよびサブネット間で重複する IP はどのように処理されますか?
サブネットおよび仮想ネットワーク間で VM または Azure 仮想マシン スケール セットを使用することで、IP 範囲が重複する場合、Azure Monitor for VMs のマップ機能によって間違った情報が表示されることがあります。 Microsoft はこの問題を認識しており、このエクスペリエンスを改善するためのオプションを検討中です。

## <a name="does-the-map-feature-support-ipv6"></a>マップ機能で IPv6 はサポートされていますか?
現在、マップ機能では IPv4 のみがサポートされています。IPv6 のサポートは検討中です。 IPv6 内でトンネリングされる IPv4 もサポートされています。

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>リソース グループや他の大規模なグループのマップを読み込んだときに、マップを表示しづらくなるのは、なぜですか?
大規模で複雑な構成に対応できるようにマップ機能を改善しましたが、マップに多数のノード、接続、クラスターとして機能するノードが含まれている場合があることがわかりました。 Microsoft では、スケーラビリティを向上させるサポートの継続的な強化に取り組んでいます。  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>[パフォーマンス] タブのネットワーク グラフと Azure VM の [概要] ページのネットワーク グラフが異なるのはなぜですか?

Azure VM の [概要] ページには、ゲスト VM でのアクティビティのホストの測定に基づいてグラフが表示されます。 Azure VM の [概要] ページのネットワーク グラフには、課金対象となるネットワーク トラフィックのみが表示されます。 これには、仮想ネットワーク間のトラフィックは含まれません。 Azure Monitor for VMs に表示されるデータとグラフは、ゲスト VM のデータに基づいており、ネットワーク グラフには、仮想ネットワークのトラフィックを含め、その VM に対する受信および送信のすべての TCP/IP トラフィックが表示されます。

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Log Analytics の無料プランにはどのような制限はありますか?
"*無料*" の価格レベルを使用して、Log Analytics ワークスペースで Azure Monitor を構成した場合、Azure Monitor for VMs のマップ機能では、ワークスペースに接続できるマシンが 5 台に制限されます。 

たとえば、無料のワークスペースに 5 台の VM が接続されているとします。 1 台の VM を切断した後に新しい VM を接続すると、新しい VM は監視されず、[マップ] ページにも反映されません。 このシナリオで新しい VM を開くと、既に VM にインストール済みであっても **[今すぐ試す]** オプションを使用して、左側のウィンドウから **[Insights (プレビュー)]** を選択するように求められます。 ただし、VM が Azure Monitor for VMs にデプロイされていない場合は、通常どおり、オプションは表示されません。 

## <a name="next-steps"></a>次の手順
お使いの仮想マシンの監視を有効にするための要件と方法については、[Azure Monitor for VMs のデプロイ (プレビュー)](vminsights-onboard.md) に関するページをご覧ください。
