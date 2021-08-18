---
title: Azure Cosmos DB クエリ言語の文字列関数
description: Azure Cosmos DB での文字列 SQL システム関数について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: c3e9948fa6dd32198cd2584589a251b0c4399817
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206440"
---
# <a name="string-functions-azure-cosmos-db"></a>文字列関数 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

文字列関数を使用すると、Azure Cosmos DB 内の文字列に対して操作を実行できます。

## <a name="functions"></a>関数

以下のスカラー関数は、文字列入力値に対して演算を実行し、文字列、数値、ブール値のいずれかを返します。 **[インデックスの使用量]** 列は、文字列システム関数と他の値を、等価フィルターで比較していることを前提としています (該当する場合)。

| システム関数                                 | インデックスの使用量        | [スカラー集計関数を使用したクエリでのインデックスの使用](../index-overview.md#index-utilization-for-scalar-aggregate-functions) | 解説                                                      |
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

Azure Cosmos DB での[インデックスの使用](../index-overview.md#index-usage)について説明します。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
- [ユーザー定義関数](sql-query-udfs.md)
- [集計](sql-query-aggregate-functions.md)
