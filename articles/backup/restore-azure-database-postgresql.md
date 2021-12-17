---
title: Azure Database for PostgreSQL を復元する
description: Azure Database for PostgreSQL のバックアップを復元する方法について説明します。
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2eb0d8bd462c50a0da09b4da059d8c0d185dbd3e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020523"
---
# <a name="restore-azure-database-for-postgresql-backups-preview"></a>Azure Database for PostgreSQL のバックアップを復元する (プレビュー)

この記事では、Azure Backup によってバックアップされた Azure PostgreSQL サーバーにデータベースを復元する方法について説明します。

ターゲット サーバーに対する適切な[一連のアクセス許可](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server)がサービスにある場合は、同じサブスクリプション内の任意の Azure PostgreSQL サーバーにデータベースを復元できます。

## <a name="restore-azure-postgresql-database"></a>Azure PostgreSQL データベースを復元する

1. **[バックアップ コンテナー]**  ->  **[Backup Instances]\(バックアップ インスタンス\)** に移動します。 データベースを選択し、 **[復元]** をクリックします。

   :::image type="content" source="./media/restore-azure-database-postgresql/select-database-for-restore-inline.png" alt-text="データベースを選択して復元するプロセスを示すスクリーンショット。" lightbox="./media/restore-azure-database-postgresql/select-database-for-restore-expanded.png":::

   また、[バックアップ センター](./backup-center-overview.md)からこのページに移動することもできます。    
  
1. **[復元ポイントの選択]** ページで、選択したバックアップ インスタンスで利用できるすべての完全バックアップの一覧からいずれかの復旧ポイントを選択します。 既定では、最新の復旧ポイントが選択されます。

   :::image type="content" source="./media/restore-azure-database-postgresql/select-restore-point-inline.png" alt-text="復旧ポイントの選択プロセスを示すスクリーンショット。" lightbox="./media/restore-azure-database-postgresql/select-restore-point-expanded.png":::

   復元ポイントがアーカイブ層にある場合は、復元する前に復旧ポイントをリハイドレートする必要があります。 リハイドレートに必要な次の追加パラメーターを指定します。

   - **Rehydration priority \(リハイドレートの優先順位\):** 既定値は **Standard** です。
   - **Rehydration duration \(リハイドレート期間\):** 最大リハイドレート期間は 30 日間、最小リハイドレート期間は 10 日間です。 既定値は **15 日** です。 復旧ポイントは、この期間、**バックアップ データ ストア** に格納されます。

1. **[Restore parameters]\(復元のパラメーター\)** ページで、 **[Restore as Database]\(データベースとして復元\)** と **[Restore as Files]\(ファイルとして復元\)** のどちらかの復元タイプを選択します。

   - **[Restore as Database]\(データベースとして復元\)**

     ターゲット サーバーは、ソース サーバーと同じにすることができます。 ただし、元のデータベースの上書きはサポートされていません。 すべてのサブスクリプションのサーバーから、コンテナーと同じリージョン内のサーバーを選択できます。

     1. **[Select key vault and the secret]\(キー コンテナーとシークレットの選択\)** ボックスの一覧から、ターゲット サーバーに接続するための資格情報が格納されるコンテナーを選択します。

     1. **[レビューと復元]** を選択すると検証がトリガーされ、サービスに [ターゲット サーバーに対する復元のアクセス許可がある](backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore)か確認されます。 これらのアクセス許可は[手動で付与](backup-azure-database-postgresql-overview.md#grant-access-on-the-azure-postgresql-server-and-key-vault-manually)する必要があります。

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-database-inline.png" alt-text="[Restore as Database]\(データベースとして復元\) として選択された復元タイプを示すスクリーンショット。" lightbox="./media/restore-azure-database-postgresql/restore-as-database-expanded.png":::

   - **[Restore as Files: Dump the backup files to the target storage account (blobs).]\(ファイルとして復元: バックアップ ファイルをターゲット ストレージ アカウント (BLOB) にダンプします。\)**

     すべてのサブスクリプションのストレージ アカウントから、コンテナーと同じリージョン内のストレージ アカウントを選択できます。     

     1. **[Select the target container]\(ターゲット コンテナーの選択\)** ボックスの一覧から、選択したストレージ アカウントについてフィルターで抽出されたコンテナーのいずれかを選択します。
     1. **[レビューと復元]** を選択して、[ターゲット ストレージ アカウントに対する復元のアクセス許可](#restore-permissions-on-the-target-storage-account)がバックアップ サービスにあるかどうかをチェックするための検証をトリガーします。

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-files-inline.png" alt-text="[Restore as Files]\(ファイルとして復元\) として選択された復元タイプを示すスクリーンショット。" lightbox="./media/restore-azure-database-postgresql/restore-as-files-expanded.png":::
   
1. 復元操作を送信し、トリガーされたジョブを **[バックアップ ジョブ]** で追跡します。
   
   :::image type="content" source="./media/restore-azure-database-postgresql/track-triggered-job-inline.png" alt-text="トリガーされたジョブを [バックアップ ジョブ] で追跡する画面のスクリーンショット。" lightbox="./media/restore-azure-database-postgresql/track-triggered-job-expanded.png":::

>[!NOTE]
>Azure Database for PostgreSQL のアーカイブ サポートは、限定パブリック プレビュー段階です。

## <a name="restore-permissions-on-the-target-storage-account"></a>ターゲット ストレージ アカウントに対するアクセス許可を復元する

Azure portal を使用して、ストレージ アカウント コンテナーにアクセスするためのアクセス許可をバックアップ コンテナーの MSI に割り当てます。

1. **[ストレージ アカウント]**  ->  **[アクセス制御]**  ->  **[ロールの割り当ての追加]** に移動します。

1. バックアップ コンテナーの MSI に対する **[ストレージ BLOB データ共同作成者]** ロールを **[ロール]** ボックスの一覧から選択します。

   :::image type="content" source="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-inline.png" alt-text="Azure portal を使用して、ストレージ アカウント コンテナーにアクセスするためのアクセス許可をバックアップ コンテナーの MSI に割り当てるプロセスを示すスクリーンショット。" lightbox="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-expanded.png":::

または、Azure CLI で [az role assignment](/cli/azure/role/assignment) create コマンドを使用して、復元先の特定のコンテナーに詳細なアクセス許可を付与します。

```azurecli
az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
```
assignee パラメーターをコンテナーの MSI の _アプリケーション ID_ に置き換え、scope パラメーターで特定のコンテナーを参照します。 コンテナー MSI の **アプリケーション ID** を取得するには、 **[アプリケーションの種類]** で **[すべてのアプリケーション]** を選択します。 コンテナー名を検索し、アプリケーション ID をコピーします。

 :::image type="content" source="./media/restore-azure-database-postgresql/select-application-type-for-id-inline.png" alt-text="コンテナーの MSI のアプリケーション ID を取得するプロセスを示すスクリーンショット。" lightbox="./media/restore-azure-database-postgresql/select-application-type-for-id-expanded.png":::

 :::image type="content" source="./media/restore-azure-database-postgresql/copy-vault-id-inline.png" alt-text="コンテナーのアプリケーション ID をコピーするプロセスを示すスクリーンショット。" lightbox="./media/restore-azure-database-postgresql/copy-vault-id-expanded.png":::
 
## <a name="next-steps"></a>次のステップ

[Azure Backup を使用した PostgreSQL データベースのバックアップのトラブルシューティング](backup-azure-database-postgresql-troubleshoot.md)