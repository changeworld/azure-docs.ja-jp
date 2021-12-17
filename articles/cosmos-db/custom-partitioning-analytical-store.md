---
title: Azure Synapse Link for Azure Cosmos DB でのカスタム パーティション分割 (プレビュー)
description: カスタム パーティション分割を使用すると、分析クエリでフィルターとして一般的に使用されるフィールドで分析ストア データをパーティション分割して、クエリ パフォーマンスを向上させることができます。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7534149aacc76bebdcf591471d08b120b885e625
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426128"
---
# <a name="custom-partitioning-in-azure-synapse-link-for-azure-cosmos-db-preview"></a>Azure Synapse Link for Azure Cosmos DB でのカスタム パーティション分割 (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

カスタム パーティション分割を使うと、分析クエリでフィルターとして一般的に使われるフィールドで分析ストア データをパーティション分割して、クエリ パフォーマンスを向上させることができます。

この記事では、分析ワークロードに不可欠なキーを使用して Azure Cosmos DB 分析ストアでデータをパーティション分割する方法について説明します。 パーティション排除によるクエリ パフォーマンスの向上を活用する方法についても解説されています。 また、多数の更新または削除が含まれるワークロードで、パーティション分割ストアがクエリ パフォーマンスの向上にどのように役立つかについても説明します。

> [!IMPORTANT]
> 現在、カスタム パーティション分割機能は、パブリック プレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

> [!NOTE]
> カスタム パーティション分割を利用するには、Azure Cosmos DB アカウントで [Azure Synapse Link](synapse-link.md) が有効になっている必要があります。 現在、カスタム パーティション分割は Azure Synapse Spark 2.0 でのみサポートされています。

## <a name="how-does-it-work"></a>それはどのように機能するのでしょうか。

分析ストアのパーティション分割は、トランザクション ストアのパーティション分割に依存しません。 既定では、分析ストアはパーティション分割されません。 日付、時刻、カテゴリなどのフィールドを使って分析ストアに頻繁にクエリを実行する場合は、カスタム パーティション分割を利用し、これらのキーに基づくパーティション分割ストアを別に作成します。 分析ストアのパーティション キーとして、データセットの 1 つのフィールドまたは複数のフィールドの組み合わせを選ぶことができます。

Azure Synapse Link を使って、Azure Synapse Spark ノートブックからパーティション分割をトリガーすることができます。 バックグラウンド ジョブとして 1 日に 1、2 回実行するようにスケジュールできますが、必要に応じてより頻繁に実行することもできます。 

> [!NOTE]
> パーティション分割ストアは、Azure Synapse ワークスペースにリンクされている ADLS Gen2 プライマリ ストレージ アカウントを指します。

:::image type="content" source="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" alt-text="Azure Synapse Link for Azure Cosmos DB のパーティション分割ストアのアーキテクチャ" lightbox="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" border="false":::

パーティション分割ストアには、パーティション分割ジョブを最後に実行したタイムスタンプまでの Azure Cosmos DB 分析データが格納されます。 Synapse Spark でパーティション キー フィルターを使って分析データのクエリを実行すると、Synapse Link によって、パーティション分割ストアのデータが分析ストアの最新データと自動的にマージされます。 このようにして、クエリの最新の結果が得られます。 データはクエリの実行前にマージされますが、差分はパーティション分割ストアに書き戻されません。 分析ストアのデータとパーティション分割ストアの間の差分が拡大するにつれて、パーティション分割されたデータのクエリ時間が変わります。 パーティション分割ジョブをより頻繁にトリガーすると、この差分は減少します。 パーティション ジョブを実行するたびに、完全なデータセットではなく、分析ストアの増分変更のみが処理されます。

## <a name="when-to-use"></a>使用する局面

Azure Cosmos DB の分析データに対してクエリを実行する場合、パーティション分割ストアの使用は省略できます。 Synapse Link を使用して、既存の分析ストアと同じデータに対して直接クエリを実行することができます。 次の要件がある場合は、パーティション分割ストアを有効にすることをお勧めします。
* パーティション列として使われる可能性がある一般的な分析クエリ フィルター
* カーディナリティの低いパーティション列
* パーティション列により、データが複数のパーティションに均等に分散される
* 更新または削除操作の量が多い
* データ インジェストが遅い 

上記の要件を満たすワークロードを除き、パーティション キーとは異なるクエリ フィルターを使ってライブ データのクエリを実行する場合は、分析ストアから直接クエリを実行することをお勧めします。 これは、パーティション分割ジョブが頻繁に実行されるスケジュールが設定されていない場合に特に当てはまります。

## <a name="benefits"></a>メリット

### <a name="reduced-data-scanning-from-partition-pruning"></a>パーティション排除によるデータ スキャンの削減

一意の各パーティション キーに対応するデータがパーティション分割ストアに併置されているため、クエリ フィルターとしてパーティション キーを使用すると、クエリ実行で基になるデータを取り除いて、必要なデータのみをスキャンできます。 パーティション排除では、限定されたデータをスキャンして、分析クエリのパフォーマンスを向上します。

### <a name="flexibility-to-partition-your-analytical-data"></a>分析データの柔軟なパーティション分割

特定の分析ストア コンテナーに対して複数のパーティション分割戦略を設定できます。 クエリの要件に基づいて、複合または個別のパーティション キーを使えます。 これについてのガイダンスは、「パーティション分割戦略」を参照してください。 

### <a name="query-performance-improvements"></a>クエリ パフォーマンスの向上

パーティションの排除によるクエリ機能の向上に加えて、カスタム パーティション分割では次のワークロードのクエリ パフォーマンスが向上します。

* **更新/削除負荷の高いワークロード** - 分析ストア内で複数バージョンのレコードを追跡し、クエリの実行時に毎回それらを読み込む代わりに、パーティション分割ストアには最新バージョンのデータのみが含まれます。 これにより、更新/削除負荷の高いワークロードで、クエリ パフォーマンスが大幅に向上します。

* **低速のデータ インジェスト ワークロード** - パーティション分割によって分析データが圧縮されるため、低速のデータ インジェストを含むワークロードでは、この圧縮によってクエリ パフォーマンスが向上する可能性があります。

### <a name="transactional-guarantee"></a>トランザクション保証

カスタム パーティション分割では完全なトランザクション保証が確保されることに注意してください。 パーティション分割の実行中は、クエリ パスがブロックされません。 各クエリ実行では、前回成功したパーティション分割から、パーティション分割されたデータを読み取ります。 分析ストアから最新のデータを読み取ることで、パーティション分割ストアの使用時に、常に最新の使用可能なデータがクエリによって返されます。

## <a name="security"></a>セキュリティ

分析ストアに対して[マネージド プライベート エンドポイント](analytical-store-private-endpoints.md)を構成した場合は、パーティション分割ストアのネットワーク分離を確保するために、パーティション分割ストアのマネージド プライベート エンドポイントも追加することをお勧めします。 パーティション分割ストアは、Synapse ワークスペースに関連付けられているプライマリ ストレージ アカウントです。

同様に、[分析ストアでカスタマー マネージド キー](how-to-setup-cmk.md#is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store)を構成した場合は、パーティション分割ストアである Synapse ワークスペース プライマリ ストレージ アカウントでも、それを直接有効にする必要があります。

## <a name="partitioning-strategies"></a>パーティション分割戦略
分析データには 1 つ以上のパーティション キーを使えます。 複数のパーティション キーを使う場合、データを分割する方法に関する推奨事項を以下に示します。 
   - **複合キーを使う:**

     たとえば、Key1 と Key2 に基づいて頻繁にクエリを実行するとします。 
      
     たとえば、"ReadDate = '2021-10-08' かつ Location = 'Sydney' であるすべてのレコードのクエリを実行する" とします。 
       
     この場合、複合キーを使うと、ReadDate に一致するレコードと、その ReadDate 内の Location に一致するレコードをすべて検索することができるので、より効率的です。 
       
     サンプル構成オプション:      
     ```python
     .option("spark.cosmos.asns.partition.keys", "ReadDate String, Location String") \
     .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
     ```
      
     次は、前述のパーティション分割ストアで、"Location" フィルターのみに基づいてクエリを実行するとします。      
     * 分析ストアに直接クエリを実行したい場合もあります。 パーティション分割ストアにより、まず ReadDate、次に Location の順ですべてのレコードがスキャンされます。 
     そのため、分析データのワークロードとカーディナリティによっては、分析ストアに直接クエリを実行した方が良い結果が得られる場合があります。 
     * また、別のパーティション ジョブを実行して、同じパーティション分割ストアで 'Location' に基づいてパーティション分割することもできます。
                           
  *  **複数のキーを個別に使う:**
     
     たとえば、あるときは 'ReadDate' に基づいて、またあるときは 'Location' に基づいて、頻繁にクエリを実行するとします。 
     
     たとえば、次のように入力します。 
     - ReadDate = '2021-10-08' であるすべてのレコードのクエリを実行する
     - Location = 'Sydney' であるすべてのレコードのクエリを実行する
     
     このシナリオでは、次のように定義されたパーティション キーを持つ 2 つのパーティション ジョブを実行します。      
     
     ジョブ 1:
     ```python
     .option("spark.cosmos.asns.partition.keys", "ReadDate String") \
     .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
     ```                  
     ジョブ 2: 
     ```python
     .option("spark.cosmos.asns.partition.keys", "Location String") \
     .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
     ```        
     前述のパーティション分割では、"ReadDate" と "Location" のフィルターを一緒に使って頻繁にクエリを実行するのは効率的ではないことに注意してください。 このような場合は、複合キーを使うとパフォーマンスが向上します。 
      
## <a name="limitations"></a>制限事項

* カスタム パーティション分割は、Azure Synapse Spark でのみ使用できます。 現在、サーバーレス SQL プールではカスタム パーティション分割はサポートされていません。

* 現在、パーティション分割ストアでは、Synapse ワークスペースに関連付けられているプライマリ ストレージ アカウントのみを指定できます。 現時点では、カスタム ストレージ アカウントの選択はサポートされていません。

* カスタム パーティション分割は Cosmos DB の SQL API にのみ使えます。 Mongo DB、Gremlin、Cassandra 用の API は現時点ではサポートされていません。 

## <a name="pricing"></a>価格

カスタム パーティション分割を使用する場合、[Azure Synapse Link の価格](synapse-link.md#pricing)に加えて、次の料金が発生します。

* 分析ストアでパーティション分割ジョブを実行するときに、Synapse Apache Spark プールの使用に対して[課金](https://azure.microsoft.com/pricing/details/synapse-analytics/#pricing)されます。

* パーティション分割されたデータは、Azure Synapse Analytics ワークスペースに関連付けられているプライマリ Azure Data Lake Storage Gen2 アカウントに格納されます。 ADLS Gen2 のストレージとトランザクションの使用に関連するコストが発生します。 これらのコストはそれぞれ、パーティション分割された分析データに必要なストレージと、Synapse の分析クエリに対して処理されるデータによって決まります。 価格の詳細については、「[Azure Data Lake Storage の価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)」ページを参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-often-should-i-run-the-custom-partitioning-job"></a>カスタム パーティション分割ジョブは、どのくらいの頻度で実行する必要がありますか。

カスタム パーティション分割ジョブを実行する頻度は、増分データの量、クエリ待機時間の要件など、いくつかの要因によって決まります。 1 日 1 回、または数時間ごとに 1 回実行することができます。 受信データ量が多く、予想されるクエリの待機時間が短い場合は、パーティション分割ジョブの頻度を増やしてスケジュール設定することをお勧めします。 また、パーティション排除を有効にするには、まず分析ストアに増分データを蓄積する必要があります。

### <a name="do-the-query-results-include-latest-data-while-the-partitioning-job-execution-is-in-progress"></a>パーティション分割ジョブの実行中、クエリ結果に最新のデータは含まれますか。

はい。カスタム パーティション分割では、完全なトランザクション保証が提供されます。 そのため、任意の時点のクエリ結果では、既存のパーティション分割されたデータと末尾のデータが結合され、最新の分析ストア データセットが返されます。

### <a name="can-the-custom-partitioning-make-use-of-linked-service-authentication-on-azure-synapse-analytics"></a>カスタム パーティション分割では、Azure Synapse Analytics でリンクされたサービスの認証を使用できますか。

はい。リンクされたサービスの認証は、分析ストアのパーティション分割に使用できます。

### <a name="can-i-change-the-partition-key-for-a-given-container-at-a-later-point-in-time"></a>特定のコンテナーのパーティション キーを、後から変更することはできますか。

はい。特定のコンテナーのパーティション キーを変更できます。新しいパーティション キーを定義すると新しいパーティション分割ストアが作成されます。

> [!NOTE]
> パーティション キーの定義は、パーティション分割されたストア パスに含まれます。

### <a name="can-different-partition-keys-point-to-the-same-basepath"></a>複数のパーティション キーで同じ BasePath を指定することはできますか。

はい、次のように、同じパーティション分割ストアに複数のパーティション キーを指定できます。 

```python
.option("spark.cosmos.asns.partition.keys", "ReadDate String, Location String") \
.option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
```

## <a name="next-steps"></a>次のステップ

詳しく学習するために、次のドキュメントを参照してください。

* 分析ストア データをパーティション分割するために、[カスタム パーティション分割を構成](configure-custom-partitioning.md)します。
* [Azure Synapse Link for Azure Cosmos DB](synapse-link.md)
* [Azure Cosmos DB 分析ストアの概要](analytical-store-introduction.md)
* [Azure Synapse Link for Azure Cosmos DB の概要](configure-synapse-link.md)
* [Azure Synapse Link for Azure Cosmos DB についてよく寄せられる質問](synapse-link-frequently-asked-questions.yml)
* [Azure Synapse Link for Azure Cosmos DB のユース ケース](synapse-link-use-cases.md)
