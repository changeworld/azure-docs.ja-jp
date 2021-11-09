---
title: 台帳テーブルを検証して改ざんを検出する
description: この記事では、Azure SQL Database テーブルが改ざんされているかどうかを検証する方法について説明します。
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: rothja
ms.author: jroth
ms.reviewer: vanto
ms.date: 09/09/2021
ms.openlocfilehash: 272138471499cbd62085301df8e518ea47a7ddc3
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058848"
---
# <a name="verify-a-ledger-table-to-detect-tampering"></a>台帳テーブルを検証して改ざんを検出する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

この記事では、Azure SQL Database 台帳テーブル内のデータの整合性を検証します。 [SQL Database でデータベースを作成](ledger-create-a-single-database-with-ledger-enabled.md)するときに **[自動ダイジェスト ストレージを有効にする]** を選択していた場合、Azure portal の指示に従って、[Query Editor](connect-query-portal.md) 内のデータベース台帳を検証するために必要な Transact-SQL (T-SQL) スクリプトを自動的に生成してください。 それ以外の場合、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用して、T-SQL の指示に従ってください。

## <a name="prerequisites"></a>前提条件

- 有効な Azure サブスクリプションを持っている。 アカウントがない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- [台帳が有効にされた SQL Database でデータベースを作成します](ledger-create-a-single-database-with-ledger-enabled.md)。
- [更新可能な台帳テーブルを作成して使用](ledger-how-to-updatable-ledger-tables.md)するか、[追加専用の台帳テーブルを作成して使用](ledger-how-to-append-only-ledger-tables.md)します。

## <a name="run-ledger-verification-for-sql-database"></a>SQL Database に対して台帳の検証を実行する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) を開き、 **[すべてのリソース]** を選択して、検証するデータベースを見つけます。 SQL Database でそのデータベースを選択します。

     :::image type="content" source="media/ledger/ledger-portal-all-resources.png" alt-text="[すべてのリソース] タブが選択されている Azure portal を示すスクリーンショット。":::

1. **[セキュリティ]** で、 **[台帳]** オプションを選択します。

   :::image type="content" source="media/ledger/ledger-portal-manage-ledger.png" alt-text="[セキュリティ台帳] タブが選択されている Azure portal を示すスクリーンショット。":::

1. **[台帳]** ペインで、 **[</> データベースの検証]** を選択し、ウィンドウ内の事前にデータが取り込まれたテキスト内で **[コピー]** アイコンを選択します。

     :::image type="content" source="media/ledger/ledger-portal-verify.png" alt-text="Azure portal の [データベースの検証] ボタン":::

   > > [!IMPORTANT]
   > データベース ダイジェストに対して自動ダイジェスト ストレージを構成していない場合に、代わりに手動でダイジェストを管理しているときは、このスクリプトをコピーしないでください。 手順 6 に進みます。

1. 左側のメニューで **[クエリ エディター]** を開きます。

     :::image type="content" source="media/ledger/ledger-portal-open-query-editor.png" alt-text="Azure portal の [クエリ エディター] メニュー オプションを示すスクリーンショット。":::

1. クエリ エディターで、手順 3 でコピーした T-SQL スクリプトを貼り付け、 **[実行]** を選択します。 手順 8 に進みます。

   :::image type="content" source="media/ledger/ledger-portal-run-query-editor.png" alt-text="クエリ エディターを実行してデータベースを検証する Azure portal を示すスクリーンショット。":::

1. 手動のダイジェスト ストレージを使用している場合、次の T-SQL をクエリ エディターに入力して、最新のデータベース ダイジェストを取得します。 次の手順用に返された結果からダイジェストをコピーします。

   ```sql
   EXECUTE sp_generate_database_ledger_digest
   ```
   
1. クエリ エディターで、次の T-SQL を貼り付けます。`<database_digest>` は、手順 6 でコピーしたダイジェストに置き換え、 **[実行]** を選択します。

   ```sql
   EXECUTE sp_verify_database_ledger N'<database_digest>'
   ```

1. 検証によって、 **[結果]** ウィンドウに次のメッセージが返されます。

   - データベースに改ざんがない場合、メッセージは次のようになります。

       ```output
       Ledger verification successful
       ```

   - データベースに改ざんがあった場合、 **[メッセージ]** ウィンドウに次のエラーが表示されます。
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger does not match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-automatic-digest-storage"></a>[自動ダイジェスト ストレージを使用している T-SQL](#tab/t-sql-automatic)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用して、データベースに接続します。

1. 次の T-SQL ステートメントを使用して、新しいクエリを作成します。

   ```sql
   DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);SELECT @digest_locations as digest_locations;
   BEGIN TRY
       EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
       SELECT 'Ledger verification succeeded.' AS Result;
   END TRY
   BEGIN CATCH
       THROW;
   END CATCH
   ```

1. クエリを実行します。 **digest_locations** によって、データベースのダイジェストが格納されている現在の場所と以前の場所が返されます。 **[結果]** に台帳検証の成功または失敗が返されます。

   :::image type="content" source="media/ledger/verification_script_exectution.png" alt-text="Azure Data Studio を使用した台帳検証の実行のスクリーンショット。":::

1. **digest_locations** 結果セットを開き、ダイジェストの場所を表示します。 次の例は、このデータベースの 2 つのダイジェスト ストレージの場所を示しています。 

   - **path** は、ダイジェストの場所を示しています。
   - **last_digest_block_id** は、**path** の場所に格納されている最新のダイジェストのブロック ID を示します。
   - **is_current** は、**path** の場所が現在のもの (true) か以前のもの (false) であるかを示します。

       ```json
       [
        {
            "path": "https:\/\/digest1.blob.core.windows.net\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 10016,
            "is_current": true
        },
        {
            "path": "https:\/\/jandersneweracl.confidential-ledger.azure.com\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 1704,
            "is_current": false
        }
       ]
       ```

   > [!IMPORTANT]
   > 台帳検証を実行するときに、**digest_locations** の場所を調べて、検証に使用されるダイジェストが予期した場所から取得されるようにします。 権限のあるユーザーによって、構成済みでロックされている不変ポリシーを使用せずに、Azure Storage などの保護されていないストレージの場所にダイジェスト ストレージの場所が変更されていないことを確認する必要があります。

1. 検証によって、 **[結果]** ウィンドウに次のメッセージが返されます。

   - データベースに改ざんがない場合、メッセージは次のようになります。

       ```output
       Ledger verification successful
       ```

   - データベースに改ざんがあった場合、 **[メッセージ]** ウィンドウに次のエラーが表示されます。
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger doesn't match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-manual-digest-storage"></a>[手動ダイジェストストレージを使用しているT-SQL](#tab/t-sql-manual)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用して、データベースに接続します。
1. 次の T-SQL ステートメントを使用して、新しいクエリを作成します。

   ```sql
   /****** This will retrieve the latest digest file  ******/
   EXECUTE sp_generate_database_ledger_digest
   ```

1. クエリを実行します。 結果には最新のデータベース ダイジェストが含まれ、現在の時点でのデータベースのハッシュが示されます。 結果の内容をコピーします。これは、次の手順で使用します。

   :::image type="content" source="media/ledger/ledger-retrieve-digest.png" alt-text="Azure Data Studio を使用してダイジェストの結果を取得する方法を示すスクリーンショット。":::

1. 次の T-SQL ステートメントを使用して、新しいクエリを作成します。 `<YOUR DATABASE DIGEST>` は、前の手順でコピーしたダイジェストに置き換えてください。

   ```
   /****** Verifies the integrity of the ledger using the referenced digest  ******/
   EXECUTE sp_verify_database_ledger N'
   <YOUR DATABASE DIGEST>
   '
   ```

1. クエリを実行します。 **[メッセージ]** ウィドウには、次の成功メッセージが表示されます。

   :::image type="content" source="media/ledger/ledger-verify-message.png" alt-text="Azure Data Studio を使用して台帳検証に T-SQL クエリを実行した後のメッセージを示すスクリーンショット。":::

   > [!TIP]
   > 最新のダイジェストを使用して台帳の検証を実行した場合、そのダイジェストが生成された時刻から検証が実行された時刻までのデータベースのみが検証されます。 データベース内の履歴データが改ざんされていないことを検証するには、複数のデータベース ダイジェスト ファイルを使用して検証を実行してください。 データベースを検証する特定の時点から開始します。 複数のダイジェストを使用した検証では、次のクエリのようになります。

   ```
   EXECUTE sp_verify_database_ledger N'
   [
       {
           "database_name":  "ledgerdb",
           "block_id":  0,
           "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
           "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
           "digest_time":  "2020-11-12T18:39:27.7385724"
       },
       {
           "database_name":  "ledgerdb",
           "block_id":  1,
           "hash":  "0xE5BE97FDFFA4A16ADF7301C8B2BEBC4BAE5895CD76785D699B815ED2653D9EF8",
           "last_transaction_commit_time":  "2020-11-12T18:39:35.6633333",
           "digest_time":  "2020-11-12T18:43:30.4701575"
       }
   ]
   ```

---

## <a name="next-steps"></a>次の手順

- [Azure SQL Database 台帳の概要](ledger-overview.md)
- [SQL Database 台帳](ledger-database-ledger.md)
- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md)
- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)
- [Azure Confidential Ledger に格納されているダイジェストにアクセスする](ledger-how-to-access-acl-digest.md)
