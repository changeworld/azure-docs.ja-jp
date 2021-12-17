---
title: Azure Virtual Network Manager (プレビュー) を使用してメッシュ ネットワーク トポロジを作成する - Azure PowerShell
description: Azure PowerShell を使用して、Azure Virtual Network Manager でメッシュ ネットワーク トポロジを作成する方法を学びます。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d763a0914a624d21d3845a05c1f2c0e6ef7e1ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091198"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Azure Virtual Network Manager (プレビュー) を使用してメッシュ ネットワーク トポロジを作成する - Azure PowerShell

この記事では、Azure PowerShell を使用して、Azure Virtual Network Manager でメッシュ ネットワーク トポロジを作成する方法を学びます。 この構成を使用すると、同じネットワーク グループ内の同じリージョンのすべての仮想ネットワークが相互に通信できます。 接続構成でグローバル メッシュ設定を有効にすることで、リージョン間の接続を有効にすることができます。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

* [メッシュ](concept-connectivity-configuration.md#mesh-network-topology) ネットワーク トポロジに関する記事を参照してください。
* [Azure Virtual Network Manager インスタンス](create-virtual-network-manager-powershell.md#create-virtual-network-manager)を作成します。
* メッシュ構成で使用する仮想ネットワークを特定するか、新しい[仮想ネットワーク](../virtual-network/quick-create-powershell.md)を作成します。

## <a name="create-a-network-group"></a>ネットワーク グループを作成する

このセクションでは、ハブ アンド スポーク ネットワーク トポロジに使用する仮想ネットワークを含むネットワーク グループを作成する方法について説明します。

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

1. [New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup) を使用して、以前に定義した静的メンバーシップ グループ (GroupMember) または動的メンバーシップ グループ (ConditionalMembership) のいずれかを使用して、ネットワーク グループを作成します。

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

## <a name="create-a-mesh-connectivity-configuration"></a>メッシュ接続構成を作成する

このセクションでは、前のセクションで作成したネットワーク グループを使用してメッシュ構成を作成する方法について説明します。

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

## <a name="deploy-the-mesh-configuration"></a>メッシュ構成をデプロイする

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

## <a name="confirm-deployment"></a>デプロイを確認する

1. ポータルで仮想ネットワークのいずれかに移動し、 **[ネットワーク マネージャー]** を *[設定]* から選びます。 そのページに構成が一覧表示されます。

1. 仮想ネットワーク間の接続をテストするには、テスト仮想マシンを各仮想ネットワークにデプロイし、それらの間で ICMP 要求を開始します。

## <a name="next-steps"></a>次のステップ

- 「[セキュリティ管理規則](concept-security-admins.md)」について学習します。
- [SecurityAdmin 構成](how-to-block-network-traffic-powershell.md)を使用してネットワーク トラフィックをブロックする方法を学びます。
