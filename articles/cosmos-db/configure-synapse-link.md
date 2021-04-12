---
title: Azure Synapse Link for Azure Cosmos DB を構成して使用する
description: Azure Cosmos DB アカウントの Synapse Link を有効にする方法、分析ストアを有効にしたコンテナーを作成する方法、Synapse ワークスペースに Azure Cosmos データベースを接続する方法、クエリを実行する方法について説明します。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: references_regions, synapse-cosmos-db
ms.openlocfilehash: 24886ff3e01e9d9b4c01eabc917ced433599c0fa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727129"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Azure Synapse Link for Azure Cosmos DB を構成して使用する
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link for Azure Cosmos DB](synapse-link.md) は、クラウド ネイティブのハイブリッド トランザクションと分析処理 (HTAP) の機能です。これを使用すると、Azure Cosmos DB のオペレーショナル データに対してリアルタイムに近い分析を実行できます。 Synapse Link によって、Azure Cosmos DB と Azure Synapse Analytics の間の緊密でシームレスな統合が実現します。

Azure Synapse Link は、Azure Cosmos DB SQL API コンテナーまたは Mongo DB コレクション用の Azure Cosmos DB API で使用できます。 Azure Cosmos DB の Azure Synapse Link を使用して分析クエリを実行するには、次の手順に従います。

* [Azure Cosmos DB アカウントの Synapse Link を有効にする](#enable-synapse-link)
* [分析ストアが有効な Azure Cosmos DB コンテナーを作成する](#create-analytical-ttl)
* [省略可能 - Azure Cosmos DB コンテナーの分析ストアの TTL を更新する](#update-analytical-ttl)
* [Azure Cosmos DB データベースを Synapse ワークスペースに接続する](#connect-to-cosmos-database)
* [Synapse Spark を使用して分析ストアにクエリを実行する](#query-analytical-store-spark)
* [サーバーレス SQL プールを使用して分析ストアのクエリを実行する](#query-analytical-store-sql-on-demand)
* [サーバーレス SQL プールを使用して Power BI のデータを分析して視覚化する](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Azure Cosmos DB アカウントの Azure Synapse Link を有効にする

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存の Azure Cosmos DB アカウントを選択します。

1. Azure Cosmos DB アカウントに移動して、 **[機能]** ペインを開きます。

1. 機能一覧から **[Synapse Link]** を選択します。

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Synapse Link 機能の検索":::

1. 次に、ご利用のアカウントで Synapse Link を有効にするように求めるメッセージが表示されます。 **[有効化]** を選択します。 処理が完了するまでに 1 ～ 5 分かかることがあります。

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Synapse Link 機能の有効化":::

1. これで、ご利用のアカウントで Synapse Link を使用できるようになりました。 次に、分析ストアが有効なコンテナーを作成して、トランザクション ストアから分析ストアへのオペレーショナル データのレプリケートを自動的に開始する方法について確認します。

> [!NOTE]
> Synapse Link を有効にしても、分析ストアは自動的に有効になりません。 Cosmos DB アカウントで Synapse Link を有効にしたら、コンテナーの作成時に分析ストアを有効にして、分析ストアへの操作データのレプリケートを開始します。 

### <a name="azure-cli"></a>Azure CLI

次のリンク先では、Azure CLI を使用して Synapse Link を有効にする方法を説明しています。

* [Synapse Link を有効にした新しい Azure Cosmos DB アカウントを作成する](/cli/azure/cosmosdb#az_cosmosdb_create-optional-parameters)
* [既存の Azure Cosmos DB アカウントを更新して Synapse Link を有効にする](/cli/azure/cosmosdb#az_cosmosdb_update-optional-parameters)

### <a name="powershell"></a>PowerShell

* [Synapse Link を有効にした新しい Azure Cosmos DB アカウントを作成する](/powershell/module/az.cosmosdb/new-azcosmosdbaccount#description)
* [既存の Azure Cosmos DB アカウントを更新して Synapse Link を有効にする](/powershell/module/az.cosmosdb/update-azcosmosdbaccount)


次のリンク先では、PowerShell を使用して Synapse Link を有効にする方法を説明しています。

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> 分析ストアを使用して Azure Cosmos コンテナーを作成する

コンテナーの作成中に、Azure Cosmos コンテナーで分析ストアを有効にすることができます。 Azure portal を使用するか、Azure Cosmos DB SDK を使用してコンテナー作成時に `analyticalTTL` プロパティを構成することができます。

> [!NOTE]
> 現時点では、分析ストアは **新しい** コンテナーに対して有効にすることができます (新規、既存のどちらのアカウントでも)。 [Azure Cosmos DB 移行ツール](cosmosdb-migrationchoices.md)を使用して、既存のコンテナーから新しいコンテナーにデータを移行できます。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com/) または [Azure Cosmos DB Explorer](https://cosmos.azure.com/) にサインインします。

1. Azure Cosmos DB アカウントに移動して、 **[データ エクスプローラー]** タブを開きます。

1. **[新しいコンテナー]** を選択し、データベースの名前、コンテナー、パーティション キー、スループットの詳細を入力します。 **[分析ストア]** オプションをオンにします。 分析ストアを有効にすると、`AnalyicalTTL` プロパティが既定値の -1 (無限のリテンション期間) に設定されたコンテナーが作成されます。 この分析ストアでは、レコードのすべての履歴バージョンが保持されます。

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Azure Cosmos コンテナーの分析ストアを有効にする":::

1. このアカウントで以前に Synapse Link を有効にしていない場合は、分析ストアが有効なコンテナーを作成するための前提条件であるため、これを行うように求めるメッセージが表示されます。 プロンプトが表示されたら、 **[Enable Synapse Link]\(Synapse Link を有効にする\)** を選択します。 処理が完了するまでに 1 ～ 5 分かかることがあります。

1. **[OK]** を選択して、分析ストアが有効な Azure Cosmos コンテナーを作成します。

1. コンテナーが作成されたら、Data Explorer の [ドキュメント] のすぐ下にある **[設定]** をクリックして分析ストアが有効になっていることを確認し、 **[分析ストアの Time-to-Live]** オプションがオンになっているかどうかを確認します。

### <a name="net-sdk"></a>.NET SDK

次のコードでは、.NET SDK を使用して、分析ストアを持つコンテナーを作成します。 分析 TTL プロパティを必要な値に設定します。 許可される値の一覧については、[分析 TTL でサポートされる値](analytical-store-introduction.md#analytical-ttl)に関する記事を参照してください。

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

次のコードでは、Java V4 SDK を使用して、分析ストアを持つコンテナーを作成します。 `AnalyticalStoreTimeToLiveInSeconds` プロパティを必要な値に設定します。

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

Python 2.7 および Azure Cosmos DB SDK 4.1.0 は最低限必要なバージョンであり、SDK は SQL API とのみ互換性があります。

最初の手順では、バージョン 4.1.0 以上の [Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) を使用していることを確認します。

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
次の手順では、Azure Cosmos DB Python SDK を使用して、分析ストアを持つコンテナーを作成します。

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="azure-cli"></a>Azure CLI

次のリンク先では、Azure CLI を使用して分析ストアを有効にしたコンテナーを作成する方法を説明しています。

* [MongoDB 用 Azure Cosmos DB API](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create-examples)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)

### <a name="powershell"></a>PowerShell

次のリンク先では、PowerShell を使用して分析ストアを有効にしたコンテナーを作成する方法を説明しています。

* [MongoDB 用 Azure Cosmos DB API](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection#description)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)


## <a name="optional---update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> 省略可能 - 分析ストアの Time to Live を更新する

特定の TTL 値で分析ストアを有効にしてから、後で別の有効な値に更新できます。 Azure portal、Azure CLI、PowerShell、Cosmos DB SDK のいずれかを使用して値を更新できます。 さまざまな分析 TTL 構成オプションの詳細については、[分析 TTL でサポートされる値](analytical-store-introduction.md#analytical-ttl)に関する記事を参照してください。


### <a name="azure-portal"></a>Azure portal

Azure portal を使用して分析ストアが有効なコンテナーを作成した場合は、それに -1 の既定の分析 TTL が含まれます。 この値を更新するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) または [Azure Cosmos DB Explorer](https://cosmos.azure.com/) にサインインします。

1. Azure Cosmos DB アカウントに移動して、 **[データ エクスプローラー]** タブを開きます。

1. 分析ストアが有効になっている既存のコンテナーを選択します。 それを展開し、次の値を変更します。

  * **[Scale & Settings]\(スケールと設定\)** ウィンドウを開きます。
  * **[設定]** で、** Analytical Storage Time to Live** を探します。
  * **[オン (既定値なし)]** または **[オン]** を選択し、TTL 値を設定します
  * **[保存]** をクリックして変更を保存します。

### <a name="net-sdk"></a>.NET SDK

次のコードは、.NET SDK を使用して分析ストアの TTL を更新する方法を示しています。

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

次のコードは、Java V4 SDK を使用して分析ストアの TTL を更新する方法を示しています。

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

現在サポートされていません。


### <a name="azure-cli"></a>Azure CLI

次のリンク先では、Azure CLI を使用してコンテナー分析 TTL を更新する方法を説明しています。

* [MongoDB 用 Azure Cosmos DB API](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_update)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_update)

### <a name="powershell"></a>PowerShell

次のリンク先では、PowerShell を使用してコンテナー分析 TTL を更新する方法を説明しています。

* [MongoDB 用 Azure Cosmos DB API](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollection)
* [Azure Cosmos DB SQL API](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer)


## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Synapse ワークスペースに接続する

Azure Synapse Link を使用して Azure Synapse Analytics Studio から Azure Cosmos DB データベースにアクセスする方法については、[Azure Synapse Link に接続する方法](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md)に関する記事の手順を参照してください。

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a>Azure Synapse Analytics 用の Apache Spark を使用して分析ストアのクエリを実行する

Synapse Spark を使用してクエリを実行する方法については、[Azure Cosmos DB 分析ストアに対してクエリを実行する方法](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md)に関する記事の手順を参照してください。 この記事では、Synapse ジェスチャから分析ストアを操作する方法について、いくつかの例を紹介しています。 これらのジェスチャは、コンテナーを右クリックすると表示されます。 ジェスチャを使用すると、コードをすばやく生成し、ニーズに合わせて調整することができます。 また、1 回のクリックでデータを検出するのにも最適です。

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Azure Synapse Analytics でサーバーレス SQL プールを使用して分析ストアにクエリを実行する

サーバーレス SQL プールを使用すると、Azure Synapse Link で有効になっている Azure Cosmos DB コンテナー内のデータに対してクエリと分析を行うことができます。 トランザクション ワークロードのパフォーマンスに影響を与えることなく、凖リアルタイムでデータを分析できます。 T-SQL インターフェイスを使用して分析ストアおよび統合された接続からさまざまな BI やアドホック クエリ ツールへのデータのクエリを実行するために、使い慣れた T-SQL 構文が用意されています。 詳細については、[サーバーレス SQL プールを使用した分析ストアのクエリ](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)に関する記事を参照してください。

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>サーバーレス SQL プールを使用して Power BI のデータを分析して視覚化する

Synapse Link for Azure Cosmos DB 上にサーバーレス SQL プール データベースおよびビューを構築できます。 後で、Azure Cosmos コンテナーのクエリを実行してから、これらのビュー上で Power BI を使用してモデルを構築して、そのクエリを反映させることができます。 詳細については、[Synapse Link でサーバーレス SQL プールを使用して Azure Cosmos DB データを分析する](synapse-link-power-bi.md)方法に関する記事を参照してください。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

[Azure Resource Manager テンプレート](./manage-with-templates.md#azure-cosmos-account-with-analytical-store)では、SQL API の Synapse Link が有効な Azure Cosmos DB アカウントを作成します。 このテンプレートでは、分析 TTL を有効にして構成されたコンテナーと、手動または自動スケールのスループットを使用するオプションで、1 つのリージョンにコア (SQL) API アカウントを作成します。 このテンプレートをデプロイするには、readme ページで **[Azure に配置する]** をクリックします。

## <a name="getting-started-with-azure-synapse-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a>Azure Synapse Link の使用を開始する - サンプル

Azure Synapse Link の使用を開始する場合、[GitHub](https://aka.ms/cosmosdb-synapselink-samples) にサンプルが用意されています。 これらは、IoT および小売のシナリオでのエンド ツー エンドのソリューションを紹介しています。 また、MongoDB 用の Azure Cosmos DB API に対応するサンプルは、[MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) フォルダーの下にある同じリポジトリにあります。 

## <a name="next-steps"></a>次のステップ

詳細については、次のドキュメントを参照してください。

* [Azure Cosmos DB の Azure Synapse Link。](synapse-link.md)

* [Azure Cosmos DB 分析ストアの概要。](analytical-store-introduction.md)

* [Azure Cosmos DB の Synapse Link に関してよく寄せられる質問。](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics での Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)。

* [Azure Synapse Analytics のサーバーレス SQL プール ランタイム サポート](../synapse-analytics/sql/on-demand-workspace-overview.md)。
