---
title: Azure SQL Database の更新可能な台帳テーブル
description: この記事では、Azure SQL Database の更新可能な台帳テーブル、台帳スキーマ、および台帳ビューについて説明します
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: df73b0719dc035e138d8cc2dbfb26da2fa9642dc
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388272"
---
# <a name="azure-sql-database-updatable-ledger-tables"></a>Azure SQL Database の更新可能な台帳テーブル

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database の台帳は **パブリック プレビュー** 段階にあります。

更新可能な台帳テーブルは、ユーザーによる更新や削除が可能な、システムでバージョン管理されている改ざん防止機能付きのテーブルです。 更新や削除が行われると、以前のバージョンのすべての行が、履歴テーブルと呼ばれるセカンダリ テーブルに保存されます。 履歴テーブルは、その更新可能な台帳テーブルのスキーマを反映しています。 ある行が更新されると、その行の最新バージョンが台帳テーブルに残り、以前のバージョンはアプリケーションから透過的に、システムによって履歴テーブルに挿入されます。 

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="台帳テーブルのアーキテクチャ":::

## <a name="updatable-ledger-tables-vs-temporal-tables"></a>更新可能な台帳テーブルとテンポラル テーブル

更新可能な台帳テーブルと[テンポラル テーブル](/sql/relational-databases/tables/temporal-tables)は、どちらもデータベース エンジンによって行バージョンの履歴がセカンダリ履歴テーブルに記録される、システムでバージョン管理されたテーブルです。 どちらのテクノロジにも、独自の利点があります。 更新可能な台帳テーブルでは、最新のデータと過去のデータの両方が改ざんされないようになります。 テンポラル テーブルでは、現時点の正しいデータのみではなく、テーブルに保存されていた任意の時点のデータ情報の参照を行うことができます。

更新可能な台帳テーブルであると同時にテンポラル テーブルでもあるテーブルを作成することで、両方のテクノロジを組み合わせて利用することができます。 更新可能な台帳テーブルを作成するには、次の 2 つの方法があります。

- **[Enable ledger on all future tables in this database]\(このデータベース内の今後のすべてのテーブルに対して、台帳を有効にする\)** を選択するか、[CREATE DATABASE (Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) ステートメントで `LEDGER = ON` 引数を指定して Azure portal で新しいデータベースを作成する場合。 これにより台帳データベースが作成され、今後データベースに作成されるすべてのテーブルが、既定で更新可能な台帳テーブルとなります。
- データベースレベルで台帳が有効になっていないデータベースに新しいテーブルを作成する場合は、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) ステートメントで `LEDGER = ON` 引数を指定します。

T-SQL ステートメントで `LEDGER` 引数を指定するときに使用できるオプションの詳細については、「[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql)」を参照してください。

> [!IMPORTANT]
> 作成した台帳テーブルを、台帳テーブル以外のテーブルに変換することはできません。 これは、攻撃者が台帳テーブルの台帳機能を一時的に削除し、変更を加えてから台帳機能を再び有効することができないようにするためです。 

### <a name="updatable-ledger-table-schema"></a>更新可能な台帳テーブルのスキーマ

更新可能な台帳テーブルには、テーブルに変更を加えたトランザクションと、そのトランザクションによって行が更新された操作の順番を示すメタデータが含まれる、次の [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列が必要です。  このデータは、データがどのように挿入されたのかを時系列で理解する、フォレンジックの目的に役立ちます。

> [!NOTE]
> [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) ステートメントで台帳テーブルと台帳履歴テーブルの必須な `GENERATED ALWAYS` 列を指定しない場合、システムによって列が自動的に追加され、次の既定の名前が使用されます。 詳細については、[更新可能な台帳テーブルの作成](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#x-creating-a-updatable-ledger-table)に関するページに記載されている例を参照してください。

| 既定の列名 | データの種類 | 説明 |
| --- | --- | --- |
| ledger_start_transaction_id | bigint | 行バージョンを作成したトランザクションの ID。 |
| ledger_end_transaction_id | bigint | 行バージョンを削除したトランザクションの ID。 |
| ledger_start_sequence_number | bigint | 行バージョンを作成したトランザクション内の操作のシーケンス番号。 |
| ledger_end_sequence_number | bigint | 行バージョンを削除したトランザクション内の操作のシーケンス番号。 |

## <a name="history-table"></a>履歴テーブル

履歴テーブルは、更新可能な台帳テーブルの作成時に自動的に作成されます。 履歴テーブルには、更新可能な台帳テーブルの更新や削除時に変更された行の履歴値が記録されます。 履歴テーブルのスキーマには、関連付けられている更新可能な台帳テーブルのスキーマが反映されています。

更新可能な台帳テーブルを作成する際には、履歴テーブルを格納するスキーマの名前と履歴テーブルの名前を指定するか、履歴テーブルの名前をシステム生成し、それを台帳テーブルと同じスキーマに追加するかのいずれかを選択できます。 システム生成された名前が付いた履歴テーブルは、匿名履歴テーブルと呼ばれます。 匿名履歴テーブルの名前付け規則は `<schema>`.`<updatableledgertablename>`.MSSQL_LedgerHistoryFor_`<GUID>` です。

## <a name="ledger-view"></a>台帳ビュー

更新可能な元帳テーブルそれぞれに対して、台帳ビューと呼ばれるビューが自動的に生成されます。 台帳ビューは、更新可能な台帳テーブルとそれに関連付けられている履歴テーブルを統合したものです。 台帳ビューには、履歴テーブルの履歴データを結合することによって、更新可能な台帳テーブルで発生したすべての行の変更が表示されます。 これにより、ユーザー、パートナー、または監査者は、すべての履歴操作を分析し、潜在的な改ざんを検出することができます。 各行の操作には、操作が `DELETE` または `INSERT` であるかどうかと共に、アクションを実行するトランザクションの ID が付けられます。 ユーザーは、トランザクションが実行された時刻や実行したユーザーの ID に関する詳細な情報を取得して、このトランザクションによって実行された他の操作と関連付けることができます。

例えば、単純な銀行のシナリオで取引履歴を追跡したい場合、更新可能な台帳テーブルや履歴テーブルを個別に表示したり、そのために独自のビューを構築したりするのではなく、取引の記録を時系列で確認できる元帳ビューは非常に便利です。

台帳ビューの使用例については、[更新可能な台帳テーブルの作成と使用](ledger-how-to-updatable-ledger-tables.md)に関するページを参照してください。

台帳ビューのスキーマには、更新可能な台帳テーブルと履歴テーブルで定義されている列が反映されますが、[GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列は、更新可能な台帳テーブルと履歴テーブルの列とは異なります。

### <a name="ledger-view-schema"></a>台帳ビューのスキーマ

> [!NOTE]
> [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) ステートメントで `<ledger_view_option>` パラメーターを使用してテーブルを作成するときに、台帳ビューの列名をカスタマイズできます。 詳細については、[台帳ビューのオプション](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options)に関するページと、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) の対応する例を参照してください。

| 既定の列名 | データの種類 | 説明 |
| --- | --- | --- |
| ledger_transaction_id | bigint | 行バージョンを作成または削除したトランザクションの ID。 |
| ledger_sequence_number | bigint | テーブルに対するトランザクション内の行レベルの操作のシーケンス番号。 |
| ledger_operation_type_id | tinyint | `0` (**INSERT**) または `1` (**DELETE**) を含みます。 台帳テーブルに行を挿入すると、列に `0` が含まれる新しい行が台帳ビューに生成されます。 台帳テーブルから行を削除すると、列に `1` が含まれる新しい行が台帳ビューに生成されます。 台帳テーブルの行を更新すると、2 つの新しい行が台帳ビューに生成されます。 その内 1 つの行には `1` (**DELETE**) が含まれ、もう 1 つの行にはこの列に `1` (**INSERT**) が含まれます。 |
| ledger_operation_type_desc | nvarchar(128) | `INSERT` または `DELETE` が含まれます。 詳細については、上記を参照してください。 |

## <a name="next-steps"></a>次の手順
 
- [更新可能な台帳テーブルを作成して使用する](ledger-how-to-updatable-ledger-tables.md)
- [追加専用のテーブルを作成して使用する](ledger-how-to-append-only-ledger-tables.md) 
