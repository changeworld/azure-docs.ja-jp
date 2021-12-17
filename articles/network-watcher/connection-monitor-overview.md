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
ms.openlocfilehash: 6b62da14473797653da17b28b5366e26e7c18357
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082533"
---
# <a name="monitor-network-connectivity-by-using-connection-monitor"></a>接続モニターを使用してネットワーク接続を監視する

> [!IMPORTANT]
> 2021 年 7 月 1 日をもって、既存のワークスペースに新しいテストを追加したり、Network Performance Monitor (NPM) で新しいワークスペースを有効にしたりできなくなりました。 接続モニター (クラシック) に新しい接続モニターを追加することもできなくなりました。 2021 年 7 月 1 日より前に作成されたテストと接続モニターは引き続き使用できます。 
> 
> 現在のワークロードに対するサービスの中断を最小限に抑えるには、2024 年 2 月 29 日より前に、[Network Performance Monitor からテストを移行する](migrate-to-connection-monitor-from-network-performance-monitor.md)か、[接続モニター (クラシック) から Azure Network Watcher の新しい接続モニターに移行](migrate-to-connection-monitor-from-connection-monitor-classic.md)します。

接続モニターを使用すると、Azure Network Watcher で、統合されたエンドツーエンドの接続監視が提供されます。 接続モニターの機能では、ハイブリッド デプロイと Azure クラウド デプロイがサポートされています。 Network Watcher には、Azure のデプロイに対する接続に関連したメトリックを監視、診断、表示するためのツールが用意されています。

以下に、接続モニターのいくつかのユース ケースを示します。

- フロントエンド Web サーバー仮想マシン (VM) と、多層アプリケーション内のデータベース サーバー VM が通信している。 2 つの VM 間のネットワーク接続を確認したい。
- たとえば、米国東部リージョンの VM から米国中部リージョンの VM に ping を実行して、リージョン間のネットワーク待ち時間を比較したい。
- 複数のオンプレミス オフィス サイトがある (たとえば、ワシントン州シアトルに 1 つ、バージニア州アッシュバーンにもう 1 つ)。 オフィス サイトは Microsoft 365 の URL に接続している。 Microsoft 365 URL のユーザーについて、シアトルとアッシュバーンの間で待機時間を比較したい。
- ハイブリッド アプリケーションで Azure ストレージ アカウント エンドポイントに接続する必要がある。 オンプレミスのサイトと Azure アプリケーションは、同じエンドポイントに接続する。 オンプレミス サイトの待機時間を Azure アプリケーションの待機時間と比較したい。
- オンプレミスのセットアップと、クラウド アプリケーションがホストされている Azure VM 間の接続を確認したい。

接続モニターは 2 つの機能の長所を兼ね備えています。Network Watcher の[接続モニター (クラシック)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) 機能と、NPM の[サービス接続モニター](../azure-monitor/insights/network-performance-monitor-service-connectivity.md)、[ExpressRoute 監視](../expressroute/how-to-npm.md)、[パフォーマンスの監視](../azure-monitor/insights/network-performance-monitor-performance-monitor.md)機能です。

以下に、接続モニターの利点をいくつか示します。

* Azure とハイブリッドの監視のニーズに対応した、統合された直感的なエクスペリエンス
* クロス リージョン、クロス ワークスペースの接続の監視
* プローブ頻度の増加、およびネットワーク パフォーマンスの可視性の向上
* ハイブリッド デプロイでのアラートの高速化
* HTTP、伝送制御プロトコル (TCP)、インターネット制御メッセージ プロトコル (ICMP) に基づく接続チェックのサポート 
* Azure と Azure 以外のテストのセットアップに対するメトリックと Log Analytics のサポート

![接続モニターと Azure VM、Azure 以外のホスト、エンドポイント、データ ストレージの場所との間の対話方法を示す図。](./media/connection-monitor-2-preview/hero-graphic.png)

監視のために接続モニターの使用を開始するには、次のようにします。 

1. [監視エージェントをインストールする](#install-monitoring-agents)。
1. [サブスクリプションで Network Watcher を有効する](#enable-network-watcher-on-your-subscription)。
1. [接続モニターを作成する](#create-a-connection-monitor)。
1. [監視データを分析してアラートを設定する](#analyze-monitoring-data-and-set-alerts)。
1. [ネットワークの問題を診断する](#diagnose-issues-in-your-network)。

以降のセクションではこれらの手順についてさらに詳しく説明します。

## <a name="install-monitoring-agents"></a>監視エージェントをインストールする

接続モニターでは、軽量の実行可能ファイルを使用して接続チェックが実行されます。 Azure 環境とオンプレミス環境両方からの接続チェックがサポートされています。 使用する実行可能ファイルは、VM が Azure とオンプレミスのどちらでホストされているかによって異なります。

### <a name="agents-for-azure-virtual-machines"></a>Azure 仮想マシンのエージェント

接続モニターにより Azure VM が監視ソースとして認識されるようにするには、Network Watcher エージェント仮想マシン拡張機能を VM にインストールします。 この拡張機能は、"*Network Watcher 拡張機能*" とも呼ばれます。 Azure 仮想マシンでは、エンドツーエンドの監視とその他の高度な機能をトリガーするために拡張機能が必要です。 

[VM を作成する](./connection-monitor.md#create-the-first-vm)ときに、Network Watcher 拡張機能をインストールできます。 また、[Linux](../virtual-machines/extensions/network-watcher-linux.md) 用と [Windows](../virtual-machines/extensions/network-watcher-windows.md) 用の Network Watcher 拡張機能を別々に、インストール、構成、トラブルシューティングすることもできます。

ネットワーク セキュリティ グループ (NSG) またはファイアウォールの規則により、ソースとターゲットの間の通信がブロックされる場合があります。 接続モニターを使うと、この問題を検出し、トポロジに診断メッセージとして表示できます。 接続監視を有効にするには、NSG とファイアウォール規則で、ソースとターゲットの間の TCP または ICMP 経由のパケットを確実に許可します。

### <a name="agents-for-on-premises-machines"></a>オンプレミス コンピューター用のエージェント

接続モニターで、オンプレミスのコンピューターが監視のソースとして認識されるようにするには、コンピューターに Log Analytics エージェントをインストールします。 その後、[Network Performance Monitor ソリューション](../network-watcher/connection-monitor-overview.md#enable-the-npm-solution-for-on-premises-machines)を有効にします。 これらのエージェントは Log Analytics ワークスペースにリンクされているので、監視を開始するには、ワークスペース ID とプライマリ キーをセットアップする必要があります。

Windows コンピューター用の Log Analytics エージェントをインストールするには、[Windows への Log Analytics エージェントのインストール](../azure-monitor/agents/agent-windows.md)に関する記事を参照してください。

パスにファイアウォールまたはネットワーク仮想アプライアンス (NVA) が含まれている場合は、ターゲットに到達可能であることを確認します。

ポートを開くには:

* Windows マシンの場合は、[EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell スクリプトを、管理者特権の PowerShell ウィンドウでパラメーターを指定せずに実行します。

* Linux マシンの場合は、PortNumber の値を手動で変更します。 そのためには次を行います。
   1. */var/opt/microsoft/omsagent/npm_state* に移動します。 
   1. *npmdregistry* ファイルを開きます。
   1. PortNumber の値を変更します: ```“PortNumber:<port of your choice>”```。

   > [!NOTE]
   > 使用するポート番号は、ワークスペースで使用されているすべてのエージェントで同じである必要があります。 

このスクリプトにより、ソリューションに必要なレジストリ キーが作成されます。 エージェントが互いに TCP 接続を作成することを許可する Windows ファイアウォール規則も作成されます。 スクリプトによって作成されたレジストリ キーは、デバッグ ログを記録するかどうかと、ログ ファイルのパスを指定します。 このスクリプトによって、通信で使われるエージェント TCP ポートも定義されます。 これらのキーの値は、スクリプトによって自動的に設定されます。 これらのキーを手動で変更しないでください。 既定では、開かれるポートは 8084 です。 パラメーター "portNumber" をスクリプトに指定することでカスタム ポートを使用できます。 スクリプトが実行されるすべてのコンピューターで同じポートを使います。 

詳細については、「[Log Analytics エージェントの概要](../azure-monitor/agents/log-analytics-agent.md#network-requirements)」の「ネットワークの要件」セクションを参照してください。

このスクリプトでは、Windows ファイアウォールがローカルでのみ構成されます。 ネットワーク ファイアウォールがある場合、Network Performance Monitor によって使われている TCP ポート宛てのトラフィックを許可する必要があります。

Log Analytics Windows エージェントをマルチホーム化すると、複数のワークスペースおよび System Center Operations Manager 管理グループにデータを送信できます。 Linux エージェントでは、ワークスペース、管理グループのどちらか 1 つのターゲットにのみデータを送信できます。

#### <a name="enable-the-npm-solution-for-on-premises-machines"></a>オンプレミスのマシンで NPM ソリューションを有効にする 

オンプレミスのマシンで NPM ソリューションを有効にするには、次のようにします。 

1. Azure portal で、**Network Watcher** に移動します。
1. 左側のペインの **[監視]** セクションで、 **[Network Performance Monitor]** を選択します。 

   NPM ソリューションが有効になっているワークスペースの一覧 (**サブスクリプション** でフィルター処理済み) が表示されます。 
1. 新しいワークスペースに NPM ソリューションを追加するには、左上にある **[NPM の追加]** を選択します。 
1. ソリューションを有効にするサブスクリプションとワークスペースを選択し、 **[作成]** を選択します。
   
   ソリューションを有効にすると、ワークスペースが表示されるまでに数分かかります。

   :::image type="content" source="./media/connection-monitor/network-performance-monitor-solution-enable.png" alt-text="接続モニターで NPM ソリューションを追加する方法を示すスクリーンショット。" lightbox="./media/connection-monitor/network-performance-monitor-solution-enable.png":::

Log Analytics エージェントとは異なり、NPM ソリューションは、1 つの Log Analytics ワークスペースにのみデータを送信するように構成できます。

## <a name="enable-network-watcher-on-your-subscription"></a>サブスクリプションで Network Watcher を有効にする

仮想ネットワークが含まれるすべてのサブスクリプションは、Network Watcher で有効になります。 サブスクリプションで仮想ネットワークを作成すると、その仮想ネットワークのリージョンとサブスクリプションで、Network Watcher が自動的に有効になります。 この自動有効化により、リソースが影響を受けたり、料金が発生したりすることはありません。 Network Watcher がサブスクリプションで明示的に無効になっていないことを確認してください。 

[リージョンで Network Watcher が使用可能](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher&regions=all)なことを確認します。 詳細については、[Network Watcher の有効化](./network-watcher-create.md)に関する記事をご覧ください。

## <a name="create-a-connection-monitor"></a>接続モニターを作成する 

接続モニターでは、一定の間隔で通信が監視されます。 それにより、到達可能性と待機時間の変化が通知されます。 また、ソース エージェントとターゲット エンドポイントの間の現在および過去のネットワーク トポロジを確認することもできます。

監視エージェントがインストールされている Azure VM またはオンプレミス コンピューターを、ソースにすることができます。 ターゲット エンドポイントとしては、Microsoft 365 URL、Dynamics 365 URL、カスタム URL、Azure VM リソース ID、IPv4、IPv6、FQDN、または任意のドメイン名を使用できます。

### <a name="access-connection-monitor"></a>接続モニターにアクセスする

1. Azure portal で、**Network Watcher** に移動します。
1. 左側のペインで、 **[監視]** の **[接続モニター]** を選択します。

   接続モニターで作成されたすべての接続モニターが表示されます。 接続モニターの従来のエクスペリエンスで作成された接続モニターを表示するには、 **[接続モニター]** タブを選択します。
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="接続モニターで作成された接続モニターを示すスクリーンショット。" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>接続モニターを作成する

接続モニター (プレビュー) で作成できる接続モニターでは、オンプレミスのマシンと Azure VM の両方をソースとして追加できます。 これらの接続モニターでは、エンドポイントへの接続も監視できます。 エンドポイントは、Azure または他の任意の URL や IP アドレス上のどこにあってもかまいません。

接続モニターには、次のエンティティが含まれています。

* **接続モニター リソース**:リージョン固有の Azure リソース。 以下に示すすべてのエンティティは、接続モニター リソースのプロパティです。
* **エンドポイント**: 接続チェックに含まれるソースまたはターゲット。 エンドポイントの例としては、Azure VM、オンプレミスのエージェント、URL、IP アドレスなどがあります。
* **テスト構成**:テスト用のプロトコル固有の構成。 選択したプロトコルに基づいて、ポート、しきい値、テストの頻度、その他の特性を定義できます。
* **テスト グループ**:ソース エンドポイント、ターゲット エンドポイント、テスト構成が含まれるグループ。 接続モニターには、複数のテスト グループを含めることができます。
* **テスト**:ソース エンドポイント、ターゲット エンドポイント、テスト構成の組み合わせ。 テストは、監視データを入手できる最も小さいレベルです。 監視データには、失敗したチェックの割合と、ラウンドトリップ時間 (RTT) が含まれます。

 ![テスト グループとテストの関係が定義されている接続モニターを示す図。](./media/connection-monitor-2-preview/cm-tg-2.png)

[Azure portal](./connection-monitor-create-using-portal.md)、[ARMClient](./connection-monitor-create-using-template.md)、または [Azure PowerShell](connection-monitor-create-using-powershell.md) を使用して、接続モニターを作成できます。

テスト グループに追加したすべてのソース、ターゲット、テスト構成は、個々のテストに分割されます。 ソースとターゲットの編成方法の例を次に示します。

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
| | |

### <a name="scale-limits"></a>スケールの上限

接続モニターには、次のようなスケール制限があります。

* 1 つのリージョンの 1 つのサブスクリプションあたりの接続モニターの最大数: 100
* 接続モニターあたりのテスト グループの最大数: 20
* 接続モニターあたりのソースとターゲットの最大数: 100
* 接続モニターあたりのテスト構成の最大数:

## <a name="analyze-monitoring-data-and-set-alerts"></a>監視データを分析してアラートを設定する

接続モニターを作成すると、ソースにより、テスト構成に基づいてターゲットへの接続のチェックが行われます。

### <a name="checks-in-a-test"></a>テストでのチェック

テスト構成で選択したプロトコルに応じて、接続モニターでソースとターゲットのペアに対して一連のチェックが実行されます。 チェックは、選択したテストの頻度に従って実行されます。

HTTP を使用した場合、サービスにより、有効な応答コードを返した HTTP 応答の数が計算されます。 有効な応答コードは、PowerShell と Azure CLI を使用して設定できます。 その結果によって、失敗したチェックの割合が判定されます。 RTT を計算するため、サービスでは HTTP の呼び出しと応答の間の時間が測定されます。

TCP または ICMP を使用した場合、サービスでは、パケット損失率を計算して、失敗したチェックの割合が決定されます。 RTT を計算するため、サービスでは、送信されたパケットに対する受信確認 (ACK) を受け取るのにかかった時間が測定されます。 ネットワーク テスト用に Traceroute データを有効にした場合は、オンプレミスのネットワークに対するホップバイホップの損失と待ち時間を表示できます。

### <a name="states-of-a-test"></a>テストの状態

チェックによって返されるデータに応じて、テストは次のような状態になる可能性があります。

* **合格**: 失敗したチェックの割合および RTT の実際の値が、指定したしきい値を超えていません。
* **失敗**: 失敗したチェックの割合または RTT の実際の値が、指定したしきい値を超えました。 しきい値が指定されていない場合は、失敗したチェックの割合が 100 のときに、テストは "*失敗*" 状態になります。
* **警告**: 
     * しきい値が指定され、接続モニターで、しきい値の 80 パーセントを超えるチェック失敗割合が観察されると、テストは "*警告*" としてマークされます。
     * しきい値が指定されていない場合は、接続モニターによって自動的にしきい値が割り当てられます。 そのしきい値を超えると、テストの状態は "*警告*" に変わります。TCP または ICMP テストでのラウンドトリップ時間については、しきい値は 750 ミリ秒 (ms) です。 チェック失敗割合のしきい値は 10 パーセントです。 
* **不確定**: Log Analytics ワークスペース内にデータはありません。メトリックを確認します。 
* **実行されていません**: テスト グループを無効にすることによって無効にされています。  

### <a name="data-collection-analysis-and-alerts"></a>データの収集、分析、アラート

接続モニターによって収集されたデータは、Log Analytics ワークスペースに格納されます。 このワークスペースは、接続モニターを作成したときに設定されています。 

監視データは Azure Monitor メトリックでも使用できます。 Log Analytics を使用すると、監視データを必要な限りいつまでも保持できます。 既定では、メトリックは Azure Monitor によって 30 日間だけ保管されます。 

[データに対してメトリックに基づくアラートを設定する](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)ことができます。

#### <a name="monitoring-dashboards"></a>監視ダッシュボード

監視ダッシュボードには、サブスクリプション、リージョン、タイムスタンプ、ソース、およびターゲットの種類でアクセスできる接続モニターの一覧が表示されます。

Network Watcher から接続モニターに移動したときには、以下の項目別にデータを表示できます。

* **接続モニター**: 自分のサブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類に対して作成されたすべての接続モニターの一覧です。 このビューが既定値です。
* **テスト グループ**: 自分のサブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類に対して作成されたすべてのテスト グループの一覧です。 これらのテスト グループは、接続モニターではフィルター処理されません。
* **テスト**: 自分のサブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類に対して実行されたすべてのテストの一覧です。 これらのテストは、接続モニターまたはテスト グループではフィルター処理されません。

次の図では、矢印 1 で 3 つのデータ ビューが示されています。

ダッシュボードでは、各接続モニターを展開して、そのテスト グループを表示できます。 その後、各テスト グループを展開して、テスト グループで実行されたテストを確認できます。 

一覧は、以下に基づいてフィルター処理できます。

* **最上位のフィルター**: テキスト、エンティティ型 (接続モニター、テスト グループ、またはテスト) のタイムスタンプおよび範囲によってリストを検索します。 範囲には、サブスクリプション、リージョン、ソース、ターゲットの種類が含まれます。 下の図のボックス 1 を参照してください。
* **状態ベースのフィルター**: 接続モニター、テスト グループ、またはテストの状態によるフィルターです。 下の図のボックス 2 を参照してください。
* **アラート ベースのフィルター**: 接続モニター リソースに対して発生するアラートによるフィルターです。 下の図のボックス 3 を参照してください。

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="接続モニターで接続モニター、テスト グループ、テストのビューをフィルター処理する方法を示すスクリーンショット。" lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
たとえば、接続モニターで発信元 IP が 10.192.64.56 であるすべてのテストを確認するには、次のようにします。
1. ビューを **[テスト]** に変更します。
1. **検索** ボックスに「**10.192.64.56**」と入力します。
1. 最上位フィルターの **[範囲]** で、 **[ソース]** を選択します。

接続モニターで発信元 IP が 10.192.64.56 である失敗したテストだけを表示するには、次のようにします。
1. ビューを **[テスト]** に変更します。
1. 状態ベースのフィルターで、 **[失敗]** を選択します。
1. **検索** ボックスに「**10.192.64.56**」と入力します。
1. 最上位フィルターの **[範囲]** で、 **[ソース]** を選択します。

接続モニターでターゲットが outlook.office365.com である失敗したテストだけを表示するには、次のようにします。
1. ビューを **[テスト]** に変更します。
1. 状態ベースのフィルターで、 **[失敗]** を選択します。
1. **検索** ボックスに「**office.live.com**」と入力します。
1. 最上位フィルターの **[範囲]** で、 **[ターゲット]** を選択します。
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="ターゲット outlook.office365.com に対する失敗したテストのみを表示するようにフィルター処理されたビューを示すスクリーンショット。" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

接続モニター、テスト グループ、またはテストが失敗した理由を知るには、 **[理由]** の列を選択します。 これにより、どのしきい値 (チェック失敗割合または RTT) に違反しているかが示され、関連する診断メッセージが表示されます。
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="接続モニター、テスト、またはテスト グループの失敗の理由を示すスクリーンショット。" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
接続モニターの RTT の傾向と失敗したチェックの割合を表示するには、次のようにします。

1. 調査する接続モニターを選択します。

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="テスト グループ別に表示された、接続モニターのメトリックを示すスクリーンショット。" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

   このペインには、次のセクションが表示されます。  
    * **要点**: 選択した接続モニターのリソース固有のプロパティ。 
    * **概要**: 
        * RTT の集計された傾向線と、接続モニターのすべてのテストに対する失敗したチェックの割合。 詳細を表示する特定の時間を設定できます。
        * RTT または失敗したチェックの割合に基づいて、テスト グループ、ソース、ターゲット全体の上位 5 件が表示されます。 
    * **タブ**: [テスト グループ]、[ソース]、[ターゲット]、[テスト構成] があります。 接続モニターのテスト グループ、ソース、またはターゲットの一覧を表示します。 失敗したテストをチェックし、RTT を集計し、失敗した割合の値をチェックします。 後で戻ってデータを表示することもできます。 
    * **問題**: 接続モニターの各テストのホップ レベルの問題。 

   :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="テスト グループ パーツ 2 別に表示された、接続モニターのメトリックを示すスクリーンショット。" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. このペインでは、次のことができます。

    * **[すべてのテストを表示]** を選択して、接続モニターのすべてのテストを表示します。

      * テストを選択し、比較します。
    
        :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="2 つのテストの比較を示すスクリーンショット" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
      * クラスターを使用して、仮想ネットワークやサブネットなどの複合リソースを子リソースに展開します。

      * トポロジを選択し、任意のテストのトポロジを表示します。    

    * **[View all test groups]\(すべてのテスト グループの表示\)** 、 **[View test configurations]\(テスト構成の表示\)** 、 **[ソースの表示]** 、 **[View destinations]\(ターゲットの表示\)** を選択し、それぞれに特有の詳細を表示します。 

    * テスト グループ、テスト構成、ソース、またはターゲットを選択して、そのエンティティ内のすべてのテストを表示します。

テスト グループの RTT の傾向と失敗したチェックの割合を表示するには、次のようにします。

* 調査するテスト グループを選択します。 

   接続モニターの場合と同様に、要点と概要、テスト グループ、ソース、ターゲット、およびテスト構成のテーブルを表示して移動することができます。 

テストの RTT の傾向と失敗したチェックの割合を表示するには、次のようにします。

1. 調査するテストを選択します。 ネットワーク トポロジと、失敗した割合とラウンドトリップ時間をチェックするためのエンドツーエンドの傾向グラフを表示できます。 

1. 特定された問題を表示するには、トポロジでパスの任意のホップを選択します。 それらのホップは Azure リソースです。 

   > [!NOTE]
   > 現在、この機能は、オンプレミスのネットワークに対しては使用できません。

   :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="テストのトポロジ ビューを示すスクリーンショット。" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Log Analytics でのログ クエリ

監視データのカスタム ビューを作成するには、Log Analytics を使用します。 表示されるすべてのデータは、Log Analytics からのものです。 リポジトリ内のデータを対話形式で分析できます。 Log Analytics に基づく Agent Health または他のソリューションからのデータを関連付けます。 Excel や Power BI にデータをエクスポートするか、または共有可能なリンクを作成します。

#### <a name="network-topology-in-connection-monitor"></a>接続モニターでのネットワーク トポロジ 

通常は、接続モニターによって実行される traceroute コマンドの結果を使用して、このトポロジを構築します。 traceroute コマンドを使用すると、基本的にソースからターゲットまでのすべてのホップが取得されます。

ただし、ソースまたはターゲットが Azure の境界内にある場合は、2 つの異なる操作の結果をマージしてトポロジを構築します。
最初の操作は、traceroute コマンドの結果です。 2 番目の操作は、Azure 境界内の (顧客) ネットワーク構成に基づく論理ルートを識別する内部コマンドの結果です。 この内部コマンドは、Network Watcher のネクスト ホップ診断ツールに似ています。  

2 番目の操作は論理的であり、最初の操作では通常、Azure の境界内のホップは識別されないため、マージされた結果のいくつかのホップ (主に Azure 境界内のもの) については、待機時間の値は表示されません。

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor のメトリック

接続モニター エクスペリエンスの前に作成された接続モニターでは、次の 4 つのメトリックすべてを使用できます: 失敗したプローブの割合、AverageRoundtripMs、ChecksFailedPercent、RoundTripTimeMs。 

接続モニター エクスペリエンスで作成された接続モニターでは、データを使用できるのは、ChecksFailedPercent、RoundTripTimeMs、および Test Result メトリックについてのみです。

メトリックは監視頻度に従って生成され、特定の時点での接続モニターの側面が示されます。 接続モニターのメトリックには、SourceName、DestinationName、TestConfiguration、TestGroup など、複数のディメンションがあります。 これらのディメンションを使用して、特定のデータを視覚化し、アラートを定義するときにそれをターゲットにすることができます。

現在、Azure のメトリックで使用できる最小の時間単位は 1 分です。 周期が 1 分未満の場合は、集計された結果が表示されます。

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="接続モニターのメトリックを示すスクリーンショット。" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

メトリックを使用するときは、リソースの種類を **Microsoft.Network/networkWatchers/connectionMonitors** に設定します。

| メトリック | Display name | ユニット | 集計の種類 | 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (クラシック) | 失敗したプローブの割合 (クラシック) | パーセント | Average | 失敗した接続監視プローブの割合。<br>このメトリックは、接続モニター クラシックでのみ使用できます。  | ディメンションなし |
| AverageRoundtripMs (クラシック) | 平均ラウンドトリップ時間 (ms) (クラシック) | ミリ秒 | Average | ソースと接続先の間で送信された接続監視プローブのネットワーク RTT。<br>このメトリックは、接続モニター クラシックでのみ使用できます。 |             ディメンションなし |
| ChecksFailedPercent | 失敗したチェックの割合 | パーセント | Average | テストで失敗したチェックの割合。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>[DestinationName] <br>DestinationResourceId <br>[DestinationType] <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>リージョン <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| RoundTripTimeMs | ラウンドトリップ時間 (ms) | ミリ秒 | Average | ソースとターゲットの間で送信されたチェックの RTT。 これは平均値ではありません。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>[DestinationName] <br>DestinationResourceId <br>[DestinationType] <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>リージョン <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| TestResult | テスト結果 | Count | Average | 接続モニターのテスト結果。 <br>結果の値の解釈: <br>0-&nbsp;不確定 <br>1- 合格 <br>2- 警告 <br>3- 失敗| SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>[DestinationName] <br>DestinationResourceId <br>[DestinationType] <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| | |

#### <a name="metric-based-alerts-for-connection-monitor"></a>接続モニターのメトリック ベースのアラート

次の方法を使用して、接続モニターでメトリック アラートを作成できます。 

* 接続モニターから、接続モニターの作成時に、[Azure portal](connection-monitor-preview-create-using-portal.md#) を使用してメトリック アラートを作成します。 
* 接続モニターから、ダッシュボードで **[警告の構成]** を使用してメトリック アラートを作成します。 
* Azure Monitor から、次のようにしてメトリック アラートを作成します。 

    1. 接続モニターで作成した接続モニター リソースを選択します。
    1. 接続モニターの信号の種類として **[メトリック]** を確実に選択します。
    1. **[条件の追加]** で、 **[シグナル名]** として **[ChecksFailedPercent]** または **[RoundTripTimeMs]** を選択します。
    1. **[シグナルの種類]** には **[メトリック]** を選択します。 たとえば、 **[ChecksFailedPercent]** を選択します。
    1. メトリックのすべてのディメンションが一覧表示されます。 ディメンション名とディメンション値を選択します。 たとえば、 **[送信元アドレス]** を選択し、接続モニターの任意のソースの IP アドレスを入力します。
    1. **[アラート ロジック]** に、次の値を入力します。
        * **[条件タイプ]** : **静的**。
        * **[条件]** と **[しきい値]** 。
        * **[集約粒度] と [評価の頻度]** : 接続モニターでは 1 分ごとにデータが更新されます。
    1. **[アクション]** で、アクション グループを選択します。
    1. アラートの詳細を指定します。
    1. アラート ルールを作成します。

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="&quot;ソース アドレス&quot; と &quot;ソース エンド ポイント名&quot; のフィールドが強調して示されている Azure Monitor の [ルールの作成] ペインを示すスクリーンショット。" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

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

  > [!NOTE]
  > 接続されたゲートウェイが 2 つあり、そのうちの 1 つがソース エンドポイントと同じリージョンにない場合、それは接続モニターによって、トポロジ ビューに対して "学習したルートがありません" と識別されます。 接続は影響を受けません。 これは既知の問題であり、修正中です。 

  * Microsoft Edge にルートがなかった。
  * システム ルートまたはユーザー定義ルート (UDR) が原因でトラフィックが停止した。
  * Border Gateway Protocol (BGP) がゲートウェイ接続で有効にならない。
  * 動的 IP アドレス (DIP) プローブがロード バランサーでダウンする。

## <a name="compare-azure-connectivity-monitoring-support-types"></a>Azure 接続監視のサポートの種類を比較する 

Network Performance Monitor と接続モニター (クラシック) から、機能が最新の接続モニターに、1 回のクリックで、ダウンタイムなしにテストを移行できます。
 
この移行は、次の結果を生み出すために役立ちます。

* エージェントとファイアウォールの設定は現状のままです。 変更の必要はありません。 

* 既存の接続モニターは、接続モニター -> テストグループ -> テスト形式にマップされます。 **[編集]** を選択することで、最新の接続モニターのプロパティを表示して変更したり、テンプレートをダウンロードして接続モニターの変更を行い、それを Azure Resource Manager 経由で送信したりできます。 
* Network Watcher 拡張機能を備えた Azure 仮想マシンでは、ワークスペースとメトリックの両方にデータが送信します。 接続モニターでは、古いメトリック(ProbesFailedPercent と AverageRoundtripMs) の代わりに、新しいメトリック (ChecksFailedPercent と RoundTripTimeMs) 経由でデータを使用できるようになります。 古いメトリックは、ProbesFailedPercent > ChecksFailedPercent および AverageRoundtripMs > RoundTripTimeMs として、新しいメトリックに移行されます。
* データの監視:
   * **アラート**:新しいメトリックに自動的に移行されます。
   * **ダッシュボードと統合**:メトリック セットを手動で編集する必要があります。 
   
Network Performance Monitor と接続モニター (クラシック) から接続モニターに移行する理由はいくつかあります。 次の表に、最新の接続モニター (クラシック) が Network Performance Monitor と接続モニター (クラシック) に対してどのように動作するかを示すユース ケースをいくつか示します。 

 | 機能  | Network Performance Monitor | 接続モニター (クラシック) | 接続モニター |
 | -------  | --------------------------- | -------------------------- | ------------------ | 
 | Azure とハイブリッド監視のための統合されたエクスペリエンス | 使用不可 | 使用不可 | 利用可能 |
 | クロス サブスクリプション、クロス リージョン、クロス ワークスペースの監視 | クロス サブスクリプションとクロス リージョンの監視は可能ですが、クロス ワークスペースの監視はできません。 |   使用不可 | クロス サブスクリプションとクロス ワークスペースの監視が可能です。クロス ワークスペースにはリージョンの境界があります。  |
 | 集中型ワークスペースのサポート |  使用不可 | 使用不可   | 利用可能 |
 | 複数のソースから複数のターゲットに ping を実行できます | パフォーマンス監視を使用すると、複数のソースから複数のターゲットに ping を実行できます。 サービス接続の監視を使用すると、複数のソースから 1 つのサービスまたは URL に ping を実行できます。 Express Route を使用すると、複数のソースから複数のターゲットに ping を実行できます。 | 使用不可 | 利用可能 |
 | オンプレミス、インターネット ホップ、Azure を対象とする統合されたトポロジ | 使用不可 | 使用不可    | 利用可能 |
 | HTTP 状態コードのチェック | 使用不可  | 使用不可 | 利用可能 |
 | 接続診断 | 使用不可 | 利用可能 | 利用可能 |
 | 複合リソース - 仮想ネットワーク、サブネット、オンプレミスのカスタム ネットワーク | パフォーマンス監視でサポートされるのは、サブネット、オンプレミス ネットワーク、および論理ネットワーク グループです。 サービス接続の監視と Express Route でサポートされるのは、オンプレミスとクロスワークスペースの監視のみです。 | 使用不可 | 利用可能 |
 | 接続メトリックとディメンションの測定 |   使用不可 | 損失、待機時間、RTT。 | 利用可能 |
 | オートメーション – PowerShell、Azure CLI、Terraform | 使用不可 | 利用可能 | 利用可能 |
 | Linux のサポート | パフォーマンスの監視で Linux がサポートされています。 サービス接続モニターと ExpressRoute で Linux はサポートされていません。 | 利用可能 | 利用可能 |
 | パブリック、政府機関向け、Mooncake、エアギャップのクラウドのサポート | 利用可能 | 利用可能 | 利用可能|
 | | |


## <a name="faq"></a>よく寄せられる質問

### <a name="does-connection-monitor-support-classic-vms"></a>接続モニターでクラシック VM はサポートされていますか?
いいえ。接続モニターでクラシック VM はサポートされていません。 クラシック リソースは[非推奨になる](../virtual-machines/classic-vm-deprecation.md)ため、サービスとしてのインフラストラクチャ (IaaS) リソースをクラシックから Azure Resource Manager に移行することをお勧めします。 詳細については、[クラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/migration-classic-resource-manager-overview.md)に関するページを参照してください。

### <a name="what-if-my-topology-isnt-decorated-or-my-hops-have-missing-information"></a>トポロジが装飾されていないか、ホップに情報がない場合は、どうなりますか?
Azure 以外から Azure へのトポロジは、ターゲットの Azure リソースと接続モニター リソースが同じリージョンにある場合にのみ装飾できます。 

### <a name="what-happens-if-the-connection-monitor-creation-fails-with-the-following-error-we-dont-allow-creating-different-endpoints-for-the-same-vm"></a>"同じ VM に異なる複数のエンドポイントを作成することはできません" という旨のエラーで接続モニターの作成が失敗した場合は、どうなりますか?
同じ Azure VM を、同じ接続モニター内の異なる構成で使用することはできません。 たとえば、同じ接続モニターで、同じ VM をフィルターありとフィルターなしで使用することはサポートされていません。

### <a name="what-happens-if-the-test-failure-reason-is-nothing-to-display"></a>テスト エラーの理由に "表示するものがありません" と表示された場合、どうなりますか?
接続モニター ダッシュボードに表示される問題は、トポロジの検出またはホップ探索中に見つかるものです。 %loss または RTT に設定されているしきい値に違反したとしても、ホップで問題が見つからない場合があります。

### <a name="when-the-existing-connection-monitor-classic-is-migrated-to-the-latest-connection-monitor-what-happens-if-the-external-endpoint-tests-are-migrated-with-the-tcp-protocol-only"></a>既存の接続モニター (クラシック) を最新の接続モニターに移行するとき、外部エンドポイント テストが TCP プロトコルでのみ移行された場合は、どうなりますか? 
接続モニター (クラシック) にはプロトコルの選択オプションはありません。 そのため、外部エンドポイントへの接続は、接続モニター (クラシック) で HTTP プロトコルを使用して指定することはできません。 接続モニター (クラシック) では、すべてのテストで TCP プロトコルのみが使用されているため、移行時には、接続モニターのテストで TCP 構成を作成します。 

## <a name="next-steps"></a>次のステップ

次のものを使用して接続モニターを作成する方法について確認します。
    
   * [Azure ポータル](./connection-monitor-create-using-portal.md)  
   * [ARMClient](./connection-monitor-create-using-template.md)
