---
title: Azure で Service Fabric クラスターをスケーリングする | Microsoft Docs
description: このチュートリアルでは、Azure で Service Fabric クラスターをすばやくスケーリングする方法を説明します。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/06/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 0c5081960345e84cf48cb1ec07713cdaec8833a5
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008027"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>チュートリアル: Azure で Service Fabric クラスターをスケーリングする

このチュートリアルはシリーズ第 2 部で、既存のクラスターをスケールアウトおよびスケールインする方法を示します。 このチュートリアルを終了すると、クラスターをスケールする方法や残ったリソースをクリーンアップする方法について知ることができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * クラスター ノード数を読み取る
> * クラスター ノードを追加する (スケールアウト)
> * クラスター ノードを削除する (スケールイン)

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * テンプレートを使用して、セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成する
> * クラスターをスケールインまたはスケールアウトする
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * [Service Fabric を使用して API Management をデプロイする](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* [Azure PowerShell モジュールのバージョン 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)または [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールします。
* セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成します。
* Windows クラスターをデプロイする場合は、Windows 開発環境を設定します。 [Visual Studio 2017](http://www.visualstudio.com)、**Azure 開発**ワークロード、**ASP.NET および Web 開発**ワークロード、**.NET Core クロス プラットフォーム開発**ワークロードをインストールします。  その後、[.NET 開発環境](service-fabric-get-started.md)をセットアップします。
* Linux クラスターをデプロイする場合は、Java 開発環境を [Linux](service-fabric-get-started-linux.md) または [MacOS](service-fabric-get-started-mac.md) にセットアップします。  [Service Fabric CLI](service-fabric-cli.md) をインストールします。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>クラスターへの接続

チュートリアルのこのパートを正常に完了するには、Service Fabric クラスターと、(クラスターをホストする) 仮想マシン スケール セットの両方に接続する必要があります。 仮想マシン スケール セットとは、Azure で Service Fabric をホストする Azure リソースのことです。

クラスターに接続すると、クラスターでクエリを実行して情報を取得できます。 このクラスターを使用して、クラスターで認識されているノードを知ることができます。 次のコードでクラスターに接続する場合、このシリーズの最初のパートで作成したのと同じ証明書が使用されます。 値に変数 `$endpoint` と `$thumbprint` が設定されていることを確認してください。

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

これで接続されたので、コマンドを使用してクラスター内の各ノードの状態を取得できます。 **PowerShell** では `Get-ServiceFabricClusterHealth` コマンドを使用し、**sfctl** では `sfctl cluster select` コマンドを使用します。

## <a name="scale-out"></a>スケールアウト

スケールアウトする場合には、スケール セットに仮想マシン インスタンスを追加します。 これらのインスタンスは、Service Fabric で使用されるノードになります。 Service Fabric は、(スケールアウトによって) スケール セットにインスタンスがいつ追加されるかを認識し、自動的に反応します。 次のコードでは、スケール セットを名前で取得し、スケール セットの**容量**を 1 ずつ増やします。

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

このコードでは容量を 6 に設定します。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>スケールイン

スケールインは、使用する**容量**の値が小さい点以外はスケールアウトと同じです。 スケール セットをスケールインする場合、スケール セットから仮想マシン インスタンスを削除します。 通常、Service Fabric では発生している状況が認識できず、ノードがなくなったと認識します。 そのため、Service Fabric はクラスターに対して異常な状態を報告します。 そのような不適切な状態を防ぐためには、ノードがなくなることを Service Fabric に伝える必要があります。

### <a name="remove-the-service-fabric-node"></a>Service Fabric ノードの削除

> [!NOTE]
> このパートは *Bronze* 耐久性レベルにのみ適用されます。 耐久性の詳細については、[Service Fabric クラスターの容量計画][durability]に関する記事をご覧ください。

仮想マシン スケール セットをスケールインする場合、スケール セットでは (ほとんどの場合) 最後に作成された仮想マシン インスタンスが削除されます。 したがって、条件に合った最後に作成された Service Fabric ノードを検索する必要があります。 この最後のノードを検索するには、Service Fabric ノードで最大の `NodeInstanceId` プロパティ値をチェックします。 次のコードの例は、ノード インスタンスでソートし、最大の ID 値を持つインスタンスの詳細を返します。

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Service Fabric クラスターは、このノードが削除されることを認識する必要があります。 以下の 3 つの手順を実行する必要があります。

1. ノードを無効にして、ノードがデータのレプリケートとならないようにします。  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. ノードを停止して、Service Fabric ランタイムが完全にシャット ダウンされ、アプリが中断要求を取得できるようにします。  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. クラスターからノードを削除します。  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

これら 3 つの手順がノードに適用されたら、ノードをスケール セットから削除できます。 [Bronze][durability] 以外の耐久性レベルを利用している場合には、スケール セット インスタンスが削除されるときに、これらの手順が自動的に行われます。

次のコード ブロックは、最後に作成されたノードを取得して、そのノードを無効化して停止し、クラスターから削除します。

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

以下の **sfctl** コードでは、コマンド `sfctl node list --query "sort_by(items[*], &name)[-1].name"` を使用して、最後に作成されたノードの **node-name** 値を取得します。

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> 以下の **sfctl** クエリを使用して、各ステップの状態を確認します。
>
> **非アクティブ化状態を確認する**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **停止状態を確認する**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>スケール セットのスケールイン

Service Fabric ノードがクラスターから削除されたので、仮想マシン スケール セットをスケールインできます。 次の例では、スケール セットの容量が 1 ずつ減少します。

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

このコードでは容量を 5 に設定します。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * クラスター ノード数を読み取る
> * クラスター ノードを追加する (スケールアウト)
> * クラスター ノードを削除する (スケールイン)

次のチュートリアルでは、クラスターのランタイムをアップグレードする方法について説明します。
> [!div class="nextstepaction"]
> [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
