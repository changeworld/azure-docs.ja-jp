---
title: 追加専用のテーブルを作成して使用する
description: Azure SQL Database で追加専用の台帳テーブルを作成して使用する方法を説明します。
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: rothja
ms.author: jroth
ms.openlocfilehash: 09b9d4dceabff4b26c586b0035f4c8b8d4c2ba0a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063574"
---
# <a name="create-and-use-append-only-ledger-tables"></a>追加専用のテーブルを作成して使用する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

この記事では、Azure SQL Database で[追加専用の台帳テーブル](ledger-append-only-ledger-tables.md)を作成する方法を示します。 次に、追加専用の台帳テーブルに値を挿入した後、データを更新してみます。 最後に、台帳ビューを使用して結果を表示します。 ここでは、ある施設のカード キー アクセス システムの例を使用します。これは、追加専用システムのパターンです。 この例では、追加専用の台帳テーブルとそれに対応する台帳ビューのリレーションシップを実際に見てみましょう。

詳細については、[追加専用の台帳テーブル](ledger-append-only-ledger-tables.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- 台帳が有効になっている Azure SQL Database。 SQL Database でデータベースをまだ作成していない場合は、「[クイックスタート: Azure SQL Database で台帳が有効化されたデータベースを作成する](ledger-create-a-single-database-with-ledger-enabled.md)」を参照してください。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)。

## <a name="create-an-append-only-ledger-table"></a>追加専用の台帳テーブルを作成する

次のスキーマを持つ `KeyCardEvents` テーブルを作成します。

| 列名 | データ型 | 説明 |
|--|--|--|
| EmployeeID | INT | 建物にアクセスする従業員の一意の ID |
| AccessOperationDescription | nvarchar (MAX) | 従業員のアクセス操作 |
| Timestamp | datetime2 | 従業員が建物にアクセスした日時 |

> [!IMPORTANT]
> 追加専用の台帳テーブルを作成するには、**ENABLE LEDGER** 権限が必要です。 台帳テーブルに関連する権限の詳細については、[権限](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)に関する記事を参照してください。 

1. [SQL Server Management Studio ](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) を使用して、新しいスキーマと `[AccessControl].[KeyCardEvents]` という名前のテーブルを作成します。

   ```sql
   CREATE SCHEMA [AccessControl] 
   CREATE TABLE [AccessControl].[KeyCardEvents]
       (
           [EmployeeID] INT NOT NULL,
           [AccessOperationDescription] NVARCHAR (MAX) NOT NULL,
           [Timestamp] Datetime2 NOT NULL
       )
       WITH (
          LEDGER = ON (
                       APPEND_ONLY = ON
                       )
         );
   ```

1. 次の値を使用して、建物への新しいアクセス イベントを `[AccessControl].[KeyCardEvents]` テーブルに追加します。

   ```sql
   INSERT INTO [AccessControl].[KeyCardEvents]
   VALUES ('43869', 'Building42', '2020-05-02T19:58:47.1234567')
   ```

1. KeyCardEvents テーブルの内容を表示し、[追加専用の台帳テーブル](ledger-append-only-ledger-tables.md)に追加された [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列を指定します。

   ```sql
   SELECT *
        ,[ledger_start_transaction_id]
        ,[ledger_start_sequence_number]
   FROM [AccessControl].[KeyCardEvents]
   ```

   :::image type="content" source="media/ledger/append-only-how-to-keycardevent-table.png" alt-text="KeyCardEvents テーブルのクエリの結果を示すスクリーンショット。":::

1. `EmployeeID` を `43869` から `34184.` に変更して、`KeyCardEvents` テーブルの更新を試みる

   ```sql
   UPDATE [AccessControl].[KeyCardEvents] SET [EmployeeID] = 34184
   ```

   追加専用台帳テーブルに対する更新が許可されていないことを示すエラー メッセージが表示されます。

   :::image type="content" source="media/ledger/append-only-how-to-1.png" alt-text="追加専用のエラー メッセージを示すスクリーンショット。":::

## <a name="next-steps"></a>次の手順

- [データベース台帳](ledger-database-ledger.md) 
- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md) 
- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)
- [更新可能な台帳テーブルを作成して使用する](ledger-how-to-updatable-ledger-tables.md)
- [Azure Confidential Ledger (ACL) に格納されているダイジェストにアクセスする](ledger-how-to-access-acl-digest.md)
- [台帳テーブルを検証して改ざんを検出する](ledger-verify-database.md)
