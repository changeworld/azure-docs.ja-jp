---
title: Azure ExpressRoute の接続モニターの構成
description: Azure ExpressRoute 回線に対してクラウドベースのネットワーク接続の監視を構成します。 ここでは、ExpressRoute プライベート ピアリングと Microsoft ピアリング経由での監視について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/28/2021
ms.author: duau
ms.openlocfilehash: 794e841d8d50683374c86301ec55bc4f6ab53ec6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779636"
---
# <a name="configure-connection-monitor-for-expressroute"></a>ExpressRoute の接続モニターの構成

この記事は、ExpressRoute を監視するための接続モニターの拡張機能を構成するのに役立ちます。 接続モニターは、Azure クラウド デプロイとオンプレミス拠点 (支社など) との間の接続性を監視するクラウドベースのネットワーク監視ソリューションです。 接続モニターは、Azure Monitor ログの一部です。  この拡張機能を使用すると、プライベートおよび Microsoft ピアリング接続のネットワーク接続を監視することもできます。 ExpressRoute で接続モニターを構成すると、特定して排除すべきネットワークの問題を検出することができるようになります。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

ExpressRoute の接続モニターを使用すると、以下を行うことができます。

* 各種 VNet 間の損失と待ち時間を監視し、アラートを設定する。

* ネットワーク上のすべてのパス (冗長パスを含む) を監視する。

* 再現が難しい、ネットワークに関する一過性の問題と特定時点の問題をトラブルシューティングする。

* パフォーマンス低下の原因となっている特定のセグメントをネットワーク上で見つけ出す。

## <a name="workflow"></a><a name="workflow"></a> ワークフロー

監視エージェントは、複数のサーバー (オンプレミスと Azure の両方) にインストールされます。 エージェントは、TCP ハンドシェイクパケットを送信することによって相互に通信します。 エージェント間の通信によって、Azure は、トラフィックが通過する可能性のある経路とネットワーク トポロジとをマッピングすることができます。

1. Log Analytics ワークスペースを作成します。

1. ソフトウェア エージェントをインストールして構成します (監視を Microsoft ピアリング経由でのみ実行する場合は、ソフトウェア エージェントのインストールと構成は必要ありません)。

    * オンプレミス サーバーと Azure VM に監視エージェントをインストールします (プライベート ピアリングの場合)。
    * 監視エージェントが通信を行うことができるように、監視エージェント サーバー上の設定を構成します (ファイアウォール ポートを開放するなど)。

1. Azure VM にインストールされている監視エージェントがオンプレミスの監視エージェントと通信を行うことができるようにネットワーク セキュリティ グループ (NSG) 規則を構成します。

1. サブスクリプションで Network Watcher を有効にします。

1. 監視を設定する: テスト グループを使用して接続モニターを作成し、ネットワーク全体のソースとターゲット エンドポイントを監視します。

既に Network Performance Monitor (非推奨) または接続モニターを使用して他のオブジェクトやサービスを監視していて、サポートされているリージョンのいずれかに既に Log Analytics ワークスペースがある場合。 手順 1 と手順 2 を省略して、手順 3 から構成を開始することができます。

## <a name="create-a-workspace"></a><a name="configure"></a> ワークスペースの作成

ExpressRoute 回線への VNet リンクを含んだサブスクリプションにワークスペースを作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。 ExpressRoute 回線に接続されている仮想ネットワークがあるサブスクリプションから、 **[+ リソースの作成]** を選択します。 *[Log Analytics ワークスペース]* を検索してから、 **[作成]** を選択します。

   >[!NOTE]
   >新しいワークスペースを作成するか、既存のワークスペースを使用することができます。 既存のワークスペースを使用する場合は、ワークスペースが新しいクエリ言語に移行されていることを確認する必要があります。 [詳細情報...](../azure-monitor/logs/log-query-overview.md)
   >

    :::image type="content" source="./media/how-to-configure-connection-monitor/search-log-analytics.png" alt-text="リソースの作成での Log Analytics の検索を示すスクリーンショット。":::

1. 次の情報を入力または選択して、ワークスペースを作成します。  

    | 設定 | 値 |
    | -------- | ----- |
    | サブスクリプション | ExpressRoute 回線があるサブスクリプションを選択します。 |
    | リソース グループ | リソース グループを新規で作成するか、既存のものを選択します。 |
    | 名前 | このワークスペースを識別する名前を入力します。 |
    | リージョン | このワークスペースが作成されるリージョンを選択します。 |

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-workspace-basic.png" alt-text="[Log Analytics ワークスペースの作成] の [基本] タブのスクリーンショット。":::

     >[!NOTE]
     >ExpressRoute 回線は、世界のどこに配置されていてもかまいません。 ワークスペースと同じリージョンに存在する必要はありません。
     >

1. **[確認と作成]** を選択し検証し、 **[作成]** を選択してワークスペースをデプロイします。 ワークスペースがデプロイされたら、次のセクションに進み、監視ソリューションを構成します。

## <a name="configure-monitoring-solution"></a><a name="npm"></a>監視ソリューションの構成

*$SubscriptionId*、 *$location*、 *$resourceGroup*、および *$workspaceName* の値を置き換えて、以下の Azure PowerShell スクリプトを完成させます。 その後、スクリプトを実行して監視ソリューションを構成します。

```azurepowershell-interactive
$subscriptionId = "Subscription ID should come here"
Select-AzSubscription -SubscriptionId $subscriptionId

$location = "Workspace location should come here"
$resourceGroup = "Resource group name should come here"
$workspaceName = "Workspace name should come here"

$solution = @{
    Location          = $location
    Properties        = @{
        workspaceResourceId = "/subscriptions/$($subscriptionId)/resourcegroups/$($resourceGroup)/providers/Microsoft.OperationalInsights/workspaces/$($workspaceName)"
    }
    Plan              = @{
        Name          = "NetworkMonitoring($($workspaceName))" 
        Publisher     = "Microsoft"
        Product       = "OMSGallery/NetworkMonitoring"
        PromotionCode = ""
    }
    ResourceName      = "NetworkMonitoring($($workspaceName))" 
    ResourceType      = "Microsoft.OperationsManagement/solutions" 
    ResourceGroupName = $resourceGroup
}

New-AzureRmResource @solution -Force
```

監視ソリューションを構成したら、 サーバーに監視エージェントをインストールして構成する次の手順に進みます。

## <a name="install-and-configure-agents-on-premises"></a><a name="agents"></a>オンプレミスでのエージェントのインストールと構成

### <a name="download-the-agent-setup-file"></a><a name="download"></a>エージェントのセットアップ ファイルをダウンロードする

1. **Log Analytics ワークスペース** に移動して、 *[設定]* から **[エージェント管理]** を選択します。 マシンのオペレーティング システムに対応するエージェントをダウンロードします。

    :::image type="content" source="./media/how-to-configure-connection-monitor/download-agent.png" alt-text="ワークスペースのエージェント管理ページのスクリーンショット。":::

1. 次に、 **[ワークスペース ID]** と **[主キー]** をメモ帳にコピーします。

    :::image type="content" source="./media/how-to-configure-connection-monitor/copy-id-key.png" alt-text="ワークスペース ID と主キーのスクリーンショット。":::

1. Windows マシンの場合は、この powershell スクリプト [*EnableRules.ps1*](https://aka.ms/npmpowershellscript) をダウンロードして、管理者特権を使用して PowerShell ウィンドウで実行します。 この PowerShell スクリプトを使用すると、TCP トランザクションに必要なファイアウォール ポートが開きます。 
 
    Linux マシンの場合は、次の手順を実行してポート番号を手動で変更する必要があります。

    * パス (/var/opt/microsoft/omsagent/npm_state) に移動します。
    * ファイル (npmdregistry) を開きます
    * ポート番号 `PortNumber:<port of your choice>` の値を変更します

### <a name="install-log-analytics-agent-on-each-monitoring-server"></a><a name="installagentonprem"></a> 各監視サーバーに Log Analytics エージェントをインストールする

冗長性を確保するために、ExpressRoute 接続の両側にある少なくとも 2 台のサーバーに Log Analytics エージェントをインストールすることをお勧めします。 たとえば、オンプレミスと Azure 仮想ネットワークなどです。 次の手順を使用してエージェントをインストールします。

1. サーバーに Log Analytics エージェントをインストールする手順については、以下の該当するオペレーティング システムを選択してください。

    * [Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)
    * [Linux](../azure-monitor/agents/agent-linux.md)

1. 完了すると、コントロール パネルに Microsoft Monitoring Agent が表示されます。 構成を検証して、Azure Monitor ログへの[エージェントの接続性](../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor)を確認できます。

1. 監視に使用する他のオンプレミスのマシンで、手順 1 と 2 を繰り返します。

### <a name="install-network-watcher-agent-on-each-monitoring-server"></a><a name="installagentazure"></a>各監視サーバーに Network Watcher エージェントをインストールする

#### <a name="new-azure-virtual-machine"></a>新しい Azure 仮想マシン

VNet への接続を監視するために新しい Azure VM を作成している場合は、[VM の作成](../network-watcher/connection-monitor.md#create-the-first-vm)時に Network Watcher エージェントをインストールすることができます。

#### <a name="existing-azure-virtual-machine"></a>既存の Azure 仮想マシン

既存の VM を使用して接続を監視する場合は、[Linux](../virtual-machines/extensions/network-watcher-linux.md) と [Windows](../virtual-machines/extensions/network-watcher-windows.md) 用のネットワーク エージェントを別々にインストールすることができます。

### <a name="open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>監視エージェント サーバーのファイアウォール ポートを開放する

ファイアウォールの規則により、ソースとターゲットの間の通信がブロックされることがあります。 接続モニターを使うと、この問題を検出し、トポロジに診断メッセージとして表示できます。 接続監視を有効にするには、ファイアウォール規則で、ソースとターゲットの間の TCP または ICMP 経由のパケットが許可されるようにします。

#### <a name="windows"></a>Windows

Windows マシンの場合、PowerShell スクリプトを実行して、接続モニターに必要なレジストリ キーを作成できます。 また、このスクリプトにより、監視エージェントが互いに TCP 接続を作成することを許可する Windows ファイアウォール規則も作成されます。 スクリプトによって作成されたレジストリ キーは、デバッグ ログを記録するかどうかと、ログ ファイルのパスを指定します。 また、通信で使用されるエージェント TCP ポートを定義します。 これらのキーの値は、スクリプトによって自動的に設定されます。 これらのキーを手動で変更しないでください。

ポート 8084 は既定で開放されます。 パラメーター "portNumber" をスクリプトに指定することでカスタム ポートを使用できます。 ただし、その場合は、スクリプトの実行対象となるすべてのサーバーで同じポートを指定する必要があります。

>[!NOTE]
>PowerShell スクリプト "EnableRules" によって Windows ファイアウォール規則を構成できるのは、スクリプトを実行したコンピューターだけです。 ネットワーク ファイアウォールがある場合、接続モニターによって使用されている TCP ポート宛てのトラフィックを必ず許可する必要があります。
>

エージェント サーバー上で、管理特権で PowerShell ウィンドウを開きます。 あらかじめダウンロードしておいた PowerShell スクリプト [EnableRules](https://aka.ms/npmpowershellscript) を実行します。 パラメーターは一切使用しません。

:::image type="content" source="./media/how-to-configure-connection-monitor/enable-rules-script.png" alt-text="PowerShell ウィンドウでルールの有効化スクリプトを実行しているスクリーンショット。":::

#### <a name="linux"></a>Linux

Linux マシンの場合、使用するポート番号を手動で変更する必要があります。

1. パス (/var/opt/microsoft/omsagent/npm_state) に移動します。
1. ファイル (npmdregistry) を開きます
1. ポート番号 `PortNumber:\<port of your choice\>` の値を変更します。 使用するポート番号は、ワークスペースで使用されているすべてのエージェントで同じである必要があります。

## <a name="configure-network-security-group-rules"></a><a name="opennsg"></a>ネットワーク セキュリティ グループ規則を構成する

Azure 内のサーバーを監視するには、接続モニターからの TCP または ICMP トラフィックを許可するようにネットワーク セキュリティ グループ (NSG) 規則を構成する必要があります。 既定のポートは **8084 で、Azure VM にインストールされた監視エージェントがオンプレミスの監視エージェントと通信できるようになっています。

NSG の詳細については、[ネットワーク トラフィックのフィルター処理](../virtual-network/tutorial-filter-network-traffic.md)に関するチュートリアルを参照してください。

> [!NOTE]
> この手順に進む前に、エージェント (オンプレミス サーバー エージェントと Azure サーバー エージェントの両方) がインストール済みであること、また PowerShell スクリプトを実行済みであることを確認してください。
>

## <a name="enable-network-watcher"></a><a name="enablenetworkwatcher"></a>Network Watcher を有効にする

仮想ネットワークが含まれるすべてのサブスクリプションは、Network Watcher で有効になります。 Network Watcher がサブスクリプションで明示的に無効になっていないことを確認してください。 詳細については、[Network Watcher の有効化](../network-watcher/network-watcher-create.md)に関する記事をご覧ください。

## <a name="create-a-connection-monitor"></a><a name="createcm"></a> 接続モニターの作成

ネットワーク内のソースおよびターゲット エンドポイント全体で接続モニター、テスト、およびテスト グループを作成する方法の概要については、[接続モニターの作成](../network-watcher/connection-monitor-create-using-portal.md)に関する記事を参照してください。 プライベート ピアリングと Microsoft ピアリングの接続監視を構成するには、次の手順に従います。

1. Azure portal で、**Network Watcher** リソースに移動し、 *[監視]* の下の **[接続モニター]** を選択します。 次に、 **[作成]** を選択して、新しい接続モニターを作成します。

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-connection-monitor.png" alt-text="Network Watcher の接続モニターのスクリーンショット。":::

1. 作成ワークフローの **[基本]** タブにある *[リージョン]* フィールドで、Log Analytics ワークスペースのデプロイ先と同じリージョンを選択します。 *[ワークスペースの構成]* には、先ほど作成した既存の Log Analytics ワークスペースを選択します。 次に、 **[次へ: テスト グループ >>]** を選択します。

    :::image type="content" source="./media/how-to-configure-connection-monitor/connection-monitor-basic.png" alt-text="接続モニターを作成するための [基本] タブのスクリーンショット。":::

1. [テスト グループの詳細の追加] ページで、テスト グループのソース エンドポイントとターゲット エンドポイントを追加します。 また、これらの間のテスト構成も設定します。 このテスト グループの **名前** を入力します。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details.png" alt-text="[テスト グループの詳細の追加] ページのスクリーンショット。":::

1. **[ソースの追加]** を選択し、 **[Azure 以外のエンドポイント]** タブに移動します。接続を監視する Log Analytics エージェントがインストールされているオンプレミス リソースを選択し、 **[エンドポイントの追加]** を選択します。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-source-endpoints.png" alt-text="ソース エンドポイントを追加しているスクリーンショット。":::

1. 次に、 **[宛先を追加する]** を選択します。 

    ExpressRoute **プライベート ピアリング** 経由の接続を監視するには、 **[Azure エンドポイント]** タブに移動します。Azure の仮想ネットワークへの接続を監視する Network Watcher エージェントがインストールされている Azure リソースを選択します。 *[IP]* 列で、これらの各リソースのプライベート IP アドレスを選択していることを確認します。 **[エンドポイントの追加]** を選択して、これらのエンドポイントをテスト グループのターゲットの一覧に追加します。
    
    :::image type="content" source="./media/how-to-configure-connection-monitor/add-destination-endpoints.png" alt-text="Azure のターゲット エンドポイントを追加しているスクリーンショット。":::

    ExpressRoute **Microsoft ピアリング** 上の接続性をモニターするには、 **[外部アドレス]** タブに移動します。Microsoft ピアリング経由で接続を監視する Microsoft サービス エンドポイントを選択します。 **[エンドポイントの追加]** を選択して、これらのエンドポイントをテスト グループのターゲットの一覧に追加します。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-external-destination-endpoints.png" alt-text="外部のターゲット エンドポイントを追加しているスクリーンショット。":::

1. 次に、 **[テスト構成の追加]** を選択します。 プロトコルとして　 **[TCP]** を選択し、サーバーで開いている **宛先ポート** を入力します。 次に、 **[テストの頻度]** と **失敗したチェックとラウンド トリップ時間のしきい値** を構成します。 次に、 **[テスト構成の追加]** を選択します。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-configuration.png" alt-text="[テスト構成の追加] ページのスクリーンショット。":::

1. ソース、宛先、およびテスト構成を追加したら、 **[テスト グループの追加]** を選択します。

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details-configured.png" alt-text="構成した [テスト グループの追加] の詳細のスクリーンショット。" lightbox="./media/how-to-configure-connection-monitor/add-test-group-details-configured-expanded.png":::

1. アラートを作成する場合は、 **[次へ: アラートを作成する >>]** を選択します。 完了したら、 **[確認と作成]** 、 **[作成]** の順に選択します。

## <a name="view-results"></a>結果の表示

1. **Network Watcher** のリソースに移動し、 *[監視]* の下の **[接続モニター]** を選択します。 5 分後に新しい接続モニターが表示されます。 接続モニターのネットワーク トポロジとパフォーマンス グラフを表示するには、[テスト グループ] ドロップダウンからテストを選択します。

    :::image type="content" source="./media/how-to-configure-connection-monitor/overview.png" alt-text="接続モニターの [概要] ページのスクリーンショット。" lightbox="./media/how-to-configure-connection-monitor/overview-expanded.png":::

1. **[パフォーマンス分析]** パネルでは、失敗したチェックの割合と、ラウンドトリップ時間に対する各テストの結果を確認できます。 パネルの上部にあるドロップダウンを選択することで、表示されるデータの時間枠を調整できます。

    :::image type="content" source="./media/how-to-configure-connection-monitor/performance-analysis.png" alt-text="パフォーマンス分析パネルのスクリーンショット。" lightbox="./media/how-to-configure-connection-monitor/performance-analysis-expanded.png":::

1. **[パフォーマンス分析]** パネルを閉じると、選択したソースとターゲットのエンドポイント間の接続モニターによって検出されたネットワーク トポロジが表示されます。 このビューには、ソース エンドポイントとターゲット エンドポイント間のトラフィックの双方向のパスが表示されます。 また、パケットが Microsoft のエッジ ネットワークに到着するまでの、ホップバイホップの待機時間を確認することもできます。 

    :::image type="content" source="./media/how-to-configure-connection-monitor/topology.png" alt-text="接続モニターのネットワーク トポロジのスクリーンショット。" lightbox="./media/how-to-configure-connection-monitor/topology-expanded.png":::

    [トポロジ] ビューで任意のホップを選択すると、そのホップに関する追加情報が表示されます。 また、接続モニターによって検出されたホップに関する問題もここに表示されます。

    :::image type="content" source="./media/how-to-configure-connection-monitor/hop-details.png" alt-text="ネットワーク ホップの詳細情報のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[Azure ExpressRoute の監視](monitor-expressroute.md)の詳細を確認してください。