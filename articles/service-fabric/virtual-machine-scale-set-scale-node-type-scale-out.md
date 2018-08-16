---
title: 仮想マシン スケール セットを追加して Azure Service Fabric クラスターをスケールアウトする | Microsoft Docs
description: 仮想マシン スケール セットを追加することで Service Fabric クラスターをスケーリングする方法を説明します。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: ryanwi
ms.openlocfilehash: cad3723f3109fa2fa7e6a1a7ab61d5c7eaca2674
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623190"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを追加して Service Fabric クラスターをスケールアウトする
この記事では、既存のクラスターに新しい仮想マシン スケール セットを追加することで、Azure Service Fabric クラスターをスケーリングする方法について説明します。 Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM を "ノード" と呼びます。 仮想マシン スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用する Azure コンピューティング リソースです。 Azure クラスターで定義されているすべてのノードの種類は、[異なるスケール セットとしてセットアップされます](service-fabric-cluster-nodetypes.md)。 その後は、ノードの種類ごとに個別に管理できます。 Service Fabric クラスターを作成した後は、クラスターのノード タイプを垂直方向にスケーリング (ノードのリソースを変更) したり、ノード タイプの VM のオペレーティング システムをアップグレードしたり、新しい仮想マシン スケール セットを既存のクラスターに追加したりすることができます。  クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。  クラスターをスケーリングすると、アプリケーションも自動的にスケーリングされます。

> [!WARNING]
> [Silver 以上の耐久性](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)で実行されている場合を除き、スケール セット/特定の種類のノードの VM SKU は変更しないことをお勧めします。 VM SKU サイズの変更は、データを破壊する、インプレース インフラストラクチャ操作です。 この変更を遅らせたり監視したりする機能がないと、この操作により、ステートレス サービスのデータの消失が発生する可能性があります。また、ステートレス ワークロードに対しても、他の予期できない運用上の問題が発生する可能性があります。 つまり、ステートフルなサービス ファブリック システム サービスを実行しているプライマリ ノード タイプまたはステートフルなアプリケーション ワークロードを実行している任意のノード タイプです。
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>プライマリ ノード タイプの VM のサイズとオペレーティング システムをアップグレードする
ここでは、プライマリ ノード タイプの VM の VM サイズとオペレーティング システムを更新するプロセスについて説明します。  アップグレード後、プライマリ ノード タイプの VM は、サイズが Standard D4_V2 で、コンテナー搭載 Windows Server 2016 Datacenter を実行します。

> [!WARNING]
> 運用クラスターでこの手順を実行する前に、サンプル テンプレートを調べて、テスト クラスターに対してプロセスを検証することをお勧めします。 クラスターも一時的に使用できなくなります。

1. これらのサンプル [テンプレート](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json)および[パラメーター](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) ファイルを使用して、2 つのノード タイプと 2 つのスケール セット (ノード タイプごとに 1 つのスケール セット) を持つ初期クラスターを展開します。  どちらのスケール セットも、サイズは Standard D2_V2 で、Windows Server 2012 R2 Datacenter を実行しています。  クラスターがベースラインのアップグレードを完了するまで待ちます。   
2. (省略可能) ステートフル サンプルをクラスターに展開します。
3. プライマリ ノード タイプの VM をアップグレードすることを決定したら、これらのサンプル [テンプレート](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json)および[パラメーター](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) ファイルを使用してプライマリ ノード タイプに新しいスケール セットを追加して、プライマリ ノード タイプが 2 つのスケール セットを持つようにします。  システム サービスとユーザー アプリケーションは、2 つの異なるスケール セットの VM 間で移行できます。  新しいスケール セットの VM は、サイズが Standard D4_V2 で、コンテナー搭載 Windows Server 2016 Datacenter を実行します。  新しいスケール セットと共に新しいロードバランサーとパブリック IP アドレスも追加されます。  
    テンプレートで新しいスケール セットを見つけるには、*vmNodeType2Name* パラメーターに指定されている "Microsoft.Compute/virtualMachineScaleSets" リソースを探します。  新しいスケール セットは、properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef 設定を使用してプライマリ ノード タイプに追加されます。
4. クラスターの正常性を確認し、すべてのノードが正常であることを確認します。
5. プライマリ ノード タイプの以前のスケール セットに含まれるノードは、削除するために無効にします。 一度にすべてを無効にすることができます。この操作はキューに格納されます。 すべてのノードが無効になるまで待ちます。この操作には時間がかかることがあります。  ノード タイプの以前のノードが無効になると、システム サービスとシード ノードによって、プライマリ ノード タイプに含まれる新しいスケール セットの VM は移行されます。
6. プライマリ ノード タイプから以前のスケール セットを削除します。
7. 以前のスケール セットに関連付けられているロード バランサーを削除します。 新しいパブリック IP アドレスとロード バランサーが新しいスケール セット用に構成されている間、クラスターは使用できません。  
8. 以前のプライマリ ノード タイプのスケール セットに関連付けられたパブリック IP アドレスの DNS 設定を変数に格納し、そのパブリック IP アドレスを削除します。
9. 新しいプライマリ ノード タイプのスケール セットに関連付けられたパブリック IP アドレスの DNS 設定を、削除されたパブリック IP アドレスの DNS 設定に置き換えます。  これで、クラスターに再びアクセスできるようになります。
10. クラスターからノードのノード状態を削除します。  以前のスケール セットの持続性レベルがシルバーまたはゴールドの場合、この手順はシステムによって自動的に実行されます。
11. 前の手順でステートフル アプリケーションをデプロイした場合は、そのアプリケーションが機能していることを確認します。

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="adding-an-additional-scale-set-to-an-existing-cluster"></a>既存クラスターへの新しいスケール セットの追加
既存クラスターへの新しい NodeType 仮想マシン スケール セットの追加は前に説明したプライマリ ノード タイプのアップグレードと似ていますが、同じ NodeTypeRef を使わないこと、明らかにアクティブに使われている仮想マシン スケール セットを無効にしないこと、およびプライマリ ノード タイプを更新しない場合はクラスターの可用性を失わないことが異なります。 

NodeTypeRef プロパティは、仮想マシン スケール セット Service Fabric 拡張機能プロパティ内で宣言されています。
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

さらに、この新しいノード タイプを Service Fabric クラスター リソースに追加する必要があります。

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>次の手順
* [アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)について学習します。
* [Azure クラスターをスケールインまたはスケールアウト](service-fabric-tutorial-scale-cluster.md)します。
* fluent Azure コンピューティング SDK を使用して [Azure クラスターをプログラムでスケーリングします](service-fabric-cluster-programmatic-scaling.md)。
* [スタンドアロン クラスターをスケールインまたはスケールアウト](service-fabric-cluster-windows-server-add-remove-nodes.md)します。

