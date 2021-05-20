---
title: REST API を使用して Azure ロールの割り当て条件を追加または編集する (プレビュー) - Azure RBAC
description: REST API と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure ロールの割り当てで属性ベースのアクセス制御 (ABAC) 条件を追加、編集、一覧表示、または削除する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: 4a929fd99f10e66ea07dacc0302dd6d0bba83a96
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656562"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-the-rest-api-preview"></a>REST API を使用して Azure ロールの割り当て条件を追加または編集する (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure ロール割り当ての条件](conditions-overview.md)は、必要に応じてロール割り当てに追加し、細かなアクセス制御を行うことで、安全確認を強化するための仕組みです。 たとえば、特定のタグが設定されたオブジェクトしか読み取れない、という条件を設定できます。 この記事では、REST API を使用して、ロールの割り当ての条件を追加、編集、一覧表示、または削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

ロールの割り当て条件を追加または編集するための前提条件について詳しくは、[条件の前提条件](conditions-prerequisites.md)に関するページをご覧ください。

## <a name="add-a-condition"></a>条件を追加する

ロールの割り当て条件を追加するには、[ロールの割り当て - 作成](/rest/api/authorization/roleassignments/create) REST API を使用します。 `api-version` を `2020-03-01-preview` 以降に設定します。 `description` ロールの割り当てにプロパティを使用する場合は、`2020-04-01-preview` 以降を使用します。  [ロールの割り当て - 作成](/rest/api/authorization/roleassignments/create) には、条件に関連する次のパラメーターが含まれています。

| パラメーター | 種類 | 説明 |
| --- | --- | --- |
| `condition` | String | ユーザーにアクセス許可を付与できる条件。 |
| `conditionVersion` | String | 条件構文のバージョン。 `conditionVersion` なしで `condition` を指定する場合、バージョンは既定値の 2.0 に設定されます。 |

次の要求と本文を使用します。

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}",
        "condition": "{condition}",
        "conditionVersion": "2.0",
        "description": "{description}"
    }
}
```

次の例は、条件を使用して[ストレージ BLOB データ 閲覧者](built-in-roles.md#storage-blob-data-reader)ロールを割り当てる方法を示しています。 この条件は、コンテナー名が 'blob-example-container' と等しいかどうかを確認します。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
        "conditionVersion": "2.0",
        "description": "Read access if container name equals blobs-example-container"
    }
}
```

出力例を次に示します。

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "principalType": "User",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
        "conditionVersion": "2.0",
        "createdOn": "2021-04-20T06:20:44.0205560Z",
        "updatedOn": "2021-04-20T06:20:44.2955371Z",
        "createdBy": null,
        "updatedBy": "{updatedById}",
        "delegatedManagedIdentityResourceId": null,
        "description": "Read access if container name equals blobs-example-container"
    },
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId}"
}
```

## <a name="edit-a-condition"></a>条件を編集する

既存のロールの割り当て条件を編集するには、ロールの割り当て条件を追加する際に使用したのと同じ [ロールの割り当て - 作成](/rest/api/authorization/roleassignments/create) REST API を使用します。 JSON ファイルで `condition` と `description` が更新される例を次に示します。 編集できるのは、`condition`、`conditionVersion`、`description` のプロパティのみです。 既存のロールの割り当てと一致するように、他のプロパティを指定する必要があります。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
        "conditionVersion": "2.0",
        "description": "Read access if container name equals blobs-example-container or blobs-example-container2"
    }
}
```


## <a name="list-a-condition"></a>条件を一覧表示する

ロールの割り当て条件を一覧表示するには、[ロールの割り当て - 一覧表示](/rest/api/authorization/roleassignments/list) API を使用します。 `api-version` を `2020-03-01-preview` 以降に設定します。 `description` ロールの割り当てにプロパティを使用する場合は、`2020-04-01-preview` 以降を使用します。 詳細については、「[REST API を使用して Azure ロールの割り当てを一覧表示する](role-assignments-list-rest.md)」を参照してください。

## <a name="delete-a-condition"></a>条件の削除

ロールの割り当て条件を削除するには、ロールの割り当て条件を編集し、条件と条件バージョンの両方を空の文字列または null に設定します。

あるいは、ロールの割り当てとその条件の両方を削除する場合は、[ロールの割り当て - 削除](/rest/api/authorization/roleassignments/delete) API を使用できます。 詳細については、「[Azure ロールの割り当ての削除](role-assignments-remove.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure ロール割り当ての条件の例 (プレビュー)](../storage/common/storage-auth-abac-examples.md)
- [チュートリアル: Azure portal でロール割り当ての条件を追加して blob へのアクセスを制限する (プレビュー)](../storage/common/storage-auth-abac-portal.md)
- [Azure ロール割り当ての条件のトラブルシューティング (プレビュー)](conditions-troubleshoot.md)
