---
title: 'クイックスタート: Azure PowerShell を使用して Azure Firewall ポリシーを作成および更新する'
description: このクイックスタートでは、Azure PowerShell を使用して Azure Firewall ポリシーを作成および更新する方法について説明します。
services: firewall-manager
author: vaboya
ms.author: victorh
ms.date: 08/16/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.openlocfilehash: 3e7eef6eed76f2b6e3e122c1a373580e05556f03
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254383"
---
# <a name="quickstart-create-and-update-an-azure-firewall-policy-using-azure-powershell"></a>クイックスタート: Azure PowerShell を使用して Azure Firewall ポリシーを作成および更新する

このクイックスタートでは、Azure PowerShell を使用して、ネットワークおよびアプリケーションのルールを設定した Azure Firewall ポリシーを作成します。 また、ネットワークとアプリケーションのルールを追加して、既存のポリシーを更新します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="set-up-the-network-and-policy"></a>ネットワークとポリシーを設定する

最初に、リソース グループと仮想ネットワークを作成します。 次に、Azure Firewall ポリシーを作成します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

このリソース グループには、この手順で使用するすべてのリソースが含まれます。

```azurepowershell
New-AzResourceGroup -Name Test-FWpolicy-RG -Location "East US"
```

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

```azurepowershell
$ServerSubnet = New-AzVirtualNetworkSubnetConfig -Name subnet-1 -AddressPrefix 10.0.0.0/24
$testVnet = New-AzVirtualNetwork -Name Test-FWPolicy-VNET -ResourceGroupName Test-FWPolicy-RG -Location "East US" -AddressPrefix 10.0.0.0/8 -Subnet $ServerSubnet
```
### <a name="create-a-firewall-policy"></a>ファイアウォール ポリシーを作成する

```azurepowershell
New-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG -Location "EAST US"
```

## <a name="create-a-network-rule-collection-group-and-add-new-rules"></a>ネットワーク ルール コレクション グループを作成して新しいルールを追加する

まず、ルール コレクション グループを作成してから、ルール コレクションにルールを追加します。

### <a name="create-the-network-rule-collection-group"></a>ネットワーク ルール コレクション グループを作成する

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newnetworkrulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "NetworkRuleCollectionGroup" -Priority 200 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
```
### <a name="create-network-rules"></a>ネットワーク ルールを作成する

```azurepowershell
$networkrule1= New-AzFirewallPolicyNetworkRule -Name NwRule1 -Description testRule1  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.1/32 -DestinationPort 22 
$networkrule2= New-AzFirewallPolicyNetworkRule -Name NWRule2 -Description TestRule2  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.10/32 -DestinationPort 1434
```
### <a name="create-a-network-rule-collection-and-add-new-rules"></a>ネットワーク ルール コレクションを作成して新しいルールを追加する

```azurepowershell
$newrulecollectionconfig=New-AzFirewallPolicyFilterRuleCollection -Name myfirstrulecollection -Priority 1000 -Rule $networkrule1,$networkrule2 -ActionType Allow
$newrulecollection = $networkrulecollectiongroup.Properties.RuleCollection.Add($newrulecollectionconfig)
```

### <a name="update-the-network-rule-collection-group"></a>ネットワーク ルール コレクション グループを更新する

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -Priority "200" -FirewallPolicyObject $firewallpolicy -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```

### <a name="output"></a>出力

新しいルール コレクションとそのルールを表示します。

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-Output  $output
```

## <a name="add-network-rules-to-an-existing-rule-collection"></a>既存のルール コレクションにネットワーク ルールを追加する

既存のルール コレクションができたので、それにさらにルールを追加できます。

### <a name="get-existing-network-rule-group-collection"></a>既存のネットワーク ルール グループ コレクションを取得する

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$networkrulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy 
```

### <a name="create-new-network-rules"></a>新しいネットワーク ルールを作成する

```azurepowershell
$newnetworkrule1 = New-AzFirewallPolicyNetworkRule -Name newNwRule01 -Description testRule01  -SourceAddress 10.0.0.0/24 -Protocol TCP -DestinationAddress 192.168.0.5/32 -DestinationPort 3389
$newnetworkrule2 = New-AzFirewallPolicyNetworkRule -Name newNWRule02 -Description TestRule02  -SourceAddress 10.0.0.0/24 -Protocol UDP -DestinationAddress 192.168.0.15/32 -DestinationPort 1434
```
### <a name="update-the-network-rule-collection-and-add-new-rules"></a>ネットワーク ルール コレクションを更新して新しいルールを追加する

```azurepowershell
$getexistingrullecollection = $networkrulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myfirstrulecollection"}
$getexistingrullecollection.RuleS.Add($newnetworkrule1)
$getexistingrullecollection.RuleS.Add($newnetworkrule2)
```
### <a name="update-network-rule-collection-group"></a>ネットワーク ルール コレクション グループを更新する

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "NetworkRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 200 -RuleCollection $networkrulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>出力

先ほど追加したルールを表示します。

```azurepowershell
$output = $networkrulecollectiongroup.Properties.GetRuleCollectionByName("myfirstrulecollection")
Write-output $output
```
## <a name="create-an-application-rule-collection-and-add-new-rules"></a>アプリケーション ルール コレクションを作成して新しいルールを追加する

まず、ルール コレクション グループを作成してから、ルール コレクションにルールを追加します。

### <a name="create-the-application-rule-collection-group"></a>アプリケーション ルール コレクション グループを作成する

```azurepowershell
$firewallpolicy = Get-AzFirewallPolicy -Name EUS-Policy -ResourceGroupName Test-FWPolicy-RG
$newapprulecollectiongroup = New-AzFirewallPolicyRuleCollectionGroup  -Name "ApplicationRuleCollectionGroup" -Priority 300 -ResourceGroupName Test-FWPolicy-RG -FirewallPolicyName EUS-Policy
```
### <a name="create-new-application-rules"></a>新しいアプリケーション ルールを作成する

```azurepowershell
$apprule1 = New-AzFirewallPolicyApplicationRule -Name apprule1 -Description testapprule1 -SourceAddress 192.168.0.1/32 -TargetFqdn "*.contoso.com" -Protocol HTTPS
$apprule2 = New-AzFirewallPolicyApplicationRule -Name apprule2 -Description testapprule2  -SourceAddress 192.168.0.10/32 -TargetFqdn "www.contosoweb.com" -Protocol HTTPS
```
### <a name="create-a-new-application-rule-collection-with-rules"></a>ルールが設定された新しいアプリケーション ルール コレクションを作成する

```azurepowershell
$apprulecollectiongroup = Get-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -ResourceGroupName Test-FWPolicy-RG -AzureFirewallPolicyName EUS-Policy
$apprulecollection = New-AzFirewallPolicyFilterRuleCollection -Name myapprulecollection -Priority 1000 -Rule $apprule1,$apprule2 -ActionType Allow 
$newapprulecollection = $apprulecollectiongroup.Properties.RuleCollection.Add($apprulecollection) 
```

### <a name="update-the-application-rule-collection-group"></a>アプリケーション ルール コレクション グループを更新する

```azurepowershell
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection 
```

### <a name="output"></a>出力

新しいルール コレクション グループとその新しいルールを調べます。

```azurepowershell
$output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="add-application-rules-to-an-existing-rule-collection"></a>既存のルール コレクションにアプリケーション ルールを追加する

既存のルール コレクションができたので、それにさらにルールを追加できます。

```azurepowershell 
#Create new Application Rules for exist Rule collection
$newapprule1 = New-AzFirewallPolicyApplicationRule -Name newapprule01 -Description testapprule01 -SourceAddress 192.168.0.5/32 -TargetFqdn "*.contosoabc.com" -Protocol HTTPS
$newapprule2 = New-AzFirewallPolicyApplicationRule -Name newapprule02 -Description testapprule02  -SourceAddress 192.168.0.15/32 -TargetFqdn "www.contosowebabcd.com" -Protocol HTTPS
```

### <a name="update-the-application-rule-collection"></a>アプリケーション ルール コレクションを更新する

```azurepowershell
$apprulecollection = $apprulecollectiongroup.Properties.RuleCollection | where {$_.Name -match "myapprulecollection"}
$apprulecollection.Rules.Add($newapprule1)
$apprulecollection.Rules.Add($newapprule2)

# Update Application Rule collection Group  
Set-AzFirewallPolicyRuleCollectionGroup -Name "ApplicationRuleCollectionGroup" -FirewallPolicyObject $firewallpolicy -Priority 300 -RuleCollection $apprulecollectiongroup.Properties.RuleCollection
```
### <a name="output"></a>出力

新しいルールを表示します。

```azurepowershell
$Output = $apprulecollectiongroup.Properties.GetRuleCollectionByName("myapprulecollection")
Write-Output $output
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースが不要になったら、リソース グループを削除してください。 これにより、作成したすべてのリソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを使用します。

```azurepowershell
Remove-AzResourceGroup -Name Test-FWpolicy-RG
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Firewall Manager のデプロイの詳細を確認する](deployment-overview.md)