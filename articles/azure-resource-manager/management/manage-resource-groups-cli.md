---
title: リソース グループを管理する - Azure CLI
description: Azure CLI を使用して、Azure Resource Manager 経由でリソース グループを管理します。 リソース グループを作成、一覧表示、および削除する方法を示します。
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2088f1b4f201f149fc4d51bf608cb5868802d4d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731132"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure CLI を使用した Azure Resource Manager リソース グループの管理

[Azure Resource Manager](overview.md) で Azure CLI を使用して Azure リソース グループを管理する方法について説明します。 Azure リソースの管理については、「[Manage Azure resources by using Azure CLI (Azure CLI を使用した Azure リソースの管理)](manage-resources-cli.md)」をご覧ください。

リソース グループの管理に関するその他の記事は次のとおりです。

- [Azure portal を使用した Azure リソース グループの管理](manage-resources-portal.md)
- [Azure PowerShell を使用した Azure リソース グループの管理](manage-resources-powershell.md)に関する記事

## <a name="what-is-a-resource-group"></a>リソース グループとは

リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 リソース グループには、ソリューションのすべてのリソースか、グループとして管理したいリソースのみを含めることができます。 組織にとって最も有用になるように、リソースをリソース グループに追加する方法を決定します。 通常は、同じライフサイクルを共有するリソースを同じリソース グループに追加して、グループとして簡単にデプロイ、更新、および削除できるようにします。

リソース グループには、リソースについてのメタデータが格納されます。 リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。 コンプライアンス上の理由から、データは特定のリージョンに格納されるようにする必要があります。

## <a name="create-resource-groups"></a>リソース グループを作成する

リソース グループを作成するには、[az group create](/cli/azure/group#az_group_create) を使用します。

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>リソース グループの一覧を表示する

サブスクリプションのリソース グループを一覧表示するには、[az group list](/cli/azure/group#az_group_list) を使用します。

```azurecli-interactive
az group list
```

リソース グループを取得するには、[az group show](/cli/azure/group#az_group_show) を使用します。

```azurecli-interactive
az group show --name exampleGroup
```

## <a name="delete-resource-groups"></a>リソース グループを削除する

リソース グループを削除するには、[az group delete](/cli/azure/group#az_group_delete) を使用します。

```azurecli-interactive
az group delete --name exampleGroup
```

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](delete-resource-group.md)」を参照してください。

## <a name="deploy-resources"></a>リソースのデプロイ

Azure リソースをデプロイするには、Azure CLI を使用するか、Azure Resource Manager (ARM) テンプレートまたは Bicep ファイルをデプロイします。

次の例では、ストレージ アカウントを作成します。 ストレージ アカウントに指定する名前は、Azure 全体で一意である必要があります。

```azurecli-interactive
az storage account create --resource-group exampleGroup --name examplestore --location westus --sku Standard_LRS --kind StorageV2
```

ARM テンプレートまたは Bicep ファイルをデプロイするには [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) を使用します。

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-file storage.bicep
```

ARM テンプレートのデプロイの詳細については、「[Azure Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../templates/deploy-cli.md)」をご覧ください。

Bicep ファイルのデプロイの詳細については、「[Bicep と Azure CLI を使用してリソースをデプロイする](../bicep/deploy-cli.md)」をご覧ください。

## <a name="lock-resource-groups"></a>リソース グループをロックする

ロックすることによって、組織内の他のユーザーが重要なリソースを誤って削除したり変更したりするのを防ぎます。

リソース グループとそのリソースが削除されるのを防ぐには、[az lock create](/cli/azure/lock#az_lock_create) を使用します。

```azurecli-interactive
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleGroup
```

リソース グループのロックを取得するには、[az lock list](/cli/azure/lock#az_lock_list) を使用します。

```azurecli-interactive
az lock list --resource-group exampleGroup
```

ロックを削除するには、[az lock delete](/cli/azure/lock#az_lock_delete) を使用します

```azurecli-interactive
az lock delete --name exampleLock --resource-group exampleGroup
```

詳細については、[「Azure Resource Manager によるリソースのロック」](lock-resources.md)を参照してください。

## <a name="tag-resource-groups"></a>リソース グループにタグを適用する

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。 詳細については、[タグを使用した Azure リソースの整理](tag-resources.md#azure-cli) に関する記事をご覧ください。

## <a name="export-resource-groups-to-templates"></a>リソース グループをテンプレートにエクスポートする

ARM テンプレートの作成に役立てるために、既存のリソースからテンプレートをエクスポートできます。 詳細については、「[Azure CLI を使用してテンプレートをエクスポートする](../templates/export-template-cli.md)」を参照してください。 

## <a name="manage-access-to-resource-groups"></a>リソース グループへのアクセスを管理する

リソース グループへのアクセスを管理するには、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を使用します。 詳細については、「[Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Resource Manager については、「[Azure Resource Manager の概要](overview.md)」を参照してください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](../templates/syntax.md)」を参照してください。