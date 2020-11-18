---
title: 接続モニターを作成する - PowerShell
titleSuffix: Azure Network Watcher
description: PowerShell を使用して接続モニターを作成する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 532f045233f26a9a2933a19ae7a0a893195ad33f
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2020
ms.locfileid: "94384056"
---
# <a name="create-a-connection-monitor-using-powershell"></a>PowerShell を使用して接続モニターを作成する

リソース間の通信を監視するために、PowerShell を使用して接続モニターを作成する方法について説明します。

## <a name="before-you-begin"></a>開始する前に 

接続モニター (プレビュー) で作成できる接続モニターでは、オンプレミスのマシンと Azure VM の両方をソースとして追加できます。 これらの接続モニターでは、エンドポイントへの接続も監視できます。 エンドポイントは、Azure または他の任意の URL や IP 上のどこにあってもかまいません。

接続モニターには、次のエンティティが含まれています。

* **接続モニター リソース** – リージョン固有の Azure リソース。 以下のすべてのエンティティが、接続モニター リソースのプロパティです。
* **エンドポイント** –接続チェックに含まれるソースまたはターゲット。 エンドポイントの例としては、Azure VM、オンプレミスのエージェント、URL、IP などがあります。
* **テスト構成** – テスト用のプロトコル固有の構成。 選択したプロトコルに基づいて、ポート、しきい値、テストの頻度、その他のパラメーターを定義できます。
* **テスト グループ** – ソース エンドポイント、ターゲット エンドポイント、テスト構成が含まれるグループ。 接続モニターには、複数のテスト グループを含めることができます。
* **テスト** – ソース エンドポイント、ターゲット エンドポイント、テスト構成の組み合わせ。 テストは、監視データを入手できる最も小さいレベルです。 監視データには、失敗したチェックの割合と、ラウンドトリップ時間 (RTT) が含まれます。

    ![テスト グループとテストの関係が定義されている接続モニターを示す図](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>PowerShell を使用して作成する手順

PowerShell を使用して接続モニターを作成するには、次のコマンドを使用します。

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>プロパティの説明

* connectionMonitorName - 接続モニター リソースの名前

* SUB - 接続モニターを作成したいサブスクリプションのサブサブスクリプション ID

* NW - CM が作成される Network Watcher リソース ID 

* location - 接続モニターが作成されるリージョン

* エンドポイント
    * name – 各エンドポイントの一意の名前
    * resourceId – Azure エンドポイントの場合、リソース ID は仮想マシンの Azure Resource Manager リソース ID を示します。Azure 以外のエンドポイントの場合、リソース ID は、Azure 以外のエージェントにリンクされている Log Analytics ワークスペースの Azure Resource Manager のリソース ID を示します。
    * address – リソース ID が指定されていない場合、またはリソース ID が Log Analytics ワークスペースである場合にのみ適用されます。 Log Analytics リソース ID と共に使用される場合、これは監視に使用できるエージェントの FQDN を示します。 リソース ID なしで使用される場合、これは、任意のパブリック エンドポイントの URL または IP とすることができます。
    * filter – Azure 以外のエンドポイントの場合は、フィルターを使用して、接続モニター リソース内で監視に使用される Log Analytics ワークスペースからエージェントを選択します。 フィルターが設定されていない場合、Log Analytics ワークスペースに属するすべてのエージェントを監視に使用できます。
        * type – type は "エージェント アドレス" として設定します。
        * address – オンプレミスのエージェントの FQDN としてアドレスを設定します。

* テスト グループ
    * name – テスト グループの名前を指定します。
    * testConfigurations - テスト構成であり、これに基づいてソース エンドポイントはターゲット エンドポイントに接続されます。
    * sources - 上記で作成したエンドポイントから選択します。 Azure ベースのソース エンドポイントには Azure Network Watcher 拡張機能がインストールされている必要があり、Azure 以外をベースとしたソース エンドポイントには Azure Log Analytics エージェントがインストールされている必要があります。 ソース用のエージェントをインストールするには、「[監視エージェントをインストールする](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)」を参照してください。
    * destinations - 前述で作成したエンドポイントから選択します。 Azure VM または任意のエンドポイント (パブリック IP、URL、FQDN) をターゲットとして指定すると、それらへの接続を監視できます。 1 つのテスト グループに、Azure VM、Office 365 URL、Dynamics 365 URL、カスタム エンドポイントを追加できます。
    * disable - このフィールドを使用すると、テスト グループで指定されているすべてのソースとターゲットに対する監視を無効にできます。

* テスト構成
    * name - テスト構成の名前です。
    * testFrequencySec - 指定したプロトコルとポートでソースがターゲットに ping を実行する頻度を指定します。 30 秒、1 分、5 分、15 分、30 分のいずれかを選択できます。 ソースでは、選択した値に基づいて、ターゲットへの接続がテストされます。 たとえば、30 秒を選択すると、ソースにより、少なくとも 30 秒に 1 回、ターゲットへの接続が確認されます。
    * protocol - TCP、ICMP、HTTP、または HTTPS を選択できます。 プロトコルによっては、いくつかのプロトコル固有の構成を行うことができます。
        * preferHTTPS - HTTP 経由で HTTPS を使用するかどうかを指定します。
        * port - 任意の宛先ポートを指定します。
        * disableTraceRoute - これは、プロトコルが TCP または ICMP であるテスト グループに適用されます。 これによって、ソースでのトポロジとホップバイホップ RTT の検出が停止します。
    * successThreshold - 以下のネットワーク パラメーターにしきい値を設定できます。
        * checksFailedPercent - 指定した条件で行われるソースによるターゲットへの接続の確認で、失敗してもかまわないチェックの割合を設定します。 TCP または ICMP プロトコルでは、失敗したチェックの割合はパケット損失の割合と同等と考えられます。 HTTP プロトコルでは、このフィールドは、応答を受け取らなかった HTTP 要求の割合を表します。
        * roundTripTimeMs - テスト構成でソースがターゲットに接続するのにかかるラウンドトリップ時間をミリ秒で設定します。

## <a name="scale-limits"></a>スケールの上限

接続モニターには、次のようなスケール制限があります。

* 1 つのリージョンの 1 つのサブスクリプションあたりの接続モニターの最大数: 100
* 接続モニターあたりのテスト グループの最大数: 20
* 接続モニターあたりのソースとターゲットの最大数: 100
* 接続モニターあたりのテスト構成の最大数:

## <a name="next-steps"></a>次のステップ

* [監視データを分析してアラートを設定する方法](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)を確認する
* [ネットワークの問題を診断する方法](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)を確認する
