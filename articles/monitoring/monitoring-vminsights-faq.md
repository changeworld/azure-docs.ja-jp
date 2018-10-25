---
title: VM 用 Azure Monitor についてよく寄せられる質問 | Microsoft Docs
description: VM 用 Azure Monitor は、Azure VM オペレーティング システムの正常性およびパフォーマンスの監視と、アプリケーション コンポーネントおよび他のリソースとの依存関係の自動検出を組み合わせ、それらの間の通信をマップする Azure のソリューションです。 この記事ではよくある質問の回答を示します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2018
ms.author: magoedte
ms.openlocfilehash: 282620342d2348868ceab5257de7415a9cb2147c
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388361"
---
# <a name="azure-monitor-for-vms-frequently-asked-questions"></a>VM 用 Azure Monitor についてよく寄せられる質問
この Microsoft FAQ では、Microsoft Azure の VM 用 Azure Monitor についてよく寄せられる質問を紹介します。 このソリューションについてほかに質問がある場合は、[ディスカッション フォーラム](https://feedback.azure.com/forums/34192--general-feedback)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="can-i-onboard-to-an-existing-workspace"></a>既存のワークスペースにオンボードすることはできますか?
仮想マシンが Log Analytics ワークスペースに既に接続されている場合、ワークスペースが[こちら](monitoring-vminsights-onboard.md#prerequisites)に記載されたサポートされているリージョンのいずれかにあれば、VM 用 Azure Monitor にオンボードするときにそのワークスペースを引き続き使用できます。

オンボード時に、ワークスペースのパフォーマンス カウンターが構成され、VM 用 Azure Monitor で表示および分析するために、ワークスペースにデータを報告するすべての VM で この情報の収集が開始されます。  これにより、選択したワークスペースに接続されているすべての VM のパフォーマンス データを確認できます。  正常性機能とマップ機能は、オンボードの対象として指定した VM に対してのみ有効になります。

有効化されるパフォーマンス カウンターの詳細については、[オンボード](monitoring-vminsights-onboard.md)に関する記事をご覧ください。

## <a name="can-i-onboard-to-a-new-workspace"></a>新しいワークスペースにオンボードすることはできますか? 
現在、VM が既存の Log Analytics ワークスペースに接続されていない場合は、データを保存するために新しいワークスペースを作成する必要があります。  Azure portal を使用して VM 用 Azure Monitor で単一の Azure VM を構成すると、新しい既定のワークスペースが自動的に作成されます。

スクリプトベースの方法を使用する場合は、[オンボード](monitoring-vminsights-onboard.md)に関する記事に手順が記載されています。 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>VM が既に既存のワークスペースに報告している場合はどうすればよいですか?
仮想マシンから既にデータを収集している場合、既存の Log Analytics ワークスペースにデータを報告するように仮想マシンを構成済みである可能性があります。  そのワークスペースがサポートされているリージョンのいずれかにあれば、その既存のワークスペースに対して VM 用 Azure Monitor を有効にすることができます。  既に使用しているワークスペースがサポートされているリージョンにない場合、現時点では VM 用 Azure Monitor にオンボードすることはできません。  Microsoft では、その他のリージョンのサポートに積極的に取り組んでいます。

>[!NOTE]
>VM 用 Azure Monitor へのオンボードを選択したかどうかに関係なく、ワークスペースのパフォーマンス カウンターが構成されます。これらは、ワークスペースに報告するすべての VM に影響を及ぼします。 ワークスペースのパフォーマンス カウンターの構成の詳細については、こちらの[ドキュメント](../log-analytics/log-analytics-data-sources-performance-counters.md)をご覧ください。 VM 用 Azure Monitor 用に構成されるカウンターについては、[オンボードに関するドキュメント](monitoring-vminsights-onboard.md#performance-counters-enabled)をご覧ください。  

## <a name="why-did-my-vm-fail-to-onboard"></a>VM のオンボードに失敗したのはなぜですか?
Azure portal から Azure VM をオンボードすると、次の手順が実行されます。

* 既定の Log Analytics ワークスペースが作成されます (該当するオプションが選択されている場合)。
* 選択したワークスペースのパフォーマンス カウンターが構成されます。 この手順が失敗すると、一部のパフォーマンス グラフやテーブルに、オンボードした VM のデータが表示されません。 これを修正するには、[こちら](monitoring-vminsights-onboard.md#enable-with-powershell)に記載された PowerShell スクリプトを実行します。
* Log Analytics エージェントが必要と判断されると、VM 拡張機能を使用して Azure VM にインストールされます。  
* VM 用 Azure Monitor Map Dependency エージェントが必要と判断されると、拡張機能を使用して Azure VM にインストールされます。  
* 必要に応じて、正常性機能をサポートする Azure Monitor コンポーネントが構成され、正常性データを報告するように VM が構成されます。

オンボード プロセス中、上記の各手順で状態がチェックされ、ポータルで通知の状態が返されます。  ワークスペースとエージェントのインストールの構成には、通常 5 から 10 分かかります。  監視データと正常性データがポータルに表示されるまでに、さらに 5 から 10 分かかります。  

オンボードを開始したときに、VM をオンボードする必要があることを示すメッセージが表示された場合は、VM がプロセスを完了するまでに最大 30 分かかります。 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>VM のパフォーマンス グラフにデータが表示されません
ディスク テーブルまたは一部のパフォーマンス グラフにパフォーマンス データが表示されない場合、ワークスペースでパフォーマンス カウンターが構成されていない可能性があります。 これを解決するには、こちらの [PowerShell スクリプト](monitoring-vminsights-onboard.md#enable-with-powershell)を実行してください。

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>VM 用 Azure Monitor のマップ機能は Service Map とどのように異なるのですか?
VM 用 Azure Monitor のマップ機能は Service Map に基づいていますが、次の点が異なります。

* マップ ビューには、VM のブレード、および [Azure Monitor] の [Azure Monitor for VMs]\(VM 用 Azure Monitor\) からアクセスできます。
* マップ内の接続がクリック可能になっており、選択した接続のサイド パネルに接続メトリック データのビューが表示されます。
* より複雑なマップのサポートを強化するために、マップの作成に使用される新しい API が用意されています。
* 監視対象の VM がクライアント グループ ノードに含まれるようになりました。グループ内の監視対象の仮想マシンと監視対象外の仮想マシンの割合がドーナツ グラフに示されます。  また、グループを展開したときに、ドーナツ グラフを使用してマシンの一覧をフィルター処理することもできます。
* 監視対象の仮想マシンがサーバー ポート グループ ノードに含まれるようになりました。グループ内の監視対象の仮想マシンと監視対象外の仮想マシンの割合がドーナツ グラフに示されます。  また、グループを展開したときに、ドーナツ グラフを使用してマシンの一覧をフィルター処理することもできます。
* Application Insights のアプリケーション マップとの一貫性を向上させるために、マップのスタイルが更新されました。
* サイド パネルが更新されましたが、Service Map でサポートされていた Update Management、Change Tracking、セキュリティ、およびサービス デスクとの統合はまだ含まれていません。 
* マップするグループとマシンを選択するオプションが更新され、サブスクリプション、リソース グループ、Azure 仮想マシン スケール セット、クラウド サービスがサポートされるようになりました。
* VM 用 Azure Monitor のマップ機能では、新しい Service Map コンピューター グループを作成することはできません。  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>パフォーマンス グラフに点線が表示されるのはなぜですか?

これはいくつかの理由で発生する可能性があります。  表示するデータ収集にギャップがある場合、点線が表示されます。  有効になっているパフォーマンス カウンターのデータ サンプリング頻度を変更したときに (既定の設定では、60 秒ごとにデータが収集されます)、グラフの時間範囲を狭め、サンプリング頻度がグラフで使用されるバケット サイズよりも少ない場合 (たとえば、サンプリング頻度が 10分 ごとで、グラフの各バケットが 5 分の場合)、グラフに点線が表示されます。  この場合、表示する時間範囲を広げると、グラフの線が点線ではなく実線で表示されます。

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>VM 用 Azure Monitor でグループはサポートされていますか?
はい、Dependency エージェントをインストールすると、VM から情報が収集され、サブスクリプション、リソース グループ、仮想マシン スケール セット、およびクラウド サービスに基づいてグループが表示されます。  Service Map を使用し、マシン グループを作成している場合も、これらが表示されます。  表示中のワークスペースでコンピューター グループを作成している場合は、それらもグループ フィルターに表示されます。 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>集計パフォーマンス グラフで 95 百分位線を引き上げているものについて詳細を確認するにはどうすればよいですか?
既定では、選択されたメトリックで 95 パーセンタイルの最大値を持つ VM を示すためにリストが並べ替えられます。ただし、5 パーセンタイルの最小値を持つマシンが表示される、使用可能なメモリのグラフを除きます。  グラフをクリックすると、適切なメトリックが選択された **[Top N List]\(上位 N のリスト\)** ビューが開きます。

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>マップ機能では、VNet 間およびサブネット間で重複する IP はどのように処理されますか?
サブネット間および VNet 間で VM または Azure 仮想マシン スケール セットの IP 範囲が重複している場合、VM 用 Azure Monitor に間違った情報が表示されることがあります。 これは既知の問題であり、このエクスペリエンスを改善するためのオプションを検討中です。

## <a name="does-map-feature-support-ipv6"></a>マップ機能で IPv6 はサポートされていますか?
現在、マップ機能では IPv4 のみがサポートされています。IPv6 のサポートは検討中です。 IPv6 内でトンネリングされる IPv4 もサポートされています。

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>リソース グループや他の大規模なグループのマップを読み込んだときに、マップを表示しづらくなります
大規模で複雑な構成に対応できるようにマップを改善しましたが、マップに多数のノード、接続、クラスターとして機能するノードが含まれている場合があることがわかりました。  Microsoft では、スケーラビリティを向上させるサポートの継続的な強化に取り組んでいます。   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>[パフォーマンス] タブのネットワーク グラフと Azure VM の概要ページのネットワーク グラフが異なるのはなぜですか?

Azure VM の概要ページには、ゲスト VM でのアクティビティのホストの測定に基づいてグラフが表示されます。  Azure VM の概要のネットワーク グラフでは、課金対象となるネットワーク トラフィックのみが表示されます。  これには、VNET 間トラフィックは含まれません。  VM 用 Azure Monitor に表示されるデータとグラフは、ゲスト VM のデータに基づいており、ネットワーク グラフには、VNET 間も含め、その VM に対する受信および送信のすべての TCP/IP トラフィックが表示されます。

## <a name="next-steps"></a>次の手順
仮想マシンの監視を有効にするための要件と方法を理解するため、「[Azure Monitor for VM の配布準備をする](monitoring-vminsights-onboard.md)」を確認します。
