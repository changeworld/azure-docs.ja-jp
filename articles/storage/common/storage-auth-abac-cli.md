---
title: 'チュートリアル: Azure CLI を使用してロールの割り当て条件を追加し、BLOB へのアクセスを制限する (プレビュー) - Azure ABAC'
titleSuffix: Azure Storage
description: Azure CLI と Azure の属性ベースのアクセス制御 (Azure ABAC) を使用してロールの割り当て条件を追加し、BLOB に対するアクセスを制限します。
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: 4050d9452a8253bbfe468b180a8eaa701e2a3684
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587463"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-cli-preview"></a>チュートリアル: Azure CLI を使用してロールの割り当て条件を追加し、BLOB へのアクセスを制限する (プレビュー)

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

## <a name="step-1-sign-in-to-azure"></a>手順 1 - Azure にサインインする

1. [az login](/cli/azure/reference-index#az_login) コマンドを実行し、表示された手順に従って[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)または[所有者](../../role-based-access-control/built-in-roles.md#owner)としてディレクトリにサインインします。

    ```azurecli
    az login
    ```

1. [az account show](/cli/azure/account#az_account_show) を使用して、サブスクリプションの ID を取得します。

    ```azurecli
    az account show
    ```

1. サブスクリプション ID を決め、変数を初期化します。

    ```azurecli
    subscriptionId="<subscriptionId>"
    ```

## <a name="step-2-create-a-user"></a>手順 2 - ユーザーを作成する

1. [Az ad user create](/cli/azure/ad/user#az_ad_user_create) を使用してユーザーを作成するか、既存のユーザーを探します。 このチュートリアルでは、例として Chandra を使用しています。

1. ユーザーのオブジェクト ID の変数を初期化します。

    ```azurecli
    userObjectId="<userObjectId>"
    ```

## <a name="step-3-set-up-storage"></a>手順 3: ストレージを設定する

Blob Storage へのアクセスは、Azure CLI から Azure AD の資格情報を使用するか、またはストレージ アカウントのアクセス キーを使用して承認することができます。 この記事では、Azure AD を使用して、Blob Storage の操作を承認する方法について説明します。 詳細については、「[クイックスタート:Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する](../blobs/storage-quickstart-blobs-cli.md)」を参照してください。

1. [az storage account](/cli/azure/storage/account) を使用して、BLOB インデックス機能に対応したストレージ アカウントを作成します。 詳細については、「[BLOB インデックス タグを使用して Azure BLOB データを管理および検索する (プレビュー)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)」を参照してください。

1. [az storage container](/cli/azure/storage/container) を使用してストレージ アカウント内に新しい BLOB コンテナーを作成し、[公開] となっているアクセス レベルを **[プライベート (匿名アクセスなし)]** に設定します。

1. [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) を使用して、コンテナーにテキスト ファイルをアップロードします。

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

    ```azurecli
    resourceGroup="<resourceGroup>"
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameCascade="<blobNameCascade>"
    blobNameBaker="<blobNameBaker>"
    ```

## <a name="step-4-assign-a-role-with-a-condition"></a>手順 4: 条件を設定したロールを割り当てる

1. [ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)ロールの変数を初期化します。

    ```azurecli
    roleDefinitionName="Storage Blob Data Reader"
    roleDefinitionId="2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. リソース グループのスコープを初期化します。

    ```azurecli
    scope="/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. 条件を初期化します。

    ```azurecli
    condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<\$key_case_sensitive\$>] StringEquals 'Cascade'))"
    ```

    Bash では、履歴展開が有効になっている場合、感嘆符 (!) が原因で "`bash: !: event not found`" というメッセージが表示されることがあります。 この場合、`set +H` コマンドを使用すると履歴展開を無効にできます。 履歴展開を再び有効にするには、`set -H` を使用します。

    Bash では、ドル記号 ($) が展開に関して特別な意味を持っています。 条件にドル記号 ($) が含まれている場合は、直前にバックスラッシュ (\\) が必要になることがあります。 たとえば、この条件ではタグ キー名を示すためにドル記号を使用しています。 Bash の引用符に関する規則の詳細については、「[二重引用符](https://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html)」を参照してください。

1. 条件のバージョンと説明を初期化します。

    ```azurecli
    conditionVersion="2.0"
    description="Read access to blobs with the tag Project=Cascade"
    ```

1. [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用して、条件を設定した[ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)ロールを、リソース グループのスコープ内のユーザーに割り当てます。

    ```azurecli
    az role assignment create --assignee-object-id $userObjectId --scope $scope --role $roleDefinitionId --description "$description" --condition "$condition" --condition-version $conditionVersion
    ```

    出力の例を次に示します。

    ```azurecli
    {
      "canDelegate": null,
      "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
      "conditionVersion": "2.0",
      "description": "Read access to blobs with the tag Project=Cascade",
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

## <a name="step-5-optional-view-the-condition-in-the-azure-portal"></a>手順 5: (省略可能) Azure portal で条件を表示する

1. Azure portal で、リソース グループを開きます。

1. **[アクセス制御 (IAM)]** をクリックします。

1. [ロールの割り当て] タブで、ロールの割り当てを探します。

1. **[条件]** 列で **[表示または編集]** をクリックすると、条件が表示されます。

    ![Azure portal 内の [ロールの割り当て条件を追加する] のスクリーンショット。](./media/shared/condition-view.png)

## <a name="step-6-test-the-condition"></a>手順 6: 条件をテストする

1. 新しいコマンド ウィンドウを開きます。

1. [az login](/cli/azure/reference-index#az_login) を使用して、Chandra としてサインインします。

    ```azurecli
    az login
    ```

1. 次の変数を、使用した名前で初期化します。

    ```azurecli
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameBaker="<blobNameBaker>"
    blobNameCascade="<blobNameCascade>"
    ```

1. [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) を使用して、Baker プロジェクトに関連するファイルのプロパティの読み取りを試します。

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameBaker --auth-mode login
    ```

    出力の例を次に示します。 追加した条件が原因となってファイルを読み取ることが **できない** ことがわかります。

    ```azurecli
    You do not have the required permissions needed to perform this operation.
    Depending on your operation, you may need to be assigned one of the following roles:
        "Storage Blob Data Contributor"
        "Storage Blob Data Reader"
        "Storage Queue Data Contributor"
        "Storage Queue Data Reader"

    If you want to use the old authentication method and allow querying for the right account key, please use the "--auth-mode" parameter and "key" value.
    ```

1. Cascade プロジェクトに関連するファイルのプロパティを読み取ります。

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameCascade --auth-mode login 
    ```

    出力の例を次に示します。 ファイルに Project=Cascade タグが設定されているため、ファイルのプロパティを読み取ることができます。

    ```azurecli
    {
      "container": "<containerName>",
      "content": "",
      "deleted": false,
      "encryptedMetadata": null,
      "encryptionKeySha256": null,
      "encryptionScope": null,
      "isAppendBlobSealed": null,
      "isCurrentVersion": null,
      "lastAccessedOn": null,
      "metadata": {},
      "name": "<blobNameCascade>",
      "objectReplicationDestinationPolicy": null,
      "objectReplicationSourceProperties": [],
      "properties": {
        "appendBlobCommittedBlockCount": null,
        "blobTier": "Hot",
        "blobTierChangeTime": null,
        "blobTierInferred": true,
        "blobType": "BlockBlob",
        "contentLength": 7,
        "contentRange": null,

      ...

    }
    ```

## <a name="step-7-optional-edit-the-condition"></a>手順 7: (省略可能) 条件を編集する

1. もう一方のコマンド ウィンドウで [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) を使用して、追加したロールの割り当てを取得します。

    ```azurecli
    az role assignment list --assignee $userObjectId --resource-group $resourceGroup
    ```

    出力は次のようになります。

    ```azurecli
    [
      {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ]
    ```

1. 次の形式の JSON ファイルを作成し、`condition` と `description` の 2 つのプロパティを更新します。

    ```json
    {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Baker'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade or Project=Baker",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

1. [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) を使用して、ロールの割り当ての条件を更新します。

    ```azurecli
    az role assignment update --role-assignment "./path/roleassignment.json"
    ```

## <a name="step-8-clean-up-resources"></a>手順 8: リソースをクリーンアップする

1. [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) を使用して、追加したロールの割り当てと条件を削除します。

    ```azurecli
    az role assignment delete --assignee $userObjectId --role "$roleDefinitionName" --resource-group $resourceGroup
    ```

1. 作成したストレージ アカウントを削除します。

1. 作成したユーザーを削除します。

## <a name="next-steps"></a>次のステップ

- [Azure のロールの割り当て条件の例](storage-auth-abac-examples.md)
- [Azure Storage での Azure のロールの割り当て条件のアクションと属性 (プレビュー)](storage-auth-abac-attributes.md)
- [Azure のロールの割り当て条件の形式と構文](../../role-based-access-control/conditions-format.md)
