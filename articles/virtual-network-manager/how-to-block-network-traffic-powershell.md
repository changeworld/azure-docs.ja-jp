---
title: Azure Virtual Network Manager (プレビュー) を使用してネットワーク トラフィックをブロックする方法 - Azure PowerShell
description: Azure PowerShell で Azure Virtual Network Manager のセキュリティ規則を使用してネットワーク トラフィックをブロックする方法について説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: d2e1a79891c0061dd49749fa2e27a2725a0b922f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092661"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-powershell"></a>Azure Virtual Network Manager (プレビュー) を使用してネットワーク トラフィックをブロックする方法 - Azure PowerShell

この記事では、ポート 80 および 443 への送信ネットワーク トラフィックをブロックするセキュリティ規則を作成して規則コレクションに追加する方法について説明します。 詳細については、[セキュリティ管理規則](concept-security-admins.md)に関するページを参照してください。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

セキュリティ規則の構成を開始する前に、次の手順を確認してください。

* [セキュリティ管理規則](concept-security-admins.md)の各要素を理解している。
* [Azure Virtual Network Manager インスタンス](create-virtual-network-manager-powershell.md)を作成した。

## <a name="create-a-securityadmin-configuration"></a>SecurityAdmin 構成を作成する

1. [New-AzNetworkManagerSecurityAdminConfiguration](/powershell/module/az.network/new-aznetworkmanagersecurityadminconfiguration) を使用して、新しい SecurityAdmin 構成を作成します。

    ```azurepowershell-interactive
    $config = @{
        Name = 'SecurityConfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $securityconfig = New-AzNetworkManagerSecurityAdminConfiguration @config 
    ```

1. [Get-AzNetworkManagerGroup](/powershell/module/az.network/get-aznetworkmanagergroup) を使用して、ネットワーク グループを変数に格納します。

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResoureceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $networkgroup = Get-AzNetworkManagerGroup @ng   
    ```

1. [New-AzNetworkManagerSecurityGroupItem](/powershell/module/az.network/new-aznetworkmanagersecuritygroupitem) を使用して、ネットワーク グループを追加する接続グループ項目を作成します。

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = '$networkgroup.Id'
    }
    $groupItem = New-AzNetworkManagerSecurityGroupItem @gi
    ```

1. 構成グループを作成し、前の手順のグループ項目を追加します。

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerSecurityGroupItem]]$configGroup = @()  
    $configGroup.Add($groupItem) 
    ```

1. [New-AzNetworkManagerSecurityAdminRuleCollection](/powershell/module/az.network/new-aznetworkmanagersecurityadminrulecollection) を使用して、セキュリティ管理規則のコレクションを作成します。

    ```azurepowershell-interactive
    $collection = @{
        Name = 'myRuleCollection'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManager = 'myAVNM'
        ConfigName = 'SecurityConfig'
        AppliesToGroup = $configGroup
    }
    $rulecollection = New-AzNetworkManagerSecurityAdminRuleCollection @collection
    ```

1. [New-AzNetworkManagerAddressPrefixItem](/powershell/module/az.network/new-aznetworkmanageraddressprefixitem) を使用して、送信元と送信先のアドレス プレフィックスとポートの変数を定義します。

    ```azurepowershell-interactive
    $sourceip = @{
        AddressPrefix = 'Internet'
        AddressPrefixType = 'ServiceTag'
    }
    $sourceprefix = New-AzNetworkManagerAddressPrefixItem @sourceip

    $destinationip = @{
        AddressPrefix = '10.0.0.0/24'
        AddressPrefixType = 'IPPrefix'
    }
    $destinationprefix = New-AzNetworkManagerAddressPrefixItem @destinationip

    [System.Collections.Generic.List[string]]$sourcePortList = @() 
    $sourcePortList.Add("65500”) 

    [System.Collections.Generic.List[string]]$destinationPortList = @() 
    $destinationPortList.Add("80”)
    $destinationPortList.Add("443”)
    ```

1. [New-AzNetworkManagerSecurityAdminRule](/powershell/module/az.network/new-aznetworkmanagersecurityadminrule) を使用してセキュリティ規則を作成します。

    ```azurepowershell-interactive
    $rule = @{
        Name = 'Block_HTTP_HTTPS'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        SecurityAdminConfigurationName = 'SecurityConfig'
        RuleCollectionName = 'myRuleCollection'
        Protocol = 'TCP'
        Access = 'Deny'
        Priority = '100'
        Direction = 'Outbound'
        Source = $sourceprefix
        SourcePortRange = $sourcePortList
        Destination = $destinationprefix
        DestinationPortRange = $destinationPortList
    }
    $securityrule = New-AzNetworkManagerSecurityAdminRule @rule
    ```

## <a name="commit-deployment"></a>デプロイをコミットする

[Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit) を使用して、ターゲット リージョンにセキュリティ構成をコミットします。

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($securityconfig.id) 
[System.Collections.Generic.List[string]]regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @deployment 
```

## <a name="delete-security-configuration"></a>セキュリティ構成を削除する

セキュリティ構成が不要になった場合は、セキュリティ構成自体を削除する前に、次の条件が満たされていることを確認する必要があります。

* どのリージョンにも構成のデプロイはありません。
* セキュリティ構成に関連付けられている規則コレクション内のすべてのセキュリティ規則を削除します。

### <a name="remove-security-configuration-deployment"></a>セキュリティ構成のデプロイを削除する

[Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit) を使用して、構成をデプロイすることによってセキュリティのデプロイを削除します。

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     
$removedeployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @removedeployment
```

### <a name="remove-security-rules"></a>セキュリティ規則を削除する

[Remove-AzNetworkManagerSecurityAdminRule](/powershell/module/az.network/remove-aznetworkmanagersecurityadminrule) を使用してセキュリティ規則を削除します。

```azurepowershell-interactive
$removerule = @{
    Name = 'Block80'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecurityConfig'
}
Remove-AzNetworkManagerSecurityAdminRule @removerule
```

### <a name="remove-security-rule-collections"></a>セキュリティ規則コレクションを削除する

```azurepowershell-interactive
$removecollection = @{
    Name = 'myRuleCollection'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecuritConfig'
}
Remove-AzNetworkManagerSecurityAdminRuleCollection @removecollection
```

### <a name="delete-configuration"></a>構成を削除する

[Remove-AzNetworkManagerSecurityAdminConfiguration](/powershell/module/az.network/remove-aznetworkmanagersecurityadminconfiguration) を使用してセキュリティ構成を削除します。

```azurepowershell-interactive
$removeconfig = @{
    Name = 'SecurityConfig'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
}
Remove-AzNetworkManagerSecurityAdminConfiguration @removeconfig
```

## <a name="next-steps"></a>次のステップ

[セキュリティ管理規則](concept-security-admins.md)の詳細を確認する
