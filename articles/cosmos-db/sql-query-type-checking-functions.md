---
title: Azure Cosmos DB クエリ言語での型チェック関数
description: Azure Cosmos DB での SQL システム関数の型チェックについて説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/04/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 0a97517e524bbd6f13a108562a85037ae9695187
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108768915"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>型チェック関数 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

型チェック関数を使用すると、SQL クエリ内の式の型をチェックできます。 型チェック関数を使用して、項目内のプロパティの型が変数または不明の場合に型をその場で判定できます。 

## <a name="functions"></a>関数

次の関数は、入力値に対する型チェックをサポートし、それぞれがブール値を返します。

| システム関数                           | インデックスの使用量 | [スカラー集計関数を使用したクエリでのインデックスの使用](index-overview.md#index-utilization-for-scalar-aggregate-functions) | 注釈 |
| ----------------------------------------- | ----------- | ------------------------------------------------------------ | ------- |
| [IS_ARRAY](sql-query-is-array.md)         | フル スキャン   | フル スキャン                                                    |         |
| [IS_BOOL](sql-query-is-bool.md)           | インデックス シーク  | インデックス シーク                                                   |         |
| [IS_DEFINED](sql-query-is-defined.md)     | インデックス シーク  | インデックス シーク                                                   |         |
| [IS_NULL](sql-query-is-null.md)           | インデックス シーク  | インデックス シーク                                                   |         |
| [IS_NUMBER](sql-query-is-number.md)       | インデックス シーク  | インデックス シーク                                                   |         |
| [IS_OBJECT](sql-query-is-object.md)       | フル スキャン   | フル スキャン                                                    |         |
| [IS_PRIMITIVE](sql-query-is-primitive.md) | インデックス シーク  | インデックス シーク                                                   |         |
| [IS_STRING](sql-query-is-string.md)       | インデックス シーク  | インデックス シーク                                                   |         

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
- [ユーザー定義関数](sql-query-udfs.md)
- [集計](sql-query-aggregate-functions.md)
