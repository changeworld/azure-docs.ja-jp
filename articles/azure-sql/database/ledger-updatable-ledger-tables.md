---
title: Azure SQL Database の更新可能な台帳テーブル
description: この記事では、Azure SQL Database の更新可能な台帳テーブル、台帳スキーマ、および台帳ビューについて説明します。
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 3010ba83f8d45083213f3725626f92b111fb839d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056192"
---
# <a name="azure-sql-database-updatable-ledger-tables"></a>Azure SQL Database の更新可能な台帳テーブル

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

更新可能な台帳テーブルはシステムによってバージョン管理されているテーブルであり、ユーザーは更新や削除を実行できますが、改ざん防止機能も用意されています。 更新や削除が行われると、以前のバージョンのすべての行が、履歴テーブルと呼ばれるセカンダリ テーブルに保存されます。 履歴テーブルは、その更新可能な台帳テーブルのスキーマを反映しています。 ある行が更新されると、その行の最新バージョンが台帳テーブルに残り、以前のバージョンはアプリケーションから透過的に、システムによって履歴テーブルに挿入されます。 

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="台帳テーブルのアーキテクチャを示す図。":::

## <a name="updatable-ledger-tables-vs-temporal-tables"></a>更新可能な台帳テーブルとテンポラル テーブル

更新可能な台帳テーブルと[テンポラル テーブル](/sql/relational-databases/tables/temporal-tables)は、どちらもデータベース エンジンによって行バージョンの履歴がセカンダリ履歴テーブルに記録される、システムでバージョン管理されたテーブルです。 どちらのテクノロジにも、独自の利点があります。 更新可能な台帳テーブルでは、最新のデータと履歴データの両方の改ざんが防止されます。 テンポラル テーブルでは、現時点の正しいデータのみではなく、任意の時点の格納データに対してクエリを実行できます。

更新可能な台帳テーブルであると同時にテンポラル テーブルでもあるテーブルを作成することで、両方のテクノロジを組み合わせて利用することができます。 更新可能な台帳テーブルは、次の 2 つの方法で作成できます。

- Azure portal で新しいデータベースを作成する場合は、 **[Enable ledger on all future tables in this database]\(このデータベース内の今後のすべてのテーブルに対して、台帳を有効にする\)** を選択するか、[CREATE DATABASE (Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) ステートメントで `LEDGER = ON` 引数を指定します。 このアクションにより、台帳データベースが作成され、今後データベースに作成されるすべてのテーブルが既定で更新可能な台帳テーブルとなることが保証されます。
- データベース レベルで台帳が有効になっていないデータベースに新しいテーブルを作成する場合は、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) ステートメントで `LEDGER = ON` 引数を指定します。

T-SQL ステートメントで `LEDGER` 引数を指定するときに使用できるオプションの詳細については、「[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql)」を参照してください。

> [!IMPORTANT]
> 作成した台帳テーブルを、台帳テーブル以外のテーブルに変換することはできません。 このため、攻撃者は台帳テーブルで台帳機能を一時的に削除し、変更を加えてた後、台帳機能を再び有効にすることはできません。 

### <a name="updatable-ledger-table-schema"></a>更新可能な台帳テーブルのスキーマ

更新可能な台帳テーブルには、テーブルに変更を加えたトランザクションと、そのトランザクションによって行が更新された操作の順番を示すメタデータが含まれる、次の [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列が必要です。 このデータは、データがどのように挿入されたのかを時系列で理解する、フォレンジックの目的に役立ちます。

> [!NOTE]
> [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) ステートメントで台帳テーブルと台帳履歴テーブルに必須の `GENERATED ALWAYS` 列を指定しない場合、システムによって列が自動的に追加され、次の既定の名前が使用されます。 詳細については、「[更新可能な台帳テーブルの作成](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#x-creating-a-updatable-ledger-table)」に記載されている例を参照してください。

| 既定の列名 | データの種類 | 説明 |
| --- | --- | --- |
| ledger_start_transaction_id | bigint | 行バージョンを作成したトランザクションの ID |
| ledger_end_transaction_id | bigint | 行バージョンを削除したトランザクションの ID |
| ledger_start_sequence_number | bigint | 行バージョンを作成したトランザクション内の操作のシーケンス番号 |
| ledger_end_sequence_number | bigint | 行バージョンを削除したトランザクション内の操作のシーケンス番号 |

## <a name="history-table"></a>履歴テーブル

履歴テーブルは、更新可能な台帳テーブルの作成時に自動的に作成されます。 履歴テーブルには、更新可能な台帳テーブルの更新や削除時に変更された行の履歴値が記録されます。 履歴テーブルのスキーマには、関連付けられている更新可能な台帳テーブルのスキーマが反映されています。

更新可能な台帳テーブルを作成する際には、履歴テーブルを格納するスキーマの名前と履歴テーブルの名前を指定するか、履歴テーブルの名前をシステム生成し、それを台帳テーブルと同じスキーマに追加するかのいずれかを選択できます。 システム生成された名前が付いた履歴テーブルは、匿名履歴テーブルと呼ばれます。 匿名履歴テーブルの名前付け規則は `<schema>`.`<updatableledgertablename>`.MSSQL_LedgerHistoryFor_`<GUID>` です。

## <a name="ledger-view"></a>台帳ビュー

更新可能な元帳テーブルそれぞれに対して、台帳ビューと呼ばれるビューが自動的に生成されます。 台帳ビューは、更新可能な台帳テーブルとそれに関連付けられている履歴テーブルを統合したものです。 台帳ビューには、履歴テーブルの履歴データを結合することによって、更新可能な台帳テーブルで発生したすべての行の変更が表示されます。 このビューにより、ユーザー、そのパートナー、または監査者は、すべての履歴操作を分析し、潜在的な改ざんを検出することができます。 各行の操作には、操作が `DELETE` または `INSERT` であるかどうかと共に、アクションを実行するトランザクションの ID が付けられます。 ユーザーは、トランザクションが実行された時刻や、実行したユーザーの ID に関する詳細な情報を取得して、このトランザクションによって実行された他の操作と関連付けることができます。

たとえば、銀行のシナリオで取引履歴を追跡する場合、台帳ビューには、取引の記録が時系列で表示されます。 台帳ビューを使用することにより、更新可能な台帳テーブルや履歴テーブルを個別に表示したり、そのために独自のビューを構築したりする必要がなくなります。

台帳ビューの使用例については、「[更新可能な台帳テーブルを作成、使用する](ledger-how-to-updatable-ledger-tables.md)」を参照してください。

台帳ビューのスキーマには、更新可能な台帳テーブルと履歴テーブルで定義されている列が反映されますが、[GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列は、更新可能な台帳テーブルと履歴テーブルの列とは異なります。

### <a name="ledger-view-schema"></a>台帳ビューのスキーマ

> [!NOTE]
> 台帳ビューの列名は、テーブルを作成するときに、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) ステートメントで `<ledger_view_option>` パラメーターを使用してカスタマイズできます。 詳細については、[台帳ビューのオプション](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options)に関するページと、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) の対応する例を参照してください。

| 既定の列名 | データの種類 | 説明 |
| --- | --- | --- |
| ledger_transaction_id | bigint | 行バージョンを作成または削除したトランザクションの ID。 |
| ledger_sequence_number | bigint | テーブルに対するトランザクション内の行レベルの操作のシーケンス番号。 |
| ledger_operation_type_id | tinyint | `0` (**INSERT**) または `1` (**DELETE**) を含みます。 台帳テーブルに行を挿入すると、この列に `0` が含まれる新しい行が台帳ビューに生成されます。 台帳テーブルから行を削除すると、この列に `1` が含まれる新しい行が台帳ビューに生成されます。 台帳テーブルの行を更新すると、2 つの新しい行が台帳ビューに生成されます。 一方の行のこの列に `1` (**DELETE**) が含まれ、もう一方の行のこの列に `1` (**INSERT**) が含まれます。 |
| ledger_operation_type_desc | nvarchar(128) | `INSERT` または `DELETE` が含まれます。 詳細については、前述の行を参照してください。 |

## <a name="next-steps"></a>次の手順
 
- [更新可能な台帳テーブルを作成して使用する](ledger-how-to-updatable-ledger-tables.md)
- [追加専用のテーブルを作成して使用する](ledger-how-to-append-only-ledger-tables.md) 
