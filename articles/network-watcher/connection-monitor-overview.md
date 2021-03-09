---
title: Azure の接続モニター | Microsoft Docs
description: 接続モニターを使用して分散環境でネットワーク通信を監視する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 7abaae033d2dbdb329a1f99d8f9845e5965d806c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712319"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>接続モニターによるネットワーク接続の監視

> [!IMPORTANT]
> 2021 年 7 月 1 日以降、既存のワークスペースに新しいテストを追加したり、Network Performance Monitor で新しいワークスペースを有効にしたりできなくなります。 接続モニター (クラシック) に新しい接続モニターを追加することもできなくなります。 2021 年 7 月 1 日より前に作成されたテストおよび接続モニターは引き続き使用することができます。 現在のワークロードに対するサービスの中断を最小限に抑えるには、2024 年 2 月 29 日より前に、[Network Performance Monitor から](migrate-to-connection-monitor-from-network-performance-monitor.md)、または[接続モニター (クラシック) から](migrate-to-connection-monitor-from-connection-monitor-classic.md) Azure Network Watcher の新しい接続モニターにテストを移行します。

接続モニターを使用すると、Azure Network Watcher で、統合されたエンド ツー エンドの接続監視が提供されます。 接続モニターの機能では、ハイブリッド デプロイと Azure クラウド デプロイがサポートされています。 Network Watcher には、Azure のデプロイに対する接続に関連したメトリックを監視、診断、表示するためのツールが用意されています。

以下に、接続モニターのいくつかのユース ケースを示します。

- フロントエンド Web サーバー VM と、多層アプリケーション内のデータベース サーバー VM が通信している。 2 つの VM 間のネットワーク接続を確認したい。
- 米国東部リージョンの VM から、米国中部リージョンの VM に ping を実行して、リージョン間のネットワーク待ち時間を比較したい。
- ワシントン州シアトルと、バージニア州アッシュバーンに複数のオンプレミス オフィス サイトがある。 オフィス サイトは Microsoft 365 の URL に接続している。 Microsoft 365 URL のユーザーについて、シアトルとアッシュバーンの間で待機時間を比較する。
- ハイブリッド アプリケーションで Azure Storage エンドポイントに接続する必要がある。 オンプレミスのサイトと Azure アプリケーションは、同じ Azure Storage エンドポイントに接続する。 オンプレミス サイトの待機時間と、Azure アプリケーションの待機時間を比較したい。
- オンプレミスのセットアップと、クラウド アプリケーションがホストされている Azure VM 間の接続を確認したい。

接続モニターは 2 つの機能の長所を兼ね備えています。Network Watcher の[接続モニター (クラシック)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) 機能と、Network Performance Monitor (NPM) の[サービス接続モニター](../azure-monitor/insights/network-performance-monitor-service-connectivity.md)、[ExpressRoute 監視](../expressroute/how-to-npm.md)、[パフォーマンス モニター](../azure-monitor/insights/network-performance-monitor-performance-monitor.md)機能です。

以下に、接続モニターの利点をいくつか示します。

* Azure とハイブリッドの監視のニーズに対応した、統合された直感的なエクスペリエンス
* クロス リージョン、クロス ワークスペースの接続の監視
* プローブ頻度の増加、およびネットワーク パフォーマンスの可視性の向上
* ハイブリッド デプロイでのアラートの高速化
* HTTP、TCP、ICMP に基づく接続チェックのサポート 
* Azure と Azure 以外のテストのセットアップに対するメトリックと Log Analytics のサポート

![接続モニターと Azure VM、Azure 以外のホスト、エンドポイント、データ ストレージの場所との間の対話方法を示す図](./media/connection-monitor-2-preview/hero-graphic.png)

監視のために接続モニターの使用を開始するには、次の手順に従います。 

1. 監視エージェントをインストールします。
1. サブスクリプションで Network Watcher を有効にします。
1. 接続モニターを作成します。
1. データ分析とアラートを設定します。
1. ネットワークの問題を診断します。

以降のセクションではこれらの手順についてさらに詳しく説明します。

## <a name="install-monitoring-agents"></a>監視エージェントをインストールする

接続モニターでは、軽量の実行可能ファイルを使用して接続チェックが実行されます。 Azure 環境とオンプレミス環境両方からの接続チェックがサポートされています。 使用する実行可能ファイルは、VM が Azure とオンプレミスのどちらでホストされているかによって異なります。

### <a name="agents-for-azure-virtual-machines"></a>Azure 仮想マシンのエージェント

接続モニターにより Azure VM が監視ソースとして認識されるようにするには、Network Watcher エージェント仮想マシン拡張機能を VM にインストールします。 この拡張機能は、"*Network Watcher 拡張機能*" とも呼ばれます。 Azure 仮想マシンでは、エンドツーエンドの監視とその他の高度な機能をトリガーするために拡張機能が必要です。 

[VM を作成する](./connection-monitor.md#create-the-first-vm)ときに、Network Watcher 拡張機能をインストールできます。 また、[Linux](../virtual-machines/extensions/network-watcher-linux.md) 用と [Windows](../virtual-machines/extensions/network-watcher-windows.md) 用の Network Watcher 拡張機能を別々に、インストール、構成、トラブルシューティングすることもできます。

ネットワーク セキュリティ グループ (NSG) またはファイアウォールの規則により、ソースとターゲットの間の通信がブロックされる場合があります。 接続モニターを使うと、この問題を検出し、トポロジに診断メッセージとして表示できます。 接続監視を有効にするには、NSG とファイアウォール規則で、ソースとターゲットの間の TCP または ICMP 経由のパケットが許可されるようにします。

### <a name="agents-for-on-premises-machines"></a>オンプレミス コンピューター用のエージェント

接続モニターで、オンプレミスのコンピューターが監視のソースとして認識されるようにするには、コンピューターに Log Analytics エージェントをインストールします。  その後、Network Performance Monitor ソリューションを有効にします。 これらのエージェントは Log Analytics ワークスペースにリンクされているので、監視を開始するには、ワークスペース ID とプライマリ キーをセットアップする必要があります。

Windows コンピューター用の Log Analytics エージェントをインストールする方法については、「[Windows 用の Azure Monitor 仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md)」を参照してください。

パスにファイアウォールまたはネットワーク仮想アプライアンス (NVA) が含まれている場合は、ターゲットに到達可能であることを確認します。

Windows マシンでポートを開くには、[EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell スクリプトを、管理者特権の PowerShell ウィンドウでパラメーターを指定せずに実行します。

Linux マシンの場合、使用する portNumber を手動で変更する必要があります。 
* パス (/var/opt/microsoft/omsagent/npm_state) に移動します。 
* ファイル (npmdregistry) を開きます
* ポート番号 ```“PortNumber:<port of your choice>”``` の値を変更します

 使用するポート番号は、ワークスペースで使用されているすべてのエージェントで同じである必要があることに注意してください。 

このスクリプトは、ソリューションで必要なレジストリ キーを作成します。 エージェントが互いに TCP 接続を作成することを許可する Windows ファイアウォール規則も作成されます。 スクリプトによって作成されたレジストリ キーは、デバッグ ログとログ ファイルのパスを記録するかどうかを指定します。 このスクリプトは、また、通信で使われるエージェント TCP ポートを定義します。 これらのキーの値は、スクリプトによって自動的に設定されます。 これらのキーを手動で変更しないでください。 既定で開かれるポートは 8084 です。 パラメーター "portNumber" をスクリプトに指定することでカスタム ポートを使用できます。 スクリプトが実行されるすべてのコンピューターで同じポートを使います。 Log Analytics エージェントのネットワーク要件の[詳細情報](../azure-monitor/agents/log-analytics-agent.md#network-requirements)を参照してください。

このスクリプトでは、Windows ファイアウォールがローカルでのみ構成されます。 ネットワーク ファイアウォールがある場合、ネットワーク パフォーマンス モニターによって使われている TCP ポート宛てのトラフィックを許可する必要があります。

## <a name="enable-network-watcher-on-your-subscription"></a>サブスクリプションで Network Watcher を有効にする

仮想ネットワークが含まれるすべてのサブスクリプションは、Network Watcher で有効になります。 サブスクリプションで仮想ネットワークを作成すると、その仮想ネットワークのリージョンとサブスクリプションで、Network Watcher が自動的に有効になります。 この自動有効化により、リソースが影響を受けたり、料金が発生したりすることはありません。 Network Watcher がサブスクリプションで明示的に無効になっていないことを確認してください。 

詳細については、[Network Watcher の有効化](./network-watcher-create.md)に関する記事をご覧ください。

## <a name="create-a-connection-monitor"></a>接続モニターを作成する 

接続モニターでは、一定の間隔で通信が監視されます。 それにより、到達可能性と待機時間の変化が通知されます。 また、ソース エージェントとターゲット エンドポイントの間の現在および過去のネットワーク トポロジを確認することもできます。

監視エージェントがインストールされている Azure VM またはオンプレミス コンピューターを、ソースにすることができます。 ターゲット エンドポイントとしては、Microsoft 365 URL、Dynamics 365 URL、カスタム URL、Azure VM リソース ID、IPv4、IPv6、FQDN、または任意のドメイン名を使用できます。

### <a name="access-connection-monitor"></a>接続モニターにアクセスする

1. Azure portal のホーム ページで、 **[Network Watcher]** に移動します。
1. 左側の **[監視]** セクションで、 **[接続モニター]** を選択します。
1. 接続モニターで作成されたすべての接続モニターが表示されます。 接続モニターの従来のエクスペリエンスで作成された接続モニターを表示するには、 **[接続モニター]** タブに移動します。
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="接続モニターで作成された接続モニターを示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>接続モニターを作成する

接続モニター (プレビュー) で作成できる接続モニターでは、オンプレミスのマシンと Azure VM の両方をソースとして追加できます。 これらの接続モニターでは、エンドポイントへの接続も監視できます。 エンドポイントは、Azure または他の任意の URL や IP 上のどこにあってもかまいません。

接続モニターには、次のエンティティが含まれています。

* **接続モニター リソース** – リージョン固有の Azure リソース。 以下のすべてのエンティティが、接続モニター リソースのプロパティです。
* **エンドポイント** –接続チェックに含まれるソースまたはターゲット。 エンドポイントの例としては、Azure VM、オンプレミスのエージェント、URL、IP などがあります。
* **テスト構成** – テスト用のプロトコル固有の構成。 選択したプロトコルに基づいて、ポート、しきい値、テストの頻度、その他のパラメーターを定義できます。
* **テスト グループ** – ソース エンドポイント、ターゲット エンドポイント、テスト構成が含まれるグループ。 接続モニターには、複数のテスト グループを含めることができます。
* **テスト** – ソース エンドポイント、ターゲット エンドポイント、テスト構成の組み合わせ。 テストは、監視データを入手できる最も小さいレベルです。 監視データには、失敗したチェックの割合と、ラウンドトリップ時間 (RTT) が含まれます。

 ![テスト グループとテストの関係が定義されている接続モニターを示す図](./media/connection-monitor-2-preview/cm-tg-2.png)

[Azure portal](./connection-monitor-create-using-portal.md)、[ARMClient](./connection-monitor-create-using-template.md)、または [PowerShell](connection-monitor-create-using-powershell.md) を使用して、接続モニターを作成できます

テスト グループに追加したすべてのソース、ターゲット、テスト構成は、個々のテストに分割されます。 ソースとターゲットの分割方法の例を次に示します。

* テスト グループ: TG1
* ソース: 3 (A、B、C)
* 宛先: 2 (D、E)
* テスト構成: 2 (Config 1、Config 2)
* 作成される総テスト数: 12

| テスト番号 | ソース | 到着地 | テスト構成 |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>スケールの上限

接続モニターには、次のようなスケール制限があります。

* 1 つのリージョンの 1 つのサブスクリプションあたりの接続モニターの最大数: 100
* 接続モニターあたりのテスト グループの最大数: 20
* 接続モニターあたりのソースとターゲットの最大数: 100
* 接続モニターあたりのテスト構成の最大数:

## <a name="analyze-monitoring-data-and-set-alerts"></a>監視データを分析してアラートを設定する

接続モニターを作成すると、ソースにより、テスト構成に基づいてターゲットへの接続のチェックが行われます。

### <a name="checks-in-a-test"></a>テストでのチェック

テスト構成で選択したプロトコルに基づいて、接続モニターで、ソースとターゲットのペアに対して一連のチェックが実行されます。 チェックは、選択したテストの頻度に従って実行されます。

HTTP を使用した場合、サービスにより、有効な応答コードを返した HTTP 応答の数が計算されます。 有効な応答コードは、PowerShell と CLI を使用して設定できます。 その結果によって、失敗したチェックの割合が判定されます。 RTT を計算するため、サービスでは HTTP の呼び出しと応答の間の時間が測定されます。

TCP または ICMP を使用した場合、サービスでは、パケット損失率を計算して、失敗したチェックの割合が決定されます。 RTT を計算するため、サービスでは、送信されたパケットに対する受信確認 (ACK) を受け取るのにかかった時間が測定されます。 ネットワーク テスト用に Traceroute データを有効にした場合は、オンプレミスのネットワークに対するホップバイホップの損失と待ち時間を確認できます。

### <a name="states-of-a-test"></a>テストの状態

チェックによって返されるデータに基づいて、テストは次のような状態になる可能性があります。

* **合格** – 失敗したチェックの割合および RTT の実際の値が、指定したしきい値を超えていません。
* **失敗** – 失敗したチェックの割合または RTT の実際の値が、指定したしきい値を超えました。 しきい値が指定されていない場合は、失敗したチェックの割合が 100 のときに、テストは失敗状態になります。
* **警告** – 
     * しきい値が指定され、接続モニターで、しきい値の 80% を超えるチェック失敗割合が観察されると、テストは警告としてマークされます。
     * しきい値が指定されていない場合は、接続モニターによって自動的にしきい値が割り当てられます。 そのしきい値を超えると、テストの状態は警告に変わります。 TCP テストまたは ICMP テストでのラウンド トリップ時間については、しきい値は 750 ミリ秒です。 チェック失敗の割合については、しきい値は 10% です。 
* **不確定**  – Log Analytics ワークスペース内にデータはありません。  メトリックをチェックします。 
* **実行されていません**  – テスト グループを無効にすることによって無効にされています。  

### <a name="data-collection-analysis-and-alerts"></a>データの収集、分析、アラート

接続モニターによって収集されたデータは、Log Analytics ワークスペースに格納されます。 このワークスペースは、接続モニターを作成したときに設定されています。 

監視データは Azure Monitor メトリックでも使用できます。 Log Analytics を使用すると、監視データを必要な限りいつまでも保持できます。 既定では、メトリックは Azure Monitor によって 30 日間だけ保管されます。 

[データに対してメトリックに基づくアラートを設定する](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)ことができます。

#### <a name="monitoring-dashboards"></a>監視ダッシュボード

監視ダッシュボードには、サブスクリプション、リージョン、タイムスタンプ、ソース、およびターゲットの種類でアクセスできる接続モニターの一覧が表示されます。

Network Watcher から接続モニターに移動したときには、以下の項目別にデータを表示できます。

* **[接続モニター]** – 自分のサブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類に対して作成されたすべての接続モニターの一覧です。 このビューが既定値です。
* **[テスト グループ]** – 自分のサブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類に対して作成されたすべてのテスト グループの一覧です。 これらのテスト グループは、接続モニターではフィルター処理されません。
* **[テスト]** – 自分のサブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類に対して実行されたすべてのテストの一覧です。 これらのテストは、接続モニターまたはテスト グループではフィルター処理されません。

次の図では、矢印 1 で 3 つのデータ ビューが示されています。

ダッシュボードでは、各接続モニターを展開して、そのテスト グループを表示できます。 その後、各テスト グループを展開して、テスト グループで実行されたテストを確認できます。 

一覧は、以下に基づいてフィルター処理できます。

* **最上位のフィルター** – テキスト、エンティティ型 (接続モニター、テスト グループ、またはテスト) のタイムスタンプおよび範囲によってリストを検索します。 範囲には、サブスクリプション、リージョン、ソース、ターゲットの種類が含まれます。 下の図のボックス 1 を参照してください。
* **状態ベースのフィルター** – 接続モニター、テスト グループ、またはテストの状態によるフィルターです。 下の図のボックス 2 を参照してください。
* **アラート ベースのフィルター** – 接続モニター リソースに対して発生するアラートによるフィルターです。 下の図のボックス 3 を参照してください。

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="接続モニターで接続モニター、テスト グループ、テストのビューをフィルター処理する方法を示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
たとえば、接続モニターでソース IP が 10.192.64.56 であるすべてのテストを確認するには:
1. ビューを **[テスト]** に変更します。
1. 検索フィールドに、「*10.192.64.56*」と入力します
1. 最上位フィルターの **[範囲]** で、 **[ソース]** を選択します。

接続モニターでソース IP が 10.192.64.56 である失敗したテストだけを表示するには:
1. ビューを **[テスト]** に変更します。
1. 状態ベースのフィルターで、 **[失敗]** を選択します。
1. 検索フィールドに、「*10.192.64.56*」と入力します
1. 最上位フィルターの **[範囲]** で、 **[ソース]** を選択します。

接続モニターでターゲットが outlook.office365.com である失敗したテストだけを表示するには:
1. ビューを **[テスト]** に変更します。
1. 状態ベースのフィルターで、 **[失敗]** を選択します。
1. 検索フィールドに、「*office.live.com*」と入力します。
1. 最上位フィルターの **[範囲]** で、 **[宛先]** を選択します。
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="ターゲット Outlook.Office365.com に対する失敗したテストのみを表示するようにフィルター処理されたビューを示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

接続モニター、テスト グループ、またはテストが失敗した理由を知るには、[reason] という名前の列をクリックします。  これにより、違反したしきい値 (チェックが失敗した割合または RTT) と関連する診断メッセージがわかります。
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="接続モニター、テスト、またはテスト グループの失敗の理由を示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
接続モニターの RTT の傾向と失敗したチェックの割合を表示するには:
1. 調査する接続モニターを選択します。

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="テスト グループ別に表示された、接続モニターのメトリックを示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. 次のセクションが表示されます。  
    1. Essentials - 選択した接続モニターのリソース固有のプロパティ。 
    1. Summary - 
        1. RTT の集計された傾向線と、接続モニターのすべてのテストに対する失敗したチェックの割合。 詳細を表示する特定の時間を設定できます。
        1. RTT または失敗したチェックの割合に基づいて、テスト グループ、ソース、宛先全体の上位 5 件が表示されます。 
    1. テスト グループ、ソース、宛先、およびテスト構成のタブ - 接続モニターのテスト グループ、ソース、または宛先の一覧が表示されます。 失敗したテストをチェックし、RTT を集計し、失敗した割合の値をチェックします。  後で戻ってデータを表示することもできます。 
    1. Issues - 接続モニターの各テストのホップ レベルの問題。 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="テスト グループ パーツ 2 別に表示された、接続モニターのメトリックを示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. そのための方法は次のとおりです。
    * [すべてのテストを表示] をクリック - 接続モニターのすべてのテストを表示します。
    * すべてのテスト グループ、テスト構成、ソースおよび宛先の表示をクリック - それぞれに特有の詳細を表示します。 
    * テスト グループ、テスト構成、ソース、または宛先を選択 - そのエンティティ内のすべてのテストを表示します。

1. [すべてのテストを表示] ビューから、次のことができます。
    * テストを選択し、比較をクリックします。
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="2 つのテストの比較を示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * クラスターを使用して、VNET、サブネットなどの複合リソースを、その子リソースに展開します。
    * トポロジをクリックし、任意のテストのトポロジを表示します。

テスト グループの RTT の傾向と失敗したチェックの割合を表示するには:
1. 調査するテスト グループを選択します。 
1. 接続モニターと類似のものが表示されます。要点、概要、テスト グループのテーブル、ソース、宛先、およびテスト構成です。 接続モニターの場合と同様に、これらの間を移動します。

テストの RTT の傾向と失敗したチェックの割合を表示するには:
1. 調査するテストを選択します。 ネットワーク トポロジと、失敗した割合とラウンド トリップ時間をチェックするためのエンド ツー エンドの傾向グラフが表示されます。 特定された問題を確認するには、トポロジでパスの任意のホップを選択します。 (これらのホップは Azure リソースです)。現在、この機能は、オンプレミスのネットワークに対しては使用できません。

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="テストのトポロジ ビューを示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Log Analytics でのログ クエリ

監視データのカスタム ビューを作成するには、Log Analytics を使用します。 UI に表示されるすべてのデータは、Log Analytics からのものです。 リポジトリ内のデータを対話形式で分析できます。 Log Analytics に基づく Agent Health または他のソリューションからのデータを関連付けます。 Excel や Power BI にデータをエクスポートするか、または共有可能なリンクを作成します。

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor のメトリック

接続モニター エクスペリエンスの前に作成された接続モニターでは、次の 4 つのメトリックすべてを使用できます: 失敗したプローブの割合、AverageRoundtripMs、ChecksFailedPercent (プレビュー)、RoundTripTimeMs (プレビュー)。 接続モニター エクスペリエンスで作成された接続モニターでは、データを使用できるのは " *(プレビュー)* " というタグが付いたメトリックについてのみです。

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="接続モニターのメトリックを示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

メトリックを使用するときは、リソースの種類を Microsoft.Network/networkWatchers/connectionMonitors に設定します

| メトリック | Display name | ユニット | 集計の種類 | 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (クラシック) | 失敗したプローブの割合 (クラシック) | パーセント | Average | 失敗した接続監視プローブの割合。 | ディメンションなし |
| AverageRoundtripMs (クラシック) | Avg.ラウンドトリップ時間 (ms) (クラシック) | ミリ秒 | Average | ソースと接続先の間で送信された接続監視プローブのネットワーク RTT。 |             ディメンションなし |
| ChecksFailedPercent | 失敗したチェックの割合 | パーセント | Average | テストで失敗したチェックの割合。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>[DestinationName] <br>DestinationResourceId <br>[DestinationType] <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>リージョン |
| RoundTripTimeMs | ラウンド トリップ時間 (ミリ秒) | ミリ秒 | Average | ソースとターゲットの間で送信されたチェックの RTT。 これは平均値ではありません。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>[DestinationName] <br>DestinationResourceId <br>[DestinationType] <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>リージョン |
| TestResult | テスト結果 | Count | Average | 接続モニターのテスト結果 | SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>[DestinationName] <br>DestinationResourceId <br>[DestinationType] <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |

#### <a name="metric-based-alerts-for-connection-monitor"></a>接続モニターのメトリック ベースのアラート

接続モニターでメトリック アラートを作成するには、次の方法を使用します。 

1. 接続モニターから、接続モニターの作成時に [Azure portal を使用](connection-monitor-preview-create-using-portal.md#) 
1. 接続モニターから、ダッシュボードで [警告の構成] を使用 
1. Azure Monitor から - Azure Monitor でアラートを作成するには: 
    1. 接続モニターで作成した接続モニター リソースを選択します。
    1. 接続モニターの信号の種類として **[メトリック]** が表示されていることを確認します。
    1. **[条件の追加]** で、 **[シグナル名]** として **[ChecksFailedPercent(Preview)]** または **[RoundTripTimeMs(Preview)]** を選択します。
    1. **[シグナルの種類]** として、 **[メトリック]** を選択します。 たとえば、 **[ChecksFailedPercent(Preview)]** を選択します。
    1. メトリックのすべてのディメンションが一覧表示されます。 ディメンション名とディメンション値を選択します。 たとえば、 **[送信元アドレス]** を選択し、接続モニターのソースの IP アドレスを入力します。
    1. **[アラート ロジック]** に、次の詳細を入力します。
        * **[条件タイプ]** : **静的**。
        * **[条件]** と **[しきい値]** 。
        * **[集約粒度] と [評価の頻度]** : 接続モニターでは 1 分ごとにデータが更新されます。
    1. **[アクション]** で、アクション グループを選択します。
    1. アラートの詳細を指定します。
    1. アラート ルールを作成します。

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Azure Monitor の [ルールの作成] 領域を示すスクリーンショット。ソース アドレスとソース エンドポイント名が強調表示" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>ネットワークの問題を診断する

接続モニターは、接続モニターとネットワークでの問題を診断するのに役立ちます。 ハイブリッド ネットワークの問題は、前にインストールした Log Analytics エージェントによって検出されます。 Azure の問題は、Network Watcher の拡張機能によって検出されます。 

Azure ネットワークの問題は、ネットワーク トポロジで確認できます。

ソースがオンプレミスの VM であるネットワークの場合、次の問題を検出できます。

* 要求がタイムアウトしました。
* エンドポイントが DNS によって解決されない (一時的または永続的)。 URL が無効。
* ホストが見つからない。
* ソースが宛先に接続できない。 ICMP 経由でターゲットに到達できない。
* 証明書関連の問題: 
    * エージェントの認証にクライアント証明書が必要である。 
    * 証明書再配置リストにアクセスできない。 
    * エンドポイントのホスト名が、証明書のサブジェクト名またはサブジェクト代替名と一致しない。 
    * ソースのローカル コンピューターの信頼された証明機関ストアにルート証明書がない。 
    * SSL 証明書が有効期限切れである、無効である、失効している、または互換性がない。

ソースが Azure VM であるネットワークの場合、次の問題を検出できます。

* エージェントに関する問題:
    * エージェントが停止した。
    * DNS の解決に失敗した。
    * ターゲット ポートでリッスンしているアプリケーションまたはリスナーがない。
    * ソケットを開けなかった。
* VM の状態の問題: 
    * 開始中
    * 停止中
    * 停止済み
    * 割り当て解除中
    * 割り当て解除済み
    * 再起動中
    * 未割り当て
* ARP テーブル エントリがない。
* ローカル ファイアウォールの問題または NSG の規則が原因で、トラフィックがブロックされた。
* 仮想ネットワーク ゲートウェイの問題: 
    * ルートがない。
    * 2 つのゲートウェイ間のトンネルが切断されているか、存在しない。
    * 2 番目のゲートウェイがトンネルによって検出されなかった。
    * ピアリング情報が見つからなかった。
* Microsoft Edge にルートがなかった。
* システム ルートまたは UDR が原因でトラフィックが停止した。
* ゲートウェイの接続で BGP が有効になっていない。
* ロード バランサーで DIP プローブがダウンする。

## <a name="next-steps"></a>次の手順
    
   * [Azure portal を使用して接続モニターを作成する方法](./connection-monitor-create-using-portal.md)を確認する  
   * [ARMClient を使用して接続モニターを作成する方法](./connection-monitor-create-using-template.md)を確認する