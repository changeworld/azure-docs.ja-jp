---
title: Azure Cosmos DB の集計関数
description: SQL 集計関数の構文、Azure Cosmos DB でサポートされる集計関数の種類について説明します。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464463"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB の集計関数

集計関数は、`SELECT` 句内の一連の値を計算して 1 つの値を返します。 たとえば、次のクエリでは、`Families` コンテナー内にある項目の数が返されます。

## <a name="examples"></a>例

```sql
    SELECT COUNT(1)
    FROM Families f
```

結果は次のようになります。

```json
    [{
        "$1": 2
    }]
```

VALUE キーワードを使用して、集計のスカラー値のみを返すこともできます。 たとえば、次のクエリは、値の数を 1 つの数値として返します。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果は次のようになります。

```json
    [ 2 ]
```

フィルターによって集計を組み合わせることもできます。 たとえば、次のクエリでは、住所の州が `WA` である項目の数が返されます。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

結果は次のようになります。

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>集計関数の種類

SQL API は、次の集計関数をサポートしています。 `SUM` と `AVG` は数値に対して機能し、`COUNT`、`MIN`、および `MAX` は、数値、文字列、ブール値、および null 値に対して機能します。

| 機能 | 説明 |
|-------|-------------|
| [COUNT] | 式の項目の数を返します。 |
| [SUM]   | 式のすべての値の合計を返します。 |
| [MIN]   | 式の最小値を返します。 |
| [MAX]   | 式内の最大値を返します。 |
| AVG   | 式の値の平均を返します。 |

配列の反復処理の結果に対して集計することもできます。

> [!NOTE]
> Azure portal のデータ エクスプローラーでは、集計クエリが、1 ページのみのクエリ ページに対する部分的な結果を集計することがあります。 SDK は、すべてのページにわたって累計した単一の値を生成します。 コードを使用して集計クエリを実行するには、.NET SDK 1.12.0、.NET Core SDK 1.1.0、または Java SDK 1.9.5 以降が必要です。

## <a name="remarks"></a>解説

この集計システム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。 あるプロパティで `COUNT`、`SUM`、`MIN`、`MAX`、`AVG` を行うことが予想される場合、[インデックス作成ポリシーに関連パスを含めてください](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の概要](introduction.md)
- [システム関数](sql-query-system-functions.md)
- [ユーザー定義関数](sql-query-udfs.md)