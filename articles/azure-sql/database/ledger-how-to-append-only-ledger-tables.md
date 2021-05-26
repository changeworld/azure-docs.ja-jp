---
title: 追加専用のテーブルを作成して使用する
description: Azure SQL Database で追加専用の台帳テーブルを作成して使用する方法
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 8b2007b473432a941d617f83cd3cc0a3bd2ce099
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388311"
---
# <a name="create-and-use-append-only-ledger-tables"></a>追加専用のテーブルを作成して使用する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database の台帳は **パブリック プレビュー** 段階にあります。

この記事では、Azure SQL Database で[追加専用の台帳テーブル](ledger-append-only-ledger-tables.md)を作成する方法、追加専用の台帳テーブルに値を挿入する方法、データの更新を試みる方法、および台帳ビューを使用して結果を表示する方法について説明します。 ここでは、ある施設のカード キー アクセス システムの例を使用します。これは、追加専用システムのパターンです。 この例では、追加専用の台帳テーブルとそれに対応する台帳ビューのリレーションシップを実際に見てみましょう。

詳細については、[追加専用の台帳テーブル](ledger-append-only-ledger-tables.md)に関するページを参照してください。

## <a name="prerequisite"></a>前提条件

- 台帳を有効にした Azure SQL Database を用意します。 Azure SQL Database をまだ作成していない場合は、[クイックスタート: 台帳を有効にした Azure SQL Database の作成](ledger-create-a-single-database-with-ledger-enabled.md)に関する記事をご覧ください。
- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)

## <a name="creating-an-append-only-ledger-table"></a>追加専用の台帳テーブルを作成する

次のスキーマを持つ `KeyCardEvents` テーブルを作成します。  

| 列名 | データ型 | 説明 |
|--|--|--|
| EmployeeID | INT | 建物にアクセスする従業員の一意の ID。 |
| AccessOperationDescription | nvarchar (MAX) | 従業員のアクセス操作。 |
| Timestamp | datetime2 | 従業員が建物にアクセスした日時 |

> [!IMPORTANT]
> 追加専用の台帳テーブルを作成するには、**ENABLE LEDGER** 権限が必要です。 台帳テーブルに関連するアクセス許可の詳細については、[アクセス許可](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)に関するページを参照してください。 

1. [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) のいずれかを使用して、新しいスキーマと `[AccessControl].[KeyCardEvents]` という名前のテーブルを作成します。

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

1. [追加専用の台帳テーブル](ledger-append-only-ledger-tables.md)に追加された [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列を指定して、KeyCardEvents テーブルの内容を表示します。

   ```sql
   SELECT *
        ,[ledger_start_transaction_id]
        ,[ledger_start_sequence_number]
   FROM [AccessControl].[KeyCardEvents]
   ```

   :::image type="content" source="media/ledger/append-only-how-to-keycardevent-table.png" alt-text="KeyCardEvents テーブルに対してクエリを実行した際の結果":::

1. `EmployeeID` を `43869` から `34184.` に変更して、`KeyCardEvents` テーブルの更新を試みる

   ```sql
   UPDATE [AccessControl].[KeyCardEvents] SET [EmployeeID] = 34184
   ```

   追加専用台帳テーブルに対する更新が許可されていないことを示すエラー メッセージが表示されます。

   :::image type="content" source="media/ledger/append-only-how-to-1.png" alt-text="追加専用であることを示すエラー メッセージ":::

## <a name="next-steps"></a>次の手順

- [データベース台帳](ledger-database-ledger.md) 
- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md) 
- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)
- [更新可能な台帳テーブルを作成して使用する](ledger-how-to-updatable-ledger-tables.md)
- [Azure Confidential Ledger (ACL) に格納されているダイジェストにアクセスする方法](ledger-how-to-access-acl-digest.md)
- [台帳テーブルを検証して改ざんを検出する方法](ledger-verify-database.md)
