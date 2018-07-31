---
title: チュートリアル - Azure PowerShell で Azure 仮想マシンを管理する | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell を使用して、RBAC、ポリシー、ロック、およびタグを適用することによって Azure 仮想マシンを管理する方法について説明します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: a785a18ac4aec3006397b6d681c476f8acf982a7
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205675"
---
# <a name="tutorial-learn-about-windows-virtual-machine-governance-with-azure-powershell"></a>チュートリアル: Azure PowerShell を使用した Windows 仮想マシンの管理方法の説明

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

この記事の例では、バージョン 6.0 以降の Azure PowerShell が必要です。 PowerShell をローカルで実行していて、バージョン 6.0 以降を持っていない場合は、[バージョンを更新](/powershell/azure/install-azurerm-ps)します。 `Connect-AzureRmAccount` を実行して、Azure との接続を作成する必要もあります。 ローカル インストールの場合は、[Azure AD PowerShell モジュールをダウンロード](https://www.powershellgallery.com/packages/AzureAD/)して、新しい Azure Active Directory グループを作成する必要もあります。

## <a name="understand-scope"></a>スコープを理解する

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

このチュートリアルでは、すべての管理設定をリソース グループに適用して、完了したらこれらの設定を容易に削除できるようにします。

そのリソース グループを作成しましょう。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

現在、リソース グループは空です。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

組織のユーザーがこれらのリソースへの適切なアクセス レベルを持つようにします。 ユーザーに無制限のアクセス権を許可したくはありませんが、ユーザーが自分の作業を実行できるようにすることも必要です。 [ロールベースのアクセス制御](../../role-based-access-control/overview.md)を使うと、あるスコープで特定のアクションを実行するアクセス許可を持つユーザーを管理することができます。

ロールの割り当てを作成および削除するには、`Microsoft.Authorization/roleAssignments/*` アクセス権が必要です。 このアクセス権は、所有者ロールまたはユーザー アクセス管理者ロールを通じて許可されます。

仮想マシン ソリューションを管理するために、一般的に必要なアクセスを提供する、リソースに固有の次の 3 つのロールがあります。

* [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Network Contributor](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Storage Account Contributor](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

多くの場合は、個々のユーザーにロールを割り当てる代わりに、類似のアクションを実行する必要のあるユーザーのための [Azure Active Directory グループを作成する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)方が簡単です。 その後、そのグループを適切なロールに割り当てます。 この記事を簡略化するために、メンバーを含まない Azure Active Directory グループを作成します。 その場合でも、このグループをスコープのロールに割り当てることができます。 

次の例では、名前が *VMDemoContributors* でメール ニックネームが *vmDemoGroup* の Azure Active Directory グループを作成します。 メール ニックネームは、グループのエイリアスとして機能します。

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

コマンド プロンプトに戻ってからグループが Azure Active Directory 全体に伝達されるまで、しばらくかかります。 20 ～ 30 秒待った後、[New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) コマンドを使って、リソース グループの仮想マシン共同作成者ロールに、新しい Azure Active Directory グループを割り当てます。  伝達される前に次のコマンドを実行した場合、"**プリンシパル <guid> がディレクトリにありません**" というエラーが発生します。 そのときは、コマンドをもう一度実行してみます。

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

通常は、デプロイされたリソースを管理するユーザーが確実に割り当てられるようにするために、このプロセスを*ネットワークの共同作業者*と*ストレージ アカウントの共同作業者*に対して繰り返します。 この記事では、これらの手順を省略できます。

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../azure-policy/azure-policy-introduction.md) は、サブスクリプション内のすべてのリソースが会社の基準を順守するために役立ちます。 サブスクリプションには、既にいくつかのポリシー定義が含まれています。 使用可能なポリシー定義を表示するには、[Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) コマンドを使います。

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

既存のポリシー定義が表示されます。 ポリシーの種類は、**[BuiltIn] (ビルトイン)** または **[カスタム]** のどちらかです。 この中から、割り当てる条件を記述している定義を探します。 この記事では、次のようなポリシーを割り当てます。

* すべてのリソースの場所を制限する。
* 仮想マシンの SKU を制限する。
* マネージド ディスクを使用しない仮想マシンを監査する。

次の例では、表示名に基づいて 3 つのポリシー定義を取得します。 [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) コマンドを使って、それらの定義をリソース グループに割り当てます。 一部のポリシーについては、許可される値を指定するパラメーター値を提供します。

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
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

[リソースのロック](../../azure-resource-manager/resource-group-lock-resources.md)は、組織内のユーザーが重要なリソースを誤って削除したり変更したりするのを防ぎます。 ロールベースのアクセス制御とは異なり、リソースのロックはすべてのユーザーとロールに制限を適用します。 ロック レベルは *CanNotDelete* または *ReadOnly* に設定できます。

仮想マシンとネットワーク セキュリティ グループをロックするには、[New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) コマンドを使います。

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

ロックをテストするには、次のコマンドを実行してみてください。

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

ロックのために削除操作を実行できないことを示すエラーが表示されます。 リソース グループは、ロックを明確に削除した場合にのみ削除できます。 その手順は、「[リソースのクリーンアップ](#clean-up-resources)」に示されています。

## <a name="tag-resources"></a>リソースへのタグ付け

Azure リソースに[タグ](../../azure-resource-manager/resource-group-using-tags.md)を適用すると、カテゴリ別に論理的に整理できます。 各タグは名前と値で構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

仮想マシンにタグを適用するには、[Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) コマンドを使います。

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>タグでリソースを見つける

タグの名前と値でリソースを検索するには、[Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) コマンドを使います。

```azurepowershell-interactive
(Get-AzureRmResource -Tag @{ Environment="Test"}).Name
```

返される値は、タグ値ですべての仮想マシンを停止するような管理タスクで使用できます。

```azurepowershell-interactive
Get-AzureRmResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>タグ値でコストを表示する

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ロックされたネットワーク セキュリティ グループは、そのロックが削除されるまで削除できません。 ロックを削除するには、[Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) コマンドを使います。

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

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、カスタム VM イメージを作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * ロールにユーザーを割り当てる
> * 基準を強制するポリシーを適用する
> * 重要なリソースをロックで保護する
> * 課金と管理のためにリソースにタグを付ける

次のチュートリアルに進み、仮想マシンの高可用性について学習してください。

> [!div class="nextstepaction"]
> [仮想マシンの監視](tutorial-monitoring.md)

