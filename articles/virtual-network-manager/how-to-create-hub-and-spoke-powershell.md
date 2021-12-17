---
title: Azure Virtual Network Manager (プレビュー) を使用してハブとスポークのトポロジを作成する-Azure PowerShell
description: Azure Virtual Network Manager で Azure PowerShell を使用してハブとスポークのネットワークトポロジを作成する方法について説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 8762a43608c52ceec3f8cb92e08f88a94c244e30
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092410"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Azure Virtual Network Manager (プレビュー) を使用してハブとスポークのトポロジを作成する-Azure PowerShell

この記事では、Azure Virtual Network Manager を使用してハブおよびスポーク ネットワーク トポロジを作成する方法について説明します。 この構成で、ハブとして機能する仮想ネットワークを選択します。すべてのスポーク仮想ネットワークで、既定でハブのみを使用する双方向ピアリングがあります。 また、スポーク仮想ネットワーク間の直接接続を有効にし、スポーク仮想ネットワークでハブの仮想ネットワーク ゲートウェイを使用できるようにすることもできます。

> [!IMPORTANT]
> 現在、*Azure Virtual Network Manager* は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳細については、「[**Microsoft Azure プレビューの追加使用条件**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

* [ハブおよびスポーク](concept-connectivity-configuration.md#hub-and-spoke-topology) ネットワーク トポロジについての説明を参照してください。
* [Azure Virtual Network Manager インスタンス](create-virtual-network-manager-powershell.md#create-virtual-network-manager)を作成します。
* ハブおよびスポーク構成で使用する仮想ネットワークを特定するか、新しい[仮想ネットワーク](../virtual-network/quick-create-powershell.md)を作成します。 

## <a name="create-a-network-group"></a>ネットワーク グループを作成する

このセクションでは、ハブ アンド スポーク ネットワーク トポロジに使用する仮想ネットワークを含むネットワーク グループを作成する方法について説明します。

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

1. 以前 [に New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup)を使用して定義した静的メンバーシップグループ (GroupMember) または動的メンバーシップグループ (conditionalmembership) を使用して、ネットワークグループを作成します。

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetworks
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>ハブおよびスポーク接続構成を作成する

このセクションでは、前のセクションで作成したネットワーク グループを使用してハブおよびスポーク構成を作成する方法について説明します。

1. スポーク接続グループ項目を作成し、 [AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem)を使用してネットワークグループを追加します。 フラグを使用した直接接続、フラグが設定された`-GroupConnectivity`グローバルメッシュ`-IsGlobal`、`-UseHubGateway`ハブ仮想ネットワークでゲートウェイを使用するフラグを有効にすることができます：

    ```azurepowershell-interactive
    $spokes = @{
        NetworkGroupId = $networkgroup.Id
    }
    $spokesGroup = New-AzNetworkManagerConnectivityGroupItem @gi -UseHubGateway -GroupConnectivity 'None' -IsGlobal
    ```

1. スポーク接続グループを作成し、前の手順で作成したグループ項目を追加します。

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($spokesGroup) 
    ```

1. ハブ接続グループ項目を作成し、 [新しい-AzNetworkManagerHub](/powershell/module/az.network/new-aznetworkmanagerhub)を使用してハブとして使用する仮想ネットワークを定義します。

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerHub]]$hubList = @()
    
    $hub = @{
        ResourceId = '/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA'
        ResourceType = 'Microsoft.Network/virtualNetworks'
    } 
    $hubvnet = New-AzNetworkManagerHub @hub

    $hubList.Add($hubvnet)
    ```

1. [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration) を使用して、接続構成を作成します。

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'HubAndSpoke'
        Hub = $hubList
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config -DeleteExistingPeering -IsGlobal
     ```

## <a name="deploy-the-hub-and-spoke-configuration"></a>ハブとスポークの構成を展開する

[Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit) を使用して、ターゲット リージョンに構成をコミットします。

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment
```

## <a name="confirm-deployment"></a>デプロイを確認する

1. ポータルのいずれかの仮想ネットワークにアクセスし、[*設定*] の下の **[ピアリング]** を選択します。 名前に *Avnm* が付いたハブとスポークの仮想ネットワークとの間に、新しいピアリング接続が作成されていることがわかります。

1. スポーク間の *直接接続* をテストするには、各スポーク仮想ネットワークに仮想マシンをデプロイします。 次に、仮想マシン間で ICMP 要求を開始します。

## <a name="next-steps"></a>次のステップ

- 「[セキュリティ管理規則](concept-security-admins.md)」について学習します。
- [SecurityAdmin 構成](how-to-block-network-traffic-powershell.md)を使用してネットワーク トラフィックをブロックする方法を学びます。
