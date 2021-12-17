---
title: Azure Cosmos DB インデックス作成メトリック
description: Azure Cosmos DB でインデックス作成メトリックを取得して解釈する方法について説明します
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: tisande
ms.openlocfilehash: f10208e83c9c7f23600285444d22ed5b5faf2488
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551978"
---
# <a name="indexing-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB のインデックス作成メトリック
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB には、使用されたインデックス付きパスと推奨されるインデックス付きパスの両方を示すためのインデックス作成メトリックが用意されています。 インデックス作成メトリックを使用すると、特に[インデックス作成ポリシー](../index-policy.md)を変更する方法がわからない場合に、クエリのパフォーマンスを最適化できます。

> [!NOTE]
> インデックス作成メトリックは、.NET SDK (バージョン 3.21.0 以降) と Java SDK (バージョン 4.19.0 以降) でのみサポートされています

## <a name="enable-indexing-metrics"></a>インデックス作成メトリックを有効にする

`PopulateIndexMetrics` プロパティを `true` に設定すると、クエリのインデックス作成メトリックを有効にすることができます。 指定しない場合、`PopulateIndexMetrics` の既定値は `false` です。 クエリのパフォーマンスのトラブルシューティングにのみ、インデックス メトリックを有効にすることをお勧めします。 クエリとインデックス作成ポリシーが同じであれば、インデックス メトリックが変化することはほとんどありません。 代わりに、診断ログを使用してクエリ RU の料金と待機時間を監視することで、コストの高いクエリを特定することをお勧めします。

### <a name="net-sdk-example"></a>.NET SDK の例

```csharp
    string sqlQueryText = "SELECT TOP 10 c.id FROM c WHERE c.Item = 'value1234' AND c.Price > 2";

    QueryDefinition query = new QueryDefinition(sqlQueryText);

    FeedIterator<Item> resultSetIterator = container.GetItemQueryIterator<Item>(
                query, requestOptions: new QueryRequestOptions
        {
            PopulateIndexMetrics = true
        });

    FeedResponse<Item> response = null;

    while (resultSetIterator.HasMoreResults)
        {
          response = await resultSetIterator.ReadNextAsync();
          Console.WriteLine(response.IndexMetrics);
        }
```

### <a name="example-output"></a>出力例

このクエリ例では、使用されているパス `/Item/?` と `/Price/?`、および考えられる複合インデックス `(/Item ASC, /Price ASC)` を確認します。

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /Item/?
    Index Impact Score: High
    ---
    Index Spec: /Price/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /Item ASC, /Price ASC
    Index Impact Score: High
    ---
```

## <a name="utilized-indexed-paths"></a>使用されたインデックス付きパス

使用された単一のインデックスと使用された複合インデックスは、それぞれ、インクルード パスと、クエリで使用された複合インデックスを示しています。 クエリでは、複数のインデックス付きパスと、インクルード パスと複合インデックスの組み合わせを使用できます。 インデックス付きパスが使用されたものとして表示されていない場合、インデックス付きパスを削除しても、クエリのパフォーマンスには影響しません。

使用されたインデックス付きパスの一覧については、クエリでそれらのパスが使用された証拠と考えてください。 新しいインデックス付きのパスによってクエリのパフォーマンスが向上するかどうかわからない場合は、新しいインデックス付きパスを追加し、クエリで使用されているかどうかを確認する必要があります。

## <a name="potential-indexed-paths"></a>考えられるインデックス付きパス

考えられる単一のインデックスと使用された複合インデックスは、それぞれ、インクルード パスと、追加されていれば、クエリで使用された可能性のある複合インデックスを示しています。 考えられるインデックス付きパスがある場合は、それらをインデックス作成ポリシーに追加して、クエリのパフォーマンスが向上するかどうかを確認する必要があります。

考えられるインデックス付きパスの一覧は、クエリで特定のインデックス付きパスが使用されることの明確な証拠ではなく、推奨されるものと考えてください。 考えられるインデックス付きパスは、クエリで使用されるインデックス付きパスの網羅的な一覧ではありません。 また、考えられるインデックス付きパスの中には、クエリのパフォーマンスに影響しないものもあります。 [推奨されるインデックス付きパスを追加](how-to-manage-indexing-policy.md)し、クエリのパフォーマンスが向上することを確認します。

> [!NOTE]
> インデックス作成メトリックについてフィードバックをお寄せください。 ご意見をお待ちしています。 ぜひ、Azure Cosmos DB エンジニアリング チーム (cosmosdbindexing@microsoft.com) まで直接ご意見をお寄せください。

## <a name="index-impact-score"></a>インデックスの影響スコア

インデックスの影響スコアとは、クエリ シェイプに基づいて、インデックス付きパスがクエリのパフォーマンスに大きな影響を与える見込みのことです。 言い換えれば、インデックスの影響スコアは、その特定のインデックス付きパスを使用しなければ、クエリ RU の料金が大幅に高くなっていた可能性を示すものです。 

インデックスの影響スコアは、**高** と **低** の 2 つがあります。 考えられるインデックス付きパスが複数ある場合は、**高** の影響スコアを持つインデックス付きパスに注目することをお勧めします。

インデックスの影響スコアで使用される基準は、クエリ シェイプだけです。 たとえば、次のクエリでは、インデックス付きパス `/name/?` に **高** のインデックスの影響スコアが割り当てられます。

```sql
SELECT * 
FROM c
WHERE c.name = "Samer"
```

実際の影響は、データの性質によって異なります。 `/name` フィルターに一致する項目が少数の場合、インデックス付きのパスによってクエリ RU の料金が大幅に向上します。 ただし、ほとんどの項目が `/name` フィルターに一致してしまう場合、インデックス付きのパスではクエリのパフォーマンスが向上しない可能性があります。 いずれの場合も、インデックス付きパス `/name/?` には **高** のインデックスの影響スコアが割り当てられます。これは、クエリ シェイプに基づいて、インデックス付きパスがクエリのパフォーマンスを向上させる可能性が高いためです。

## <a name="additional-examples"></a>追加の例

### <a name="example-query"></a>クエリの例

```sql
SELECT c.id 
FROM c 
WHERE c.name = 'Tim' AND c.age > 15 AND c.town = 'Redmond' AND c.timestamp > 2349230183
```

### <a name="index-metrics"></a>インデックス メトリック

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /name/?
    Index Impact Score: High
    ---
    Index Spec: /age/?
    Index Impact Score: High
    ---
    Index Spec: /town/?
    Index Impact Score: High
    ---
    Index Spec: /timestamp/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /name ASC, /town ASC, /age ASC
    Index Impact Score: High
    ---
    Index Spec: /name ASC, /town ASC, /timestamp ASC
    Index Impact Score: High
    ---
```
これらのインデックス メトリックは、クエリがインデックス付きパス `/name/?`、`/age/?`、`/town/?`、`/timestamp/?` を使用したことを示しています。 また、インデックス メトリックは、複合インデックス `(/name ASC, /town ASC, /age ASC)`、`(/name ASC, /town ASC, /timestamp ASC)` を追加するとパフォーマンスがさらに向上する可能性が高いことも示しています。

## <a name="next-steps"></a>次のステップ

以下の記事で、インデックス作成についての詳細を参照してください。

- [インデックス作成の概要](../index-overview.md)
- [インデックス作成ポリシーを管理する方法](how-to-manage-indexing-policy.md)
