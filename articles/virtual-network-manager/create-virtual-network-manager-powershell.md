---
title: 'クイック スタート: Azure PowerShell を使用して Azure Virtual Network Manager を使用したメッシュ ネットワークを作成する'
description: このクイックスタートを使用して、Azure PowerShell を使用して Virtual Network Manager でメッシュ ネットワークを作成する方法を学習します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021
ms.openlocfilehash: effbdd0482e40535793e2c60dcdce3b4cdb518e6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466467"
---
# <a name="quickstart-create-a-mesh-network-with-azure-virtual-network-manager-using-azure-powershell"></a>クイック スタート: Azure PowerShell を使用して Azure Virtual Network Manager を使用したメッシュ ネットワークを作成する

Azure PowerShell を使用して、Azure Virtual Network Manager で仮想ネットワークの接続の管理を始めます。

このクイックスタートでは、3 つの仮想ネットワークをデプロイし、Azure Virtual Network Manager を使用してメッシュ ネットワーク トポロジを作成します。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 最新の Azure PowerShell モジュールがあることを確認してください。ポータルで Azure Cloud Shell を使用することもできます。
* PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="register-subscription-for-public-preview"></a>サブスクリプションをパブリック プレビューに登録する

次のコマンドを使用して、Azure サブスクリプションを Azure Virtual Network Manager のパブリック プレビューに登録します。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowAzureNetworkManager -ProviderNamespace Microsoft.Network
```

## <a name="install-azure-powershell-module"></a>Azure PowerShell モジュールをインストールする

次のコマンドを使用して、最新の *Az.Network* Azure PowerShell モジュールをインストールします。

```azurepowershell-interactive
Install-Module -Name Az.Network -AllowPrerelease
```

## <a name="create-virtual-network-manager"></a>Virtual Network Manager を作成する

1. この Azure Virtual Network Manager インスタンスが持つスコープとアクセスの種類を定義します。 スコープは、サブスクリプション グループまたは管理グループ、またはこれら両方の組み合わせを使用して作成できます。 [New-AzNetworkManagerScope](/powershell/module/az.network/new-aznetworkmanagerscope) を使用してスコープを作成します。

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$subGroup = @()  
    $group.Add("/subscriptions/abcdef12-3456-7890-abcd-ef1234567890")
    [System.Collections.Generic.List[string]]$mgGroup = @()  
    $group.Add("/managementGroups/abcdef12-3456-7890-abcd-ef1234567890")
    
    [System.Collections.Generic.List[String]]$access = @()  
    $access.Add("Connectivity");  
    $access.Add("Security"); 
 
    $scope = New-AzNetworkManagerScope -Subscription $subGroup  -ManagementGroup $mgGroup
    ```

1. [New-AzNetworkManager](/powershell/module/az.network/new-aznetworkmanager) を使用して Azure Virtual Network Manager を作成します。 この例では、**myAVNM** という名前の Azure Virtual Network Manager を米国西部の場所に作成します。

    ```azurepowershell-interactive
    $avnm = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerScope = $scope.id
        NetworkManagerScopeAccess = $access
        Location = 'West US'
    }
    $networkmanager = New-AzNetworkManager @avnm
    ```

## <a name="create-resource-group-and-virtual-networks"></a>リソース グループと仮想ネットワークを作成する

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure Virtual Network Manager を作成する前に、ネットワーク マネージャーをホストするリソース グループを作成する必要があります。 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して Azure リソース グループを作成します。 次の例では、**myAVNMResourceGroup** という名前のリソース グループを場所 **WestUS** に作成します。

```azurepowershell-interactive
$rg = @{
    Name = 'myAVNMResourceGroup'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-three-virtual-networks"></a>3 つの仮想ネットワークを作成する

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して、3 つの仮想ネットワークを作成します。 この例では、**VNetA**、**VNetB**、**VNetC** という名前の仮想ネットワークを **米国西部** の場所に作成します。 メッシュ ネットワークと一緒に作成する仮想ネットワークが既にある場合は、次のセクションに進めます。

```azurepowershell-interactive
$vnetA = @{
    Name = 'VNetA'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetworkA = New-AzVirtualNetwork @vnetA

$vnetB = @{
    Name = 'VNetB'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.1.0.0/16'    
}
$virtualNetworkB = New-AzVirtualNetwork @vnetB

$vnetC = @{
    Name = 'VNetC'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.2.0.0/16'    
}
$virtualNetworkC = New-AzVirtualNetwork @vnetC
```

### <a name="add-a-subnet-to-each-virtual-network"></a>各仮想ネットワークにサブネットを追加します。

仮想ネットワークの構成を完了するには、/24 サブネットをそれぞれに追加します。 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) を使用して、**default** というサブネット構成を作成します。

```azurepowershell-interactive
$subnetA = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkA
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigA = Add-AzVirtualNetworkSubnetConfig @subnetA
$virtualnetworkA | Set-AzVirtualNetwork

$subnetB = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkB
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetB
$virtualnetworkB | Set-AzVirtualNetwork

$subnetC = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkC
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetC
$virtualnetworkC | Set-AzVirtualNetwork
```

## <a name="create-a-network-group"></a>ネットワーク グループを作成する

### <a name="static-membership"></a>静的メンバーシップ

1. [New-AzNetworkManagerGroupMembersItem](/powershell/module/az.network/new-aznetworkmanagergroupmembersitem) を使用して静的仮想ネットワーク メンバーを作成します。

    ```azurepowershell-interactive
    $member = New-AzNetworkManagerGroupMembersItem –ResourceId "/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA"
    ```

1. 次のコマンドを使用して、静的メンバーを静的メンバーシップ グループに追加します。

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerGroupMembersItem]]$groupMembers = @()  
    $groupMembers.Add($member)
    ```

### <a name="dynamic-membership"></a>動的メンバーシップ

1. 条件付きステートメントを定義し、変数に格納します。

    ```azurepowershell-interactive
    $conditionalMembership = '{ 
        "allof":[ 
            { 
            "field": "name", 
            "contains": "VNet" 
            } 
        ] 
    }' 
    ```

1. [New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup) を使用して、最後の手順で定義した条件付きステートメントを使用してネットワーク グループを作成します。

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetwork'
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-configuration"></a>構成を作成する

1. [New-AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem) を使用して、ネットワーク グループを追加する接続グループ項目を作成します。

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = $networkgroup.Id
    }
    $groupItem = New-AzNetworkManagerConnectivityGroupItem @gi
    ```

1. 構成グループを作成し、前の手順のグループ項目を追加します。

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($groupItem)
    ```

1. [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration) を使用して、接続構成を作成します。

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'Mesh'
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config
     ```

## <a name="commit-deployment"></a>デプロイをコミットする

[Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit) を使用して、ターゲット リージョンに構成をコミットします。

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$target = @()   
$target.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $target
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment 
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Virtual Network Manager が不要になった場合は、リソースを削除する前に、次のすべてに当てはまることを確認する必要があります。

* どのリージョンにも構成のデプロイはありません。
* すべての構成が削除されました。
* すべてのネットワーク グループが削除されました。

1. [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit) を使用して空の構成をデプロイして、接続のデプロイを削除します。

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$configIds = @()
    [System.Collections.Generic.List[string]]$target = @()   
    $target.Add("westus")     
    $removedeployment = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        ConfigurationId = $configIds
        Target = $target
        CommitType = 'Connectivity'
    }
    Deploy-AzNetworkManagerCommit @removedeployment
    ```

1. [Remove-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/remove-aznetworkmanagerconnectivityconfiguration) を使用して接続構成を削除する

    ```azurepowershell-interactive
    $removeconfig = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerConnectivityConfiguration @removeconfig   
    ```

1. [Remove-AzNetworkManagerGroup](/powershell/module/az.network/remove-aznetworkmanagergroup) を使用してネットワーク グループを削除します。

    ```azurepowershell-interactive
    $removegroup = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerGroup @removegroup
    ```

1. [Remove-AzNetworkManager](/powershell/module/az.network/remove-aznetworkmanager) を使用してネットワーク マネージャー インスタンスを削除します。

    ```azurepowershell-interactive
    $removenetworkmanager = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
    }
    Remove-AzNetworkManager @removenetworkmanager
    ```

1. 作成したリソースが不要になったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用してリソース グループを削除します。

    ```azurepowershell-interactive
    Remove-AzResourceGroup -Name 'myAVNMResourceGroup'
    ```

## <a name="next-steps"></a>次のステップ

Azure Virtual Network Manager を作成したら、セキュリティ管理者の構成を使用してネットワーク トラフィックをブロックする方法についての学習に進みます。

> [!div class="nextstepaction"]
> [セキュリティ管理者規則を使用してネットワーク トラフィックをブロックする](how-to-block-network-traffic-powershell.md)
