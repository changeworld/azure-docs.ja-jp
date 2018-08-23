---
title: Azure SQL Database Managed Instance にバックアップを復元する | Microsoft Docs
description: SSMS を使用して Azure SQL Database マネージド インスタンスにデータベース バックアップを復元します。
keywords: SQL Database チュートリアル, SQL Database Managed Instance, バックアップの復元
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 5dd8b1f662f1ae6d6502743c6d976db4b58e962f
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "41921038"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Azure SQL Database マネージド インスタンスにデータベース バックアップを復元する

このチュートリアルでは、Wide World Importers - Standard バックアップ ファイルを使用して、Azure Blob Storage に格納されたデータベースのバックアップをマネージド インスタンスに復元する方法について説明します。 この方法には、ある程度のダウンタイムが必要です。 Azure Database Migration Service (DMS) を使用して移行する方法のチュートリアルについては、[DMS を使用したマネージド インスタンスの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。 各種の移行方法の説明については、「[Azure SQL Database Managed Instance への SQL Server インスタンスの移行](sql-database-managed-instance-migrate.md)」を参照してください。

> [!div class="checklist"]
> * Wide World Importers - 標準のバックアップ ファイルをダウンロードする
> * Azure ストレージ アカウントを作成してバックアップ ファイルをアップロードする
> * バックアップ ファイルから Wide World Importers データベースを復元する

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、[Azure SQL Database マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するチュートリアルで作成したリソースを出発点として使用します。

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Wide World Importers - 標準のバックアップ ファイルをダウンロードする

ここでは、Wide World Importers - 標準のバックアップ ファイルをダウンロードする手順について説明します。

Internet Explorer を使用して、URL アドレス ボックスに https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak と入力し、メッセージが表示されたら **[保存]** をクリックし、このファイルを **Downloads** フォルダーに保存します。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/#create/Microsoft.SQLManagedInstance) にログインします。

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Azure ストレージ アカウントを作成してバックアップ ファイルをアップロードする

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[ストレージ]** を見つけ、**[ストレージ アカウント]** をクリックしてストレージ アカウント フォームを開きます。

   ![ストレージ アカウント](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. 次の表の情報を参考にして、ストレージ アカウントのフォームに必要な情報を入力します。

   | Setting| 推奨値 | Description |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**デプロイ モデル**|リソース モデル||
   |**アカウントの種類**|BLOB ストレージ||
   |**パフォーマンス**|Standard または Premium|磁気ドライブまたは SSD|
   |**レプリケーション**|ローカル冗長ストレージ||
   |\*\*アクセス層 (既定)|クールまたはホット||
   |**安全な転送が必須**|Disabled||
   |**サブスクリプション**|該当するサブスクリプション|サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。|
   |**[リソース グループ]**|以前の手順で作成したリソース グループ|| 
   |**場所**|以前の手順で選択した場所||
   |**仮想ネットワーク**|Disabled||

4. **Create** をクリックしてください。

   ![ストレージ アカウントの詳細](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. ストレージ アカウントの展開が成功したら、新しいストレージ アカウントを開きます。
6. **[設定]** の **[Shared Access Signature]** をクリックし、Shared Access Signature (SAS) フォームを開きます。

   ![SAS フォーム](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. SAS フォームで、必要に応じて既定値を変更します。 有効期限は既定で 8 時間です。
8. **[SAS の生成]** をクリックします。

   ![SAS フォームの入力](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. **[SAS トークン]** と **[Blob server SAS URL]\(BLOB サーバーの SAS の URL\)** をコピーして保存します。
10. **[設定]** で **[コンテナー]** をクリックします。

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. **[+ コンテナー]** をクリックしてバックアップ ファイルを格納するコンテナーを作成します。
12. 次の表の情報を参考にして、コンテナーのフォームに必要な情報を入力します。

    | Setting| 推奨値 | Description |
   | ------ | --------------- | ----------- |
   |**名前**|有効な名前|有効なサーバー名については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**パブリック アクセス レベル**|プライベート (匿名アクセスなし)||

    ![コンテナーの詳細](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Click **OK**.
14. コンテナーが作成されたら、コンテナーを開きます。

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. **[コンテナーのプロパティ]** をクリックし、URL をコンテナーにコピーします。

    ![コンテナーの URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. **[アップロード]** をクリックして **[BLOB のアップロード]** フォームを開きます。

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. ダウンロード フォルダーを参照し、**WideWorldIimporters-Standard.bak** ファイルを選択します。

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. **[アップロード]** をクリックします。
19. アップロードが完了するまで、次の手順には進まないでください。

    ![アップロード完了](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>バックアップ ファイルから Wide World Importers データベースを復元する

SSMS で、以下の手順を使用して、バックアップ ファイルから Wide World Importers データベースをマネージド インスタンスに復元します。

1. SSMS で、新しいクエリ ウィンドウを開きます。
2. 次のスクリプトを使用して SAS 資格情報を作成します。ストレージ アカウント コンテナーと SAS キーの URL を指示に従って入力します。

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![credential](./media/sql-database-managed-instance-tutorial/credential.png)

3. 次のスクリプトを使用して、SAS 資格情報とバックアップが有効であることを確認します。コンテナーの URL にバックアップ ファイルを指定します。

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![ファイル一覧](./media/sql-database-managed-instance-tutorial/file-list.png)

4. 次のスクリプトを使用して、バックアップ ファイルから Wide World Importers データベースを復元します。コンテナーの URL にバックアップ ファイルを指定します。

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![復元の実行](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. 復元の状態を追跡するには、新しいクエリ セッションで次のクエリを実行します。

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![復元の完了率](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. 復元が完了したら、オブジェクト エクスプローラーで確認します。 

    ![復元の完了](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Wide World Importers - Standard バックアップ ファイルを使用して、Azure Blob Storage に格納されたデータベースのバックアップをマネージド インスタンスに復元する方法について学習しました。 以下の方法について学習しました。 

> [!div class="checklist"]
> * Wide World Importers - 標準のバックアップ ファイルをダウンロードする
> * Azure ストレージ アカウントを作成してバックアップ ファイルをアップロードする
> * バックアップ ファイルから Wide World Importers データベースを復元する

次のチュートリアルに進んで、DMS を使用して SQL Server を Azure SQL Database Managed Instance に移行する方法を学習してください。

> [!div class="nextstepaction"]
>
  [DMS を使用して SQL Server を Azure SQL Database Managed Instance に移行する](../dms/tutorial-sql-server-to-managed-instance.md)
