---
title: PowerShell で Azure ソリューションを管理する | Microsoft Docs
description: Azure PowerShell と Resource Manager を使用してリソースを管理します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: conceptual
ms.date: 07/20/2018
ms.author: tomfitz
ms.openlocfilehash: 7cda2a406c6c49e9252bfd5840e8f943e5b7043f
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205801"
---
# <a name="manage-resources-with-azure-powershell"></a>Azure PowerShell でリソースを管理する

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をローカルにインストールして使用することを選択する場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="understand-scope"></a>スコープを理解する

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

この記事では、すべての管理設定をリソース グループに適用して、完了したらこれらの設定を容易に削除できるようにします。

リソース グループを作成しましょう。

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

現在、リソース グループは空です。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>ロールの割り当て

この記事では、仮想マシンとそれに関連する仮想ネットワークをデプロイします。 仮想マシン ソリューションを管理するために、一般的に必要なアクセスを提供する、リソースに固有の次の 3 つのロールがあります。

* [Virtual Machine Contributor](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Network Contributor](../role-based-access-control/built-in-roles.md#network-contributor)
* [Storage Account Contributor](../role-based-access-control/built-in-roles.md#storage-account-contributor)

多くの場合は、個々のユーザーにロールを割り当てる代わりに、類似のアクションを実行する必要のあるユーザーのための [Azure Active Directory グループを作成する](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)方が簡単です。 その後、そのグループを適切なロールに割り当てます。 この記事を簡略化するために、メンバーを含まない Azure Active Directory グループを作成します。 その場合でも、このグループをスコープのロールに割り当てることができます。 

次の例では、グループを作成し、それをリソース グループの仮想マシンの共同作業者ロールに割り当てます。 `New-AzureAdGroup` コマンドを実行するには、[Azure Cloud Shell](/azure/cloud-shell/overview) を使用するか、または [Azure AD PowerShell モジュールをダウンロードする](https://www.powershellgallery.com/packages/AzureAD/)必要があります。

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

通常は、デプロイされたリソースを管理するユーザーが確実に割り当てられるようにするために、このプロセスを**ネットワークの共同作業者**と**ストレージ アカウントの共同作業者**に対して繰り返します。 この記事では、これらの手順を省略できます。

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../azure-policy/azure-policy-introduction.md) は、サブスクリプション内のすべてのリソースが会社の基準を順守するために役立ちます。 サブスクリプションには、既にいくつかのポリシー定義が含まれています。 使用可能なポリシー定義を確認するには、次を使用します。

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

既存のポリシー定義が表示されます。 ポリシーの種類は、**[BuiltIn] (ビルトイン)** または **[カスタム]** のどちらかです。 この中から、割り当てる条件を記述している定義を探します。 この記事では、次のようなポリシーを割り当てます。

* すべてのリソースの場所を制限する
* 仮想マシンの SKU を制限する
* マネージド ディスクを使用しない仮想マシンを監査する

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>仮想マシンをデプロイする

ロールとポリシーを割り当てたため、ソリューションをデプロイする準備ができました。 既定のサイズは Standard_DS1_v2 です。これは、許可される SKU の 1 つです。 この手順の実行時に、資格情報の入力を求められます。 入力した値は、仮想マシンのユーザー名とパスワードとして構成されます。

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

デプロイが完了したら、そのソリューションにより多くの管理設定を適用できます。

## <a name="lock-resources"></a>リソースのロック

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>リソースのロック

仮想マシンとネットワーク セキュリティ グループをロックするには、次を使用します。

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

仮想マシンは、ロックを明確に削除した場合にのみ削除できます。 その手順は、「[リソースのクリーンアップ](#clean-up-resources)」に示されています。

## <a name="tag-resources"></a>リソースへのタグ付け

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>リソースへのタグ付け

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

仮想マシンにタグを適用するには、次を使用します。

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>タグでリソースを見つける

タグ名と値でリソースを見つけるには、次を使用します。

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

返される値は、タグ値ですべての仮想マシンを停止するような管理タスクで使用できます。

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>タグ値でコストを表示する

リソースにタグを適用した後、それらのタグでリソースのコストを表示できます。 コスト分析に最新の使用状況が表示されるには少し時間がかかるため、まだコストが表示されない可能性があります。 コストが使用可能な場合は、サブスクリプション内のリソース グループにまたがるリソースのコストを表示できます。 コストを表示するには、ユーザーに[課金情報へのサブスクリプション レベルのアクセス権](../billing/billing-manage-access.md)が必要です。

ポータル内でタグでコストを表示するには、サブスクリプションを選択し、**[コスト分析]** を選択します。

![コスト分析](./media/powershell-azure-resource-manager/select-cost-analysis.png)

次に、タグ値でフィルター処理し、**[適用]** を選択します。

![タグでコストを表示する](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

[Azure Billing API](../billing/billing-usage-rate-card-overview.md) を使用して、プログラムでコストを表示することもできます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ロックされたネットワーク セキュリティ グループは、そのロックが削除されるまで削除できません。 ロックを削除するには、次を使用します。

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順
* 仮想マシンの監視の詳細については、[Azure PowerShell による Windows 仮想マシンの監視および更新](../virtual-machines/windows/tutorial-monitoring.md)に関するページを参照してください。
* Azure Security Center を使用した推奨されるセキュリティ対策の実装の詳細については、[Azure Security Center を使用した仮想マシン セキュリティの監視](../virtual-machines/windows/tutorial-azure-security.md)に関するページを参照してください。
* 新しいリソース グループに、既存のリソースを移動できます。 例については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](/azure/architecture/cloud-adoption-guide/subscription-governance)」を参照してください。
