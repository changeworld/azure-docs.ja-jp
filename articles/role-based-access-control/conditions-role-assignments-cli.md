---
title: Azure CLI を使用して Azure ロールの割り当て条件を追加または編集する (プレビュー) - Azure RBAC
description: Azure CLI と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure ロールの割り当てで属性ベースのアクセス制御 (ABAC) 条件を追加、編集、一覧表示、または削除する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: c7baafbd34e9af488fbbee6237625aa469dff12c
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656634"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-azure-cli-preview"></a>Azure CLI を使用して Azure ロールの割り当て条件を追加または編集する (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure ロールの割り当て条件](conditions-overview.md)は、必要に応じてロールの割り当てに追加して、よりきめ細かなアクセス制御を提供できるようにする追加のチェックです。 たとえば、オブジェクトを読み取るために、そのオブジェクトに特定のタグが必要であるという条件を追加できます。 この記事では、Azure CLI を使用して、ロールの割り当ての条件を追加、編集、一覧表示、または削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

ロールの割り当て条件を追加または編集するための前提条件について詳しくは、[条件の前提条件](conditions-prerequisites.md)に関するページをご覧ください。

## <a name="add-a-condition"></a>条件を追加する

ロールの割り当て条件を追加するには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用します。 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) コマンドには、条件に関連する次のパラメーターが含まれています。

| パラメーター | 種類 | 説明 |
| --- | --- | --- |
| `condition` | String | ユーザーにアクセス許可を付与できる条件。 |
| `condition-version` | String | 条件構文のバージョン。 `--condition-version` なしで `--condition` を指定した場合、バージョンは既定値の 2.0 に設定されます。 |

次の例は、条件を使用して[ストレージ BLOB データ 閲覧者](built-in-roles.md#storage-blob-data-reader)ロールを割り当てる方法を示しています。 この条件は、コンテナー名が 'blob-example-container' と等しいかどうかを確認します。

```azurecli
az role assignment create --role "Storage Blob Data Reader" --assignee "user1@contoso.com" --resource-group {resourceGroup} \
--description "Read access if container name equals blobs-example-container" \
--condition "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))" \
--condition-version "2.0"
```

出力例を次に示します。

```azurecli
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="edit-a-condition"></a>条件を編集する

既存のロールの割り当て条件を編集するには、[az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) と、入力として JSON ファイルを使用します。 条件と説明が更新される JSON ファイルの例を次に示します。 編集できるのは、`condition`、`conditionVersion`、`description` のプロパティのみです。 ロールの割り当て条件を更新するには、すべてのプロパティを指定する必要があります。

```json
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

[az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) を使用して、ロールの割り当ての条件を更新します。

```azurecli
az role assignment update --role-assignment "./path/roleassignment.json"
```

出力例を次に示します。

```azurecli
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="list-a-condition"></a>条件を一覧表示する

ロールの割り当て条件を一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) を使用します。 詳細については、「[Azure CLI を使用して Azure ロールの割り当てを一覧表示する](role-assignments-list-cli.md)」を参照してください。

## <a name="delete-a-condition"></a>条件の削除

ロールの割り当て条件を削除するには、ロールの割り当て条件を編集し、`condition` と `condition-version` の両方のプロパティを空の文字列 (`""`) または `null` に設定します。

また、ロールの割り当てとその条件の両方を削除する場合は、[az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) コマンドを使用できます。 詳細については、「[Azure ロールの割り当ての削除](role-assignments-remove.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure ロール割り当て条件の例 (プレビュー)](../storage/common/storage-auth-abac-examples.md)
- [チュートリアル: Azure CLI を使用して BLOB へのアクセスを制限するロールの割り当て条件を追加する (プレビュー)](../storage/common/storage-auth-abac-cli.md)
- [Azure ロールの割り当て条件のトラブルシューティング (プレビュー)](conditions-troubleshoot.md)
