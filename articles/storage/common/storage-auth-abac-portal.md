---
title: 'チュートリアル: Azure portal を使用してロールの割り当て条件を追加し、BLOB へのアクセスを制限する (プレビュー) - Azure ABAC'
titleSuffix: Azure Storage
description: Azure portal と Azure の属性ベースのアクセス制御 (Azure ABAC) を使用してロールの割り当て条件を追加し、BLOB に対するアクセスを制限します。
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 09/24/2021
ms.openlocfilehash: 0ab670b9ef6309b8db01f4a53f41c703dea69423
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092283"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-the-azure-portal-preview"></a>チュートリアル: Azure portal を使用してロールの割り当て条件を追加し、BLOB へのアクセスを制限する (プレビュー)

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
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEqualsIgnoreCase 'Cascade'
    )
)
```

## <a name="step-1-create-a-user"></a>手順 1 - ユーザーを作成する

1. サブスクリプションの所有者として Azure portal にサインインします。

1. **[Azure Active Directory]** をクリックします。

1. ユーザーを作成するか、既存のユーザーを探します。 このチュートリアルでは、例として Chandra を使用しています。

## <a name="step-2-set-up-storage"></a>手順 2: ストレージを設定する

1. BLOB インデックス タグ機能 (現在はパブリック プレビュー段階) に対応しているストレージ アカウントを作成します。 詳細については、「[BLOB インデックス タグを使用して Azure BLOB データを管理および検索する (プレビュー)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)」を参照してください。

1. ストレージ アカウント内に新しいコンテナーを作成し、[公開] となっているアクセス レベルを **[プライベート (匿名アクセスなし)]** に設定します。

1. コンテナーで **[アップロード]** をクリックして、[BLOB のアップロード] ペインを開きます。

1. アップロードするテキスト ファイルを探します。

1. **[詳細設定]** をクリックしてペインを展開します。

1. **[Blob index tags]\(BLOB インデックス タグ\)** セクションで、テキスト ファイルに次の BLOB インデックス タグを追加します。

    [Blob index tags]\(BLOB インデックス タグ\) セクションが表示されず、サブスクリプションを登録したばかりの場合は、数分待つと変更が反映されることがあります。 詳細については、「[BLOB インデックス タグ (プレビュー) を使用して Azure Blob Storage でデータを管理および検索する](../blobs/storage-blob-index-how-to.md)」を参照してください。

    > [!NOTE]
    > BLOB ではほかにも、ユーザー定義による任意のキー値メタデータを格納する機能がサポートされています。 メタデータと BLOB インデックス タグはよく似ていますが、条件では BLOB インデックス タグを使用する必要があります。

    | キー | 値 |
    | --- | --- |
    | Project  | Cascade |

   ![[BLOB のアップロード] ペインの [Blog index tags]\(ブログ インデックス タグ\) セクションを示したスクリーンショット。](./media/storage-auth-abac-portal/container-upload-blob.png)

1. **[アップロード]** をクリックして、ファイルをアップロードします。

1. 2 つめのテキスト ファイルをアップロードします。

1. 2 つめのテキストファイルには、次の BLOB インデックス タグを追加します。

    | キー | 値 |
    | --- | --- |
    | Project  | Baker |

## <a name="step-3-assign-a-storage-blob-data-role"></a>手順 3: ストレージ BLOB のデータ ロールを割り当てる

1. リソース グループを開きます。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

1. **[追加]**  >  **[ロールの割り当ての追加 (プレビュー)]** の順にクリックします。

   ![[追加] > [ロールの割り当ての追加 (プレビュー)] メニューのスクリーンショット。](./media/storage-auth-abac-portal/add-role-assignment-menu-preview.png)

    [ロールの割り当ての追加] ページが開きます。

1. **[ロール]** タブで [[ストレージ BLOB データ閲覧者]](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) ロールを選択します。

    ![[ロールの割り当ての追加] ページで [ロール] タブが表示された状態のスクリーンショット。](./media/storage-auth-abac-portal/roles.png)

1. **[メンバー]** タブで、先ほど作成したユーザーを選択します。

   ![[ロールの割り当ての追加] ページで [メンバー] タブが表示された状態のスクリーンショット。](./media/storage-auth-abac-portal/members.png)

1. (省略可能) **[説明]** ボックスに「**タグ Project=Cascade が設定されている BLOB に対する読み取りアクセス**」と入力します。

1. **[次へ]** をクリックします。

## <a name="step-4-add-a-condition"></a>手順 4: 条件を追加する

1. **[条件 (省略可能)]** タブで **[条件の追加]** をクリックします。

    ![新しい条件の [ロールの割り当て条件を追加する] ページのスクリーンショット。](./media/storage-auth-abac-portal/condition-add-new.png)

    [ロールの割り当て条件を追加する] ページが表示されます。

1. [アクションの追加] セクションで、 **[操作の選択]** をクリックします。

    [アクションを選択します] ペインが表示されます。 このペインは、条件の対象となるロールの割り当てに基づいてデータに対するアクションをフィルター処理した一覧になっています。

    ![[アクションを選択します] ペインでアクションを 1 件選択した状態のスクリーンショット。](./media/storage-auth-abac-portal/condition-actions-select.png)

1. [Read a blog]\(ブログの読み取り\) の下にある **[Read content from a blob with tag conditions]\(タグ条件が設定されている BLOB からコンテンツを読み取る\)** をクリックしてから、 **[選択]** をクリックします。

1. [Build expression]\(式の作成\) セクションで、 **[式の追加]** をクリックします。

    [式] セクションが展開されます。

1. 次の式の設定を行います。

    | 設定 | 値 |
    | --- | --- |
    | Attribute source (属性ソース) | リソース |
    | 属性 | Blob index tags [Values in key] (BLOB インデックス タグ [キー内の値]) |
    | キー | Project |
    | 演算子 | StringEqualsIgnoreCase |
    | 値 | Cascade |

    ![BLOB インデックス タグの [Build expression]\(式の作成\) セクションのスクリーンショット。](./media/storage-auth-abac-portal/condition-expressions.png)

1. 上にスクロールして **[Editor type]\(エディターの種類\)** まで来たら、 **[コード]** をクリックします。

    条件がコードとして表示されます。 このコード エディターでは、条件を変更できます。 ビジュアル エディターに戻るには、 **[ビジュアル]** をクリックします。

    ![コード エディターに条件が表示された状態のスクリーンショット。](./media/storage-auth-abac-portal/condition-code.png)

1. **[保存]** をクリックして条件を追加し、[ロールの割り当ての追加] ページに戻ります。

1. **[次へ]** をクリックします。

1. **[Review + assign]\(確認と割り当て\)** タブで **[Review + assign]\(確認と割り当て\)** をクリックして、条件を設定したロールを割り当てます。

    しばらくすると、セキュリティ プリンシパルに選択されたスコープのロールが割り当てられます。

    ![ロールを割り当てた後のロールの割り当ての一覧のスクリーンショット。](./media/storage-auth-abac-portal/rg-role-assignments-condition.png)

## <a name="step-5-test-the-condition"></a>手順 5: 条件をテストする

条件をテストするには、Azure PowerShell を使用する必要があります。

1. PowerShell ウィンドウを開きます。

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
    Get-AzStorageBlob: This request is not authorized to perform this operation using this permission. HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sun, 13 Sep 2020 12:33:42 GMT
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

## <a name="step-6-clean-up-resources"></a>手順 6: リソースをクリーンアップする

1. 追加したロールの割り当てを削除します。

1. 作成したテスト用ストレージ アカウントを削除します。

1. 作成したユーザーを削除します。

## <a name="next-steps"></a>次のステップ

- [Azure のロールの割り当て条件の例](storage-auth-abac-examples.md)
- [Azure Storage での Azure のロールの割り当て条件のアクションと属性 (プレビュー)](storage-auth-abac-attributes.md)
- [Azure のロールの割り当て条件の形式と構文](../../role-based-access-control/conditions-format.md)
