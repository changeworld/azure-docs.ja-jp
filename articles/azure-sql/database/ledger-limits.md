---
title: Azure SQL Database 台帳の制約
description: Azure SQL Database の台帳機能の制約
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 1a3451356f0c38e8006b4724873f2b82f39d6a85
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388287"
---
# <a name="limitations-for-azure-sql-database-ledger"></a>Azure SQL Database 台帳の制約

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 台帳は現在 **パブリック プレビュー** です。

この記事では、Azure SQL Database で台帳テーブルを使用する際の制限を概説します。  

## <a name="limitations"></a>制限事項

| 機能 | 制限事項 |
| :--- | :--- |
| [台帳データベース](ledger-database-ledger.md)を無効にする   | 台帳データベースは一度有効にすると無効に戻すことはできません。 |
| 列の最大数 | [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)を作成すると、4 つの [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列がそこに追加され、[追加専用台帳テーブル](ledger-append-only-ledger-tables.md)を作成すると、2 つの列がそこに追加されます。 これらの新しい列の数も、Azure SQL Database でサポートしている最大列数 (1,024) に対してカウントされます。 |
| 使用できないデータの種類 | データの種類に関して、XML、SqlVariant、ユーザー定義型、FILESTREAM はサポートしていません。 |
| イン メモリ テーブル | メモリ内テーブルはサポートしていません。 |
| スパース列セット | スパース列セットはサポートしていません。 |
| 台帳の切り詰め | [追加専用台帳テーブル](ledger-append-only-ledger-tables.md)、および[更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)の履歴テーブルの古いデータの削除はサポートしていません。 |
| 既存のテーブルを台帳テーブルに変換する | 台帳が有効になっていないデータベースの既存のテーブルは、台帳テーブルに変換できません。 |
|[自動ダイジェスト管理](ledger-digest-management-and-database-verification.md)の LRS サポート | [Azure Storage の不変 BLOB](../../storage/blobs/storage-blob-immutable-storage.md) を使用した台帳テーブルによる自動ダイジェスト管理では、[ローカル冗長ストレージ (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage) アカウントを使用できません。|

## <a name="remarks"></a>注釈

- 台帳データベースを作成するとき、既定で (`APPEND_ONLY = ON` 句を指定せずに) データベース上に新たに作成されるテーブルはすべて、[更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)です。 [追加専用台帳テーブル](ledger-append-only-ledger-tables.md)は、[CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) 文で作成できます。
- 台帳テーブルを FILETABLE にはできません。
- 台帳テーブルではフルテキスト インデックスを使用できません。
- 台帳テーブルの名前は変更できません。
- 台帳テーブルは異なるスキーマに移動できません。
- 台帳テーブルには、WITH VALUES を指定していない場合に限り、Null 許容の列だけを追加できます。
- 台帳テーブルの列は削除できません。
- 台帳テーブルでは決定論的な計算列だけを使用できます。
- 列の形式を変える方法で、既存の列を変更することはできません。
  - 次のものは変更できます。
    - NULL 値の許容
    - nvarchar/ntext 列の照合順序、コード ページが変化しない場合に限り char/text 列の照合順序
    - 可変長列の長さを変更する
    - スパース性
- 台帳テーブルでは SWITCH IN/OUT を使用できません
- `LEDGER = ON` を持つデータベースでは、長期的なバックアップ (LTR) をサポートしていません
- 台帳テーブルでは `LEDGER` と `SYSTEM_VERSIONING` は無効にできません。
- 台帳テーブルでは `UPDATETEXT` および `WRITETEXT` API を使用できません。
- 1 回のトランザクションで最大 200 個の台帳テーブルを更新できます。
- 一時テーブルに存在する制約はすべて、更新可能な台帳テーブルにも存在します。
- 台帳テーブルでは変更履歴を使用できません。
- 台帳テーブルでは、クラスター化列ストアインデックスが存在する場合、行ストア非クラスター化インデックスを作成できません。

## <a name="next-steps"></a>次の手順

- [更新可能な台帳テーブル](ledger-updatable-ledger-tables.md)   
- [追加専用台帳テーブル](ledger-append-only-ledger-tables.md)   
- [データベース台帳](ledger-database-ledger.md)   
- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)   
