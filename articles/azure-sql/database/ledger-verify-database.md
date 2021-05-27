---
title: 台帳テーブルを検証して改ざんを検出する方法
description: この記事では、Azure SQL Database テーブルが改ざんされているかどうかを検証する方法について説明します
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.reviewer: vanto
ms.date: 05/25/2021
ms.openlocfilehash: 6669b071d79c78b35dc2cfb94f99fa9597f44bd6
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388239"
---
# <a name="how-to-verify-a-ledger-table-to-detect-tampering"></a>台帳テーブルを検証して改ざんを検出する方法

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳は **パブリック プレビュー** 段階にあります。

この記事では、Azure SQL Database 台帳テーブル内のデータの整合性を検証します。 [Azure SQL Database を作成](ledger-create-a-single-database-with-ledger-enabled.md)するときに **[自動ダイジェスト ストレージを有効にする]** をオンにしていた場合、Azure portal の指示に従って、[Query Editor](connect-query-portal.md) 内のデータベース台帳を検証するために必要な Transact-SQL (T-SQL) スクリプトを自動的に生成してください。 それ以外の場合は、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用して、T-SQL の指示に従ってください。

## <a name="prerequisite"></a>前提条件

- 有効な Azure サブスクリプション アカウントがない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- [台帳が有効化された Azure SQL Database を作成します。](ledger-create-a-single-database-with-ledger-enabled.md)
- [更新可能な台帳テーブルを作成して使用する](ledger-how-to-updatable-ledger-tables.md)、または[追加専用の台帳テーブルを作成して使用する](ledger-how-to-append-only-ledger-tables.md)

## <a name="run-ledger-verification-for-azure-sql-database"></a>Azure SQL Database に対して台帳の検証を実行する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) を開き、 **[すべてのリソース]** を選択して、検証するデータべーを見つけます。 SQL データベースを選択します。

     :::image type="content" source="media/ledger/ledger-portal-all-resources.png" alt-text="[すべてのリソース] タブが表示されている Azure portal":::

1. **[セキュリティ]** で、 **[台帳]** オプションを選択します。

   :::image type="content" source="media/ledger/ledger-portal-manage-ledger.png" alt-text="Azure portal 台帳の [セキュリティ] タブ":::

1. **[台帳]** ペインで **[</> データベースの検証]** ボタンを選択し、ウィンドウ内の事前にデータが取り込まれたテキスト内で **[コピー]** アイコンを選択します。

     :::image type="content" source="media/ledger/ledger-portal-verify.png" alt-text="Azure portal の [データベースの検証] ボタン":::

1. 左側のメニューで **[クエリ エディター]** を開きます。

     :::image type="content" source="media/ledger/ledger-portal-open-query-editor.png" alt-text="Azure portal の [クエリ エディター] ボタン":::

1. **[クエリ エディター]** で、ステップ 3 でコピーした T-SQL スクリプトを貼り付け、 **[実行]** を選択します。

   :::image type="content" source="media/ledger/ledger-portal-run-query-editor.png" alt-text="Azure portal でデータベースを検証するためのクエリ エディターの実行":::

1. 検証が正常に完了すると、 **[結果]** ウィンドウに次のメッセージが返されます。

   - データベースに改ざんがない場合、メッセージは次のようになります。

   ```output
   Ledger verification successful
   ```

   - データベースに改ざんがあった場合、 **[メッセージ]** ウィンドウに次のエラーが表示されます。
  
   ```output
   Failed to execute query. Error: The hash of block xxxx in the database ledger does not match the hash provided in the digest for this block.
   ```

# <a name="t-sql"></a>[T-SQL](#tab/t-sql)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用してデータベースに接続します。
1. 次の T-SQL ステートメントを使用して、新しいクエリを作成します。

   ```sql
   /****** This will retrieve the latest digest file  ******/
   EXECUTE sp_generate_database_ledger_digest
   ```

1. クエリを実行します。 結果には最新のデータベース ダイジェストが含まれ、現在の時点でのデータベースのハッシュが示されます。 結果の内容をコピーします。これは、次の手順で使用します。

   :::image type="content" source="media/ledger/ledger-retrieve-digest.png" alt-text="Azure Data Studio を使用してダイジェストの結果を取得する":::

1. 次の T-SQL ステートメントを使用して、新しいクエリを作成します。 `<YOUR DATABASE DIGEST>` は、前の手順でコピーしたダイジェストに置き換えてください。

   ```
   /****** Verifies the integrity of the ledger using the referenced digest  ******/
   EXECUTE sp_verify_database_ledger N'
   <YOUR DATABASE DIGEST>
   '
   ```

1. クエリを実行します。 **[メッセージ]** に、正常な完了を示す次のメッセージが表示されます。  

   :::image type="content" source="media/ledger/ledger-verify-message.png" alt-text="Azure Data Studio を使用して台帳の検証を行うために T-SQL クエリを実行した後のクエリ":::

   > [!TIP]
   > 最新のダイジェストを使用して台帳の検証を実行した場合、そのダイジェストが生成された時刻から検証が実行された時刻までのデータベースのみが検証されます。 データベース内の履歴データが改ざんされていないことを検証するには、複数のデータベース ダイジェスト ファイルを使用して検証を実行してください。 データベースを検証したい特定の時点から開始します。 複数のダイジェストを使用した検証では、次のクエリのようになります。

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
- [データベース台帳](ledger-database-ledger.md)
- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md)
- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)
- [Azure Confidential Ledger (ACL) に格納されているダイジェストにアクセスする方法](ledger-how-to-access-acl-digest.md)
