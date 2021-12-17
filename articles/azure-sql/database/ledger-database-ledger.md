---
title: データベース台帳
description: この記事では、Azure SQL Database の台帳データベースのテーブルと、関連するビューについての情報を提供します。
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: f5273208104853a276ae78e8debc685fa2a05c4d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062210"
---
# <a name="what-is-the-database-ledger"></a>データベース台帳とは

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

データベース台帳は、Azure SQL Database の台帳機能の一部です。 データベース台帳では、時間と共に変化するデータベースの状態を差分によって把握しつつ、台帳テーブルを更新します。 ブロックチェーンと[マークル ツリーデータ構造](/archive/msdn-magazine/2018/march/blockchain-blockchain-fundamentals)を論理的に使用します。 

データベースの状態を把握するため、データベース台帳には、すべてのトランザクションのエントリを保存します。 コミットのタイムスタンプやそれを実行したユーザーの ID など、トランザクションのメタデータも保存します。 各台帳テーブルのマークル ツリーのルートのデータも保存します。 これらのエントリは、将来整合性を検証するために、開封明示のデータ構造に追加されます。

:::image type="content" source="media/ledger/merkle-tree.png" alt-text="台帳機能のマークル ツリーを表す図。":::

Azure SQL Database 台帳でデータの整合性を提供する方法について詳しくは、「[ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)」を参照してください。

## <a name="where-are-database-transaction-and-block-data-stored"></a>データベース トランザクションとブロック データの格納先

トランザクションとブロックのデータは、 次の 2 つのシステム カタログ ビューの行に物理的に保存します。

- [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql): データベース台帳での各トランザクションの情報を含む行を管理します。 その情報には、このトランザクションが属するブロックの ID や、このトランザクションのブロック内での序数が含まれます。 
- [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql): 台帳のすべてのブロックに対応する行を管理します。ブロック内のトランザクションのマークル ツリーのルートや、ブロックチェーン形成のための直前のブロックのハッシュなどの情報を含みます。

データベース台帳を見るには、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) で次の T-SQL 文を実行します。

> [!IMPORTANT]
> データベース台帳を表示するには、**VIEW LEDGER CONTENT** アクセス許可が必要です。 台帳テーブルに関連するアクセス許可の詳細については、[アクセス許可](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)に関するページを参照してください。 

```sql
SELECT * FROM sys.database_ledger_transactions
GO

SELECT * FROM sys.database_ledger_blocks
GO
```

次に挙げる台帳テーブルのサンプルは 4 つのトランザクションで構成されています。これらのトランザクションにより、データベース台帳のブロックチェーンのブロック 1 つが構成されます。

:::image type="content" source="media/ledger/database-ledger-1.png" alt-text="台帳テーブルのサンプルのスクリーンショット。":::

ブロックは 30 秒ごとに閉じるか、またはユーザーが手動で [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) ストアド プロシージャを実行してデータベースのダイジェストを生成したときに閉じます。 

ブロックが閉じられると、新しいトランザクションが新しいブロックに挿入されます。 それ以降のブロック生成プロセスは、次のようになります。

1. *閉じた* ブロックに属するすべてのトランザクションを、メモリ内のキューと [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql) システム カタログ ビューの両方から取得します。
1. これらのトランザクションのマークル ツリーのルートと、直前のブロックのハッシュを計算します。
1. 閉じたブロックを [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql) システム カタログ ビューに保存します。 

このテーブル更新は定期的に実行され、システムの持続性が自動的に保証されます。 ブロックのチェーン 1 本を管理するのに、この操作がシングル スレッドで実行されます。 ただし、トランザクション情報に関わるハッシュだけを計算すればよく、非同期で実行することから、これは効率的なやり方であるといえます。 トランザクションのパフォーマンスには影響がありません。   

## <a name="next-steps"></a>次の手順

- [Azure SQL Database 台帳の概要](ledger-overview.md) 
- [セキュリティ カタログ ビュー (Transact-SQL)](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)
