---
title: Azure Resource Manager のロックをストレージ アカウントに適用する
titleSuffix: Azure Storage
description: Azure Resource Manager のロックをストレージ アカウントに適用する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 79b88ad58a2eb95a48a140b3b98d606af495cb94
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799787"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Azure Resource Manager のロックをストレージ アカウントに適用する

ストレージ アカウントが誤って削除されないように、または悪意のある削除から保護するために、Azure Resource Manager ロックを使用してすべてのストレージ アカウントをロックすることをお勧めします。 Azure Resource Manager のリソース ロックには、次の 2 種類があります。

- **CannotDelete** ロックは、ユーザーがストレージ アカウントを削除できないようにしますが、その構成の読み取りと変更は許可します。
- **ReadOnly** ロックは、ユーザーがストレージ アカウントを削除したり、構成を変更したりできないようにしますが、構成の読み取りは許可します。

Azure Resource Manager のロックの詳細については、[リソースのロックによる変更の防止](../../azure-resource-manager/management/lock-resources.md)に関する記事を参照してください。

> [!CAUTION]
> ストレージ アカウントをロックしても、そのアカウント内のコンテナーまたは BLOB は、削除または上書きから保護されません。 BLOB データを保護する方法の詳細については、[データ保護の概要](../blobs/data-protection-overview.md)に関する記事を参照してください。

## <a name="configure-an-azure-resource-manager-lock"></a>Azure Resource Manager ロックを構成する

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal を使用してストレージ アカウントのロックを構成するには、以下の手順に従います。

1. Azure Portal のストレージ アカウントに移動します。
1. **[設定]** セクションの **[ロック]** を選択します。
1. **[追加]** を選択します。
1. リソース ロックの名前を入力し、ロックの種類を指定します。 必要に応じて、ロックに関するメモを追加します。

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="CannotDelete ロックを使用してストレージ アカウントをロックする方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してストレージ アカウントのロックを構成するには、まず [Az PowerShell モジュール](https://www.powershellgallery.com/packages/Az)がインストールされていることを確認します。 次の例に示すように、[New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) コマンドを呼び出し、作成するロックの種類を指定します。

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントのロックを構成するには、次の例に示すように、[az lock create](/cli/azure/lock#az_lock_create) コマンドを呼び出し、作成するロックの種類を指定します。

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>ReadOnly ロックが有効になっている場合のデータ操作の認可

ストレージ アカウントに **ReadOnly** ロックが適用されると、そのストレージ アカウントの[キーの一覧表示](/rest/api/storagerp/storageaccounts/listkeys)操作はブロックされます。 **キーの一覧表示** 操作は HTTPS POST 操作であり、アカウントに対して **ReadOnly** ロックが構成されている場合、すべての POST 操作が禁止されます。 **キーの一覧表示** 操作によって、アカウント アクセス キーが返されます。このキーを使用して、ストレージ アカウント内の任意のデータの読み取りと書き込みを行うことができます。

ロックがストレージ アカウントに適用されたときにクライアントがアカウント アクセス キーを所有している場合、そのクライアントは引き続きそのキーを使用してデータにアクセスできます。 ただし、キーにアクセスできないクライアントは、Azure Active Directory (Azure AD) 資格情報を使用してストレージ アカウントの BLOB またはキュー データにアクセスする必要があります。

以前にアカウント アクセス キーを使用してポータルの BLOB またはキュー データにアクセスしていた場合、**ReadOnly** ロックが適用されると、Azure portal のユーザーに影響が及ぶ可能性があります。 ロックが適用された後、ポータルのユーザーは、ポータルで BLOB またはキュー データにアクセスするには Azure AD 資格情報を使用する必要が生じます。 これを行うには、ユーザーに少なくとも 2 つの RBAC ロールが割り当てられている必要があります。Azure Resource Manager ロールが必須で、Azure Storage データ アクセス ロールのいずれかも必要になります。 詳細については、以下の記事のいずれかを参照してください。

- [Azure portal で BLOB データへのアクセスの承認方法を選択する](../blobs/authorize-data-operations-portal.md)
- [Azure portal でキュー データへのアクセスの承認方法を選択する](../queues/authorize-data-operations-portal.md)

Azure Files または Table service のデータには、アカウント キーを使用して以前にアクセスしたクライアントからアクセスできなくなる可能性があります。 ベスト プラクティスとして、**ReadOnly** ロックをストレージ アカウントに適用する必要がある場合は、Azure Files と Table service のワークロードを、**ReadOnly** ロックでロックされていないストレージ アカウントに移動します。

## <a name="next-steps"></a>次のステップ

- [データ保護の概要](../blobs/data-protection-overview.md)
- [変更されないようにリソースをロックする](../../azure-resource-manager/management/lock-resources.md)
