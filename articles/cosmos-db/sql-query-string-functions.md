---
title: Azure Cosmos DB クエリ言語の文字列関数
description: Azure Cosmos DB での文字列 SQL システム関数について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/04/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 4fe60a119626dca51f5dd4f38a81f6a2ef99c83c
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108743725"
---
# <a name="string-functions-azure-cosmos-db"></a>文字列関数 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

文字列関数を使用すると、Azure Cosmos DB 内の文字列に対して操作を実行できます。

## <a name="functions"></a>関数

次のスカラー関数は、文字列入力値に対して演算を実行し、文字列、数値またはブール値を返します。
  
| システム関数                                 | インデックスの使用量        | [スカラー集計関数](index-overview.md#index-utilization-for-scalar-aggregate-functions)を使用したクエリでのインデックスの使用 | 解説                                                      |
| ----------------------------------------------- | ------------------ | ------------------------------------------------------ | ------------------------------------------------------------ |
| [CONCAT](sql-query-concat.md)                   | フル スキャン          | フル スキャン                                              |                                                              |
| [CONTAINS](sql-query-contains.md)               | フル インデックス スキャン    | フル スキャン                                              |                                                              |
| [ENDSWITH](sql-query-endswith.md)               | フル インデックス スキャン    | フル スキャン                                              |                                                              |
| [INDEX_OF](sql-query-index-of.md)               | フル スキャン          | フル スキャン                                              |                                                              |
| [LEFT](sql-query-left.md)                       | 正確なインデックス スキャン | 正確なインデックス スキャン                                     |                                                              |
| [LENGTH](sql-query-length.md)                   | フル スキャン          | フル スキャン                                              |                                                              |
| [LOWER](sql-query-lower.md)                     | フル スキャン          | フル スキャン                                              |                                                              |
| [LTRIM](sql-query-ltrim.md)                     | フル スキャン          | フル スキャン                                              |                                                              |
| [REGEXMATCH](sql-query-regexmatch.md)           | フル インデックス スキャン    | フル スキャン                                              |                                                              |
| [REPLACE](sql-query-replace.md)                 | フル スキャン          | フル スキャン                                              |                                                              |
| [REPLICATE](sql-query-replicate.md)             | フル スキャン          | フル スキャン                                              |                                                              |
| [REVERSE](sql-query-reverse.md)                 | フル スキャン          | フル スキャン                                              |                                                              |
| [RIGHT](sql-query-right.md)                     | フル スキャン          | フル スキャン                                              |                                                              |
| [RTRIM](sql-query-rtrim.md)                     | フル スキャン          | フル スキャン                                              |                                                              |
| [STARTSWITH](sql-query-startswith.md)           | 正確なインデックス スキャン | 正確なインデックス スキャン                                     | 大文字と小文字を区別しないオプションが true の場合、拡張インデックス スキャンとなります。 |
| [STRINGEQUALS](sql-query-stringequals.md)       | インデックス シーク         | インデックス シーク                                             | 大文字と小文字を区別しないオプションが true の場合、拡張インデックス スキャンとなります。 |
| [StringToArray](sql-query-stringtoarray.md)     | フル スキャン          | フル スキャン                                              |                                                              |
| [StringToBoolean](sql-query-stringtoboolean.md) | フル スキャン          | フル スキャン                                              |                                                              |
| [StringToNull](sql-query-stringtonull.md)       | フル スキャン          | フル スキャン                                              |                                                              |
| [StringToNumber](sql-query-stringtonumber.md)   | フル スキャン          | フル スキャン                                              |                                                              |

Azure Cosmos DB での[インデックスの使用](index-overview.md#index-usage)について説明します。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
- [ユーザー定義関数](sql-query-udfs.md)
- [集計](sql-query-aggregate-functions.md)
