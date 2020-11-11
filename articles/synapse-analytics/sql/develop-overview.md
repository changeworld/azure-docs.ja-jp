---
title: Synapse SQL 機能を開発するためのリソース
description: Synapse SQL に関する開発コンセプト、設計上の決定、レコメンデーション、およびコーディング技法。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 2a24a61e26bdfaba0619e3965e95b1828380a1c8
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322102"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Azure Synapse Analytics の Synapse SQL 機能の設計上の決定とコーディング技法
この記事では、Synapse SQL の専用 SQL プールとサーバーレス SQL プール (プレビュー) の関数に関するリソースの一覧を紹介します。 推奨される記事は、次の 2 つのセクションに分かれています。重要な設計上の決定と、開発およびコーディング技法。

これらの記事の目的は、Synapse Analytics 内の Synapse SQL コンポーネントの最適な技術的手法を開発するために役立つことです。

## <a name="key-design-decisions"></a>主要な設計上の決定
以下の記事では、Synapse SQL 開発の概念と設計上の決定について説明します。

| [アーティクル] | 専用 SQL プール | サーバーレス SQL プール |
| ------- | -------- | ------------- |
| [接続](connect-overview.md)                    | ○ | ○ |
| [リソース クラスとコンカレンシー](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | [はい]    | いいえ |
| [トランザクション](develop-transactions.md)              | [はい] | いいえ |
| [ユーザー定義スキーマ](develop-user-defined-schemas.md) | ○ | ○ |
| [テーブルのディストリビューション](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | [はい] | いいえ |
| [列ストア インデックスの品質の低さの原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | [はい] | いいえ |
| [テーブル パーティション](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | [はい] | いいえ |
| [統計](develop-tables-statistics.md)            | ○ | ○ |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | [はい] | いいえ |
| [外部テーブル](develop-tables-external-tables.md) | ○ | ○ |
| [CETAS](develop-tables-cetas.md)                     | ○ | はい |


## <a name="recommendations"></a>Recommendations

次に、開発のための特定のコーディング技法、ヒント、およびレコメンデーションに焦点を合わせた重要な記事を紹介します。

| [アーティクル] | 専用 SQL プール | サーバーレス SQL プール |
| ------- | -------- | ------------- |
| [ストアド プロシージャ](develop-stored-procedures.md)  | [はい]                | いいえ                      |
| [ラベル](develop-label.md)                           | [はい]                | いいえ                      |
| [ビュー](develop-views.md)                             | ○                | ○                     |
| [一時テーブル](develop-tables-temporary.md)       | ○                | ○                     |
| [動的 SQL](develop-dynamic-sql.md)                 | はい                | はい                     |
| [ループ](develop-loops.md)                         | ○                | ○                     |
| [オプションでグループ化する](develop-group-by-options.md)       | [はい]                | いいえ                      |
| [変数の代入](develop-variable-assignment.md) | はい                | ○                     |

## <a name="next-steps"></a>次のステップ
詳細な参照情報については、[SQL プール T-SQL ステートメント](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページをご覧ください。

