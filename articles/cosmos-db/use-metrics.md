---
title: Azure Cosmos DB の分析情報を使用した監視とデバッグ
description: Azure Cosmos DB のメトリックを使用して、一般的な問題をデバッグし、データベースを監視します。
ms.author: esarroyo
author: StefArroyo
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 11/08/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ba9fac9fef3e418cc48bc3185ca91af89dd7946f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057064"
---
# <a name="monitor-and-debug-with-insights-in-azure-cosmos-db"></a>Azure Cosmos DB の分析情報を使用した監視とデバッグ
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB には、スループット、ストレージ、整合性、可用性、および待機時間の分析情報が用意されています。 Azure portal では、これらのメトリックの集計ビューが提供されます。 Azure Monitor API から Azure Cosmos DB メトリックを表示することもできます。 メトリックのディメンション値 (コンテナー名など) は、大文字と小文字が区別されません。 そのため、これらのディメンション値に対して文字列比較を行う場合は、大文字と小文字を区別しない比較を使用する必要があります。 Azure Monitor からメトリックを表示する方法の詳細については、[Azure Monitor からのメトリックの取得](./monitor-cosmos-db.md)に関する記事を参照してください。

この記事では、一般的なユース ケースと、Azure Cosmos DB 分析情報を使用してこれらの問題を分析およびデバッグする手順について説明します。 既定では、メトリックの分析情報は 5 分ごとに収集され、7 日間保持されます。

## <a name="view-insights-from-azure-portal"></a>Azure portal から分析情報を表示する

1. [Azure portal](https://portal.azure.com/) にサインインし、Azure Cosmos DB アカウントに移動します。

1. アカウントのメトリックは、 **[メトリック]** ペインまたは **[分析情報]** ペインのいずれかで表示できます。

   * **メトリック:** このペインには、一定の間隔で収集され、ある時点でのシステムのある側面を表す数値メトリックが表示されます。 たとえば、[サーバー側の待機時間メトリック](monitor-server-side-latency.md)、[正規化された要求ユニット使用率メトリック](monitor-normalized-request-units.md)などを表示し、監視することができます。

   * **分析情報:** このペインでは、Azure Cosmos DB 用にカスタマイズされた監視エクスペリエンスを利用できます。 Azure Monitor で収集されたものと同じメトリックとログが使用され、アカウントの集計されたビューが表示されます。

1. **[分析情報]** ペインを開きます。 [分析情報] ペインには、既定でアカウント内のすべてのコンテナーのスループット、要求、ストレージ、可用性、待機時間、システム、アカウント管理のメトリックが表示されます。 分析情報を表示する **[時間範囲]** 、 **[データベース]** 、 **[コンテナー]** を選択することができます。 **[概要]** タブには、選択したデータベースとコンテナーの RU/s 使用量、データ使用量、インデックス使用量、調整された要求数、正規化された RU/s 消費量が表示されます。

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Azure portal での Cosmos DB のパフォーマンス メトリック" lightbox="./media/use-metrics/performance-metrics.png" :::

1. **[分析情報]** ペインから、次のメトリックを入手できます。

   * **スループット** - このタブには、要求ユニットのうち、消費されたもの、またはコンテナーに対してプロビジョニングされたスループットまたはストレージ容量が超過したために失敗した (応答コード 429) ものの合計数が表示されます。

   * **要求** - このタブには、状態コードごと、操作の種類ごとの処理済み要求の合計数と、失敗した要求 (429 応答コード) の数が表示されます。 コンテナーにプロビジョニングされたスループットまたはストレージ容量を超過すると、要求は失敗します。

   * **ストレージ** - このタブには、選択した期間のデータとインデックスの使用量のサイズが表示されます。

   * **可用性** - このタブには、1 時間あたりの要求の合計に対する成功した要求の割合が表示されます。 成功率は、Azure Cosmos DB の SLA によって定義されます。

   * **待機時間** - このタブには、アカウントが動作しているリージョンで Azure Cosmos DB によって観察された読み取りと書き込みの待機時間が表示されます。 Geo レプリケートされたアカウントのリージョン間での待機時間を視覚化することができます。 また、さまざまな操作別のサーバー側の待機時間も確認できます。 このメトリックでは、エンド ツー エンドの要求の待機時間は表されません。

   * **システム** - このタブには、プライマリ パーティションによって処理されているメタデータ要求の数が表示されます。 スロットルされた要求を識別するためにも役立ちます。

   * **アカウント管理** - このタブには、アカウントの作成、削除、キーの更新、ネットワークとレプリケーションの設定など、アカウント管理アクティビティのメトリックが表示されます。

次のセクションで、Azure Cosmos DB のメトリックを使用する一般的なシナリオについて説明します。

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>成功した要求数とエラーになった要求数の把握

まず [Azure portal](https://portal.azure.com) を開き、 **[分析情報]** ブレードに移動します。 このブレードから **[要求]** タブを開くと、状態コードと操作の種類ごとに分けられた合計要求数を示すグラフが表示されます。 HTTP 状態コードの詳細については、「[HTTP Status Codes for Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)」(Azure Cosmos DB の HTTP 状態コード) を参照してください。

最も一般的なエラー状態コードは 429 (レート制限/調整) です。 このエラーは、Azure Cosmos DB への要求がプロビジョニングされたスループットを超えることを意味します。 この問題の最も一般的な解決策は、そのコレクションの [RU をスケール アップ](./set-throughput.md)することです。

:::image type="content" source="media/use-metrics/request-count.png" alt-text="毎分の要求数" lightbox= "media/use-metrics/request-count.png":::

## <a name="determine-the-throughput-consumption-by-a-partition-key-range"></a>パーティション キーの範囲ごとにスループット消費量を判断する

適切なカーディナリティのパーティション キーを持つことは、スケーラブルなアプリケーションのために重要です。 パーティション キーの範囲の ID ごとに分けられたパーティション コンテナーのスループットの分散を判断するには、 **[分析情報]** ペインに移動します。 **[スループット]** タブを開くと、さまざまなパーティション キー範囲にわたる正規化された RU/s 消費量がグラフに表示されます。

:::image type="content" source="media/use-metrics/throughput-consumption-partition-key-range.png" alt-text="パーティション キー範囲 ID ごとの正規化されたスループット消費量" lightbox="media/use-metrics/throughput-consumption-partition-key-range.png":::

このグラフを使用して、ホット パーティションがあるかどうかを特定できます。 スループット分散が不均一の場合、*ホット* パーティションが発生します。また、その結果、要求が調整され、再パーティションが必要になる可能性があります。 分散の偏りの原因となっているパーティション キーを特定した後は、必要に応じて、より分散されたパーティション キーでコンテナーを再パーティションします。 Azure Cosmos DB でのパーティション分割の詳細については、「[Azure Cosmos DB でのパーティション分割とスケーリング](./partitioning-overview.md)」を参照してください。

## <a name="determine-the-data-and-index-usage"></a>データとインデックスの使用量を判断する

データ使用量、インデックス使用量、ドキュメント使用量によってパーティション コンテナーのストレージの分散を判断することが重要です。 インデックス使用量を最小限に抑え、データ使用量を最大化し、クエリを最適化することができます。 このデータを取得するには、 **[分析情報]** ペインに移動し、 **[ストレージ]** タブを開きます。

:::image type="content" source="media/use-metrics/data-index-consumption.png" alt-text="データ、インデックス、ドキュメントの消費量" lightbox="media/use-metrics/data-index-consumption.png" :::

## <a name="compare-data-size-against-index-size"></a>データ サイズとインデックス サイズを比較する

Azure Cosmos DB の合計使用ストレージは、データ サイズとインデックス サイズ両方の組み合わせです。 通常、インデックス サイズは、データ サイズよりもはるかに小さいサイズです。 詳細については、[インデックス サイズ](index-policy.md#index-size)に関する記事を参照してください。 [Azure Portal](https://portal.azure.com) の [メトリック] ブレードの [ストレージ] タブには、データとインデックスに基づくストレージ使用量の内訳が表示されます。

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

インデックス領域を節約するには、[インデックス ポリシー](index-policy.md)を調整します。

## <a name="debug-why-queries-are-running-slow"></a>クエリの実行速度が遅い原因をデバッグする

SQL API SDK の Azure Cosmos DB は、クエリ実行の統計情報を提供します。

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* は、クエリの各コンポーネントが実行にかかった時間について詳細情報を提供します。 クエリの時間が長くなる最も一般的な根本原因はスキャンです。つまり、クエリがインデックスを利用できなかったことを示します。 この問題は、フィルター条件を修正することで解決できます。

## <a name="next-steps"></a>次のステップ

Azure portal で提供されているメトリックを使用して、問題の監視とデバッグを行う方法について説明しました。 データベースのパフォーマンスを改善する方法については、次の記事を参照してください。

* Azure Monitor からメトリックを表示する方法の詳細については、[Azure Monitor からのメトリックの取得](./monitor-cosmos-db.md)に関する記事を参照してください。 
* [Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)
* [Azure Cosmos DB のパフォーマンスに関するヒント](performance-tips.md)