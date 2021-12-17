---
title: Azure Database for PostgreSQL のバックアップのトラブルシューティング
description: Azure Database for PostgreSQL のバックアップに関するトラブルシューティング情報。
ms.topic: troubleshooting
ms.date: 09/22/2021
ms.openlocfilehash: 0dcb5b3a85fd1364d90327648274194d3bc30364
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699235"
---
# <a name="troubleshoot-postgresql-database-backup-by-using-azure-backup-preview"></a>Azure Backup を使用した PostgreSQL データベースのバックアップのトラブルシューティング (プレビュー)

この記事では、Azure Backup を使用した Azure PostgreSQL データベースのバックアップに関するトラブルシューティング情報を記載します。

## <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

バックアップ コンテナー MSI に対して、バックアップまたは復元する PG サーバーの **読み取り** アクセス権を付与します。

PostgreSQL データベースへのセキュリティで保護された接続を確立するために、Azure Backup では、[マネージド サービス ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 認証モデルが使用されます。 したがって、バックアップ コンテナーは、ユーザーによって明示的にアクセス許可が付与されているリソースにのみアクセスできます。

システム MSI は、作成時に自動的にコンテナーに割り当てられます。 このコンテナー MSI に、データベースのバックアップ元となる PostgreSQL サーバーへのアクセス権を付与する必要があります。

手順:

1. Postgres サーバーで、 **[アクセス制御 (IAM)]** ペインに移動します。

    ![[アクセス制御] ペイン](./media/backup-azure-database-postgresql/access-control-pane.png)

1. **[ロールの割り当ての追加]** を選択します。

    ![[ロールの割り当ての追加] のスクリーンショット。](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. 開いた右側のコンテキスト ペインで、次の情報を入力します。<br>

   - **ロール:** ドロップダウン リストから **[閲覧者]** ロールを選択します。<br>
   - **アクセスの割り当て先:** ドロップダウン リストから **[ユーザー、グループ、またはサービス プリンシパル]** オプションを選択します。<br>
   - **選択:** このサーバーとそのデータベースをバックアップする先のバックアップ コンテナーの名前を入力します。<br>

    ![ロールの選択方法を示すスクリーンショット。](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

## <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Azure Active Directory で認証できるデータベース バックアップ ユーザーを作成します。

このエラーは、PostgreSQL サーバーの Azure Active Directory 管理者が存在しない場合、または Azure Active Directory を使用して認証できるバックアップ ユーザーが存在しない場合に発生する可能性があります。

手順:

OSS サーバーに Active Directory 管理者を追加します。

パスワードの代わりに Azure Active Directory で認証できるユーザーを使用してデータベースに接続する場合、この手順が必要です。 Azure Database for PostgreSQL の Azure AD 管理者ユーザーは **azure_ad_admin** ロールを所有します。 Azure AD で認証できる新しいデータベース ユーザーを作成できるのは、**azure_ad_admin** ロールのみです。

1. サーバー ビューの左側のナビゲーション ウィンドウにある [Active Directory 管理者] タブに移動し、Active Directory 管理者として自分 (または他のユーザー) を追加します。

    ![Active Directory 管理者の設定方法を示すスクリーンショット。](./media/backup-azure-database-postgresql/set-admin.png)

1. 必ず、AD 管理者ユーザー設定を **保存** してください。

    ![Active Directory 管理者ユーザー設定の保存方法を示すスクリーンショット。](./media/backup-azure-database-postgresql/save-admin-setting.png)

アクセス許可の付与手順を完了するために実行する必要がある手順の一覧については、[こちらのドキュメント](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)をご覧ください。

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

サーバー ビューで **[Azure サービスへのアクセス許可]** フラグを有効にすることで、ネットワーク接続を確立ます。 サーバー ビューの **[接続のセキュリティ]** ペインで、 **[Azure サービスへのアクセス許可]** フラグを **[はい]** に設定します。

>[!Note]
>このフラグを有効にする前に、 **[パブリック ネットワーク アクセスの拒否]** フラグを **[いいえ]** に設定したことを確認してください。

![Azure サービスへのアクセスを許可する方法を示すスクリーンショット。](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

## <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>ファイルとして復元するときにストレージ アカウント コンテナーに復元するアクセス許可

1. Azure portal を使用して、ストレージ アカウント コンテナーにアクセスするためのアクセス許可をバックアップ コンテナー MSI に付与します。
    1. **[ストレージ アカウント]**  ->  **[アクセス制御]**  ->  **[ロールの割り当ての追加]** に移動します。
    1. **[ストレージ BLOB データ共同作成者]** ロールをバックアップ コンテナー MSI に割り当てます。

    ![ストレージ BLOB データ共同作成者ロールの割り当てプロセスを示すスクリーンショット。](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. または、Azure CLI で [az role assignment create](/cli/azure/role/assignment) コマンドを使用して、復元先の特定のコンテナーに詳細なアクセス許可を付与します。

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. assignee パラメーターをコンテナーの MSI の **アプリケーション ID** に置き換え、scope パラメーターで特定のコンテナーを参照します。
    1. コンテナー MSI の **アプリケーション ID** を取得するには、 **[アプリケーションの種類]** で **[すべてのアプリケーション]** を選択します。

        ![[すべてのアプリケーション] の選択方法を示すスクリーンショット。](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. コンテナー名を検索し、アプリケーション ID をコピーします。

        ![コンテナー名の検索方法を示すスクリーンショット。](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>次のステップ

[Azure Database for PostgreSQL のバックアップについて (プレビュー)](backup-azure-database-postgresql-overview.md)
