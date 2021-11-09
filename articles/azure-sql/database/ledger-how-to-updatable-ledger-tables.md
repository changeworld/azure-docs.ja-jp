---
title: 更新可能な台帳テーブルを作成、使用する
description: Azure SQL Database で更新可能な台帳テーブルを作成、使用する方法を説明します。
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: rothja
ms.author: jroth
ms.openlocfilehash: 39d7373fc3106501588d98c2b2e0177e92dd6176
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060569"
---
# <a name="create-and-use-updatable-ledger-tables"></a>更新可能な台帳テーブルを作成、使用する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

この記事では、Azure SQL Database で[更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)を作成する方法を示します。 次に、更新可能な台帳テーブルに値を挿入した後、データを更新します。 最後に、台帳ビューを使用して結果を表示します。 顧客の口座残高の変化を追跡する銀行のアプリケーションを例に使用します。 この例では、更新可能な台帳テーブルとそれに対応する履歴テーブルおよび台帳ビューの関係を、実際の使用に役立つよう説明します。

## <a name="prerequisites"></a>前提条件

- 台帳が有効になっている Azure SQL Database。 SQL Database でデータベースをまだ作成していない場合は、「[クイックスタート: Azure SQL Database で台帳が有効化されたデータベースを作成する](ledger-create-a-single-database-with-ledger-enabled.md)」を参照してください。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)。

## <a name="create-an-updatable-ledger-table"></a>更新可能な台帳テーブルを作成する

次のスキーマで口座残高テーブルを作成します。

| 列名 | データ型      | 説明                         |
| ----------- | -------------- | ----------------------------------- |
| CustomerID  | INT            | 顧客 ID - クラスター化された主キー |
| LastName    | varchar (50)   | 顧客の名字                  |
| FirstName   | varchar (50)   | 顧客のファースト ネーム                 |
| Balance     | decimal (10,2) | 口座残高                     |

> [!IMPORTANT]
> 更新可能な台帳テーブルの作成には **ENABLE LEDGER** 権限が必要です。 台帳テーブルに関連する権限の詳細については、[権限](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)に関する記事を参照してください。 

1. [SQL Server Management Studio ](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用して、新しいスキーマと `[Account].[Balance]` という名前のテーブルを作成します。

   ```sql
   CREATE SCHEMA [Account]
   GO
   
   CREATE TABLE [Account].[Balance]
   (
       [CustomerID] INT NOT NULL PRIMARY KEY CLUSTERED,
       [LastName] VARCHAR (50) NOT NULL,
       [FirstName] VARCHAR (50) NOT NULL,
       [Balance] DECIMAL (10,2) NOT NULL
   )
   WITH 
   (
    SYSTEM_VERSIONING = ON,
    LEDGER = ON
   );
   GO
   ```

    > [!NOTE]
    > SQL Database でデータベースを作成するときに台帳データベースを有効にした場合、`LEDGER = ON` 引数の指定は省略できます。
    >
    > 前の例では、テーブルの [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列の名前、[台帳ビュー](ledger-updatable-ledger-tables.md#ledger-view)の名前、[台帳ビューの列](ledger-updatable-ledger-tables.md#ledger-view-schema)の名前がシステムによって生成されます。
    >
    > 台帳ビューの列名は、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) ステートメントで `<ledger_view_option>` パラメーターを使用してテーブルを作成するときにカスタマイズできます。 `GENERATED ALWAYS` 列と[履歴テーブル](ledger-updatable-ledger-tables.md#history-table)の名前はカスタマイズできます。 詳しくは[台帳ビューのオプション](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options)に関する記事と、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true##x-creating-a-updatable-ledger-table) に関する記事中の対応する例をご覧ください。

1. [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)を作成すると、これに対応する履歴テーブルと台帳ビューも作成されます。 次の T-SQL コマンドを実行して、新しいテーブルと新しいビューを表示します。

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/ledger-updatable-how-to-new-tables.png" alt-text="新しい台帳テーブルのクエリを示すスクリーンショット。":::

1. 開始残高が $50 の新規顧客として、名前 `Nick Jones` を挿入します。

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (1, 'Jones', 'Nick', 50)
   ```

1. 開始残高がそれぞれ $500、$30、$200 の新しい顧客として、名前 `John Smith`、`Joe Smith`、`Mary Michaels` を挿入します。

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (2, 'Smith', 'John', 500),
   (3, 'Smith', 'Joe', 30),
   (4, 'Michaels', 'Mary', 200)
   ```

1. 更新可能な台帳テーブル `[Account].[Balance]` を表示し、テーブルに追加する [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列を指定します。

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   ```

   結果ウィンドウでは最初に、T-SQL コマンドで挿入した値と、データ系列で使用するシステムのメタデータが表示されます。

   - `ledger_start_transaction_id` 列には、データを挿入したトランザクションに関連付けられた一意のトランザクション ID が表示されます。 `John`、`Joe`、`Mary` は同じトランザクションを使用して挿入されたので、トランザクション ID は共通です。
   - `ledger_start_sequence_number` 列には、トランザクションで値を挿入した際の順序が表示されます。

      :::image type="content" source="media/ledger/sql-updatable-how-to-1.png" alt-text="台帳テーブルの例を示すスクリーンショット 1。":::

1. `Nick` の残高を `50` から `100` に更新します。

   ```sql
   UPDATE [Account].[Balance] SET [Balance] = 100
   WHERE [CustomerID] = 1
   ```

1. 履歴テーブルの一意の名前をコピーします。 この情報は、次のステップで必要です。

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/sql-updatable-how-to-2.png" alt-text="台帳テーブルの例を示すスクリーンショット 2。":::

1. 更新可能な台帳テーブル `[Account].[Balance]` と、これに対応する履歴テーブルおよび台帳ビューを表示します。

   > [!IMPORTANT]
   > 前のステップでコピーした名前で `<history_table_name>` を置き換えます。

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   GO
   
   SELECT * FROM [<Your unique history table name>]
   GO 
   
   SELECT * FROM Account.Balance_Ledger
   ORDER BY ledger_transaction_id
   GO
   ```

   > [!TIP]
   > [履歴テーブル](ledger-updatable-ledger-tables.md#history-table)ではなく[台帳ビュー](ledger-updatable-ledger-tables.md#ledger-view)で変更の履歴を検索することをお勧めします。

1. 更新可能な台帳テーブルで、`Nick` の口座残高を `100` に更新できました。
1. これ以降、履歴テーブルには `Nick` の `50` の以前の残高が表示されます。
1. 台帳ビューに、台帳テーブルの更新が、`50` である元の行の `DELETE` であることが示されます。 `100` である新しい行の対応する `INSERT` の残高で、`Nick` の新しい残高が示されます。

   :::image type="content" source="media/ledger/sql-updatable-how-to-3.png" alt-text="台帳テーブルの例を示すスクリーンショット 3。":::


## <a name="next-steps"></a>次の手順

- [データベース台帳](ledger-database-ledger.md)
- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md) 
- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md) 
- [追加専用台帳テーブルを作成、使用する](ledger-how-to-append-only-ledger-tables.md) 
- [Azure Confidential Ledger (ACL) に格納されているダイジェストにアクセスする](ledger-how-to-access-acl-digest.md)
- [台帳テーブルを検証して改ざんを検出する](ledger-verify-database.md)
