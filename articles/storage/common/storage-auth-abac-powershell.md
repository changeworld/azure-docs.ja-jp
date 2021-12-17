---
title: 'チュートリアル: Azure PowerShell を使用してロールの割り当て条件を追加し、BLOB へのアクセスを制限する (プレビュー) - Azure ABAC'
titleSuffix: Azure Storage
description: Azure PowerShell と Azure の属性ベースのアクセス制御 (Azure ABAC) を使用してロールの割り当て条件を追加し、BLOB に対するアクセスを制限します。
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: 19aee45977dfde4b401dc4736e24970e311a4cc9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621131"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-powershell-preview"></a>チュートリアル: Azure PowerShell を使用してロールの割り当て条件を追加し、BLOB へのアクセスを制限する (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure のロールの割り当て条件は、現在プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ほとんどの場合、ロールの割り当てを使えば Azure リソースに必要なアクセス許可を付与できます。 しかし、さらにきめ細かなアクセス制御をしたい局面が出てくることもあるでしょう。そのような場合には、ロールの割り当て条件を追加します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> - ロールの割り当てに条件を追加する
> - BLOB インデックス タグに基づいて BLOB に対するアクセスを制限する

## <a name="prerequisites"></a>前提条件

ロールの割り当て条件を追加または編集するための前提条件の詳細については、[条件の前提条件](../../role-based-access-control/conditions-prerequisites.md)に関するページを参照してください。

## <a name="condition"></a>条件

このチュートリアルでは、特定のタグが設定されている BLOB に対するアクセスを制限します。 たとえば、Chandra が読み取ることができるファイルを Project=Cascade というタグが設定されているものだけにするような条件を、ロールの割り当てに追加します。

![条件を設定したロールの割り当ての図。](./media/shared/condition-role-assignment-rg.png)

Chandra が Project=Cascade タグの設定されていない BLOB を読み取ろうとしても、アクセスが許可されません。

![Project=Cascade タグが設定されている BLOB に対する読み取りアクセスを示した図。](./media/shared/condition-access.png)

条件をコードで示すと、次のようになります。

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

## <a name="step-1-install-prerequisites"></a>手順 1: 前提条件をインストールする

1. PowerShell ウィンドウを開きます。

1. [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule) を使用して、インストール済みのモジュールのバージョンを確認します。

    ```azurepowershell
    Get-InstalledModule -Name Az
    Get-InstalledModule -Name Az.Resources
    Get-InstalledModule -Name Az.Storage
    ```

1. 必要であれば、[Install-Module](/powershell/module/powershellget/install-module) を使用して、必要なバージョンのモジュール ([Az](https://www.powershellgallery.com/packages/Az/)、[Az.Resources](https://www.powershellgallery.com/packages/Az.Resources/)、[Az.Storage](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) の 3 つ) をインストールします。

    ```azurepowershell
    Install-Module -Name Az -RequiredVersion 5.5.0
    Install-Module -Name Az.Resources -RequiredVersion 3.2.1
    Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
    ```

1. PowerShell をいったん閉じてから開き直し、セッションを更新します。

## <a name="step-2-sign-in-to-azure"></a>手順 2 - Azure にサインインする

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドを実行し、表示された手順に従って[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)または[所有者](../../role-based-access-control/built-in-roles.md#owner)としてディレクトリにサインインします。

    ```azurepowershell
    Connect-AzAccount
    ```

1. [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) を使用して、サブスクリプションの一覧を表示します。

    ```azurepowershell
    Get-AzSubscription
    ```

1. サブスクリプション ID を決め、変数を初期化します。

    ```azurepowershell
    $subscriptionId = "<subscriptionId>"
    ```

1. そのサブスクリプションを、アクティブなサブスクリプションとして設定します。

    ```azurepowershell
    $context = Get-AzSubscription -SubscriptionId $subscriptionId
    Set-AzContext $context
    ```

## <a name="step-3-create-a-user"></a>手順 3 - ユーザーを作成する

1. [New-AzureADUser](/powershell/module/azuread/new-azureaduser) を使用してユーザーを作成するか、既存のユーザーを探します。 このチュートリアルでは、例として Chandra を使用しています。

1. ユーザーのオブジェクト ID の変数を初期化します。

    ```azurepowershell
    $userObjectId = "<userObjectId>"
    ```

## <a name="step-4-set-up-storage"></a>手順 4: ストレージを設定する

1. [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) を使用して、BLOB インデックス機能に対応したストレージ アカウントを作成します。 詳細については、「[BLOB インデックス タグを使用して Azure BLOB データを管理および検索する (プレビュー)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)」を参照してください。

1. [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) を使用してストレージ アカウント内に新しい BLOB コンテナーを作成し、[公開] となっているアクセス レベルを **[プライベート (匿名アクセスなし)]** に設定します。

1. [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) を使用してコンテナーにテキスト ファイルをアップロードします。

1. そのテキストファイルに、次の BLOB インデックス タグを追加します。 詳細については、「[BLOB インデックス タグ (プレビュー) を使用して Azure Blob Storage でデータを管理および検索する](../blobs/storage-blob-index-how-to.md)」を参照してください。

    > [!NOTE]
    > BLOB ではほかにも、ユーザー定義による任意のキー値メタデータを格納する機能がサポートされています。 メタデータと BLOB インデックス タグはよく似ていますが、条件では BLOB インデックス タグを使用する必要があります。

    | キー | 値 |
    | --- | --- |
    | Project  | Cascade |

1. コンテナーに 2 つめのテキスト ファイルをアップロードします。

1. 2 つめのテキストファイルには、次の BLOB インデックス タグを追加します。

    | キー | 値 |
    | --- | --- |
    | Project  | Baker |

1. 次の変数を、使用した名前で初期化します。

    ```azurepowershell
    $resourceGroup = "<resourceGroup>"
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameCascade = "<blobNameCascade>"
    $blobNameBaker = "<blobNameBaker>"
    ```

## <a name="step-5-assign-a-role-with-a-condition"></a>手順 5: 条件を設定したロールを割り当てる

1. [ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)ロールの変数を初期化します。

    ```azurepowershell
    $roleDefinitionName = "Storage Blob Data Reader"
    $roleDefinitionId = "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. リソース グループのスコープを初期化します。

    ```azurepowershell
    $scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. 条件を初期化します。

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
    ```

    PowerShell では、条件にドル記号 ($) が含まれている場合は、その前にバッククォート (\`) を付ける必要があります。 たとえば、この条件ではタグ キー名を示すためにドル記号を使用しています。

1. 条件のバージョンと説明を初期化します。

    ```azurepowershell
    $conditionVersion = "2.0"
    $description = "Read access to blobs with the tag Project=Cascade"
    ```

1. [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用して、条件を設定した[ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)ロールを、リソース グループのスコープ内のユーザーに割り当てます。

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $userObjectId -Scope $scope -RoleDefinitionId $roleDefinitionId -Description $description -Condition $condition -ConditionVersion $conditionVersion
    ```

    出力の例を次に示します。

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))
    ```

## <a name="step-6-optional-view-the-condition-in-the-azure-portal"></a>手順 6: (省略可能) Azure portal で条件を表示する

1. Azure portal で、リソース グループを開きます。

1. **[アクセス制御 (IAM)]** をクリックします。

1. [ロールの割り当て] タブで、ロールの割り当てを探します。

1. **[条件]** 列で **[表示または編集]** をクリックすると、条件が表示されます。

    ![Azure portal 内の [ロールの割り当て条件を追加する] のスクリーンショット。](./media/shared/condition-view.png)

## <a name="step-7-test-the-condition"></a>手順 7: 条件をテストする

1. 新しい PowerShell ウィンドウを開きます。

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) を使用して、Chandra としてサインインします。

    ```azurepowershell
    Connect-AzAccount
    ```

1. 次の変数を、使用した名前で初期化します。

    ```azurepowershell
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameBaker = "<blobNameBaker>"
    $blobNameCascade = "<blobNameCascade>"
    ```

1. [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) を使用して、ストレージ アカウントにもっと簡単にアクセスするための特殊なコンテキストを作成します。

    ```azurepowershell
    $bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
    ```

1. [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) を使用して、Baker プロジェクトに関連するファイルの読み取りを試します。

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Context $bearerCtx 
    ```

    出力の例を次に示します。 追加した条件が原因となってファイルを読み取ることが **できない** ことがわかります。

    ```azurepowershell
    Get-AzStorageBlob : This request is not authorized to perform this operation using this permission. HTTP Status Code:
    403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sat, 24 Apr 2021 13:26:25 GMT
    At line:1 char:1
    + Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Con ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzStorageBlob], StorageException
        + FullyQualifiedErrorId : StorageException,Microsoft.WindowsAzure.Commands.Storage.Blob.Cmdlet.GetAzureStorageBlob
       Command
    ```

1. Cascade プロジェクトに関連するファイルを読み取ります。

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameCascade -Context $bearerCtx 
    ```

    出力の例を次に示します。 ファイルに Project=Cascade タグが設定されているため、読み取ることができます。

    ```azurepowershell
       AccountName: <storageAccountName>, ContainerName: <containerName>

    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="step-8-optional-edit-the-condition"></a>手順 8: (省略可能) 条件を編集する

1. もう一方の PowerShell ウィンドウで [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) を使用して、追加したロールの割り当てを取得します。

    ```azurepowershell
    $testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectId
    ```

1. 条件を編集します。

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Baker'))"
    ```

1. 条件と説明を初期化します。

    ```azurepowershell
    $testRa.Condition = $condition
    $testRa.Description = "Read access to blobs with the tag Project=Cascade or Project=Baker"
    ```

1. [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) を使用して、ロールの割り当ての条件を更新します。

    ```azurepowershell
    Set-AzRoleAssignment -InputObject $testRa -PassThru
    ```

    出力の例を次に示します。

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade or Project=Baker
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.S
                         torage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>]
                         StringEquals 'Baker'))
    ```

## <a name="step-9-clean-up-resources"></a>手順 9: リソースをクリーンアップする

1. [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) を使用して、追加したロールの割り当てと条件を削除します。

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $userObjectId -RoleDefinitionName $roleDefinitionName -ResourceGroupName $resourceGroup
    ```

1. 作成したストレージ アカウントを削除します。

1. 作成したユーザーを削除します。

## <a name="next-steps"></a>次のステップ

- [Azure のロールの割り当て条件の例](storage-auth-abac-examples.md)
- [Azure Storage での Azure のロールの割り当て条件のアクションと属性 (プレビュー)](storage-auth-abac-attributes.md)
- [Azure のロールの割り当て条件の形式と構文](../../role-based-access-control/conditions-format.md)
