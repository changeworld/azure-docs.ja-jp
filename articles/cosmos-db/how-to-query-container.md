---
title: Azure Cosmos DB のコンテナーを照会する
description: Azure Cosmos DB のコンテナーを照会する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 647c4dfd6038a04d4d55516ab816bf7701542075
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040423"
---
# <a name="query-containers-in-azure-cosmos-db"></a>Azure Cosmos DB のコンテナーを照会する

この記事では、Azure Cosmos DB のコンテナー (コレクション、グラフ、テーブル) を照会する方法について説明します。

## <a name="in-partition-query"></a>インパーティション クエリ

コンテナー内のデータを照会し、クエリにパーティション キー フィルターが指定されている場合、Azure Cosmos DB により、フィルターに指定したパーティション キー値に対応するパーティションにクエリが自動ルーティングされます。 たとえば、次のクエリは、パーティション キー値 "XMS-0001"に対応するすべてのドキュメントを保持する DeviceId パーティションにルーティングされます。

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>クロスパーティション クエリ

次のクエリにはパーティション キー (DeviceId) にフィルターがなく、パーティションのインデックスに対してこのクエリが実行されるすべてのパーティションにファン アウトされます。 複数パーティションにまたがってクエリを実行するには、**EnableCrossPartitionQuery** (REST API では x-ms-documentdb-query-enablecrosspartition) を true に設定します。

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB は、コンテナーに対する SQL を使用した集計関数 (COUNT、MIN、MAX、AVG) をサポートしています。 コンテナーに対する集計関数は、SDK バージョン 1.12.0 以降でサポートされます。 クエリには、1 つの集計演算子と、プロジェクション内の 1 つの値を含める必要があります。

## <a name="parallel-cross-partition-query"></a>並列クロスパーティション クエリ

Cosmos DB SDK 1.9.0 以降では、並列クエリ実行オプションがサポートされています。  並列パーティション間クエリを使用すれば、複数のパーティションにまたがるクエリでも、少ない待ち時間で実行できます。 たとえば、次のクエリはパーティション全体で並列に実行されるように構成されています。

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

次のパラメーターを調整することで、並列クエリの実行を管理できます。

- **MaxDegreeOfParallelism**:コンテナーのパーティションに対する同時ネットワーク接続数の上限を設定します。 このプロパティを -1 に設定した場合、並列処理次数は SDK によって管理されます。 MaxDegreeOfParallelism が指定されていないか、0 (既定値) に設定されている場合、コンテナーのパーティションへのネットワーク接続は 1 つのみです。

- **MaxBufferedItemCount**:クエリの待ち時間とクライアント側のメモリ使用率のバランスを調整します。 このオプションを省略するか -1 に設定した場合、並列クエリの実行中にバッファリングされる項目の数は SDK によって管理されます。

コレクションが同じ状態の場合、並列クエリでは順次実行と同じ順序で結果が返されます。 並べ替え演算子 (ORDER BY、TOP、またはその両方) を含むクロスパーティション クエリを実行したときは、Azure Cosmos DB SDK からパーティション全体に並列クエリが発行され、部分的に並べ替えられた結果がクライアント側でマージされて、グローバルに並べ替えられた結果が作成されます。

## <a name="next-steps"></a>次の手順

Cosmos DB でのパーティション分割については、次の記事を参照してください。

- [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
- [Azure Cosmos DB での合成パーティション キー](synthetic-partition-keys.md)
