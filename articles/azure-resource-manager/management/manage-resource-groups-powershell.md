---
title: リソース グループの管理 - Azure PowerShell
description: Azure PowerShell を使用して、Azure Resource Manager 経由でリソース グループを管理します。 リソース グループを作成、一覧表示、および削除する方法を示します。
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c3bb028186155cc3af47f8efb293b7dbe61e13c9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731058"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Azure PowerShell を使用して Azure Resource Manager リソース グループを管理する

[Azure Resource Manager](overview.md) で Azure PowerShell を使用して Azure リソースグループを管理する方法について説明します。 Azure リソースの管理については、[Manage Azure resources by using Azure PowerShell (Azure PowerShell を使用して Azure リソースを管理する)](manage-resources-powershell.md) を参照してください。

リソース グループの管理に関するその他の記事は次のとおりです。

- [Azure portal を使用した Azure リソース グループの管理](manage-resources-portal.md)
- [Azure CLI を使用した Azure リソース グループの管理](manage-resources-cli.md)に関する記事

## <a name="what-is-a-resource-group"></a>リソース グループとは

リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 リソース グループには、ソリューションのすべてのリソースか、グループとして管理したいリソースのみを含めることができます。 組織にとって最も有用になるように、リソースをリソース グループに追加する方法を決定します。 通常は、同じライフサイクルを共有するリソースを同じリソース グループに追加して、グループとして簡単にデプロイ、更新、および削除できるようにします。

リソース グループには、リソースについてのメタデータが格納されます。 リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。 コンプライアンス上の理由から、データは特定のリージョンに格納されるようにする必要があります。

## <a name="create-resource-groups"></a>リソース グループを作成する

リソース グループを作成するには、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用します。

```azurepowershell-interactive
New-AzResourceGroup -Name exampleGroup -Location westus
```

## <a name="list-resource-groups"></a>リソース グループの一覧を表示する

サブスクリプションのリソース グループのを一覧表示するには、[Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) を使用します。

```azurepowershell-interactive
Get-AzResourceGroup
```

1 つのリソース グループを取得するには、そのリソース グループの名前を指定します。

```azurepowershell-interactive
Get-AzResourceGroup -Name exampleGroup
```

## <a name="delete-resource-groups"></a>リソース グループを削除する

リソース グループを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name exampleGroup
```

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](delete-resource-group.md)」を参照してください。

## <a name="deploy-resources"></a>リソースのデプロイ

Azure リソースをデプロイするには、Azure PowerShell を使用するか、Azure Resource Manager (ARM) テンプレートまたは Bicep ファイルをデプロイします。

次の例では、ストレージ アカウントを作成します。 ストレージ アカウントに指定する名前は、Azure 全体で一意である必要があります。

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName exampleGroup -Name examplestore -Location westus -SkuName "Standard_LRS"
```

ARM テンプレートまたは Bicep ファイルをデプロイするには[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用します。

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile storage.bicep
```

ARM テンプレートのデプロイの詳細については、「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](../templates/deploy-powershell.md)」をご覧ください。

Bicep ファイルのデプロイの詳細については、「[Bicep と Azure PowerShell を使用してリソースをデプロイする](../bicep/deploy-powershell.md)」をご覧ください。

## <a name="lock-resource-groups"></a>リソース グループをロックする

ロックすることによって、組織内の他のユーザーが重要なリソースを誤って削除したり変更したりするのを防ぎます。 

リソース グループとそのリソースが削除されるのを防ぐには、[New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) を使用します。

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleGroup
```

リソース グループのロックを取得するには、[Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) を使用します。

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleGroup
```

詳細については、[「Azure Resource Manager によるリソースのロック」](lock-resources.md)を参照してください。

## <a name="tag-resource-groups"></a>リソース グループにタグを適用する

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。 詳細については、[タグを使用した Azure リソースの整理](tag-resources.md#powershell) に関する記事をご覧ください。

## <a name="export-resource-groups-to-templates"></a>リソース グループをテンプレートにエクスポートする

ARM テンプレートの作成に役立てるために、既存のリソースからテンプレートをエクスポートできます。 詳細については、「[Azure PowerShell を使用してテンプレートをエクスポートする](../templates/export-template-powershell.md)」を参照してください。 

## <a name="manage-access-to-resource-groups"></a>リソース グループへのアクセスを管理する

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) は、Azure のリソースへのアクセスを管理する方法です。 詳細については、「[Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Resource Manager については、「[Azure Resource Manager の概要](overview.md)」を参照してください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](../templates/syntax.md)」を参照してください。
