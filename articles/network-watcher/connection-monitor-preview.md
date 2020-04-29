---
title: 接続モニター (プレビュー) | Microsoft Docs
description: 接続モニター (プレビュー) を使用して分散環境でネットワーク通信を監視する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: d926a9f686f0f4c39203b8a217a7c608cfad926e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548114"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>接続モニター (プレビュー) によるネットワーク接続の監視

接続モニター (プレビュー) では、Azure Network Watcher での統合されたエンド ツー エンドの接続監視が提供されます。 接続モニター (プレビュー) の機能では、ハイブリッド デプロイと Azure クラウド デプロイがサポートされています。 Network Watcher には、Azure のデプロイに対する接続に関連したメトリックを監視、診断、表示するためのツールが用意されています。

接続モニター (プレビュー) の使用例を次に示します。

- フロントエンド Web サーバー VM と、多層アプリケーション内のデータベース サーバー VM が通信している。 2 つの VM 間のネットワーク接続を確認したい。
- 米国東部リージョンの VM から、米国中部リージョンの VM に ping を実行して、リージョン間のネットワーク待ち時間を比較したい。
- ワシントン州シアトルと、バージニア州アッシュバーンに複数のオンプレミス オフィス サイトがある。 オフィス サイトは Office 365 の URL に接続している。 Office 365 URL のユーザーについて、シアトルとアッシュバーンの間で待機時間を比較する。
- ハイブリッド アプリケーションで Azure Storage エンドポイントに接続する必要がある。 オンプレミスのサイトと Azure アプリケーションは、同じ Azure Storage エンドポイントに接続する。 オンプレミス サイトの待機時間と、Azure アプリケーションの待機時間を比較したい。
- オンプレミスのセットアップと、クラウド アプリケーションがホストされている Azure VM 間の接続を確認したい。

プレビュー フェーズの接続モニターでは、Network Watcher の[接続モニター](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint)機能と、Network Performance Monitor (NPM) の[サービス接続モニター](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)機能の 2 つが組み合わされています。

接続モニター (プレビュー) の利点を次に示します。

* Azure とハイブリッドの監視のニーズに対応した、統合された直感的なエクスペリエンス
* クロス リージョン、クロス ワークスペースの接続の監視
* プローブ頻度の増加、およびネットワーク パフォーマンスの可視性の向上
* ハイブリッド デプロイでのアラートの高速化
* HTTP、TCP、ICMP に基づく接続チェックのサポート 
* Azure と Azure 以外のテストのセットアップに対するメトリックと Log Analytics のサポート

![接続モニターと Azure VM、Azure 以外のホスト、エンドポイント、データ ストレージの場所との間の対話方法を示す図](./media/connection-monitor-2-preview/hero-graphic.png)

接続モニター (プレビュー) を使用して監視を始めるには、次の手順のようにします。 

1. 監視エージェントをインストールします。
1. サブスクリプションで Network Watcher を有効にします。
1. 接続モニターを作成します。
1. データ分析とアラートを設定します。
1. ネットワークの問題を診断します。

以降のセクションではこれらの手順についてさらに詳しく説明します。

## <a name="install-monitoring-agents"></a>監視エージェントをインストールする

接続モニターでは、軽量の実行可能ファイルを使用して接続チェックが実行されます。  Azure 環境とオンプレミス環境両方からの接続チェックがサポートされています。 使用する実行可能ファイルは、VM が Azure とオンプレミスのどちらでホストされているかによって異なります。

### <a name="agents-for-azure-virtual-machines"></a>Azure 仮想マシンのエージェント

接続モニターにより Azure VM が監視ソースとして認識されるようにするには、Network Watcher エージェント仮想マシン拡張機能を VM にインストールします。 この拡張機能は、"*Network Watcher 拡張機能*" とも呼ばれます。 Azure 仮想マシンでは、エンドツーエンドの監視とその他の高度な機能をトリガーするために拡張機能が必要です。 

[VM を作成する](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)ときに、Network Watcher 拡張機能をインストールできます。 また、[Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) 用と [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) 用の Network Watcher 拡張機能を別々に、インストール、構成、トラブルシューティングすることもできます。

ネットワーク セキュリティ グループ (NSG) またはファイアウォールの規則により、ソースとターゲットの間の通信がブロックされる場合があります。 接続モニターを使うと、この問題を検出し、トポロジに診断メッセージとして表示できます。 接続監視を有効にするには、NSG とファイアウォール規則で、ソースとターゲットの間の TCP または ICMP 経由のパケットが許可されるようにします。

### <a name="agents-for-on-premises-machines"></a>オンプレミス コンピューター用のエージェント

接続モニターで、オンプレミスのコンピューターが監視のソースとして認識されるようにするには、コンピューターに Log Analytics エージェントをインストールします。 その後、Network Performance Monitor ソリューションを有効にします。 これらのエージェントは Log Analytics ワークスペースにリンクされているので、監視を開始するには、ワークスペース ID とプライマリ キーをセットアップする必要があります。

Windows コンピューター用の Log Analytics エージェントをインストールする方法については、「[Windows 用の Azure Monitor 仮想マシン拡張機能](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)」を参照してください。

パスにファイアウォールまたはネットワーク仮想アプライアンス (NVA) が含まれている場合は、ターゲットに到達可能であることを確認します。

## <a name="enable-network-watcher-on-your-subscription"></a>サブスクリプションで Network Watcher を有効にする

仮想ネットワークが含まれるすべてのサブスクリプションは、Network Watcher で有効になります。 サブスクリプションで仮想ネットワークを作成すると、その仮想ネットワークのリージョンとサブスクリプションで、Network Watcher が自動的に有効になります。 この自動有効化により、リソースが影響を受けたり、料金が発生したりすることはありません。 Network Watcher がサブスクリプションで明示的に無効になっていないことを確認してください。 

詳細については、[Network Watcher の有効化](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)に関する記事をご覧ください。

## <a name="create-a-connection-monitor"></a>接続モニターを作成する 

接続モニターでは、一定の間隔で通信が監視されます。 それにより、到達可能性と待機時間の変化が通知されます。 また、ソース エージェントとターゲット エンドポイントの間の現在および過去のネットワーク トポロジを確認することもできます。

監視エージェントがインストールされている Azure VM またはオンプレミス コンピューターを、ソースにすることができます。 ターゲット エンドポイントとしては、Office 365 URL、Dynamics 365 URL、カスタム URL、Azure VM リソース ID、IPv4、IPv6、FQDN、または任意のドメイン名を使用できます。

### <a name="access-connection-monitor-preview"></a>接続モニター (プレビュー) にアクセスする

1. Azure portal のホーム ページで、 **[Network Watcher]** に移動します。
1. 左側の **[監視]** セクションで、 **[接続モニター (プレビュー)]** を選択します。
1. 接続モニター (プレビュー) で作成されたすべての接続モニターが表示されます。 接続モニターの従来のエクスペリエンスで作成された接続モニターを表示するには、 **[接続モニター]** タブに移動します。

    ![接続モニター (プレビュー) で作成されたすべての接続モニターを示すスクリーンショット](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>接続モニターを作成する

接続モニター (プレビュー) で作成する接続モニターでは、オンプレミスのコンピューターと Azure VM の両方をソースとして追加できます。 これらの接続モニターでは、エンドポイントへの接続も監視できます。 エンドポイントは、Azure または他の任意の URL や IP 上のどこにあってもかまいません。

接続モニター (プレビュー) には、次のエンティティが含まれています。

* **接続モニター リソース** – リージョン固有の Azure リソース。 以下のすべてのエンティティが、接続モニター リソースのプロパティです。
* **エンドポイント** –接続チェックに含まれるソースまたはターゲット。 エンドポイントの例としては、Azure VM、オンプレミスのエージェント、URL、IP などがあります。
* **テスト構成** – テスト用のプロトコル固有の構成。 選択したプロトコルに基づいて、ポート、しきい値、テストの頻度、その他のパラメーターを定義できます。
* **テスト グループ** – ソース エンドポイント、ターゲット エンドポイント、テスト構成が含まれるグループ。 接続モニターには、複数のテスト グループを含めることができます。
* **テスト** – ソース エンドポイント、ターゲット エンドポイント、テスト構成の組み合わせ。 テストは、監視データを入手できる最も小さいレベルです。 監視データには、失敗したチェックの割合と、ラウンドトリップ時間 (RTT) が含まれます。

 ![テスト グループとテストの関係が定義されている接続モニターを示す図](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Azure portal から接続モニターを作成する

Azure portal で接続モニターを作成するには、次の手順のようにします。

1. **[接続モニター (プレビュー)]** ダッシュボードの左上隅にある **[作成]** を選択します。
1. **[基本]** タブで、接続モニターの情報を入力します。
   * **[接続モニター名]** – 接続モニターの名前を追加します。 Azure リソースの標準的な名前付けルールを使用します。
   * **[サブスクリプション]** – 接続モニターのサブスクリプションを選択します。
   * **[リージョン]** – 接続モニターのリージョンを選択します。 選択できるのは、このリージョンに作成されたソース VM のみです。
   * **[ワークスペースの構成]** - ワークスペースには監視データが保持されます。 カスタム ワークスペースまたは既定のワークスペースを使用できます。 
       * 既定のワークスペースを使用するには、チェック ボックスをオンにします。 
       * カスタム ワークスペースを選択するには、チェック ボックスをオフにします。 そして、カスタム ワークスペースのサブスクリプションとリージョンを選択します。 
1. タブの下部にある **[次へ: テスト グループ]** を選択します。

   ![接続モニターの [基本] タブを示すスクリーンショット](./media/connection-monitor-2-preview/create-cm-basics.png)

1. **[テスト グループ]** タブで、 **[+ テスト グループ]** を選択します。 テスト グループの設定については、「[接続モニターでテスト グループを作成する](#create-test-groups-in-a-connection-monitor)」をご覧ください。 
1. タブの下部にある **[次へ: 確認と作成]** を選択して、接続モニターを確認します。

   ![[テスト グループ] タブと、テスト グループの詳細を追加するペインを示すスクリーンショット](./media/connection-monitor-2-preview/create-tg.png)

1. 接続モニターを作成する前に、 **[確認と作成]** タブで基本情報とテスト グループを確認します。 接続モニターを編集する必要がある場合:
   * 基本の詳細を編集するには、鉛筆アイコンを選択します。
   * テスト グループを編集するには、テスト グループを選択します。

   > [!NOTE] 
   > **[確認と作成]** タブには、接続モニターのプレビュー段階の間の 1 か月あたりのコストが表示されます。 現在、 **[現在のコスト]** 列には料金が表示されません。 接続モニターが一般提供されるようになると、この列に月額料金が表示されます。 
   > 
   > 接続モニターのプレビュー段階でも、Log Analytics のインジェスト料金がかかります。

1. 接続モニターを作成する準備ができたら、 **[確認と作成]** タブの下部にある **[作成]** を選択します。

   ![接続モニターの [確認と作成] タブのスクリーンショット](./media/connection-monitor-2-preview/review-create-cm.png)

接続モニター (プレビュー) により、接続モニター リソースがバックグラウンドで作成されます。

#### <a name="create-a-connection-monitor-by-using-armclient"></a>ARMClient を使用して接続モニターを作成する

ARMClient を使用して接続モニターを作成するには、次のコードを使用します。

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

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

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

デプロイ コマンドを次に示します。
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>接続モニターでテスト グループを作成する

接続モニターの各テストグループには、ネットワーク パラメーターでテストされるソースとターゲットが含まれています。 それらは、テスト構成でのチェックの失敗率と RTT についてテストされます。

Azure portal を使用して、接続モニターでテスト グループを作成するには、次のフィールドの値を指定します。

* **[テスト グループの無効化]** – このフィールドをオンにすると、テスト グループで指定されているすべてのソースとターゲットに対する監視を無効にできます。 既定ではオフになっています。
* **[名前]** – テスト グループの名前です。
* **[ソース]** – エージェントがインストールされていれば、Azure VM でもオンプレミスのコンピューターでもソースとして指定できます。 ソース用のエージェントをインストールするには、「[監視エージェントをインストールする](#install-monitoring-agents)」を参照してください。
   * Azure エージェントを選択するには、 **[Azure エージェント]** タブを選択します。ここでは、接続モニターを作成するときに指定したリージョンにバインドされている VM のみが表示されます。 既定では、VM は属しているサブスクリプションにグループ化されます。 これらのグループは折りたたまれています。 
   
       サブスクリプション レベルから階層内の他のレベルにドリルダウンできます。

      **サブスクリプション** > **リソース グループ** > **VNET** > **サブネット** > **エージェントがインストールされている VM**

      また、 **[グループ化]** フィールドの値を変更して、他のレベルからツリーを開始することもできます。 たとえば、仮想ネットワークでグループ化すると、エージェントがインストールされている VM は階層 **VNET** > **サブネット** > **エージェントがインストールされている VM** に表示されます。

      ![接続モニターの [ソースの追加] パネルと [Azure エージェント] タブを示すスクリーンショット](./media/connection-monitor-2-preview/add-azure-sources.png)

   * オンプレミスのエージェントを選択するには、 **[Non–Azure Agents]\(非 Azure エージェント\)** タブを選択します。既定では、エージェントはリージョンごとにワークスペースにグループ化されます。 これらのワークスペースのすべてに、Network Performance Monitor ソリューションが構成されます。 
   
       ワークスペースに Network Performance Monitor ソリューションを追加する必要がある場合は、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) から入手します。 Network Performance Monitor を追加する方法について詳しくは、「[Azure Monitor での監視ソリューション](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)」をご覧ください。 
   
       **[接続モニターの作成]** ビューの **[基本]** タブでは、既定のリージョンが選択されています。 リージョンを変更する場合、新しいリージョンのワークスペースからエージェントを選択できます。 サブネットでグループ化するように **[グループ化]** フィールドの値を変更することもできます。

      ![接続モニターの [ソースの追加] パネルと [Non-Azure Agents]\(非 Azure エージェント\) タブを示すスクリーンショット](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * 選択した Azure と Azure 以外のエージェントを確認するには、 **[確認]** をクリックします。

      ![接続モニターの [ソースの追加] パネルと [確認] タブを示すスクリーンショット](./media/connection-monitor-2-preview/review-sources.png)

   * ソースの設定が終わったら、 **[ソースの追加]** パネルの下部にある **[完了]** を選択します。

* **[ターゲット]** – Azure VM または任意のエンドポイント (パブリック IP、URL、FQDN) をターゲットとして指定することにより、それらへの接続を監視できます。 1 つのテスト グループに、Azure VM、Office 365 URL、Dynamics 365 URL、カスタム エンドポイントを追加できます。

    * Azure VM をターゲットとして選択するには、 **[Azure VM]** タブを選択します。既定では、Azure VM は、 **[接続モニターの作成]** ビューの **[基本]** タブで選択したのと同じリージョン内のサブスクリプション階層にグループ化されます。リージョンを変更し、新しく選択したリージョンから Azure VM を選択できます。 その後、Azure エージェント レベルと同じように、サブスクリプション レベルから階層内の他のレベルにドリルダウンできます。

       ![[ターゲットの追加] ペインの [Azure VM] タブが示されているスクリーンショット](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![[ターゲットの追加] ペインのサブスクリプション レベルが示されているスクリーンショット](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * ターゲットとしてのエンドポイントを選択するには、 **[エンドポイント]** タブを選択します。エンドポイントの一覧には、Office 365 のテスト URL と Dynamics 365 のテスト URL が含まれ、名前でグループ化されています。 これらのエンドポイントに加えて、同じ接続モニターの他のテスト グループに作成されたエンドポイントを選択できます。 
    
        新しいエンドポイントを追加するには、右上隅の **[+ エンドポイント]** を選択します。 次に、エンドポイントの名前と、URL、IP、または FQDN を指定します。

       ![接続モニターのターゲットとしてエンドポイントを追加する場所を示すスクリーンショット](./media/connection-monitor-2-preview/add-endpoints.png)

    * 選択した Azure VM とエンドポイントを確認するには、 **[確認]** タブを選択します。
    * ターゲットの選択が終わったら、 **[完了]** を選択します。

* **[テスト構成]** – テスト グループ内のテスト構成を関連付けることができます。 Azure portal では、テスト グループごとに 1 つのテスト構成だけが許可されますが、ARMClient を使用してさらに追加できます。

    * **[名前]** – テスト構成の名前です。
    * **[プロトコル]** – TCP、ICMP、または HTTP を選択します。 HTTP を HTTPS に変更するには、プロトコルとして **[HTTP]** を、ポートとして **[443]** を選択します。
        * **[Create network test configuration]\(ネットワーク テスト構成の作成\)** – このチェック ボックスは、 **[プロトコル]** フィールドで **[HTTP]** を選択した場合にのみ表示されます。 構成の別の場所で指定したのと同じソースおよびターゲットを使用して別のテスト構成を作成するには、このボックスをオンにします。 新しく作成したテスト構成は、`<the name of your test configuration>_networkTestConfig` という名前になります。
        * **[Traceroute の無効化]** – このフィールドは、プロトコルが TCP または ICMP であるテスト グループに適用されます。 このボックスをオンにすると、ソースでのトポロジとホップバイホップ RTT の検出が停止します。
    * **[接続先ポート]** – このフィールドは、選択したターゲット ポートでカスタマイズできます。
    * **[テストの頻度]** – このフィールドを使用して、指定したプロトコルとポートでソースがターゲットに ping を実行する頻度を選択します。 30 秒、1 分、5 分、15 分、30 分のいずれかを選択できます。 ソースでは、選択した値に基づいて、ターゲットへの接続がテストされます。  たとえば、30 秒を選択すると、ソースにより、少なくとも 30 秒に 1 回、ターゲットへの接続が確認されます。
    * **[成功のしきい値]** – 以下のネットワーク パラメーターにしきい値を設定できます。
       * **[チェックの失敗]** – 指定した条件で行われるソースによるターゲットへの接続の確認で、失敗してもかまわないチェックの割合を設定します。 TCP または ICMP プロトコルでは、失敗したチェックの割合はパケット損失の割合と同等と考えられます。 HTTP プロトコルでは、このフィールドは、応答を受け取らなかった HTTP 要求の割合を表します。
       * **[ラウンド トリップ時間]** – テスト構成でソースがターゲットに接続するのにかかってもかまわない時間をミリ秒で設定します。
    
       ![接続モニターでテスト構成を設定する場所を示すスクリーンショット](./media/connection-monitor-2-preview/add-test-config.png)

テスト グループに追加したすべてのソース、ターゲット、テスト構成は、個々のテストに分割されます。 ソースとターゲットの分割方法の例を次に示します。

* テスト グループ: TG1
* ソース: 3 (A、B、C)
* 宛先: 2 (D、E)
* テスト構成: 2 (Config 1、Config 2)
* 作成される総テスト数: 12

| テスト番号 | source | 宛先 | テスト構成 |
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
    * 20 (ARMClient を使用した場合)
    * 2 (Azure portal を使用した場合)

## <a name="analyze-monitoring-data-and-set-alerts"></a>監視データを分析してアラートを設定する

接続モニターを作成すると、ソースにより、テスト構成に基づいてターゲットへの接続のチェックが行われます。

### <a name="checks-in-a-test"></a>テストでのチェック

テスト構成で選択したプロトコルに基づいて、接続モニター (プレビュー) により、ソースとターゲットのペアに対する一連のチェックが実行されます。 チェックは、選択したテストの頻度に従って実行されます。

HTTP を使用した場合、サービスにより、応答コードを返した HTTP 応答の数が計算されます。 その結果によって、失敗したチェックの割合が判定されます。 RTT を計算するため、サービスでは HTTP の呼び出しと応答の間の時間が測定されます。

TCP または ICMP を使用した場合、サービスでは、パケット損失率を計算して、失敗したチェックの割合が決定されます。 RTT を計算するため、サービスでは、送信されたパケットに対する受信確認 (ACK) を受け取るのにかかった時間が測定されます。 ネットワーク テスト用に Traceroute データを有効にした場合は、オンプレミスのネットワークに対するホップバイホップの損失と待ち時間を確認できます。

### <a name="states-of-a-test"></a>テストの状態

チェックによって返されるデータに基づいて、テストは次のような状態になる可能性があります。

* **合格** – 失敗したチェックの割合および RTT の実際の値が、指定したしきい値を超えていません。
* **失敗** – 失敗したチェックの割合または RTT の実際の値が、指定したしきい値を超えました。 しきい値が指定されていない場合は、失敗したチェックの割合が 100 のときに、テストは失敗状態になります。
* **警告** – 失敗したチェックの割合に対して条件が指定されませんでした。 条件が指定されていない場合は、接続モニター (プレビュー) によって自動的にしきい値が割り当てられます。 そのしきい値を超えると、テストの状態は警告に変わります。

### <a name="data-collection-analysis-and-alerts"></a>データの収集、分析、アラート

接続モニター (プレビュー) によって収集されたデータは、Log Analytics ワークスペースに格納されます。 このワークスペースは、接続モニターを作成したときに設定されています。 

監視データは Azure Monitor メトリックでも使用できます。 Log Analytics を使用すると、監視データを必要な限りいつまでも保持できます。 既定では、メトリックは Azure Monitor によって 30 日間だけ保管されます。 

[データに対してメトリックに基づくアラートを設定する](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)ことができます。

#### <a name="monitoring-dashboards"></a>監視ダッシュボード

監視ダッシュボードには、サブスクリプション、リージョン、タイムスタンプ、ソース、およびターゲットの種類でアクセスできる接続モニターの一覧が表示されます。

Network Watcher から接続モニター (プレビュー) にアクセスし、次の方法でデータを表示できます。

* **[接続モニター]** – 自分のサブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類に対して作成されたすべての接続モニターの一覧です。 このビューが既定値です。
* **[テスト グループ]** – 自分のサブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類に対して作成されたすべてのテスト グループの一覧です。 これらのテスト グループは、接続モニターではフィルター処理されません。
* **[テスト]** – 自分のサブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類に対して実行されたすべてのテストの一覧です。 これらのテストは、接続モニターまたはテスト グループではフィルター処理されません。

次の図では、矢印 1 で 3 つのデータ ビューが示されています。

ダッシュボードでは、各接続モニターを展開して、そのテスト グループを表示できます。 その後、各テスト グループを展開して、テスト グループで実行されたテストを確認できます。 

一覧は、以下に基づいてフィルター処理できます。

* **最上位のフィルター** – サブスクリプション、リージョン、タイムスタンプ、ソース、ターゲットの種類を選択します。 下の図のボックス 2 を参照してください。
* **状態ベースのフィルター** – 接続モニター、テスト グループ、またはテストの状態によるフィルターです。 下の図の矢印 3 を参照してください。
* **カスタム フィルター** – 汎用検索を行うには、 **[すべて選択]** を選択します。 特定のエンティティで検索するには、ドロップダウン リストから選択します。 下の図の矢印 4 を参照してください。

![接続モニター (プレビュー) で接続モニター、テスト グループ、テストのビューをフィルター処理する方法を示すスクリーンショット](./media/connection-monitor-2-preview/cm-view.png)

たとえば、接続モニター (プレビュー) でソース IP が 10.192.64.56 であるすべてのテストを確認するには:
1. ビューを **[テスト]** に変更します。
1. 検索フィールドに、「*10.192.64.56*」と入力します
1. ドロップダウン リストで、 **[ソース]** を選択します。

接続モニター (プレビュー) でソース IP が 10.192.64.56 である失敗したテストだけを表示するには、次のようにします。
1. ビューを **[テスト]** に変更します。
1. 状態ベースのフィルターで、 **[失敗]** を選択します。
1. 検索フィールドに、「*10.192.64.56*」と入力します
1. ドロップダウン リストで、 **[ソース]** を選択します。

接続モニター (プレビュー) でターゲットが outlook.office365.com である失敗したテストだけを表示するには:
1. ビューを **[テスト]** に変更します。
1. 状態ベースのフィルターで、 **[失敗]** を選択します。
1. 検索フィールドに、「*outlook.office365.com*」と入力します
1. ドロップダウン リストで、 **[ターゲット]** を選択します。

   ![ターゲット Outlook.Office365.com に対する失敗したテストのみを表示するようにフィルター処理されたビューを示すスクリーンショット](./media/connection-monitor-2-preview/tests-view.png)

接続モニターの RTT の傾向と失敗したチェックの割合を表示するには:
1. 調査する接続モニターを選択します。 既定では、監視データはテスト グループ別に整理されています。

   ![テスト グループ別に表示された、接続モニターのメトリックを示すスクリーンショット](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. 調査するテスト グループを選択します。

   ![テスト グループを選択する場所を示すスクリーンショット](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    RTT または失敗したチェックの割合に基づいて、テスト グループの失敗したテストの上位 5 件が表示されます。 テストごとに、RTT と失敗したチェックの割合の傾向線が表示されます。
1. 一覧からテストを選択するか、調査する別のテストを選択します。 時間間隔と失敗したチェックの割合について、しきい値と実際の値が表示されます。 RTT の場合、しきい値、平均値、最小値、および最大値が表示されます。

   ![RTT と失敗したチェックの割合のテスト結果を示すスクリーンショット](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. さらに多くのデータを表示するには、期間を変更します。
1. ソース、ターゲット、またはテスト構成を表示するには、ビューを変更します。 
1. 失敗したテストに基づいてソースを選択し、失敗したテストの上位 5 件を調査します。 たとえば、接続モニターで関連するテストを調査する場合は、 **[View by]\(表示方法\)**  >  **[ソース]** および **[View by]\(表示方法\)**  >  **[ターゲット]** を選択します。

   ![失敗したテストの上位 5 つのパフォーマンス メトリックを示すスクリーンショット](./media/connection-monitor-2-preview/cm-drill-select-source.png)

テスト グループの RTT の傾向と失敗したチェックの割合を表示するには:

1. 調査するテスト グループを選択します。 

    既定では、監視データは、ソース、ターゲット、およびテスト構成 (テスト) ごとに整理されています。 後で、テスト グループからソース、ターゲット、またはテスト構成にビューを変更できます。 次に、失敗したテストの上位 5 件を調査するエンティティを選択します。 たとえば、選択した接続モニターで関連するテストを調査するには、ビューをソースとターゲットに変更します。
1. 調査するテストを選択します。

   ![テストを選択する場所を示すスクリーンショット](./media/connection-monitor-2-preview/tg-drill.png)

    時間間隔と失敗したチェックの割合について、しきい値と実際の値が表示されます。 RTT の場合、しきい値、平均値、最小値、および最大値が表示されます。 また、選択したテストに対して生成されたアラートも表示されます。
1. さらに多くのデータを表示するには、期間を変更します。

テストの RTT の傾向と失敗したチェックの割合を表示するには:
1. 調査するソース、ターゲット、テスト構成を選択します。

    時間間隔と失敗したチェックの割合について、しきい値と実際の値が表示されます。 RTT の場合、しきい値、平均値、最小値、および最大値が表示されます。 また、選択したテストに対して生成されたアラートも表示されます。

   ![テストのメトリックを示すスクリーンショット](./media/connection-monitor-2-preview/test-drill.png)

1. ネットワーク トポロジを表示するには、 **[トポロジ]** を選択します。

   ![ネットワーク トポロジのタブを示すスクリーンショット](./media/connection-monitor-2-preview/test-topo.png)

1. 特定された問題を確認するには、トポロジでパスの任意のホップを選択します。 (これらのホップは Azure リソースです)。現在、この機能は、オンプレミスのネットワークに対しては使用できません。

   ![[トポロジ] タブで選択したホップ リンクを示すスクリーンショット](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Log Analytics でのログ クエリ

監視データのカスタム ビューを作成するには、Log Analytics を使用します。 UI に表示されるすべてのデータは、Log Analytics からのものです。 リポジトリ内のデータを対話形式で分析できます。 Log Analytics に基づく Agent Health または他のソリューションからのデータを関連付けます。 Excel や Power BI にデータをエクスポートするか、または共有可能なリンクを作成します。

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor のメトリック

接続モニター (プレビュー) エクスペリエンスの前に作成された接続モニターでは、全部で次の 4 つメトリックを使用できます: 失敗したプローブの割合、AverageRoundtripMs、ChecksFailedPercent (プレビュー)、RoundTripTimeMs (プレビュー)。 接続モニター (プレビュー) エクスペリエンスで作成された接続モニターでは、" *(プレビュー)* " というタグが付いたメトリックに対してのみデータを使用できます。

![接続モニター (プレビュー) のメトリックを示すスクリーンショット](./media/connection-monitor-2-preview/monitor-metrics.png)

メトリックを使用するときは、リソースの種類を Microsoft.Network/networkWatchers/connectionMonitors に設定します

| メトリック | Display name | ユニット | 集計の種類 | 説明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失敗したプローブの割合 | パーセント | Average | 失敗した接続監視プローブの割合。 | ディメンションなし |
| AverageRoundtripMs | Avg.ラウンド トリップ時間 (ミリ秒) | ミリ秒 | Average | ソースと接続先の間で送信された接続監視プローブのネットワーク RTT。 |             ディメンションなし |
| ChecksFailedPercent (プレビュー) | チェックの失敗率 (%) (プレビュー) | パーセント | Average | テストで失敗したチェックの割合。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>[DestinationName] <br>DestinationResourceId <br>[DestinationType] <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>リージョン |
| RoundTripTimeMs (プレビュー) | ラウンド トリップ時間 (ミリ秒) (プレビュー) | ミリ秒 | Average | ソースとターゲットの間で送信されたチェックの RTT。 これは平均値ではありません。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>[DestinationName] <br>DestinationResourceId <br>[DestinationType] <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>リージョン |

#### <a name="metric-alerts-in-azure-monitor"></a>Azure Monitor でのメトリック アラート

Azure Monitor でアラートを作成するには:

1. 接続モニター (プレビュー) で作成した接続モニター リソースを選択します。
1. 接続モニターの信号の種類として **[メトリック]** が表示されていることを確認します。
1. **[条件の追加]** で、 **[シグナル名]** として **[ChecksFailedPercent(Preview)]** または **[RoundTripTimeMs(Preview)]** を選択します。
1. **[シグナルの種類]** として、 **[メトリック]** を選択します。 たとえば、 **[ChecksFailedPercent(Preview)]** を選択します。
1. メトリックのすべてのディメンションが一覧表示されます。 ディメンション名とディメンション値を選択します。 たとえば、 **[送信元アドレス]** を選択し、接続モニターのソースの IP アドレスを入力します。
1. **[アラート ロジック]** に、次の詳細を入力します。
   * **[条件タイプ]** : **静的**。
   * **[条件]** と **[しきい値]** 。
   * **[集約粒度] と [評価の頻度]** : 接続モニター (プレビュー) で 1 分ごとにデータを更新します。
1. **[アクション]** で、アクション グループを選択します。
1. アラートの詳細を指定します。
1. アラート ルールを作成します。

   !["ソース アドレス" と "ソース エンド ポイント名" が強調して示されている Azure Monitor の [ルールの作成] 領域を示すスクリーンショット](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>ネットワークの問題を診断する

接続モニター (プレビュー) は、接続モニターとネットワークでの問題を診断するのに役立ちます。 ハイブリッド ネットワークの問題は、前にインストールした Log Analytics エージェントによって検出されます。 Azure の問題は、Network Watcher の拡張機能によって検出されます。 

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
