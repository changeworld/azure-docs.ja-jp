---
title: Azure Cosmos DB の集計関数
description: SQL 集計関数の構文、Azure Cosmos DB でサポートされる集計関数の種類について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 1ce3b18dd31944a1a4d4e6fad8fb49e63996dace
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871841"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB の集計関数

集計関数は、SELECT 句内の一連の値を計算して 1 つの値を返します。 たとえば、次のクエリでは、`Families` コンテナー内にある項目の数が返されます。

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

SQL API は、次の集計関数をサポートしています。 SUM と AVG は数値に対して機能し、COUNT、MIN、および MAX は、数値、文字列、ブール値、および null 値に対して機能します。

| Function | 説明 |
|-------|-------------|
| COUNT | 式の項目の数を返します。 |
| SUM   | 式のすべての値の合計を返します。 |
| MIN   | 式の最小値を返します。 |
| MAX   | 式の最大値を返します。 |
| AVG   | 式の値の平均を返します。 |

配列の反復処理の結果に対して集計することもできます。

> [!NOTE]
> Azure portal のデータ エクスプローラーでは、集計クエリが、1 ページのみのクエリ ページに対する部分的な結果を集計することがあります。 SDK は、すべてのページにわたって累計した単一の値を生成します。 コードを使用して集計クエリを実行するには、.NET SDK 1.12.0、.NET Core SDK 1.1.0、または Java SDK 1.9.5 以降が必要です。

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の概要](introduction.md)
- [システム関数](sql-query-system-functions.md)
- [ユーザー定義関数](sql-query-udfs.md)