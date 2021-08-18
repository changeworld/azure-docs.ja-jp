---
title: Azure Cosmos DB クエリ言語の数学関数
description: 引数として指定された入力値に基づいて計算を実行し、数値を返す、Azure Cosmos DB の数学関数について学びます。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e3114220b6250afb9d633b073342ba6f2cf92b8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206495"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>数学関数 (Azure Cosmos DB)  
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

各数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。

次の例のようなクエリを実行できます。

```sql
    SELECT VALUE ABS(-4)
```

結果は次のとおりです。

```json
    [4]
```

## <a name="functions"></a>関数

一般に、次のサポートされている組み込みの数学関数では、入力引数に基づいて計算を実行し、数値式を返します。 **[インデックスの使用量]** 列は、数学システム関数と他の値を、等価フィルターで比較していることを前提としています (該当する場合)。
 
| システム関数                 | インデックスの使用量 | [スカラー集計関数を使用したクエリでのインデックスの使用](../index-overview.md#index-utilization-for-scalar-aggregate-functions) | 注釈                                                      |
| ------------------------------- | ----------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| [ABS](sql-query-abs.md)         | インデックス シーク  | インデックス シーク                                             |                                                              |
| [ACOS](sql-query-acos.md)       | フル スキャン   | フル スキャン                                              |                                                              |
| [ASIN](sql-query-asin.md)       | フル スキャン   | フル スキャン                                              |                                                              |
| [ATAN](sql-query-atan.md)       | フル スキャン   | フル スキャン                                              |                                                              |
| [ATN2](sql-query-atn2.md)       | フル スキャン   | フル スキャン                                              |                                                              |
| [CEILING](sql-query-ceiling.md) | インデックス シーク  | インデックス シーク                                             |                                                              |
| [COS](sql-query-cos.md)         | フル スキャン   | フル スキャン                                              |                                                              |
| [COT](sql-query-cot.md)         | フル スキャン   | フル スキャン                                              |                                                              |
| [DEGREES](sql-query-degrees.md) | インデックス シーク  | インデックス シーク                                             |                                                              |
| [EXP](sql-query-exp.md)         | フル スキャン   | フル スキャン                                              |                                                              |
| [FLOOR](sql-query-floor.md)     | インデックス シーク  | インデックス シーク                                             |                                                              |
| [LOG](sql-query-log.md)         | フル スキャン   | フル スキャン                                              |                                                              |
| [LOG10](sql-query-log10.md)     | フル スキャン   | フル スキャン                                              |                                                              |
| [PI](sql-query-pi.md)           | N/A         | N/A                                                    | PI () は定数値を返します。 結果は決定論的であるため、PI() との比較ではインデックスを使用できます。 |
| [POWER](sql-query-power.md)     | フル スキャン   | フル スキャン                                              |                                                              |
| [RADIANS](sql-query-radians.md) | インデックス シーク  | インデックス シーク                                             |                                                              |
| [RAND](sql-query-rand.md)       | N/A         | N/A                                                    | Rand() は乱数を返します。 結果は非決定論的であるため、Rand() を含む比較ではインデックスを使用できません。 |
| [ROUND](sql-query-round.md)     | インデックス シーク  | インデックス シーク                                             |                                                              |
| [SIGN](sql-query-sign.md)       | インデックス シーク  | インデックス シーク                                             |                                                              |
| [SIN](sql-query-sin.md)         | フル スキャン   | フル スキャン                                              |                                                              |
| [SQRT](sql-query-sqrt.md)       | フル スキャン   | フル スキャン                                              |                                                              |
| [SQUARE](sql-query-square.md)   | フル スキャン   | フル スキャン                                              |                                                              |
| [TAN](sql-query-tan.md)         | フル スキャン   | フル スキャン                                              |                                                              |
| [TRUNC](sql-query-trunc.md)     | インデックス シーク  | インデックス シーク                                              |                                                              |
## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
- [ユーザー定義関数](sql-query-udfs.md)
- [集計](sql-query-aggregate-functions.md)
