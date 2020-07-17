---
title: T-SQL ビューの使用
description: T-SQL ビューの使用と Synapse SQL プールでのソリューションの開発に関するヒント。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633389"
---
# <a name="views-in-synapse-sql-pool"></a>Synapse SQL プールのビュー

ビューをさまざまな方法で使用して、ソリューションの品質を向上させることができます。

SQL プールでは、標準ビューと具体化されたビューの両方がサポートされます。 どちらも SELECT 式を使って作成され、クエリからは論理テーブルとして認識される仮想テーブルです。

ビューでは一般的なデータ計算に伴う複雑さがカプセル化され、計算の変更に抽象化レイヤーが追加されます。これにより、クエリの書き換えが不要になります。

## <a name="standard-view"></a>標準ビュー

標準ビューでは、ビューを使用するたびにそのデータが計算されます。 ディスクには一切データは格納されません。 通常、標準ビューは、データベース内の論理オブジェクトとクエリを体系化しやすくするツールとして使用されます。

標準ビューを使用するには、クエリでそれを直接参照する必要があります。 詳細については、[CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)のドキュメントを参照してください。

SQL プールのビューはメタデータ専用として格納されます。 そのため、次のオプションは使用できません。

* スキーマ バインド オプションはありません
* ビューを通してベース テーブルを更新することはできません
* 一時テーブルのビューを作成することはできません
* EXPAND / NOEXPAND ヒントはサポートされていません
* SQL プールにはインデックス付きビューはありません

標準ビューを利用して、パフォーマンスが最適化された結合をテーブル間に適用することができます。 たとえば、ビューで結合条件の一部として冗長分散キーを組み込むことで、データの移動を最小限に抑えることができます。

ビューの利点として、特定のクエリ ヒントや結合ヒントを強制できることも挙げられます。 この方法でビューを使用すると、結合が常に最適な方法で実行されることが保証され、ユーザーに依存せずに結合を適切に作成できます。

## <a name="materialized-view"></a>具体化されたビュー

具体化されたビューでは、そのデータが事前に計算され、テーブルと同じように SQL プールに格納して管理されます。 具体化されたビューは、使用のたびに再計算を実行する必要がありません。

データがベース テーブルに読み込まれると、SQL プールによって具体化されたビューが同期的に更新されます。  クエリでビューが参照されていない場合でも、クエリ オプティマイザーでデプロイ済みの具体化されたビューが自動的に使用され、クエリのパフォーマンスが向上します。  

具体化されたビューのメリットが最も大きいクエリは、小さな結果セットを生成する、大きなテーブルに対する複雑なクエリ (通常は結合と集計を使用するクエリ) です。  

具体化されたビューの構文とその他の要件の詳細については、[CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) に関するページを参照してください。  

クエリのチューニングの概要については、[具体化されたビューを使用したパフォーマンス チューニング](performance-tuning-materialized-views.md)に関するページを参照してください。

## <a name="example"></a>例

一般的なアプリケーション パターンでは、データを読み込みながら、CREATE TABLE AS SELECT (CTAS) とオブジェクトの名前変更パターンを使用してテーブルが再作成されます。  

次の例では、日付ディメンションに新しい日付レコードを追加します。 新しいテーブルの DimDate_New が最初に作成され、名前が変更され、テーブルの最初のバージョンに代わることに注意してください。

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

ただし、この方法では、[テーブルが存在しません] というエラー メッセージの発行と共に、ユーザーのビューにテーブルが表示されたり消えたりする結果になる可能性があります。

ビューを使用すると、基になるオブジェクトの名前は変更された場合でも、ユーザーに一貫性のあるプレゼンテーション レイヤーを提供できます。 ビュー経由でのデータへのアクセスを提供することで、ユーザーが基になるテーブルを表示可能である必要はなくなります。

このレイヤーでは、データ ウェアハウスの設計者がデータ モデルを発展させることが可能な状況を確保したうえで、一貫性のあるユーザー エクスペリエンスを提供します。 基になるテーブルを発展させることで、設計者は CTAS を使用して、データの読み込みプロセスにおけるパフォーマンスを最大限に活かすことが可能になります。

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントについては、[SQL プールの開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。
