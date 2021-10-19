---
title: Azure Cosmos DB の SQL キーワード
description: Azure Cosmos DB の SQL キーワードについて説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: tisande
ms.openlocfilehash: 78f2db0f13c1f4b73b926e14cca72645eb626223
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618764"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB でのキーワード
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB の SQL クエリで使用できるキーワードについて説明します。

## <a name="between"></a>BETWEEN

`BETWEEN` キーワードを使用して、文字列値や数値の範囲に対してクエリを表現することができます。 たとえば、次のクエリでは、最初の子の学年が 1 以上 5 以下であるすべての項目が返されます。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

`BETWEEN` キーワードはまた、次の例のように `SELECT` 句で使用することもできます。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

SQL API では、ANSI SQL と異なり、混合型のプロパティに対して範囲クエリを表すことができます。 たとえば、一部の項目の `grade` が `5` などの数値で、他の項目が `grade4` などの文字列である場合があります。 このような場合、JavaScript の場合と同様に、2 つの異なる種類を比較した結果は `Undefined` になるため、項目がスキップされます。

## <a name="distinct"></a>DISTINCT

`DISTINCT` キーワードは、クエリのプロジェクションでの重複を除去します。

この例では、クエリは各姓の値をプロジェクションしています。

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

結果は次のようになります。

```json
[
    "Andersen"
]
```

一意のオブジェクトをプロジェクションすることもできます。 この場合、lastName フィールドが 2 つのドキュメントのいずれかに存在しないため、クエリが空のオブジェクトを返します。

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

結果は次のようになります。

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` は、サブクエリ内のプロジェクションでも使用できます。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

このクエリは、重複が削除された各子の givenName を格納する配列をプロジェクションします。 この配列は、ChildNames という別名が付けられ、外側のクエリでプロジェクションされます。

結果は次のようになります。

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

集計システム関数を使用したクエリと `DISTINCT` を使用したサブクエリは、特定の SDK バージョンでのみサポートされます。 たとえば、次の図形を使用したクエリは、下の特定の SDK バージョンでのみサポートされます。

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

**サポートされている SDK バージョン:**

|**SDK**|**サポートされているバージョン**|
|-------|----------------------|
|.NET SDK|3.18.0 以降|
|Java SDK|4.19.0 以降|
|Node.js SDK|サポートされていない|
|Python SDK|サポートされていない|

集計システム関数を使用したクエリと `DISTINCT` を使用したサブクエリには、いくつかの追加の制限があります。 次のクエリはサポートされていません。

|**制限**|**例**|
|-------|----------------------|
|外部クエリの WHERE 句|SELECT COUNT(1) FROM (SELECT DISTINCT VALUE c.lastName FROM c) AS lastName WHERE lastName = "Smith"|
|外部クエリの ORDER BY 句|SELECT VALUE COUNT(1) FROM (SELECT DISTINCT VALUE c.lastName FROM c) AS lastName ORDER BY lastName|
|外部クエリの GROUP BY 句|SELECT COUNT(1) as annualCount, d.year FROM (SELECT DISTINCT c.year, c.id FROM c) AS d GROUP BY d.year|
|入れ子になったサブクエリ|SELECT COUNT(1) FROM (SELECT y FROM (SELECT VALUE StringToNumber(SUBSTRING(d.date, 0, 4 FROM (SELECT DISTINCT c.date FROM c) d) AS y WHERE y > 2012)|
|複数の集計|SELECT COUNT(1) as AnnualCount, SUM(d.sales) as TotalSales FROM (SELECT DISTINCT c.year, c.sales, c.id FROM c) AS d|

## <a name="like"></a>LIKE

特定の文字列が指定されたパターンに一致するかどうかによって、ブール値を返します。 パターンは、標準の文字とワイルドカード文字を含むことができます。 `LIKE` キーワードまたは [RegexMatch](sql-query-regexmatch.md) システム関数のいずれかを使用して、論理的に等価のクエリを記述できます。 どちらを選択しても、インデックスの使用率は同じになります。 したがって、正規表現よりも構文を選ぶ場合は、`LIKE` を使用する必要があります。

> [!NOTE]
> `LIKE` はインデックスを利用できるため、`LIKE` を使用して比較するプロパティの[範囲インデックスを作成](./../index-policy.md)する必要があります。

LIKE で使用できるワイルドカード文字は次のとおりです。

| ワイルドカード文字 | 説明                                                  | 例                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | 0 個以上の文字で構成される任意の文字列                      | WHERE   c.description LIKE   “%SO%PS%”      |
| _ (アンダースコア)     | 任意の 1 文字                                       | WHERE   c.description LIKE   “%SO_PS%”      |
| [ ]                  | 指定した範囲 ([a-f]) またはセット ([abcdef]) 内にある任意の 1 文字。 | WHERE   c.description LIKE   “%SO[t-z]PS%”  |
| [^]                  | 指定した範囲 ([^a-f]) またはセット ([^abcdef]) 内にない任意の 1 文字。 | WHERE   c.description LIKE   “%SO[^abc]PS%” |


### <a name="using-like-with-the--wildcard-character"></a>LIKE を % ワイルドカード文字と共に使用する

`%` 文字は、0 個以上の文字で構成される任意の文字列と一致します。 たとえば、パターンの先頭と末尾に `%` を配置すると、次のクエリでは、説明に `fruit` が含まれているすべての項目が返されます。

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

パターンの末尾にのみ `%` 文字を使用した場合は、説明が `fruit` で始まる項目のみが返されます。

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>NOT LIKE を使用する

次の例では、説明に `fruit` が含まれないすべての項目が返されます。

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>ESCAPE 句を使用する

ESCAPE 句を使用して、1 つまたは複数のワイルドカード文字を含むパターンを検索できます。 たとえば、文字列 `20-30%` が含まれている説明を検索する場合は、`%` をワイルドカード文字として解釈するのは望ましくありません。

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>ワイルドカード文字をリテラルとして使用する

ワイルドカード文字を角かっこで囲むと、リテラル文字として扱うことができます。 ワイルドカード文字を角かっこで囲む場合は、特殊な属性が削除されます。 次に例をいくつか示します。

| パターン           | 説明 |
| ----------------- | ------- |
| LIKE   “20-30[%]” | 20-30%  |
| LIKE   “[_]n”     | _n      |
| LIKE   “[ [ ]”    | [       |
| LIKE   “]”        | ]       |

## <a name="in"></a>IN

IN キーワードは、指定した値がリスト内のいずれかの値と一致するかどうかをチェックするために使用します。 たとえば以下のクエリでは、`id` が `WakefieldFamily` または `AndersenFamily` であるすべての家族の項目が返されます。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

次の例では、状態が指定された値のいずれかであるすべての項目が返されます。

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API では、FROM ソースの IN キーワードで追加される新しいコンストラクトによって、[JSON 配列に対する反復](sql-query-object-array.md#Iteration)がサポートされています。

`IN` フィルターにパーティション キーを含めると、クエリは関連するパーティションのみに自動的にフィルターを適用します。

## <a name="top"></a>TOP

TOP キーワードは、任意の順序で最初の `N` 個のクエリ結果を返します。 ベスト プラクティスとして、`ORDER BY` 句で TOP を使用して、最初の `N` 個の順序付けされた値に結果を制限します。 これらの 2 つの句を組み合わせることが、TOP の影響を受ける行を予想どおりに指定する唯一の方法です。

TOP は、次の例のように定数で、またはパラメーター化されたクエリを使用した変数値で使用できます。

```sql
    SELECT TOP 1 *
    FROM Families f
```

結果は次のようになります。

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>次のステップ

- [作業の開始](sql-query-getting-started.md)
- [結合](sql-query-join.md)
- [サブクエリ](sql-query-subquery.md)
