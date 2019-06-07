---
title: Azure portal とさまざまな SDK を使用して大きいパーティション キーを持つ Azure Cosmos コンテナーを作成する
description: Azure portal と各種の SDK を使って大きいパーティション キーを持つコンテナーを Azure Cosmos DB で作成する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 33f871564b7c8435395db6b97122ba6a75800271
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225990"
---
# <a name="create-containers-with-large-partition-key"></a>大きいパーティション キーを持つコンテナーを作成する

Azure Cosmos DB では、データの水平方向のスケーリングを実現するために、ハッシュ ベースのパーティション構成が使われます。 2019 年 5 月 3 日より前に作成されたすべての Azure Cosmos コンテナーでは、パーティション キーの最初の 100 バイトに基づいてハッシュを計算するハッシュ関数が使われています。 複数のパーティション キーで最初の 100 バイトが同じ場合、それらの論理パーティションはサービスによって同じ論理パーティションと見なされます。 これにより、パーティション サイズ クォータが正しくなくなったり、一意のインデックスが複数のパーティション キーに適用されるといった問題が発生する可能性があります。 この問題を解決するため、大きいパーティション キーが導入されました。 Azure Cosmos DB では、値が最大 2 KB の大きいパーティション キーがサポートされるようになっています。 

大きいパーティション キーは、ハッシュ関数の拡張バージョンの機能を使ってサポートされ、最大 2 KB の大きいパーティション キーから一意のハッシュを生成できます。 このハッシュ バージョンは、パーティション キーのサイズに関係なく、パーティション キーのカーディナリティが高いシナリオにも推奨されます。 パーティション キーのカーディナリティは、一意の論理パーティションの数として定義されます (たとえば、コンテナー内の論理パーティションの数が約 30000)。 この記事では、Azure portal とさまざまな SDK を使って、大きいパーティション キーを持つコンテナーを作成する方法について説明します。 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>大きいパーティション キーを作成する (.Net SDK V2)

.Net SDK を使って大きいパーティション キーを持つコンテナーを作成するときは、`PartitionKeyDefinitionVersion.V2` プロパティを指定する必要があります。 次の例では、PartitionKeyDefinition オブジェクト内で Version プロパティを指定し、それを PartitionKeyDefinitionVersion.V2 に設定する方法を示します。

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>大きいパーティション キーを作成する (Azure portal) 

Azure portal を使って新しいコンテナーを作成するときに、大きいパーティション キーを作成するには、 **[My partition key is larger than 100-bytes]\(100 バイトを超えるパーティション キー\)** オプションをオンにします。 既定では、すべての新しいコンテナーで大きいパーティション キーの使用が選択されます。 大きいパーティション キーを使う必要がない場合、または 1.18 より前の SDK バージョンでアプリケーションを実行する場合は、チェック ボックスをオフにします。

![Azure portal を使って大きいパーティション キーを作成する](./media/large-partition-keys/large-partition-key-with-portal.png)


## <a name="supported-sdk-versions"></a>サポートされている SDK バージョン

大きいパーティション キーは、次の最小バージョンの SDK でサポートされています。

|SDK の種類  | 最小バージョン   |
|---------|---------|
|.NET     |    1.18     |
|Java Sync     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | `x-ms-version` 要求ヘッダーを使用する `2017-05-03` より後のバージョン。|

現在、Power BI と Azure Logic Apps 内に大きなパーティション キーがあるコンテナーは使用できません。 これらのアプリケーションから、大きなパーティション キーなしでコンテナーを使用できます。 
 
## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [Azure Cosmos アカウントの使用](account-overview.md)


