---
title: Azure Synapse Link for Azure Cosmos DB でのカスタム パーティション分割 (プレビュー)
description: カスタム パーティション分割を使用すると、分析クエリでフィルターとして一般的に使用されるフィールドで分析ストア データをパーティション分割して、クエリ パフォーマンスを向上させることができます。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: 72bd4c8a2001cc8e6f314f12862d7ed563ac770b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091763"
---
# <a name="custom-partitioning-in-azure-synapse-link-for-azure-cosmos-db-preview"></a>Azure Synapse Link for Azure Cosmos DB でのカスタム パーティション分割 (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

カスタム パーティション分割を使用すると、分析クエリでフィルターとして一般的に使用されるフィールドで分析ストア データをパーティション分割して、クエリ パフォーマンスを向上させることができます。

この記事では、分析ワークロードに不可欠なキーを使用して Azure Cosmos DB 分析ストアでデータをパーティション分割する方法について説明します。 パーティション排除によるクエリ パフォーマンスの向上を活用する方法についても解説されています。 また、多数の更新または削除が含まれるワークロードで、パーティション分割ストアがクエリ パフォーマンスの向上にどのように役立つかについても説明します。

> [!IMPORTANT]
> 現在、カスタム パーティション分割機能は、パブリック プレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

> [!NOTE]
> カスタム パーティション分割を利用するには、Azure Cosmos DB アカウントで Azure Synapse Link が有効になっている必要があります。 現在、カスタム パーティション分割は Azure Synapse Spark 2.0 でのみサポートされています。

## <a name="how-does-it-work"></a>それはどのように機能するのでしょうか。

カスタム パーティション分割では、分析ストアのパーティション キーとして、データセットの 1 つのフィールドまたは複数のフィールドの組み合わせを選択できます。

分析ストアのパーティション分割は、トランザクション ストアのパーティション分割に依存しません。 既定では、分析ストアはパーティション分割されません。 日付、時刻、カテゴリなどのフィールドを使用して分析ストアに頻繁にクエリを実行する場合は、これらのキーに基づくパーティション分割ストアを作成することをお勧めします。

パーティション分割をトリガーするには、Azure Synapse Link を使用して、Azure Synapse Spark ノートブックからパーティション分割ジョブを定期的に実行できます。 都合の良いときにバックグラウンド ジョブとして実行するように、スケジュールを設定できます。

> [!NOTE]
> パーティション分割ストアは、Azure Synapse ワークスペースにリンクされている ADLS Gen2 プライマリ ストレージ アカウントを指します。

:::image type="content" source="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" alt-text="Azure Synapse Link for Azure Cosmos DB のパーティション分割ストアのアーキテクチャ" lightbox="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" border="false":::

パーティション分割ストアには、パーティション分割ジョブを最後に実行したタイムスタンプまでの Azure Cosmos DB 分析データが格納されます。 Synapse Spark のパーティション キー フィルターを使用して分析データに対してクエリを実行すると、Synapse Link は分析ストアの最新データをパーティション分割ストアのデータと自動的にマージします。 これにより、最新の結果を取得できます。 データはクエリの実行前にマージされますが、差分はパーティション分割ストアに書き戻されません。 分析ストアのデータとパーティション分割ストアの間の差分が拡大するにつれて、パーティション分割されたデータのクエリ時間が変わります。 パーティション分割ジョブをより頻繁にトリガーすると、この差分は減少します。 パーティション ジョブを実行するたびに、完全なデータセットではなく、分析ストアの増分変更のみが処理されます。

## <a name="when-to-use"></a>使用する局面

Azure Cosmos DB の分析データに対してクエリを実行する場合、パーティション分割ストアの使用は省略できます。 Synapse Link を使用して、既存の分析ストアと同じデータに対して直接クエリを実行することができます。 次の要件がある場合は、パーティション分割ストアを有効にすることをお勧めします。

* 一部のフィールドでフィルター処理された分析データに対して頻繁にクエリを実行する場合。

* 大量の更新/削除操作が含まれていたり、データが低速で取り込まれる場合。 パーティション分割ストアでは、パーティション キーを使用してクエリを実行するかどうかに関係なく、このような場合にクエリ パフォーマンスが向上します。

上記のワークロードを除き、パーティション キーとは異なるクエリ フィルターを使用してライブ データにクエリを実行する場合、特にパーティション分割ジョブが頻繁に実行されない場合は、分析ストアからこのクエリを直接実行することをお勧めします。

## <a name="benefits"></a>メリット

### <a name="reduced-data-scanning-from-partition-pruning"></a>パーティション排除によるデータ スキャンの削減

一意の各パーティション キーに対応するデータがパーティション分割ストアに併置されているため、クエリ フィルターとしてパーティション キーを使用すると、クエリ実行で基になるデータを取り除いて、必要なデータのみをスキャンできます。 パーティション排除では、限定されたデータをスキャンして、分析クエリのパフォーマンスを向上します。

### <a name="flexibility-to-partition-your-analytical-data"></a>分析データの柔軟なパーティション分割

特定の分析ストア コンテナーに対して複数のパーティション分割戦略を設定できます。このコンテナーでは、別のパーティション キーを使用して分析ストア データをパーティション分割できます。 たとえば、"store_sales" コンテナーに対して "sold_date" をキーとしてパーティション分割し、さらに "item" をキーとしてパーティション分割できます。 この場合、2 つのパーティション分割ジョブを個別に作成する必要があります。これにより、原則的にデータが 2 つの異なるパーティション分割ストアに分割されます。 このパーティション分割戦略は、一部のクエリで "sold_date" をクエリ フィルターとして使用し、他のクエリで "item" をクエリ フィルターとして使用する場合に便利です。

複数のパーティション キーにわたるデータは、同じパーティション分割ストアに含まれます。パーティション キーに基づいてクエリを実行すると、対応するデータを選択できます。

### <a name="query-performance-improvements"></a>クエリ パフォーマンスの向上

パーティションの排除によるクエリ機能の向上に加えて、カスタム パーティション分割では次のワークロードのクエリ パフォーマンスが向上します。

* **更新/削除負荷の高いワークロード** - 分析ストア内で複数バージョンのレコードを追跡し、クエリの実行時に毎回それらを読み込む代わりに、パーティション分割ストアには最新バージョンのデータのみが含まれます。 これにより、更新/削除負荷の高いワークロードで、クエリ パフォーマンスが大幅に向上します。

* **低速のデータ インジェスト ワークロード** - パーティション分割によって分析データが圧縮されるため、低速のデータ インジェストを含むワークロードでは、この圧縮によってクエリ パフォーマンスが向上する可能性があります。

### <a name="transactional-guarantee"></a>トランザクション保証

カスタム パーティション分割では完全なトランザクション保証が確保されることに注意してください。 パーティション分割の実行中は、クエリ パスがブロックされません。 各クエリ実行では、前回成功したパーティション分割から、パーティション分割されたデータを読み取ります。 分析ストアから最新のデータを読み取ることで、パーティション分割ストアの使用時に、常に最新の使用可能なデータがクエリによって返されます。

## <a name="security"></a>セキュリティ

分析ストアに対して[マネージド プライベート エンドポイント](analytical-store-private-endpoints.md)を構成した場合は、パーティション分割ストアのネットワーク分離を確保するために、パーティション分割ストアのマネージド プライベート エンドポイントも追加することをお勧めします。 パーティション分割ストアは、Synapse ワークスペースに関連付けられているプライマリ ストレージ アカウントです。

同様に、[分析ストアでカスタマー マネージド キー](how-to-setup-cmk.md#is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store)を構成した場合は、パーティション分割ストアである Synapse ワークスペース プライマリ ストレージ アカウントでも、それを直接有効にする必要があります。

## <a name="limitations"></a>制限事項

* カスタム パーティション分割は、Azure Synapse Spark でのみ使用できます。 現在、サーバーレス SQL プールではカスタム パーティション分割はサポートされていません。

* 現在、パーティション分割ストアでは、Synapse ワークスペースに関連付けられているプライマリ ストレージ アカウントのみを指定できます。 現時点では、カスタム ストレージ アカウントの選択はサポートされていません。

* MongoDB 用 API では、分析ストアと Synapse Link がサポートされていますが、現時点ではカスタム パーティション分割はサポートされていません。

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

はい。パーティション キーの定義はパーティション分割されたストア パスに含まれるため、異なるパーティション キーは、同じ BasePath から分岐する異なるパスを持つことになります。

ベース パスの形式は、次のように指定できます: /mnt/partitionedstorename/\<Cosmos_DB_account_name\>/\<Cosmos_DB_database_rid\>/\<Cosmos_DB_container_rid\>/partition=partitionkey/

例: /mnt/CosmosDBPartitionedStore/store_sales/…/partition=sold_date/... /mnt/CosmosDBPartitionedStore/store_sales/…/partition=Date/...

## <a name="next-steps"></a>次のステップ

詳しく学習するために、次のドキュメントを参照してください。

* 分析ストア データをパーティション分割するために、[カスタム パーティション分割を構成](configure-custom-partitioning.md)します。
* [Azure Synapse Link for Azure Cosmos DB](synapse-link.md)
* [Azure Cosmos DB 分析ストアの概要](analytical-store-introduction.md)
* [Azure Synapse Link for Azure Cosmos DB の概要](configure-synapse-link.md)
* [Azure Synapse Link for Azure Cosmos DB についてよく寄せられる質問](synapse-link-frequently-asked-questions.yml)
* [Azure Synapse Link for Azure Cosmos DB のユース ケース](synapse-link-use-cases.md)
