---
title: Azure ロール割り当て条件の例 (プレビュー) - Azure RBAC
titleSuffix: Azure Storage
description: Azure 属性ベースのアクセス制御 (Azure ABAC) の Azure ロール割り当て条件の例。
services: storage
author: rolyon
ms.service: storage
ms.topic: conceptual
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: a04c205370ee07900b649ed0a3f6f4bf3a892685
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577826"
---
# <a name="example-azure-role-assignment-conditions-preview"></a>Azure ロール割り当て条件の例 (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロール割り当て条件は、現在プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、ロール割り当て条件の例の一部を示します。

## <a name="prerequisites"></a>前提条件

ロール割り当て条件を追加または編集するための前提条件について詳しくは、[条件の前提条件](../../role-based-access-control/conditions-prerequisites.md)に関するページをご覧ください。

## <a name="example-1-read-access-to-blobs-with-a-tag"></a>例 1: タグを使用した BLOB への読み取りアクセス

この条件により、ユーザーが Project の BLOB のインデックス タグ キーと Cascade のタグ値を使用して、BLOB を読み取ることが許可されます。 このキーと値のタグを使用せずに BLOB にアクセスしようとする試みは許可されません。

> [!TIP]
> また、BLOB では任意のユーザー定義のキー値メタデータを格納する機能もサポートされています。 メタデータと BLOB インデックス タグはよく似ていますが、条件では BLOB インデックス タグを使用する必要があります。 詳しくは、「[BLOB インデックス タグを使用して Azure BLOB データを管理および検索する (プレビュー)](../blobs/storage-manage-find-blobs.md)」をご覧ください。

![タグを使用した BLOB への読み取りアクセスを示す例 1 の条件の図。](./media/storage-auth-abac-examples/example-1.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
    )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用してこの条件を追加する設定を次に示します。

| 条件 #1 | 設定 |
| --- | --- |
| アクション | Read content from a blob with tag conditions (タグ条件を使用して BLOB からコンテンツを読み取る) |
| Attribute source (属性ソース) | リソース |
| 属性 | Blob index tags [Values in key] (BLOB インデックス タグ [キー内の値]) |
| キー | {keyName} |
| 演算子 | StringEquals |
| 値 | {keyValue} |

![Azure portal のエディターの例 1 の条件のスクリーンショット。](./media/storage-auth-abac-examples/example-1-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してこの条件を追加する方法を次に示します。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

この条件をテストする方法は次のとおりです。

```azurepowershell
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
Get-AzStorageBlob -Container <containerName> -Blob <blobName> -Context $bearerCtx 
```

## <a name="example-2-new-blobs-must-include-a-tag"></a>例 2: 新しい BLOB にタグを含める必要がある

この条件では、新しい BLOB に Project の BLOB インデックス タグ キーと Cascade のタグ値を含める必要があります。

> [!TIP]
> また、BLOB では任意のユーザー定義のキー値メタデータを格納する機能もサポートされています。 メタデータと BLOB インデックス タグはよく似ていますが、条件では BLOB インデックス タグを使用する必要があります。 詳しくは、「[BLOB インデックス タグを使用して Azure BLOB データを管理および検索する (プレビュー)](../blobs/storage-manage-find-blobs.md)」をご覧ください。

新しい BLOB を作成することを許可するアクセス許可が 2 つあるため、両方をターゲットにする必要があります。 この条件は、次のいずれかのアクセス許可があるすべてのロール割り当てに追加する必要があります。

- /blobs/write (作成または更新)
- /blobs/add/action (作成)

![新しい BLOB にタグを含める必要があることを示す例 2 の条件の図。](./media/storage-auth-abac-examples/example-2.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用してこの条件を追加する設定を次に示します。

| 条件 #1 | 設定 |
| --- | --- |
| アクション | Write to a blob with blob index tags (BLOB インデックス タグを使用して BLOB に書き込む)<br/>Write content to a blob with blob index tags (BLOB インデックス タグを使用して BLOB にコンテンツを書き込む) |
| Attribute source (属性ソース) | Request |
| 属性 | Blob index tags [Values in key] (BLOB インデックス タグ [キー内の値]) |
| キー | {keyName} |
| 演算子 | StringEquals |
| 値 | {keyValue} |

![Azure portal のエディターの例 2 の条件 #1 のスクリーンショット。](./media/storage-auth-abac-examples/example-2-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してこの条件を追加する方法を次に示します。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

この条件をテストする方法は次のとおりです。

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Project'='Baker'}
$grantedTag = @{'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-3-existing-blobs-must-have-tag-keys"></a>例 3: 既存の BLOB にタグ キーが必要

この条件では、すべての既存の BLOB に、許可されているいずれかの BLOB インデックス タグ キー (Project または Program) が 1 つ以上タグ付けされている必要があります。 この条件は、既存の BLOB にガバナンスを追加する場合に役立ちます。

> [!TIP]
> また、BLOB では任意のユーザー定義のキー値メタデータを格納する機能もサポートされています。 メタデータと BLOB インデックス タグはよく似ていますが、条件では BLOB インデックス タグを使用する必要があります。 詳しくは、「[BLOB インデックス タグを使用して Azure BLOB データを管理および検索する (プレビュー)](../blobs/storage-manage-find-blobs.md)」をご覧ください。

既存の BLOB のタグを更新することを許可するアクセス許可が 2 つあるため、両方をターゲットにする必要があります。 この条件は、次のいずれかのアクセス許可があるすべてのロール割り当てに追加する必要があります。

- /blobs/write (更新または作成、作成は除外できない)
- /blobs/tags/write

![既存の BLOB にタグ キーが必要なことを示す例 3 の条件の図。](./media/storage-auth-abac-examples/example-3.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用してこの条件を追加する設定を次に示します。

| 条件 #1 | 設定 |
| --- | --- |
| アクション | Write to a blob with blob index tags (BLOB インデックス タグを使用して BLOB に書き込む)<br/>Write blob index tags (BLOB インデックス タグを書き込む) |
| Attribute source (属性ソース) | Request |
| 属性 | Blob index tags [Keys] (BLOB インデックス タグ [キー]) |
| 演算子 | ForAllOfAnyValues:StringEquals |
| 値 | {keyName1}<br/>{keyName2} |

![Azure portal のエディターの例 3 の条件 #1 のスクリーンショット。](./media/storage-auth-abac-examples/example-3-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してこの条件を追加する方法を次に示します。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

この条件をテストする方法は次のとおりです。

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Mode'='Baker'}
$grantedTag = @{'Program'='Alpine';'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-4-existing-blobs-must-have-a-tag-key-and-values"></a>例 4: 既存の BLOB にタグ キーと値が必要

この条件では、既存の BLOB に Project の BLOB インデックス タグ キーと Cascade、Baker、または Skagit のタグ値が必要です。 この条件は、既存の BLOB にガバナンスを追加する場合に役立ちます。

> [!TIP]
> また、BLOB では任意のユーザー定義のキー値メタデータを格納する機能もサポートされています。 メタデータと BLOB インデックス タグはよく似ていますが、条件では BLOB インデックス タグを使用する必要があります。 詳しくは、「[BLOB インデックス タグを使用して Azure BLOB データを管理および検索する (プレビュー)](../blobs/storage-manage-find-blobs.md)」をご覧ください。

既存の BLOB のタグを更新することを許可するアクセス許可が 2 つあるため、両方をターゲットにする必要があります。 この条件は、次のいずれかのアクセス許可があるすべてのロール割り当てに追加する必要があります。

- /blobs/write (更新または作成、作成は除外できない)
- /blobs/tags/write

![既存の BLOB にタグ キーと値が必要なことを示す例 4 の条件の図。](./media/storage-auth-abac-examples/example-4.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAnyOfAnyValues:StringEquals {'Project'}
  AND
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用してこの条件を追加する設定を次に示します。

| 条件 #1 | 設定 |
| --- | --- |
| アクション | Write to a blob with blob index tags (BLOB インデックス タグを使用して BLOB に書き込む)<br/>Write blob index tags (BLOB インデックス タグを書き込む) |
| Attribute source (属性ソース) | Request |
| 属性 | Blob index tags [Keys] (BLOB インデックス タグ [キー]) |
| 演算子 | ForAnyOfAnyValues:StringEquals |
| 値 | {keyName} |
| 演算子 | および |
| **式 2** |  |
| Attribute source (属性ソース) | Request |
| 属性 | Blob index tags [Values in key] (BLOB インデックス タグ [キー内の値]) |
| キー | {keyName} |
| 演算子 | ForAllOfAnyValues:StringEquals |
| 値 | {keyValue1}<br/>{keyValue2}<br/>{keyValue3} |

![Azure portal のエディターの例 4 の条件 #1 のスクリーンショット。](./media/storage-auth-abac-examples/example-4-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してこの条件を追加する方法を次に示します。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAnyOfAnyValues:StringEquals {'Project'} AND @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

この条件をテストする方法は次のとおりです。

```azurepowershell
$localSrcFile = <pathToLocalFile>
$ungrantedTag = @{'Project'='Alpine'}
$grantedTag1 = @{'Project'='Cascade'}
$grantedTag2 = @{'Project'='Baker'}
$grantedTag3 = @{'Project'='Skagit'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag1 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag2 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag3 -Context $bearerCtx
```

## <a name="example-5-read-write-or-delete-blobs-in-named-containers"></a>例 5: 名前付きコンテナー内の BLOB の読み取り、書き込み、または削除

この条件によって、ユーザーは、blobs-example-container という名前のストレージ コンテナー内の BLOB の読み取り、書き込み、または削除を行うことができます。 この条件は、特定のストレージ コンテナーをサブスクリプション内の他のユーザーと共有する場合に便利です。

既存の BLOB の読み取り、書き込み、削除のためのアクセス許可は 4 つあるため、すべてのアクセス許可をターゲットにする必要があります。 この条件は、次のいずれかのアクセス許可があるすべてのロール割り当てに追加する必要があります。

- /blobs/delete
- /blobs/read
- /blobs/write (更新または作成)
- /blobs/add/action (作成)

subOperation はタグに基づいて条件が作成されるときにのみ必要であるため、この条件では subOperation は使用されません。

![名前付きコンテナー内の BLOB の読み取り、書き込み、または削除を示す例 5 の条件の図。](./media/storage-auth-abac-examples/example-5.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用してこの条件を追加する設定を次に示します。

| 条件 #1 | 設定 |
| --- | --- |
| アクション | BLOB を削除する<br/>Read a blob (BLOB を読み取る)<br/>Write to a blob (BLOB に書き込む)<br/>Create a blob or snapshot, or append data (BLOB またはスナップショットを作成するか、データを追加する) |
| Attribute source (属性ソース) | リソース |
| 属性 | コンテナー名 |
| 演算子 | StringEquals |
| 値 | {containerName} |

![Azure portal のエディターの例 5 の条件 #1 のスクリーンショット。](./media/storage-auth-abac-examples/example-5-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してこの条件を追加する方法を次に示します。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

この条件をテストする方法は次のとおりです。

```azurepowershell
$localSrcFile = <pathToLocalFile>
$grantedContainer = "blobs-example-container"
$ungrantedContainer = "ungranted"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Ungranted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
# Granted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
```

## <a name="example-6-read-access-to-blobs-in-named-containers-with-a-path"></a>例 6: パスを使用した名前付きコンテナー内の BLOB への読み取りアクセス

この条件により、readonly/* の BLOB パスを使用して、blobs-example-container という名前のストレージ コンテナーへの読み取りアクセスが許可されます。 この条件は、読み取りアクセスのためにストレージ コンテナーの特定の部分をサブスクリプション内の他のユーザーと共有するときに役立ちます。

この条件は、次のアクセス許可があるすべてのロール割り当てに追加する必要があります。

- /blobs/read

![パスを使用した名前付きコンテナー内の BLOB への読み取りアクセスを示す例 6 の条件の図。](./media/storage-auth-abac-examples/example-6.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
        AND
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'
    )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用してこの条件を追加する設定を次に示します。

| 条件 #1 | 設定 |
| --- | --- |
| アクション | Read a blob (BLOB を読み取る) |
| Attribute source (属性ソース) | リソース |
| 属性 | コンテナー名 |
| 演算子 | StringEquals |
| 値 | {containerName} |
| **式 2** |  |
| 演算子 | および |
| Attribute source (属性ソース) | リソース |
| 属性 | BLOB パス |
| 演算子 | StringLike |
| 値 | {pathString} |

![Azure portal のエディターの例 6 の条件 #1 のスクリーンショット。](./media/storage-auth-abac-examples/example-6-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してこの条件を追加する方法を次に示します。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

この条件をテストする方法は次のとおりです。

```azurepowershell
$grantedContainer = "blobs-example-container"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Ungranted.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "readonly/Example6.txt" -Context $bearerCtx
```

## <a name="example-7-write-access-to-blobs-in-named-containers-with-a-path"></a>例 7: パスを使用した名前付きコンテナー内の BLOB への書き込みアクセス

この条件により、パートナー (Azure AD のゲスト ユーザー) が、uploads/contoso/* のパスを使用して、Contosocorp という名前のストレージ コンテナーにファイルをドロップすることを許可します。 この条件は、他のユーザーがストレージ コンテナーにデータを格納することを許可するのに役立ちます。

この条件は、次のアクセス許可があるすべてのロール割り当てに追加する必要があります。

- /blobs/write (作成または更新)
- /blobs/add/action (作成)

![パスを使用した名前付きコンテナー内の BLOB への書き込みアクセスを示す例 7 の条件の図。](./media/storage-auth-abac-examples/example-7.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp'
  AND
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用してこの条件を追加する設定を次に示します。

| 条件 #1 | 設定 |
| --- | --- |
| アクション | Write to a blob (BLOB に書き込む)<br/>Create a blob or snapshot, or append data (BLOB またはスナップショットを作成するか、データを追加する) |
| Attribute source (属性ソース) | リソース |
| 属性 | コンテナー名 |
| 演算子 | StringEquals |
| 値 | {containerName} |
| **式 2** |  |
| 演算子 | および |
| Attribute source (属性ソース) | リソース |
| 属性 | BLOB パス |
| 演算子 | StringLike |
| 値 | {pathString} |

![Azure portal のエディターの例 7 の条件 #1 のスクリーンショット。](./media/storage-auth-abac-examples/example-7-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してこの条件を追加する方法を次に示します。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

この条件をテストする方法は次のとおりです。

```azurepowershell
$grantedContainer = "contosocorp"
$localSrcFile = <pathToLocalFile>
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to set ungranted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "Example7.txt" -Context $bearerCtx -File $localSrcFile
# Try to set granted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "uploads/contoso/Example7.txt" -Context $bearerCtx -File $localSrcFile
```

## <a name="example-8-read-access-to-blobs-with-a-tag-and-a-path"></a>例 8: タグとパスを使用した BLOB への読み取りアクセス

この条件により、ユーザーは、Program の BLOB インデックス タグ キー、Alpine のタグ値、および logs *の BLOB パスを使用して BLOB を読み取ることをユーザーに許可します。logs* の BLOB パスには、BLOB 名も含まれます。

> [!TIP]
> また、BLOB では任意のユーザー定義のキー値メタデータを格納する機能もサポートされています。 メタデータと BLOB インデックス タグはよく似ていますが、条件では BLOB インデックス タグを使用する必要があります。 詳しくは、「[BLOB インデックス タグを使用して Azure BLOB データを管理および検索する (プレビュー)](../blobs/storage-manage-find-blobs.md)」をご覧ください。

この条件は、次のアクセス許可があるすべてのロール割り当てに追加する必要があります。

- /blobs/read

![タグとパスを使用した BLOB への読み取りアクセスを示す例 8 の条件の図。](./media/storage-auth-abac-examples/example-8.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<$key_case_sensitive$>] StringEquals 'Alpine'
    )
)
AND
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'
    )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用してこの条件を追加する設定を次に示します。

| 条件 #1 | 設定 |
| --- | --- |
| アクション | Read content from a blob with tag conditions (タグ条件を使用して BLOB からコンテンツを読み取る) |
| Attribute source (属性ソース) | リソース |
| 属性 | Blob index tabs [Values in key] (BLOB インデックス タブ [キー内の値]) |
| キー | {keyName} |
| 演算子 | StringEquals |
| 値 | {keyValue} |

![Azure portal のエディターの例 8 の条件 #1 のスクリーンショット。](./media/storage-auth-abac-examples/example-8-condition-1-portal.png)

| 条件 #2 | 設定 |
| --- | --- |
| アクション | Read a blob (BLOB を読み取る) |
| Attribute source (属性ソース) | リソース |
| 属性 | BLOB パス |
| 演算子 | StringLike |
| 値 | {pathString} |

![Azure portal のエディターの例 8 の条件 #2 のスクリーンショット。](./media/storage-auth-abac-examples/example-8-condition-2-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してこの条件を追加する方法を次に示します。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<`$key_case_sensitive`$>] StringEquals 'Alpine')) AND ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

この条件をテストする方法は次のとおりです。

```azurepowershell
$grantedContainer = "contosocorp"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blobs
# Wrong name but right tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "AlpineFile.txt" -Context $bearerCtx
# Right name but wrong tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logsAlpine.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logs/AlpineFile.txt" -Context $bearerCtx
```

## <a name="next-steps"></a>次のステップ

- [チュートリアル: Azure portal を使用して BLOB へのアクセスを制限するロール割り当て条件を追加する (プレビュー)](storage-auth-abac-portal.md)
- [Azure Storage での Azure ロール割り当て条件のアクションと属性 (プレビュー)](storage-auth-abac-attributes.md)
- [Azure ロール割り当て条件の形式と構文 (プレビュー)](../../role-based-access-control/conditions-format.md)
