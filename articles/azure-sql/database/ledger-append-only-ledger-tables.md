---
title: Azure SQL Database 追加専用台帳テーブル
description: この記事では、Azure SQL Database における追加専用の台帳テーブルのスキーマとビューに関する情報を提供します
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 53783152a7968fff5375be82c9b72f0881c349c4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388407"
---
# <a name="azure-sql-database-append-only-ledger-tables"></a>Azure SQL Database 追加専用台帳テーブル

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳は **パブリック プレビュー** 段階にあります。

追加専用台帳テーブルでは、テーブルに対する操作`INSERT`のみを許可し、データベース管理者 (DBA) などの特権ユーザーが従来の[データ操作言語 (DML)](/sql/t-sql/queries/queries) の操作を使用してデータを変更することはできません。 追加専用台帳テーブルは、セキュリティ情報イベント管理 (SIEM) システムなどのレコードを更新または削除しないシステムや、ブロックチェーンからデータベースにデータをレプリケートする必要があるブロックチェーン システムに最適です。  追加専用テーブルに対する `UPDATE`操作や `DELETE` 操作はないので、対応する履歴テーブルは必要ありません。これは、[更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)と同様です。

:::image type="content" source="media/ledger/ledger-table-architecture-append-only.png" alt-text="台帳テーブルのアーキテクチャ":::

追加専用台帳テーブルを作成するには[、CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) ステートメントで `LEDGER = ON` 引数を指定し、 `APPEND_ONLY = ON` オプションを指定します。

> [!IMPORTANT]
> テーブルが台帳テーブルとして作成された後は、台帳機能を持たないテーブルに戻すことはできません。 これは、攻撃者が台帳機能を一時的に削除し、テーブルに変更を加えてから台帳機能を再び有効にすることができないようにするためです。

### <a name="append-only-ledger-table-schema"></a>追加専用の台帳テーブルのスキーマ

追加専用台帳テーブルには、テーブルに変更を加えたトランザクションと、そのトランザクションによって行が更新された操作の順番を示すメタデータを含む、次の [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列が必要です。 追加専用台帳テーブルを作成すると、台帳テーブルに `GENERATED ALWAYS` 列が作成されます。 このデータは、データがどのように挿入されたのかを時系列で理解する上で、フォレンジクスの目的に役立ちます。

[CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) ステートメントで `GENERATED ALWAYS` 列の定義を指定しない場合は、次の既定の名前を使用して自動的に追加されます。

| 既定の列名 | データの種類 | 説明 |
|--|--|--|
| ledger_start_transaction_id | bigint | 行バージョンを作成したトランザクションの ID。 |
| ledger_start_sequence_number | bigint | 行バージョンを作成したトランザクション内の操作のシーケンス番号。 |

## <a name="ledger-view"></a>台帳ビュー

追加専用のすべての元帳テーブルに対して、台帳ビューと呼ばれるビューが自動的に生成されます。 台帳ビューは、テーブルで発生したすべての行の挿入を報告します。 台帳ビューは、追加専用の台帳テーブルではなく、 [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)に対して主に役立ちます。これは、追加専用の台帳テーブルに `UPDATE` または `DELETE` の機能がないためです。 追加専用の台帳テーブルの台帳ビューでは、更新可能な台帳テーブルと追加専用の台帳テーブルの間で一貫性を保つことができます。

### <a name="ledger-view-schema"></a>台帳ビューのスキーマ

> [!NOTE]
> [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) ステートメントで `<ledger_view_option>` パラメーターを使用してテーブルを作成するときに、台帳ビューの列名をカスタマイズできます。 詳細については、[台帳ビューのオプション](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options)に関するページと、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) の対応する例を参照してください。

| 既定の列名 | データの種類 | 説明 |
| --- | --- | --- |
| ledger_transaction_id | bigint | 行バージョンを作成または削除したトランザクションの ID。 |
| ledger_sequence_number | bigint | テーブルに対するトランザクション内の行レベルの操作のシーケンス番号。 |
| ledger_operation_type_id | tinyint | `0` (**INSERT**) または `1` (**DELETE**) を含みます。 台帳テーブルに行を挿入すると、列に `0` が含まれる新しい行が台帳ビューに生成されます。 台帳テーブルから行を削除すると、列に `1` が含まれる新しい行が台帳ビューに生成されます。 台帳テーブルの行を更新すると、2 つの新しい行が台帳ビューに生成されます。 この列において、1 つの行には `1` (**DELETE**) が含まれ、もう 1 つの行には `1` (**INSERT**) が含まれます。 追加専用台帳テーブルでは DELETE は実行しないでください。 |
| ledger_operation_type_desc | nvarchar(128) | `INSERT` または `DELETE` が含まれます。 詳細については、上記を参照してください。 |

## <a name="next-steps"></a>次の手順
  
- [追加専用のテーブルを作成して使用する](ledger-how-to-append-only-ledger-tables.md)
- [更新可能な台帳テーブルを作成して使用する](ledger-how-to-updatable-ledger-tables.md)
