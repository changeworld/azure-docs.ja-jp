---
title: Azure Cosmos DB を使用する場合のクエリの問題のトラブルシューティング
description: Azure Cosmos DB の SQL クエリに関する問題を特定、診断、およびトラブルシューティングする方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 01/14/2020
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 5f4728c4b604c606d12edcc7a00879b31e54bc85
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264273"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Azure Cosmos DB を使用する場合のクエリの問題のトラブルシューティング

この記事では、Azure Cosmos DB のクエリのトラブルシューティングに関する一般的な推奨アプローチについて説明します。 このドキュメントに記載されている手順は、クエリの潜在的な問題すべてに対応するわけではありませんが、ここでは最も一般的なパフォーマンスのヒントをご紹介します。 このドキュメントは、Azure Cosmos DB のコア (SQL) API の低速クエリまたはコストの高いクエリのトラブルシューティングのための出発点として使用してください。 また、[診断ログ](cosmosdb-monitor-resource-logs.md)を使用して、低速なクエリや大量のスループットを消費しているクエリを識別したりすることもできます。

Azure Cosmos DB では、次のようにクエリ最適化を幅広く分類できます。クエリの要求ユニット (RU) 使用量を削減し、待機時間のみを減らす最適化。 クエリの RU 使用量を減らすことで、待機時間も短くなります。

このドキュメントでは、[栄養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)データセットを使用して再作成できる例を使用します。

### <a name="obtaining-query-metrics"></a>クエリ メトリックの取得:

Azure Cosmos DB でクエリを最適化する場合、最初の手順は常にクエリの[クエリ メトリックを取得する](profile-sql-api-query.md)ことです。 これらは、次に示すように Azure portal でも使用できます。

[ ![クエリ メトリックの取得](./media/troubleshoot-query-performance/obtain-query-metrics.png) ](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

クエリ メトリックを取得したら、クエリの取得したドキュメント数と出力したドキュメント数を比較します。 この比較により、参照する以下の関連セクションを特定します。

取得したドキュメント数は、クエリで読み込む必要があるドキュメントの数です。 出力したドキュメント数は、クエリの結果に必要なドキュメントの数です。 取得したドキュメント数が出力したドキュメント数より大幅に多い場合は、クエリの少なくとも 1 部分でインデックスを使用できず、スキャンを実行する必要がありました。

以下のセクションを参照して、該当シナリオに関連するクエリの最適化について理解することができます。

### <a name="querys-ru-charge-is-too-high"></a>クエリの RU 使用量が高すぎる

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>取得したドキュメント数が出力したドキュメント数を大幅に超えている

- [インデックス作成ポリシーに必要なパスを含める](#include-necessary-paths-in-the-indexing-policy)

- [インデックスを使用するシステム関数について理解する](#understand-which-system-functions-utilize-the-index)

- [フィルターと ORDER BY 句の両方を使用したクエリ](#queries-with-both-a-filter-and-an-order-by-clause)

- [サブクエリを使用して JOIN 式を最適化する](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>取得したドキュメント数が出力したドキュメント数とほぼ等しい

- [クロス パーティション クエリを回避する](#avoid-cross-partition-queries)

- [複数のプロパティのフィルター](#filters-on-multiple-properties)

- [フィルターと ORDER BY 句の両方を使用したクエリ](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>クエリの RU 使用量は許容されるが、待機時間は依然として長すぎる

- [近接性の向上](#improve-proximity)

- [プロビジョニングされたスループットの増加](#increase-provisioned-throughput)

- [MaxConcurrency の増加](#increase-maxconcurrency)

- [MaxBufferedItemCount の増加](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>取得したドキュメント数が出力したドキュメント数を超えるクエリ

 取得したドキュメント数は、クエリで読み込む必要があるドキュメントの数です。 出力したドキュメント数は、クエリの結果に必要なドキュメントの数です。 取得したドキュメント数が出力したドキュメント数より大幅に多い場合は、クエリの少なくとも 1 部分でインデックスを使用できず、スキャンを実行する必要がありました。

 インデックスによって完全には処理されなかったスキャン クエリの例を次に示します。

クエリ:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

クエリ メトリック:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

取得したドキュメント数 (60,951) は出力したドキュメント数 (7) を大幅に上回っているため、このクエリでスキャンを実行する必要がありました。 この場合、システム関数 [UPPER()](sql-query-upper.md) はインデックスを使用しません。

## <a name="include-necessary-paths-in-the-indexing-policy"></a>インデックス作成ポリシーに必要なパスを含める

インデックス作成ポリシーは、`WHERE` 句、`ORDER BY` 句、`JOIN`、およびほとんどのシステム関数に含まれるすべてのプロパティに対応している必要があります。 インデックス ポリシーで指定されたパスは、JSON ドキュメントのプロパティと一致する必要があります (大文字と小文字は区別されます)。

[栄養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)データセットに対して単純なクエリを実行した場合、`WHERE` 句のプロパティのインデックスが作成されると、より低い RU 使用量が発生します。

### <a name="original"></a>変更元

クエリ:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

インデックス作成ポリシー:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**RU 使用量:** 409.51 RU

### <a name="optimized"></a>最適化

更新されたインデックス作成ポリシー:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**RU 使用量:** 2.98 RU

書き込み可用性やパフォーマンスに影響を与えることなく、いつでもインデックス作成ポリシーにプロパティを追加できます。 新しいプロパティをインデックスに追加すると、このプロパティを使用するクエリでは、使用可能な新しいインデックスが直ちに使用されます。 このクエリは、ビルド中に新しいインデックスを使用します。 その結果、インデックスの再構築が進行中であるため、クエリ結果が不整合になる可能性があります。 新しいプロパティにインデックスが作成されている場合、インデックスの再構築中に既存のインデックスのみを使用するクエリは影響を受けません。 [インデックス変換の進行状況を追跡](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)できます。

## <a name="understand-which-system-functions-utilize-the-index"></a>インデックスを使用するシステム関数について理解する

式を文字列値の範囲に変換できる場合、インデックスを使用できます。そうでない場合は使用できません。

インデックスを使用できる文字列関数の一覧を次に示します。

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0

インデックスを使用せず、各ドキュメントを読み込む必要がある一般的なシステム関数は、次のとおりです。

| **システム関数**                     | **最適化のアイデア**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | フルテキスト検索に Azure Search を使用する                        |
| UPPER/LOWER                             | システム関数を使用して比較のたびにデータを正規化する代わりに、挿入時に大文字と小文字を正規化することができます。 次に、```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` などのクエリが ```SELECT * FROM c WHERE c.name = 'BOB'``` になります |
| 数学関数 (非集計) | クエリで値を頻繁に計算する必要がある場合は、JSON ドキュメントにこの値をプロパティとして格納することを検討してください。 |

------

インデックスを使用していないシステム関数でも、クエリの他の部分でインデックスが使用される場合があります。

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>フィルターと ORDER BY 句の両方を使用したクエリ

通常、フィルターと `ORDER BY` 句を使用したクエリでは範囲インデックスが使用されますが、複合インデックスから処理できる場合は、効率が向上します。 インデックス作成ポリシーを変更するだけでなく、複合インデックス内のすべてのプロパティを `ORDER BY` 句に追加する必要があります。 このクエリの変更により、複合インデックスが利用されるようになります。  [栄養](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)データセットに対してクエリを実行することで、影響を観察できます。

### <a name="original"></a>変更元

クエリ:

```sql
SELECT * FROM c WHERE c.foodGroup = “Soups, Sauces, and Gravies” ORDER BY c._ts ASC
```

インデックス作成ポリシー:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**RU 使用量:** 44.28 RU

### <a name="optimized"></a>最適化

更新されたクエリ (`ORDER BY` 句の両方のプロパティが含まれます):

```sql
SELECT * FROM c 
WHERE c.foodGroup = “Soups, Sauces, and Gravies” 
ORDER BY c.foodGroup, c._ts ASC
```

更新されたインデックス作成ポリシー:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**RU 使用量:** 8.86 RU

## <a name="optimize-join-expressions-by-using-a-subquery"></a>サブクエリを使用して JOIN 式を最適化する
複数値サブクエリは、`WHERE` 句内のすべてのクロス結合の後ではなく、それぞれの select-many 式の後に述語をプッシュすることによって `JOIN` 式を最適化できます。

次のクエリを考えてみます。

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU 使用量:** 167.62 RU

このクエリでは、インデックスは、"infant formula"(乳児用調製粉乳) という名前のタグを持ち、nutritionValue が 0 より大きく、処理量が 1 より大きい任意のドキュメントに一致します。 ここでの `JOIN` 式は、一致するドキュメントごとに、タグ、栄養素、1 回分の各配列の全項目の外積を、フィルターが適用される前に実行します。 `WHERE` 句はその後、`<c, t, n, s>` タプルごとにフィルター述語を適用します。

たとえば、一致するドキュメントで、3 つの配列のそれぞれに 10 個の項目があるとした場合、1 x 10 x 10 x 10 (つまり、1,000) タプルに展開されます。 ここでサブクエリを使用すると、次の式と結合する前に、結合された配列項目をフィルターで除外するために役立ちます。

このクエリは前のものと同等ですが、サブクエリを使用します。

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 使用量:** 22.17 RU

tags 配列の 1 つの項目のみがフィルターに一致し、nutrients 配列と servings 配列の両方に 5 つの項目があるとします。 `JOIN` 式は、最初のクエリの 1,000 項目とは異なり、1 x 1 x 5 x 5 = 25 項目に展開されます。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>取得したドキュメント数が出力したドキュメント数と等しいクエリ

取得したドキュメント数が出力したドキュメント数とほぼ同じ場合は、クエリで多くの不要なドキュメントをスキャンする必要がなかったことを意味します。 TOP キーワードを使用するクエリなど、多くのクエリでは、取得したドキュメント数が出力したドキュメント数が 1 つ多い可能性があります。 これは問題の原因にはなりません。

## <a name="avoid-cross-partition-queries"></a>クロス パーティション クエリを回避する

Azure Cosmos DB では[パーティション分割](partitioning-overview.md)を使用して、要求ユニットとデータ ストレージのニーズの増加に応じて個々のコンテナーをスケーリングします。 各物理パーティションには、区切られて独立したインデックスがあります。 クエリにコンテナーのパーティション キーに一致する等値フィルターがある場合、確認する必要があるのは関連するパーティションのインデックスのみです。 この最適化により、クエリが必要とする RU の合計数が減少します。

プロビジョニングされた RU 使用量の数が多い (3 万以上) 場合や、大量のデータ (100 GB 以上) が格納されている場合は、おそらく、クエリ RU の使用量を大幅に削減するために必要なコンテナーがあります。

たとえば、foodGroup というパーティション キーを持つコンテナーを作成する場合、次のクエリでは 1 つの物理パーティションのみを確認する必要があります。

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies” and c.description = "Mushroom, oyster, raw"
```

クエリにパーティション キーを含めることで、これらのクエリを最適化することもできます。

```sql
SELECT * FROM c
WHERE c.foodGroup IN(“Soups, Sauces, and Gravies”, “"Vegetables and Vegetable Products”) and  c.description = "Mushroom, oyster, raw"
```

パーティション キーに対して範囲フィルターが設定されているか、パーティション キーにフィルターがない場合、クエリは "ファンアウト" し、すべての物理パーティションのインデックスの結果を確認する必要があります。

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > “Soups, Sauces, and Gravies” and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>複数のプロパティのフィルター

通常、複数のプロパティのフィルターを使用したクエリでは範囲インデックスが使用されますが、複合インデックスから処理できる場合は、効率が向上します。 少量のデータの場合、この最適化は大きな影響を与えません。 ただし、大量のデータを使用する場合には、便利な場合があります。 最適化できる非等値フィルターは、複合インデックスごとに最大 1 つです。 クエリに複数の非等値フィルターがある場合は、複合インデックスを使用するクエリを 1 つ選択する必要があります。 それ以外では、範囲インデックスが引き続き使用されます。 非等値フィルターは、複合インデックス内で最後に定義する必要があります。 [複合インデックスについての詳細情報](index-policy.md#composite-indexes)

複合インデックスを使用して最適化できるクエリの例をいくつか次に示します。

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

関連する複合インデックスは次のとおりです。

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>クエリの待機時間を短縮する最適化:

多くの場合、RU 使用量は許容されますが、クエリの待機時間が長くなる可能性があります。 以下のセクションでは、クエリの待機時間を短縮するためのヒントの概要を説明します。 同じクエリを同じデータセットに対して複数回実行すると、毎回同じ RU 使用量が発生します。 ただし、クエリの待機時間はクエリの実行間隔によって異なる場合があります。

## <a name="improve-proximity"></a>近接性の向上

Azure Cosmos DB アカウントとは異なるリージョンから実行されるクエリでは、同じリージョン内で実行された場合よりも待機時間が長くなります。 たとえば、デスクトップ コンピューターでコードを実行していた場合、クエリが Azure Cosmos DB と同じ Azure リージョン内の仮想マシンからのものである場合よりも、待機時間は数十ミリ秒または数百ミリ秒 (またはそれ以上) 分長くなることが予想されます。 データをアプリの近くに配置できるように、[Azure Cosmos DB にデータをグローバルに分散する](distribute-data-globally.md)ことは簡単です。

## <a name="increase-provisioned-throughput"></a>プロビジョニングされたスループットの増加

Azure Cosmos DB では、プロビジョニングされたスループットは要求ユニット (RU) で測定されます。 5 RU のスループットを使用するクエリがあると仮定します。 たとえば、1,000 RU をプロビジョニングする場合、そのクエリは 1 秒あたり 200 回実行できます。 スループットが十分でないときにクエリを実行しようとすると、Azure Cosmos DB によって HTTP 429 エラーが返されます。 現在のコア (SQL) API SDK では、短時間待機した後に、このクエリを自動的に再試行します。 スロットルされた要求にはさらに時間がかかるため、プロビジョニングされたスループットを増やすとクエリの待機時間が改善します。 Azure portal のメトリック ブレードで、[スロットルされた要求の合計数](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)を確認できます。

## <a name="increase-maxconcurrency"></a>MaxConcurrency の増加

並列クエリは、複数のパーティションに並列にクエリを実行することによって機能します。 ただし、個々のパーティション分割されたコレクションからのデータは、クエリごとに順番に取得されます。 そのため、MaxConcurrency をパーティションの数に調整すると、その他のすべてのシステムの条件が変わらなければ、クエリのパフォーマンスを最大にできる可能性が最大になります。 パーティションの数が不明な場合は、MaxConcurrency (古いバージョンでは MaxDegreeOfParallelism) を大きな数に設定すると、システムが並列処理の最大限度として最小値 (パーティションの数、ユーザー指定の入力) を選びます。

## <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount の増加

クエリは、結果の現在のバッチがクライアントによって処理されている間に結果をプリフェッチするように設計されています。 プリフェッチは、クエリの全体的な遅延の削減に役立ちます。 MaxBufferedItemCount を設定すると、プリフェッチされる結果の数が制限されます。 この値を、返される結果の予期される数 (またはそれ以上の数) に設定すると、クエリに対するプリフェッチの効果が最大になります。 この値を -1 に設定すると、バッファーする項目の数をシステムが自動的に決定します。

## <a name="next-steps"></a>次のステップ
次のドキュメントを参照して、クエリあたりの RU 数を測定する方法を確認し、実行の統計を取得してクエリの調整などを行います。

* [.NET SDK を使用して SQL クエリの実行メトリックを取得する](profile-sql-api-query.md)
* [Azure Cosmos DB を使用したクエリ パフォーマンスのチューニング](sql-api-sql-query-metrics.md)
* [.NET SDK のパフォーマンスに関するヒント](performance-tips.md)
