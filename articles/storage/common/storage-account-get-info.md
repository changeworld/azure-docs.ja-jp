---
title: ストレージ アカウントの構成情報を取得する
titleSuffix: Azure Storage
description: Azure portal、PowerShell、または Azure CLI を使用して、ストレージ アカウントの構成プロパティを取得します。これには、Azure Resource Manager リソース ID、アカウントの場所、アカウントの種類、レプリケーション SKU などが含まれます。
services: storage
author: tamram
ms.author: tamram
ms.date: 06/23/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: f3f94dda19f11b1a0aad9a84e7ae624e41c5015c
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573654"
---
# <a name="get-storage-account-configuration-information"></a>ストレージ アカウントの構成情報を取得する

この記事では、Azure portal、PowerShell、または Azure CLI を使用して、Azure ストレージ アカウントの構成情報とプロパティを取得する方法について説明します。

## <a name="get-the-resource-id-for-a-storage-account"></a>ストレージ アカウントのリソース ID を取得する

すべての Azure Resource Manager リソースには、それを一意に識別するためのリソース ID が関連付けられています。 一部の操作では、このリソース ID を指定する必要があります。 ストレージ アカウントのリソース ID は、Azure portal、PowerShell、または Azure CLI を使用して取得することができます。

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Azure portal でストレージ アカウントの Azure Resource Manager リソース ID を表示するには、次の手順を行います。

1. Azure Portal のストレージ アカウントに移動します。
1. **[概要]** ページの **[要点]** セクションで、 **[JSON ビュー]** リンクを選択します。
1. ストレージ アカウントのリソース ID は、ページの上部に表示されます。

    :::image type="content" source="media/storage-account-get-info/resource-id-portal.png" alt-text="ストレージ アカウントのリソース ID をポータルからコピーする方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してストレージ アカウントの Azure Resource Manager リソース ID を取得するには、[Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) モジュールがインストールされていることを確認します。 次に、[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) コマンドを呼び出し、ストレージ アカウントが返されたら、そのリソース ID を取得します。

```azurepowershell
(Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントの Azure Resource Manager リソース ID を取得するには、[az storage account show](/cli/azure/storage/account#az_storage_account_show) コマンドを呼び出して、リソース ID をクエリします。

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query id \
    --output tsv
```

---

また、REST API で[ストレージ アカウント - プロパティの取得](/rest/api/storagerp/storage-accounts/get-properties)操作を呼び出して、ストレージ アカウントのリソース ID を取得することもできます。

Azure Resource Manager によって管理されるリソースの種類の詳細については、[リソース プロバイダーとリソースの種類](../../azure-resource-manager/management/resource-providers-and-types.md)に関するページを参照してください。

## <a name="get-the-account-type-location-or-replication-sku-for-a-storage-account"></a>ストレージ アカウントのアカウントの種類、場所、またはレプリケーション SKU を取得する

アカウントの種類、場所、およびレプリケーション SKU は、ストレージ アカウントで使用可能なプロパティの一部です。 これらの値は、Azure portal、PowerShell、または Azure CLI を使用して表示できます。

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Azure portal で、ストレージ アカウントのアカウントの種類、場所、またはレプリケーション SKU を表示するには、次の手順を行います。

1. Azure Portal のストレージ アカウントに移動します。
1. **[概要]** ページの **[要点]** セクションで、目的のプロパティを見つけます。

    :::image type="content" source="media/storage-account-get-info/account-configuration-portal.png" alt-text="ポータルでのアカウント構成を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してストレージ アカウントのアカウントの種類、場所、またはレプリケーション SKU を表示するには、[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) コマンドを呼び出してストレージ アカウントを取得してから、プロパティを確認します。

```azurepowershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>
$account.Location
$account.Sku
$account.Kind
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell を使用してストレージ アカウントのアカウントの種類、場所、またはレプリケーション SKU を表示するには、[az storage account show](/cli/azure/storage/account#az_storage_account_show) コマンドを呼び出してプロパティをクエリします。

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query '[location,sku,kind]' \
    --output tsv
```

---

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの概要](storage-account-overview.md)
- [ストレージ アカウントの作成](storage-account-create.md)
