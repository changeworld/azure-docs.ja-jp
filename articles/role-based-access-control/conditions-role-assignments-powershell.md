---
title: Azure PowerShell を使用して Azure ロールの割り当て条件を追加または編集する (プレビュー) - Azure RBAC
description: Azure PowerShell と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure ロールの割り当てで属性ベースのアクセス制御 (ABAC) 条件を追加、編集、一覧表示、または削除する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: 0ecc87a59d6db01c5c5dc4093bb42e4ee9c190d9
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656580"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-azure-powershell-preview"></a>Azure PowerShell を使用して Azure ロールの割り当て条件を追加または編集する (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロールの割り当て条件は、現在プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure ロールの割り当て条件](conditions-overview.md)は、必要に応じてロールの割り当てに追加して、よりきめ細かなアクセス制御を提供できるようにする追加のチェックです。 たとえば、オブジェクトを読み取るために、そのオブジェクトに特定のタグが必要であるという条件を追加できます。 この記事では、Azure PowerShell を使用して、ロールの割り当ての条件を追加、編集、一覧表示、または削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

ロールの割り当て条件を追加または編集するための前提条件について詳しくは、[条件の前提条件](conditions-prerequisites.md)に関するページをご覧ください。

## <a name="add-a-condition"></a>条件を追加する

ロールの割り当て条件を追加するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用します。 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) コマンドには、条件に関連する次のパラメーターが含まれています。

| パラメーター | 種類 | 説明 |
| --- | --- | --- |
| `Condition` | String | ユーザーにアクセス許可を付与できる条件。 |
| `ConditionVersion` | String | 条件構文のバージョン。 2\.0 に設定されている必要があります。 `Condition` を指定するときは `ConditionVersion` も指定する必要があります。 |

次の例は、変数を初期化して、条件付きの[ストレージ BLOB データ 閲覧者](built-in-roles.md#storage-blob-data-reader)ロールを割り当てる方法を示しています。 この条件は、コンテナー名が 'blob-example-container' と等しいかどうかを確認します。

```azurepowershell
$subscriptionId = "<subscriptionId>"
$resourceGroup = "<resourceGroup>"
$roleDefinitionName = "Storage Blob Data Reader"
$roleDefinitionId = "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
$userObjectId = "<userObjectId>"
$scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
$description = "Read access if container name equals blobs-example-container"
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))"
$conditionVersion = "2.0"
```

[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用して、条件付きのロールを割り当てます。

```azurepowershell
New-AzRoleAssignment -ObjectId $userObjectId -Scope $scope -RoleDefinitionId $roleDefinitionId -Description $description -Condition $condition -ConditionVersion $conditionVersion
```

出力の例を次に示します。

```azurepowershell
RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
DisplayName        : User1
SignInName         : user1@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : <userObjectId>
ObjectType         : User
CanDelegate        : False
Description        : Read access if container name equals blobs-example-container
ConditionVersion   : 2.0
Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))
```

PowerShell では、条件にドル記号 ($) が含まれている場合は、その前にバッククォート (\`) を付ける必要があります。 たとえば、次の条件では、ドル記号を使用してタグ キー名を示しています。 PowerShell の引用符の規則の詳細については、「[引用符の規則について](/powershell/module/microsoft.powershell.core/about/about_quoting_rules)」を参照してください。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
```

## <a name="edit-a-condition"></a>条件を編集する

既存のロールの割り当て条件を編集するには、[Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) を使用します。 編集できるのは、`Condition`、`ConditionVersion`、`Description` のプロパティのみです。 `-PassThru` パラメーターを指定すると、[Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) により更新されたロールの割り当てが返されます。これにより、視覚化やさらに使用するための変数内への格納が可能になります。

条件を編集するには、2 つの方法があります。 `PSRoleAssignment` オブジェクトまたは JSON ファイルを使用できます。

### <a name="edit-a-condition-using-the-psroleassignment-object"></a>PSRoleAssignment オブジェクトを使用して条件を編集する

1. [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用して、条件付きの既存のロールの割り当てを `PSRoleAssignment` オブジェクトとして取得します。

    ```azurepowershell
    $testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectId
    ```

1. 条件を編集します。

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))"
    ```

1. 条件と説明を初期化します。

    ```azurepowershell
    $testRa.Condition = $condition
    $testRa.Description = "Read access if container name equals blobs-example-container or blobs-example-container2"
    ```

1. [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) を使用して、ロールの割り当ての条件を更新します。

    ```azurepowershell
    Set-AzRoleAssignment -InputObject $testRa -PassThru
    ```

    出力の例を次に示します。

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : User1
    SignInName         : user1@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access if container name equals blobs-example-container or blobs-example-container2
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))
    ```

### <a name="edit-a-condition-using-a-json-file"></a>JSON ファイルを使用して条件を編集する

条件を編集する場合、JSON ファイルを入力として指定することもできます。 `Condition` と `Description` が更新される JSON ファイルの例を次に示します。 条件を更新するには、JSON ファイル内にすべてのプロパティを指定する必要があります。

```json
{
    "RoleDefinitionId": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "ObjectId": "<userObjectId>",
    "ObjectType": "User",
    "Scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>",
    "Condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "ConditionVersion": "2.0",
    "CanDelegate": false,
    "Description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "RoleAssignmentId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>"
}
```

[Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) を使用して、ロールの割り当ての条件を更新します。

```azurepowershell
Set-AzRoleAssignment -InputFile "C:\path\roleassignment.json" -PassThru
```

出力の例を次に示します。

```azurepowershell
RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
DisplayName        : User1
SignInName         : user1@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : <userObjectId>
ObjectType         : User
CanDelegate        : False
Description        : Read access if container name equals blobs-example-container or blobs-example-container2
ConditionVersion   : 2.0
Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))
```

## <a name="list-a-condition"></a>条件を一覧表示する

ロールの割り当て条件を一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用します。 詳細については、「[Azure PowerShell を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-powershell.md)」を参照してください。

## <a name="delete-a-condition"></a>条件の削除

ロールの割り当て条件を削除するには、ロールの割り当て条件を編集し、`Condition` と `ConditionVersion` の両方のプロパティを空の文字列 (`""`) または `$null` に設定します。

また、ロールの割り当てとその条件の両方を削除する場合は、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) コマンドを使用できます。 詳細については、「[Azure ロールの割り当ての削除](role-assignments-remove.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure ロール割り当て条件の例 (プレビュー)](../storage/common/storage-auth-abac-examples.md)
- [チュートリアル: Azure PowerShell を使用して BLOB へのアクセスを制限するロールの割り当て条件を追加する (プレビュー)](../storage/common/storage-auth-abac-powershell.md)
- [Azure ロールの割り当て条件のトラブルシューティング (プレビュー)](conditions-troubleshoot.md)
