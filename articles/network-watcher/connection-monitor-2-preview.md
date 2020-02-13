---
title: 接続モニター (プレビュー) | Microsoft Docs
description: 接続モニター (プレビュー) を使用して分散環境でネットワーク通信を監視する方法について説明します
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: c993a08a4163d50a9632055da355e39b5bdde004
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026451"
---
# <a name="overview"></a>概要

接続モニター (プレビュー) は、ハイブリッドおよび Azure クラウドのデプロイに対して Azure Network Watcher で統合されたエンド ツー エンドの接続監視機能を提供します。 Azure Network Watcher には、Azure のデプロイに対する接続に関連したメトリックを監視、診断、および表示するためのツールが用意されています。

主なユースケース:

- 多層アプリケーション内のデータベース サーバー VM と通信するフロントエンド Web サーバー VM がある。 2 つの VM 間のネットワーク接続を確認したい。
- 米国東部リージョンの VM から、米国中部リージョンの VM に ping を実行して、リージョン間のネットワーク待ち時間を比較したい
- シアトルのような都市に、Office 365 URL に接続する複数のオンプレミスのオフィス サイトがある。 connecting to Office 365 URLs. シアトルとアッシュバーンから Office 365 URL を使用しているユーザーが体験した待ち時間を比較したい。
- Azure Storage エンドポイントへの接続を必要とするハイブリッド アプリケーションをセットアップしている。 同じ Azure Storage エンドポイントに接続しているオンプレミス サイトと Azure アプリケーションの間の待ち時間を比較したい。
- クラウド アプリケーションをホストしている Azure VM からオンプレミス セットアップへの接続を確認したい。

このプレビュー段階で、このソリューションには、Network Watcher の[接続モニター](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint)と Network Performance Monitor (NPM) の[サービス接続モニター](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)という 2 つの重要な機能の優れた特性が組み合わされています。

ハイライト:

* Azure とハイブリッドの監視のニーズに対応した、統合された直感的なエクスペリエンス
* クロス リージョン、クロス ワークスペースの接続の監視
* プローブ頻度の増加、およびネットワーク パフォーマンスの可視性の向上
* ハイブリッド デプロイでのアラートの高速化
* HTTP、TCP、および ICMP ベースの接続チェックのサポート
* Azure と Azure 以外のテストのセットアップに対するメトリックと Log Analytics のサポート

![接続モニター](./media/connection-monitor-2-preview/hero-graphic.png)

接続モニター (プレビュー) を使用して監視を開始するには、次の手順に従ってください。

## <a name="step-1-install-monitoring-agents"></a>手順 1:監視エージェントをインストールする

接続モニターは、軽量の実行可能ファイルを使用して接続チェックを実行します。  Azure とオンプレミスの両方の環境からの接続チェックがサポートされます。 使用する特定の実行可能ファイルは、VM が Azure でホストされているかオンプレミスでホストされているかによって異なります。

### <a name="agents-for-azure-virtual-machines"></a>Azure 仮想マシンのエージェント

接続モニターがユーザーの Azure VM を監視のソースとして認識するには、Network Watcher Agent 仮想マシン拡張機能 (Network Watcher 拡張機能とも呼ばれる) をインストールする必要があります。 Network Watcher Agent 拡張機能は、Azure 仮想マシンでエンドツーエンドの監視とその他の高度な機能をトリガーするための要件です。 [VM を作成し、それに Network Watcher 拡張機能をインストール](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[できます](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)。  また、[Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) と [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) 用で別々に、Network Watcher 拡張機能をインストール、構成、およびトラブルシューティングすることもできます。

NSG またはファイアウォール規則によって、ソースと宛先の間の通信がブロックされている場合、接続モニターはその問題を検出し、トポロジに診断メッセージとして表示します。 接続の監視を有効にするには、NSG とファイアウォール規則で、ソースと宛先の間での TCP または ICMP 経由のパケットが許可されるようにします。

### <a name="agents-for-on-premise-machines"></a>オンプレミス マシンのエージェント

接続モニターがユーザーのオンプレミス マシンを監視のソースとして認識するには、Log Analytics エージェントをマシンにインストールし、Network Performance Monitoring ソリューションを有効にする必要があります。 これらのエージェントは Log Analytics ワークスペースにリンクされており、監視を開始するには、ワークスペース ID と主キーがセットアップされている必要があります。

Windows マシン用の Log Analytics エージェントをインストールするには、[こちらのリンク](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)に記載されている手順に従ってください。

パスにファイアウォールや仮想ネットワーク アプライアンス (NVA) が含まれている場合は、宛先が到達可能であることを確認します。

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>手順 2:サブスクリプションで Network Watcher を有効にする

VNET を使用するすべてのサブスクリプションでは、Network Watcher が有効になります。 サブスクリプションで仮想ネットワークを作成すると、その仮想ネットワークのリージョンとサブスクリプションで Network Watcher が自動的に有効になります。 Network Watcher は自動的に有効化され、リソースへの影響や関連する課金はありません。 Network Watcher がサブスクリプションで明示的に無効になっていないことを確認してください。 詳細については、[Network Watcher の有効化](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)に関する記事をご覧ください。

## <a name="step-3-create-connection-monitor"></a>手順 3:接続モニターを作成する 

"_接続モニター_" は、通信を一定の間隔で監視し、ソース エージェントと宛先エンドポイントの間の到達可能性、待ち時間、ネットワーク トポロジの変更について通知します。 ソースは、監視エージェントがインストールされている Azure VM またはオンプレミス マシンの場合があります。 宛先エンドポイントとしては、Office 365 URL、Dynamics 365 URL、カスタム URL、Azure VM リソース ID、IPv4、IPv6、FQDN、または任意のドメイン名が考えられます。

### <a name="accessing-connection-monitor-preview"></a>接続モニター (プレビュー) へのアクセス

1. 次のリンクを使用して Network Watcher にアクセスします:[https://ms.portal.azure.com/?Microsoft\_Azure\_Network\_connectionmonitorpreview=true#blade/Microsoft\_Azure\_Network/NetworkWatcherMenuBlade/connectionMonitorPreview](https://ms.portal.azure.com/?Microsoft_Azure_Network_connectionmonitorpreview=true#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/connectionMonitorPreview)
2. Network Watcher の左ペインの [監視] セクションにある [接続モニター (プレビュー)] タブをクリックします。 このタブは、手順 1 で指定されているリンクを使用して Network Watcher にアクセスした場合にのみ表示されます。
3. 接続モニター (プレビュー) エクスペリエンスを使用して作成されたすべての接続モニターを表示できます。 [接続モニター] タブの従来のエクスペリエンスを使用して作成されたすべての接続モニターは、[接続モニター] タブに表示されます。

    ![接続モニターを作成する](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>接続モニターの作成

接続モニター (プレビュー) を使用して作成された接続モニターは、オンプレミスと Azure VM の両方をソースとして追加し、エンドポイント (Azure または他の URL/IP にまたがる場合がある) への接続を監視する機能を提供します。

接続モニターのエンティティは次のとおりです。

* 接続モニター リソース – リージョン固有の Azure リソース。 次に示すすべてのエンティティは、接続モニター リソースのプロパティです。
* エンドポイント – 接続チェックに関与するすべてのソースと宛先はエンドポイントと呼ばれます。 エンドポイントの例 – Azure VM、オンプレミス エージェント、URL、IP
* テスト構成 – 各テスト構成はプロトコルに固有です。 選択されたプロトコルに基づいて、ポート、しきい値、テストの頻度、その他のパラメーターを定義できます。
* テスト グループ – 各テスト グループには、ソース エンドポイント、宛先エンドポイント、およびテスト構成が含まれています。 各接続モニターには、複数のテスト グループを含めることができます。
* テスト – ソース エンドポイント、宛先エンドポイント、およびテスト構成を組み合わせて 1 つのテストが作成されます。 テストは、監視データ (チェックの失敗率 (%) と RTT) が使用可能な最下位レベルです。

 ![接続モニターを作成する](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>ポータルから

接続モニターを作成するには、次の手順に従います。

1. 接続モニター (プレビュー) ダッシュボードで、左上隅にある [作成] をクリックします。
2. [基本] タブで、接続モニターの情報を入力します。
   1. [接続モニター名] – 接続モニターの名前。 ここでは、Azure リソースの標準的な名前付けルールが適用されます。
   2. [サブスクリプション] – 接続モニターのサブスクリプションを選択します。
   3. [リージョン] – 接続モニター リソースのリージョンを選択します。 選択できるのは、このリージョンに作成されたソース VM のみです。
   4. [ワークスペースの構成] - 既定のチェックボックスをオンにすると、接続モニターによって作成された既定のワークスペースを使用して監視データを保存できます。 カスタム ワークスペースを選択するには、このチェックボックスをオフにします。 サブスクリプションとリージョンを選択して、監視データを保持するワークスペースを選択します。
   5. [Next:Test Groups]\(次へ: テスト グループ\) をクリックして、テスト グループを追加します。

      ![接続モニターを作成する](./media/connection-monitor-2-preview/create-cm-basics.png)

3. [テスト グループ] タブで、[+ Test Group]\(+ テスト グループ\) をクリックしてテスト グループを追加します。 「_接続モニターでのテスト グループの作成_」を使用してテスト グループを追加します。 [レビューと作成] をクリックして、接続モニターを確認します。

   ![接続モニターを作成する](./media/connection-monitor-2-preview/create-tg.png)

4. 接続モニターを作成する前に、[レビューと作成] タブで基本情報とテスト グループを確認します。 [レビューと作成] ビューから接続モニターを編集するには、次のようにします。
   1. 基本的な詳細を編集するには、図 2 のボックス 1 で指定されている鉛筆アイコンを使用します。
   2. 個々のテスト グループを編集するには、編集するテスト グループをクリックし、編集モードでそのテスト グループを開きます。
   3. [現在のコスト/月] は、プレビュー中のコストを示していました。 現在、接続モニターの使用に料金は発生しないため、この列にはゼロが表示されます。 [実際のコスト/月] は、一般公開後に課金される料金を示していました。 ログ分析の取り込み料金はプレビュー期間中でも適用されますので注意してください。

5. [レビューと作成] タブで、[作成] ボタンをクリックして接続モニターを作成します。

   ![接続モニターを作成する](./media/connection-monitor-2-preview/review-create-cm.png)

6.  接続モニター (プレビュー) では、接続モニター リソースがバックグラウンドで作成されます。

#### <a name="from-armclient"></a>Armclient から

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

デプロイ コマンド:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>接続モニターでのテスト グループの作成

接続モニターの各テストグループには、テスト構成でのチェックの失敗率と RTT のネットワーク パラメーターでテストされたソースと宛先が含まれています。

#### <a name="from-portal"></a>ポータルから

接続モニターでテスト グループを作成するには、次に示すフィールドの値を指定します。

1. [Disable Test Group]\(テスト グループの無効化\) – このフィールドをオンにすると、テスト グループで指定されたすべてのソースと宛先の監視が無効になります。 既定では、このオプションはオフになっています。
2. [Name]\(名前\) – テスト グループの名前
3. [Sources]\(ソース\) – Azure VM とオンプレミス マシンの両方を指定できます (それらにエージェントがインストールされている場合)。 ソースに固有のエージェントをインストールするには、手順 1 を参照してください。
   1. Azure エージェントを選択するには、[Azure エージェント] タブをクリックします。 接続モニターの作成時に指定したリージョンにバインドされている VM のみが一覧表示されます。 既定では、VM は所属するサブスクリプションにグループ化され、グループは折りたたまれています。 サブスクリプション レベルから階層内の他のレベルにドリルダウンできます。

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      また、[Group by]\(グループ化\) フィールドの値を変更して、他のレベルからツリーを開始することもできます。 次に例を示します。[Group by]\(グループ化\) – VNET では、階層内のエージェントがある VM が表示されます (VNET -\&gt; Subnets -\&gt; VMs with agents)。

      ![ソースを追加する](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. オンプレミス エージェントを選択するには、[Non-Azure Agents]\(Azure 以外のエージェント\) タブをクリックします。 既定では、リージョン内のワークスペースにグループ化されたエージェントが表示されます。Network Performance Monitor ソリューションが構成されているワークスペースのみが一覧表示されます。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) から Network Performance Monitor ソリューションをワークスペースに追加します。 また、[Solutions Gallery からの Azure Monitor ソリューションの追加](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)に関する記事で説明されているプロセスを使用することもできます。既定では、[接続モニターの作成] ビューの [基本情報] タブで選択されたリージョンが表示されます。 リージョンを変更し、新しく選択したリージョンのワークスペースからエージェントを選択できます。 [Group by]\(グループ化\) フィールドの値をサブネット上のグループに変更することもできます。

      ![Azure 以外のソース](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. 選択されている Azure と Azure 以外のエージェントを確認するには、[レビュー] をクリックします。

      ![ソースを確認する](./media/connection-monitor-2-preview/review-sources.png)

   4. ソースの選択が完了したら、[完了] をクリックします。

4. [Destinations]\(宛先\) – Azure VM または任意のエンドポイント (パブリック IP、URL、FQDN) を宛先として指定することにより、それらへの接続を監視できます。 1 つのテスト グループで、Azure VM、O365 URL、D365 URL、またはカスタム エンドポイントを追加できます。

   1. Azure VM を宛先として選択するには、[Azure VM] タブをクリックします。 既定では、[接続モニターの作成] ビューの [基本情報] タブで選択したのと同じリージョンに、サブスクリプション階層にグループ化された Azure VM が表示されます。 リージョンを変更し、新しく選択したリージョンから Azure VM を選択できます。 Azure エージェントのように、サブスクリプション レベルから階層内の他のレベルにドリルダウンできます。

      ![宛先の追加](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![宛先の追加 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. [エンドポイント] タブをクリックして、Azure VM を宛先として選択します。 エンドポイント リストには、O365 と D365 のテスト URL が名前でグループ化されて設定されます。  同じ接続モニターの他のテスト グループで作成されたエンドポイントを選択することもできます。 新しいエンドポイントを追加するには、画面の右上隅にある [+ Endpoint]\(+ エンドポイント\) をクリックし、エンドポイントの URL/IP/FQDN と名前を指定します

      ![エンドポイントの追加](./media/connection-monitor-2-preview/add-endpoints.png)

   3. 選択されている Azure と Azure 以外のエージェントを確認するには、[レビュー] をクリックします。
   4. ソースの選択が完了したら、[完了] をクリックします。

5. [テスト構成] – 指定したテスト グループ内で任意の数のテスト構成を関連付けることができます。 ポータルでは、テスト グループごとに 1 つのテスト構成に制限されますが、Armclient を使用すると、さらに追加することができます。
   1. [名前] – テスト構成の名前
   2. [プロトコル] – TCP、ICMP、または HTTP のいずれかを選択できます。 HTTP を HTTPS に変更するには、プロトコルとして HTTP を、ポートとして 443 を選択します。
   3. [Create Network Test Configuration]\(ネットワーク テスト構成の作成\) – このチェックボックスは、[プロトコル] フィールドで HTTP を選択した場合にのみ表示されます。 このフィールドを有効にすると、TCP/ICMP プロトコルで、手順 3 と 4 で指定したのと同じソースと宛先を使用して、別のテスト構成を作成できます。 新しく作成されたテスト構成は、"\&lt;5.a で指定した名前\&gt;\_networkTestConfig" という名前になります。
   4. [Traceroute の無効化] – このフィールドは、TCP または ICMP をプロトコルとして使用するテスト グループに適用されます。  このフィールドをオンにすると、ソースでのトポロジとホップバイホップ ラウンド トリップ時間の検出が停止します。
   5. [Destination Port]\(宛先ポート\) – このフィールドは、カスタマイズして任意の宛先ポートを指定できます。
   6. [Test Frequency]\(テストの頻度\) – このフィールドは、上で指定したプロトコルとポートでソースが宛先に対して ping を実行する頻度を決定します。 30 秒、1 分、5 分、15 分、30 分のいずれかを選択できます。 ソースは、選択された値に基づいて宛先への接続をテストします。  たとえば、30 秒を選択すると、ソースは 30 秒間に少なくとも 1 回、宛先への接続を確認します。
   7. [Health Thresholds]\(正常性のしきい値\) – 次に示すネットワーク パラメーターにしきい値を設定することができます。
      1. [Checks Failed %]\(チェックの失敗率 (%)\) - 上で指定した条件でソースが宛先への接続をチェックしたときにチェックが失敗した割合。 TCP/ICMP プロトコルの場合、チェックの失敗率 (%) は、パケット損失率 (%) と同じである可能性があります。 HTTP プロトコルの場合、このフィールドは応答を取得しなかった HTTP 要求の数を表します。
      2. [RTT in milliseconds]\(RTT (ミリ秒)\) – 上で指定したテスト構成でソースが宛先に接続する際のラウンド トリップの時間 (ミリ秒)。

      ![TG の追加](./media/connection-monitor-2-preview/add-test-config.png)

テスト構成が指定されたテスト グループに追加されたすべてのソースと宛先は、個々のテストに分割されます。 次に例を示します。

* テスト グループ: TG1
* ソース: 3 (A、B、C)
* 宛先: 2 (D、E)
* テスト構成: 2 (Config 1、Config 2)
* 作成されたテスト: 合計 = 12

| **テスト番号** | **ソース** | **宛先** | **テスト構成名** |
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

* リージョン 1 つあたりのサブスクリプションごとの接続モニターの最大数 – 100
* 接続モニター 1 つあたりのテスト グループの最大数 - 20
* 接続モニター 1 つあたりのソースと宛先の最大数 – 100
* 接続モニター 1 つあたりのテスト構成の最大数 – 20 (Armclient を使用)。 2 (ポータルを使用)。

## <a name="step-4--data-analysis-and-alerts"></a>手順 4:データ分析とアラート

接続モニターが作成されると、ソースは、指定されたテスト構成に基づいて宛先への接続をチェックします。

### <a name="checks-in-a-test"></a>テストでのチェック

テスト構成でユーザーが選択したプロトコルに基づいて、接続モニター (プレビュー) は、選択されたテスト頻度でソースと宛先のペアに対して一連のチェックを実行します。

HTTP が選択されている場合、このサービスは、応答コードを返した HTTP 応答の数を計算して、チェックの失敗率 (%) を判別します。  RTT を計算するには、HTTP 呼び出しの応答を受信するためにかかった時間を測定します。

TCP または ICMP が選択されている場合、このサービスは、パケットの割合 (%) を計算して、チェックの失敗率 (%) を判別します。 RTT を計算するには、送信されたパケットの ACK を受信するためにかかった時間を測定します。 ネットワーク テストで traceroute データを有効にした場合は、オンプレミス ネットワークのホップバイホップの損失と待ち時間を確認できます。

### <a name="states-of-a-test"></a>テストの状態

テストのチェックによって返されたデータに基づいて、各テストの状態は次のようになります。

* Pass (合格) = チェックの失敗率 (%) と RTT の実際の値が、指定されたしきい値内である場合
* Fail (失敗) = チェックの失敗率 (%) または RTT の実際の値が、指定されたしきい値を超えている場合。 しきい値が指定されていない場合、チェックの失敗率 (%) = 100% のときは、テストに失敗のマークが付きます。
* Warning (警告) – チェックの失敗率 (%) の条件が指定されていないとき。 このような場合、接続モニター (プレビュー) では自動設定条件がしきい値として使用され、そのしきい値に違反すると、テストの状態が "警告" に設定されます。

### <a name="data-collection-analysis-and-alerts"></a>データの収集、分析、およびアラート

接続モニター (プレビュー) によって収集されたすべてのデータは、接続モニターの作成時に構成された Log Analytics ワークスペースに格納されます。 監視データは Azure Monitor メトリックでも使用できます。 Log Analytics を使用すると、監視データを必要に応じていつまでも保持できますが、Azure Monitor でのメトリックの保持期間は既定で 30 日間 になります。 **** その後、[メトリックに基づくアラートをデータに設定](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)することができます。

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>接続モニター ソリューションの監視ダッシュボード

特定のサブスクリプション、リージョン、タイムスタンプ、ソース、および宛先の種類の選択に対してアクセスできる接続モニターの一覧が表示されます。

Network Watcher サービスから接続モニター (プレビュー) に移動するときに、 **[View By]\(表示形式\)** を選択できます。

* 接続モニター (既定) – 選択したサブスクリプション、リージョン、タイムスタンプ、ソース、および宛先の種類に対して作成されたすべての接続モニターの一覧
* テスト グループ – 選択したサブスクリプション、リージョン、タイムスタンプ、ソース、および宛先の種類に対して作成されたすべてのテスト グループの一覧。 これらのテスト グループは、接続モニターでフィルター処理されません
* テスト – 選択したサブスクリプション、リージョン、タイムスタンプ、ソース、および宛先の種類に対して実行されているすべてのテストの一覧。 これらのテストは、接続モニターまたはテスト グループでフィルター処理されていません。

ダッシュボードで、各接続モニターをテスト グループに展開し、各テスト グループを、そこで実行されているさまざまな個別のテストに展開することができます。 下の画像で [1] とマークされています。

この一覧は、次の条件に基づいてフィルター処理できます。

* 最上位のフィルター - サブスクリプション、リージョン、タイムスタンプ、ソース、および宛先の種類。 下の画像で [2] とマークされています。
* 状態ベースのフィルター - 接続モニター/テスト グループ/テストの状態に基づいた第 2 レベルのフィルター。 下の画像で [3] とマークされています。
* 検索フィールド – 汎用検索を実行するには、"すべて" を選択します。 特定のエンティティで検索するには、ドロップダウンを使用して検索結果を絞り込みます。 下の画像で [4] とマークされています。

![テストのフィルター処理](./media/connection-monitor-2-preview/cm-view.png)

次に例を示します。

1. ソース IP = 10.192.64.56 のすべての接続モニター (プレビュー) ですべてのテストを確認するには、次のようにします。
   1. 表示方法を "テスト" に変更します
   2. 検索フィールド = 10.192.64.56
   3. 値の横のドロップダウンを使用して、"Sources" (ソース) を選択します
2. ソース IP = 10.192.64.56 のすべての接続モニター (プレビュー) で失敗したテストだけをフィルターで除外するには、次のようにします。
   1. 表示方法を "テスト" に変更します
   2. 状態ベースのフィルターから、"Fail" (失敗) を選択します。
   3. 検索フィールド = 10.192.64.56
   4. 値の横のドロップダウンを使用して、"Sources" (ソース) を選択します
3. outlook.office365.com を宛先とするすべての接続モニター (プレビュー) で失敗したテストだけをフィルターで除外するには、次のようにします。
   1. 表示方法を "テスト" に変更します
   2. 状態ベースのフィルターから、"Fail" (失敗) を選択します。
   3. 検索フィールド = outlook.office365.com
   4. 値の横のドロップダウンを使用して、"Destinations" (宛先) を選択します

   ![失敗したテスト](./media/connection-monitor-2-preview/tests-view.png)

以下に対するチェックの失敗率 (%) と RTT の傾向を確認するには、次のようにします。

1. 接続モニター
   1. 詳細に調査する接続モニターをクリックします
   2. 既定では、"テスト グループ" 別に監視データが表示されます。

      ![メトリックの表示方法](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. 詳細に調査するテスト グループを選択します。

      ![TG 別のメトリック](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. 前の手順で選択したテスト グループを対象に、チェックの失敗率 (%) または RTT ミリ秒で上位 5 件の失敗したテストが表示されます。 テストごとに、チェックの失敗率 (%) の近似曲線と RTT ミリ秒が表示されます。
   5. 上記の一覧からテストを選択するか、詳細に調査する別のテストを選択します。
   6. 選択されている期間とチェックの失敗率 (%) については、しきい値と実際の値が表示されます。 RTT ミリ秒については、しきい値、平均値、最小値、および最大値が表示されます。

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. さらに多くのデータを表示するには、期間を変更します。
  8. 手順 b でビューを変更し、ソース、宛先、またはテスト構成別に表示するように選択できます。 次に、失敗したテストに基づいてソースを選択し、失敗したテストの上位 5 件を調査します。  次に例を示します。表示方法: ソースと宛先を選択して、選択した接続モニターでその組み合わせの間で実行されているすべてのテストを調査します。

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. テスト グループ
   1. 詳細に調査するテスト グループをクリックします。
   2. 既定では、"ソース + 宛先 + テスト構成 (テスト)" で監視データを表示します。

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. 詳細に調査するテストを選択します。
   4. 選択されている期間とチェックの失敗率 (%) については、しきい値と実際の値が表示されます。 RTT ミリ秒については、しきい値、平均値、最小値、および最大値が表示されます。 また、選択したテストに固有の発生したアラートも表示されます。
   5. さらに多くのデータを表示するには、期間を変更します。
   6. 手順 b でビューを変更し、ソース、宛先、またはテスト構成別に表示するように選択できます。 次に、失敗したテストの上位 5 件を調査するエンティティを選択します。  次に例を示します。表示方法: ソースと宛先を選択して、選択した接続モニターでその組み合わせの間で実行されているすべてのテストを調査します。

3. テスト
   1. 詳細に調査するソースと宛先とテスト構成をクリックします
   2. 選択されている期間とチェックの失敗率 (%) については、しきい値と実際の値が表示されます。 RTT ミリ秒については、しきい値、平均値、最小値、および最大値が表示されます。 また、選択したテストに固有の発生したアラートも表示されます。

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. また、[トポロジ] をクリックすると、任意の時点のネットワーク トポロジを確認できます。

      ![Test2](./media/connection-monitor-2-preview/test-topo.png)

   4. Azure ネットワークの任意のホップ リンクをクリックすると、接続モニターによって特定された問題を確認できます。 現在、この機能は、オンプレミス ネットワークでは使用できません。

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics でのログ クエリ

監視データのカスタム ビューを作成するには、Log Analytics を使用します。 UI に表示されるすべてのデータは、Log Analytics から設定されます。 リポジトリ内のデータの対話型分析を実行し、エージェントの正常性やその他の Log Analytics ベースのアプリケーションなど、さまざまなソースからのデータを関連付けることができます。 さらに、Excel、Power BI、または共有可能なリンクにデータをエクスポートすることもできます。

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor のメトリック

接続モニター (プレビュー) エクスペリエンスの前に作成された接続モニターの場合、4 つのメトリックすべてを使用できます。 接続モニター (プレビュー) エクスペリエンスを使用して作成された接続モニターの場合、"(プレビュー)" というタグが付いたメトリックに対してのみデータが使用可能です。

リソースの種類 - Microsoft.Network/networkWatchers/connectionMonitors

| メトリック | メトリックの表示名 | ユニット | 集計の種類 | 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失敗したプローブの割合 | Percent | Average | 失敗した接続監視プローブの割合 | ディメンションなし |
| AverageRoundtripMs | Avg.ラウンド トリップ時間 (ミリ秒) | MilliSeconds | Average | ソースと接続先の間で送信された接続監視プローブのネットワークの平均ラウンド トリップ時間 (ミリ秒) |             ディメンションなし |
| ChecksFailedPercent (プレビュー) | チェックの失敗率 (%) (プレビュー) | Percent | Average | テストのチェックの失敗率 (%) |リスト: - ConnectionMonitorResourceId - SourceAddress - SourceName - SourceResourceId - SourceType - Protocol - DestinationAddress - DestinationName - DestinationResourceId - DestinationType -DestinationPort - TestGroupName - TestConfigurationName - Region |
| RoundTripTimeMs (プレビュー) | ラウンド トリップ時間 (ミリ秒) (プレビュー) | ミリ秒 | Average | ソースと宛先の間で送信されたチェックのラウンド トリップ時間 (ミリ秒)。 この値は平均化されません | リスト: - ConnectionMonitorResourceId - SourceAddress - SourceName - SourceResourceId - SourceType - Protocol - DestinationAddress - DestinationName - DestinationResourceId - DestinationType - DestinationPort - TestGroupName - TestConfigurationName - Region |

 ![監視メトリック](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Azure Monitor でのメトリック アラート

アラートを作成するには:

1. 接続モニター (プレビュー) を使用して作成された接続モニター リソースを選択します
2. 前の手順で選択したリソースのシグナルの種類として "メトリック" が表示されていることを確認します
3. [Add Condition]\(条件の追加\) で、[シグナル名] として [ChecksFailedPercent(プレビュー)] または [RoundTripTimeMs(プレビュー)] を選択し、[シグナルの種類] として [メトリック] を選択します。 例: ChecksFailedPercent(プレビュー) を選択します
4. メトリックごとに適用可能なすべてのディメンションが一覧表示されます。  ディメンション名とディメンション値を選択します。 例: [ソース アドレス] を選択し、手順 1 で選択した接続モニター リソースに関連するソースの IP アドレスを指定します。
5. [アラート ロジック] で、次を選択します。
   1. 条件の種類 – 静的
   2. 条件としきい値
   3. 集計の粒度と評価の頻度 – 接続モニター (プレビュー) は 1 分ごとにデータを更新します。
6.  [アクション] で、アクション グループを選択します
7. アラートの詳細を指定します
8. アラート ルールの作成

   ![警告](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>手順 5:ネットワークの問題を診断する

接続モニターは、接続モニター リソースおよびネットワークに関連する問題の診断に役立ちます。 ハイブリッド ネットワークの問題は、手順 1 でインストールした Log Analytics エージェントによって検出され、Azure の問題は Network Watcher 拡張機能によって検出されます。  ハイブリッド ネットワークの問題は [診断] ページに表示され、Azure ネットワークの問題はネットワークト ポロジに表示されます。

オンプレミス VM をソースとするネットワークでは、次のことが検出されます。

* 要求がタイムアウトになった
* エンドポイントが DNS によって解決されない (一時的または永続的)。 URL が無効。
* ホストが見つからない。
* ソースが宛先に接続できない。 ICMP 経由でターゲットに到達できない。
* 証明書に関連した問題 - エージェントを認証するためにクライアント証明書が必要、証明書の再配置リストにアクセスできない、エンドポイントのホスト名が証明書のサブジェクトまたはサブジェクトの代替名と一致しない、ソースのローカル コンピューターの信頼された証明機関ストアにルート証明書が存在しない、SSL 証明書が有効期限切れ/無効/失効している、非互換である

Azure VM をソースとするネットワークでは、次のことが検出されます。

* エージェントの問題 – エージェントが停止した、DNS の解決に失敗した、宛先ポートでリッスンしているアプリケーション/リスナーがない、ソケットを開けなかった
* VM の状態の問題 – 開始中、停止中、停止済み、割り当て解除中、割り当て解除済み、再起動中、割り当てられていない
* ARP テーブル エントリがない
* ローカル ファイアウォールの問題、NSG の規則が原因で、トラフィックがブロックされた
* VNET ゲートウェイ – ルートがない、2 つのゲートウェイ間のトンネルが切断されているか存在しない、または 2 番目のゲートウェイがトンネルによって検出されない、ピアリング情報がない
* MS Edge にルートが存在しない。
* システム ルートまたは UDR が原因でトラフィックが停止した
* ゲートウェイ接続で BGP が有効になっていない
* Load Balancer で DIP プローブがダウンしている
