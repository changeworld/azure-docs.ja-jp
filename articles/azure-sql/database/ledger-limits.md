---
title: Azure SQL Database 台帳の制約
description: Azure SQL Database の台帳機能の制約
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 684ca40f1469b826029f1b0bcc51e33ae0d9c9a5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053692"
---
# <a name="limitations-for-azure-sql-database-ledger"></a>Azure SQL Database 台帳の制約

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

この記事では、Azure SQL Database で使用される元帳テーブルの制限を概説します。

## <a name="limitations"></a>制限事項

| 機能 | 制限事項 |
| :--- | :--- |
| [台帳データベース](ledger-database-ledger.md)を無効にする   | 台帳データベースは、有効にした後で無効にすることはできません。 |
| 列の最大数 | [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)が作成されると、4 つの [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列が台帳テーブルに追加されます。 [追加専用の台帳テーブル](ledger-append-only-ledger-tables.md)では、2 つの列が台帳テーブルに追加されます。 これらの新しい列の数は、SQL Database でサポートしている最大列数 (1,024) に対してカウントされます。 |
| 使用できないデータの種類 | データの種類に関して、XML、SqlVariant、ユーザー定義型、FILESTREAM はサポートしていません。 |
| イン メモリ テーブル | メモリ内テーブルはサポートしていません。 |
| スパース列セット | スパース列セットはサポートしていません。 |
| 台帳の切り詰め | [追加専用の台帳テーブル](ledger-append-only-ledger-tables.md)、および[更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)の履歴テーブルの古いデータの削除はサポートしていません。 |
| 既存のテーブルを台帳テーブルに変換する | 台帳が有効になっていないデータベースの既存のテーブルは、台帳テーブルに変換できません。 |
|[自動ダイジェスト管理](ledger-digest-management-and-database-verification.md)のためのローカル冗長ストレージ (LRS) のサポート | [Azure Storage の不変 BLOB](../../storage/blobs/immutable-storage-overview.md) を使用した台帳テーブルによる自動ダイジェスト管理では、ユーザーが [LRS](../../storage/common/storage-redundancy.md#locally-redundant-storage) アカウントを使用することはできません。|

## <a name="remarks"></a>注釈

- 台帳データベースを作成するとき、既定で (`APPEND_ONLY = ON` 句を指定せずに) データベース上に新たに作成されるテーブルはすべて、[更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)です。 [追加専用の台帳テーブル](ledger-append-only-ledger-tables.md)を作成するには、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) ステートメントを使用します。
- 台帳テーブルを FILETABLE にはできません。
- 台帳テーブルではフルテキスト インデックスを使用できません。
- 台帳テーブルの名前は変更できません。
- 台帳テーブルは異なるスキーマに移動できません。
- 台帳テーブルには、WITH VALUES を指定していない場合に限り、Null 許容の列だけを追加できます。
- 台帳テーブルの列は削除できません。
- 台帳テーブルでは決定論的な計算列だけを使用できます。
- 列の形式を変える方法で既存の列を変更することはできません。
  - 次のものは変更できます。
    - NULL 値の許容
    - nvarchar または ntext 列の照合順序、コード ページが変化しない場合に限り char または text 列の照合順序。
    - 可変長列の長さ。
    - スパース性。
- 台帳テーブルでは SWITCH IN または OUT を使用できません。
- `LEDGER = ON` があるデータベースでは、長期的なバックアップ (LTR) をサポートしていません。
- `LEDGER` または `SYSTEM_VERSIONING` のバージョン管理は、台帳テーブルに対して無効にできません。
- 台帳テーブルでは `UPDATETEXT` および `WRITETEXT` API を使用できません。
- 1 回のトランザクションで最大 200 個の台帳テーブルを更新できます。
- 一時テーブルに存在する制約はすべて、更新可能な台帳テーブルにも存在します。
- 台帳テーブルでは変更履歴を使用できません。
- 台帳テーブルでは、クラスター化列ストア インデックスが存在する場合、行ストア非クラスター化インデックスを作成できません。

## <a name="next-steps"></a>次の手順

- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md)
- [データベース台帳](ledger-database-ledger.md)
- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)