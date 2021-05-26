---
title: データベース台帳
description: この記事では、Azure SQL Database の台帳データベース テーブルと関連ビューに関する情報を提供します
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 031674854af41877483ece5c3969a0208a7a8167
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388352"
---
# <a name="what-is-the-database-ledger"></a>データベース台帳とは

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database の台帳は **パブリック プレビュー** 段階です。

データベース台帳では、ブロックチェーンと [Merkle ツリー データ構造](/archive/msdn-magazine/2018/march/blockchain-blockchain-fundamentals)が論理的に使用されます。 データベース台帳では、台帳テーブルに更新が行われる間、時間の経過と共に変化するデータベースの状態を段階的にキャプチャします。 これを実現するために、データベース台帳にはすべてのトランザクションのエントリが格納され、トランザクションに関するメタデータ (コミット タイムスタンプやトランザクションを実行したユーザーの ID など) だけでなく、各台帳テーブルで更新された行の Merkle ツリー ルートもキャプチャされます。 これらのエントリは、将来整合性を検証するために、開封明示のデータ構造に追加されます。

:::image type="content" source="media/ledger/merkle-tree.png" alt-text="sql ledger merkle tree":::

Azure SQL Database 台帳でデータの整合性を提供する方法について詳しくは、「[ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)」を参照してください。

## <a name="where-are-database-transaction-and-block-data-stored"></a>データベース トランザクションとブロック データの格納先

トランザクションとブロックに関するデータは、2 つの新しいシステム カタログ ビューの行として物理的に格納されます。

- [**sys.database_ledger_transactions**](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql) - 台帳内の各トランザクションの情報を含む行を保持します (このトランザクションが属するブロックの ID やブロック内のトランザクションの序数など)。 
- [**sys.database_ledger_blocks**](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql) - 台帳内のすべてのブロックの行を保持します (ブロック内のトランザクションに対する Merkle ツリーのルート、ブロックチェーンを形成するための前のブロックのハッシュなど)。

データベース台帳を表示するには、次の T-SQL ステートメントを [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) または [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) で実行します。

> [!IMPORTANT]
> データベース台帳を表示するには、**VIEW LEDGER CONTENT** アクセス許可が必要です。 台帳テーブルに関連するアクセス許可の詳細については、[アクセス許可](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)に関するページを参照してください。 

```sql
SELECT * FROM sys.database_ledger_transactions
GO

SELECT * FROM sys.database_ledger_blocks
GO
```

下に示すのは、データベース台帳のブロックチェーン内の 1 つのブロックを構成する 4 つのトランザクションで構成される台帳テーブルの例です。

:::image type="content" source="media/ledger/database-ledger-1.png" alt-text="台帳テーブルの例":::

ブロックは 30 秒ごとに、またはユーザーが [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) ストアド プロシージャの実行によってデータベース ダイジェストを手動で生成した場合に閉じられます。 ブロックが閉じられると、新しいトランザクションが新しいブロックに挿入されます。 次に、ブロック生成プロセスは、"*閉じた*" ブロックに属するすべてのトランザクションを、インメモリ キューと [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql) システム カタログ ビューの両方から取得し、これらのトランザクションの Merkle ツリー ルートと前のブロックのハッシュを計算して、[sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql) システム カタログ ビューで閉じられたブロックを永続化します。 これは通常のテーブル更新であるため、その持続性はシステムによって自動的に保証されます。 1 つのブロック チェーンを維持するために、この操作はシングルスレッドですが、効率的でもあります。なぜなら、トランザクション情報に対してハッシュを計算するだけで、非同期的に実行されるので、トランザクションのパフォーマンスに影響しないためです。   

## <a name="next-steps"></a>次の手順

- [ダイジェストの管理とデータベースの検証](ledger-digest-management-and-database-verification.md)
- [Azure SQL Database 台帳の概要](ledger-overview.md) 
- [セキュリティ カタログ ビュー (Transact-SQL)](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)
