---
title: Azure Synapse Link for Azure Cosmos DB を構成して使用する
description: Azure Cosmos DB アカウントの Synapse Link を有効にする方法、分析ストアを有効にしたコンテナーを作成する方法、Synapse ワークスペースに Azure Cosmos データベースを接続する方法、クエリを実行する方法について説明します。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: references_regions, synapse-cosmos-db, devx-track-azurepowershell
ms.openlocfilehash: 6baedff1ef084940b91c40b57572844f4b63de4b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319398"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Azure Synapse Link for Azure Cosmos DB を構成して使用する
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link for Azure Cosmos DB](synapse-link.md) は、クラウド ネイティブのハイブリッド トランザクションと分析処理 (HTAP) の機能です。これを使用すると、Azure Cosmos DB のオペレーショナル データに対してリアルタイムに近い分析を実行できます。 Synapse Link によって、Azure Cosmos DB と Azure Synapse Analytics の間の緊密でシームレスな統合が実現します。

Azure Synapse Link は、Azure Cosmos DB SQL API または Mongo DB アカウント用の Azure Cosmos DB API で使用できます。 Azure Cosmos DB の Azure Synapse Link を使用して分析クエリを実行するには、次の手順に従います。

* [Azure Cosmos DB アカウントの Azure Synapse Link を有効にする](#enable-synapse-link)
* [分析ストアが有効なコンテナーを作成する](#create-analytical-ttl)
* [既存のコンテナーで分析ストアを有効にする](#update-analytical-ttl)
* [省略可能 - コンテナーの分析ストアの TTL を更新する](#update-analytical-ttl)
* [Azure Cosmos データベースを Azure Synapse ワークスペースに接続する](#connect-to-cosmos-database)
* [Azure Synapse Spark プールを使用して分析ストアにクエリを実行する](#query-analytical-store-spark)
* [Azure Synapse サーバーレス SQL プールを使用して分析ストアにクエリを実行する](#query-analytical-store-sql-on-demand)
* [Azure Synapse サーバーレス SQL プールを使用して Power BI のデータを分析して視覚化する](#analyze-with-powerbi)

また、[Azure Cosmos DB の Azure Synapse Link を構成](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/)する方法に関する Learn モジュールも確認できます。

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Azure Cosmos DB アカウントの Azure Synapse Link を有効にする

> [!NOTE]
> Azure Synapse Link でカスタマー マネージド キーを使用する場合は、ご自身のアカウント上で Synapse Link を有効にする前に、お使いの Azure Key Vault アクセス ポリシー内でご自身のアカウントのマネージド ID を構成する必要があります。 詳細については、[Azure Cosmos DB アカウントのマネージド ID を使用して、カスタマー マネージド キーを構成](how-to-setup-cmk.md#using-managed-identity)する方法に関する記事をご覧ください。

> [!NOTE]
> SQL (CORE) API アカウントに対して完全に忠実なスキーマを使用する必要がある場合は、Azure portal を使用して Synapse Link を有効にすることはできません。 このオプションは、ご自身のアカウント内で Synapse Link が有効にされた後は変更できません。これを設定するには、Azure CLI または PowerShell を使用する必要があります。 詳細については、[分析ストアのスキーマ表現に関するドキュメント](analytical-store-introduction.md#schema-representation)をご確認ください。 

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
> Synapse Link を有効にしても、分析ストアは自動的に有効になりません。 Cosmos DB アカウントで Synapse Link を有効にしたら、コンテナーで分析ストアを有効にして Synapse Link の使用を開始します。 

### <a name="command-line-tools"></a>コマンド ライン ツール

Azure CLI または PowerShell を使用して、Cosmos DB SQL API または MongoDB API アカウントで Synapse Link を有効にします。

#### <a name="azure-cli"></a>Azure CLI

**作成** または **更新** の操作で `--enable-analytical-storage true` を使用します。 また、表現スキーマの種類も選択する必要があります。 SQL API アカウントでは、`--analytical-storage-schema-type` を `FullFidelity` または `WellDefined` の値で使用できます。 MongoDB API アカウントの場合は、常に `--analytical-storage-schema-type FullFidelity` を使用します。

* [Synapse Link を有効にした新しい Azure Cosmos DB アカウントを作成する](/cli/azure/cosmosdb#az_cosmosdb_create-optional-parameters)
* [既存の Azure Cosmos DB アカウントを更新して Synapse Link を有効にする](/cli/azure/cosmosdb#az_cosmosdb_update-optional-parameters)

#### <a name="powershell"></a>PowerShell

**作成** または **更新** の操作で `EnableAnalyticalStorage true` を使用します。 また、表現スキーマの種類も選択する必要があります。 SQL API アカウントでは、`--analytical-storage-schema-type` を `FullFidelity` または `WellDefined` の値で使用できます。 MongoDB API アカウントの場合は、常に `-AnalyticalStorageSchemaType FullFidelity` を使用します。

* [Synapse Link を有効にした新しい Azure Cosmos DB アカウントを作成する](/powershell/module/az.cosmosdb/new-azcosmosdbaccount#description)
* [既存の Azure Cosmos DB アカウントを更新して Synapse Link を有効にする](/powershell/module/az.cosmosdb/update-azcosmosdbaccount)


## <a name="create-an-analytical-store-enabled-container"></a><a id="create-analytical-ttl"></a> 分析ストアが有効なコンテナーを作成する

次のいずれかのオプションを使用して、Azure Cosmos DB コンテナーを作成するときに分析ストアを有効にできます。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com/) または [Azure Cosmos DB Explorer](https://cosmos.azure.com/) にサインインします。

1. Azure Cosmos DB アカウントに移動して、 **[データ エクスプローラー]** タブを開きます。

1. **[新しいコンテナー]** を選択し、データベースの名前、コンテナー、パーティション キー、スループットの詳細を入力します。 **[分析ストア]** オプションをオンにします。 分析ストアを有効にすると、`analytical TTL` プロパティが既定値の -1 (無限のリテンション期間) に設定されたコンテナーが作成されます。 この分析ストアでは、レコードのすべての履歴バージョンが保持され、後で変更することができます。

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Azure Cosmos DB コンテナーの分析ストアを有効にする":::

1. このアカウントで以前に Synapse Link を有効にしていない場合は、分析ストアが有効なコンテナーを作成するための前提条件であるため、これを行うように求めるメッセージが表示されます。 プロンプトが表示されたら、 **[Enable Synapse Link]\(Synapse Link を有効にする\)** を選択します。 処理が完了するまでに 1 ～ 5 分かかることがあります。

1. **[OK]** を選択して、分析ストアが有効な Azure Cosmos DB コンテナーを作成します。

1. コンテナーが作成されたら、Data Explorer の [ドキュメント] のすぐ下にある **[設定]** をクリックして分析ストアが有効になっていることを確認し、 **[分析ストアの Time-to-Live]** オプションがオンになっているかどうかを確認します。

### <a name="azure-cosmos-db-sdks"></a>Azure Cosmos DB SDK

分析ストアが有効なコンテナーを作成するには、`analytical TTL` プロパティを必要な値に設定します。 許可される値の一覧については、[分析 TTL でサポートされる値](analytical-store-introduction.md#analytical-ttl)に関する記事を参照してください。

#### <a name="net-sdk"></a>.NET SDK

次のコードでは、.NET SDK を使用して、分析ストアを持つコンテナーを作成します。 `AnalyticalStoreTimeToLiveInSeconds` プロパティを必要な値 (秒) に設定するか、`-1` を使用して無限のリテンション期間にします。 この設定は後で変更できます。

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

#### <a name="java-v4-sdk"></a>Java V4 SDK

次のコードでは、Java V4 SDK を使用して、分析ストアを持つコンテナーを作成します。 `AnalyticalStoreTimeToLiveInSeconds` プロパティを必要な値 (秒) に設定するか、`-1` を使用して無限のリテンション期間にします。 この設定は後で変更できます。


```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

#### <a name="python-v4-sdk"></a>Python V4 SDK

次のコードでは、Python V4 SDK を使用して、分析ストアを持つコンテナーが作成されます。 `analytical_storage_ttl` プロパティを必要な値 (秒) に設定するか、`-1` を使用して無限のリテンション期間にします。 この設定は後で変更できます。

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos as cosmos
import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

# Client
client = cosmos_client.CosmosClient(HOST,  KEY )

# Database client
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled
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
### <a name="command-line-tools"></a>コマンド ライン ツール

分析ストアが有効なコンテナーを作成するには、`analytical TTL` プロパティを必要な値に設定します。 許可される値の一覧については、[分析 TTL でサポートされる値](analytical-store-introduction.md#analytical-ttl)に関する記事を参照してください。

#### <a name="azure-cli"></a>Azure CLI

次のオプションでは、Azure CLI を使用して分析ストアを持つコンテナーが作成されます。 `--analytical-storage-ttl` プロパティを必要な値 (秒) に設定するか、`-1` を使用して無限のリテンション期間にします。 この設定は後で変更できます。

* [Azure Cosmos DB MongoDB コレクションを作成する](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create-examples)
* [Azure Cosmos DB SQL API コンテナーを作成する](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create) 

#### <a name="powershell"></a>PowerShell

次のオプションでは、PowerShell を使用して分析ストアを持つコンテナーが作成されます。 `-AnalyticalStorageTtl` プロパティを必要な値 (秒) に設定するか、`-1` を使用して無限のリテンション期間にします。 この設定は後で変更できます。

* [Azure Cosmos DB MongoDB コレクションを作成する](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection#description)
* [Azure Cosmos DB SQL API コンテナーを作成する](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer)


## <a name="enable-analytical-store-on-an-existing-container"></a><a id="update-analytical-ttl"></a> 既存のコンテナーで分析ストアを有効にする

> [!NOTE]
> 短期的な容量の制約により、既存のコンテナーに対して Synapse Link が有効になるように登録する必要があります。 保留中の要求によっては、この要求の承認に 1 日から 1 週間かかる場合があります。 要求の状態を確認する手順を以下に示します。 問題や質問がある場合は、[cosmosdbsynapselink@microsoft.com](mailto:cosmosdbsynapselink@microsoft.com) にお問い合わせください。 このステップはサブスクリプションごとに 1 回必要であり、すべての新しいデータベース アカウントでもこの機能が有効になります。

> [!NOTE]
> 既存の Azure Cosmos DB SQL API コンテナーで分析ストアを有効にすることができます。 この機能は一般提供されており、運用ワークロードに使用できます。

 既存のコンテナーで Synapse Link を有効にする場合は次の内容に注意してください。

* 分析ストアの自動同期プロセスの同じパフォーマンス分離が初期同期に適用されるため、OLTP ワークロードでパフォーマンスへの影響はありません。

* 分析ストアとのコンテナーの初期同期の合計時間は、データ ボリュームとドキュメントの複雑さによって異なります。 このプロセスには、数秒から数日かかる場合があります。 移行の進行状況を監視するには、Azure portal を使用してください。

* コンテナーのスループットまたはデータベース アカウントは、初期同期時間の合計にも影響します。 この移行では RU/秒は使用されませんが、使用可能な RU/秒の合計はプロセスのパフォーマンスに影響します。 環境の使用可能な RU を一時的に増やして、プロセスを高速化できます。

* Synapse Link が既存のコンテナーが有効になっている間、そのコンテナーの分析ストアにクエリを実行することはできません。 OLTP ワークロードは影響を受けないので、データの読み取りを通常の状態に維持できます。 初期同期の開始後に取り込まれたデータは、通常の分析ストアの自動同期プロセスによって分析ストアにマージされます。

* 現在、既存の MongoDB API コレクションはサポートされていません。 分析ストアを有効にした状態で作成された新しいコレクションにデータを移行する方法もあります。
 
> [!NOTE]
> 現在、コンテナーから分析ストアを無効にすることはできません。 分析ストアの価格の詳細については、[こちら](analytical-store-introduction.md#analytical-store-pricing)を参照してください。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com/) または [Azure Cosmos DB Explorer](https://cosmos.azure.com/) にサインインします。
2. Azure Cosmos DB アカウントに移動し、 **[統合]** セクションの **[Synapse Link]** タブを開きます。 このタブでは、次のことができます。
3. **[登録]** をクリックして、サブスクリプションの承認を要求します。 要求の状態を確認するには、この同じポータル ペインに戻ってください。
4. 承認されると、アカウントのコンテナーの一覧が表示され、分析ストアが有効になっているものを選択できるようになります。
5. 必要に応じて、 **[Power BI]** タブの **[統合]** セクションに移動して、Synapse Link が有効なコンテナーに Power BI ダッシュボードを作成できます。


### <a name="command-line-tools"></a>コマンド ライン ツール

分析ストアが有効なコンテナーを作成するには、`analytical TTL` プロパティを必要な値に設定します。 許可される値の一覧については、[分析 TTL でサポートされる値](analytical-store-introduction.md#analytical-ttl)に関する記事を参照してください。


### <a name="azure-cli"></a>Azure CLI

次のステップを使用して、Azure CLI で既存のコンテナーの分析ストアを有効にします。 `--analytical-storage-ttl` プロパティを必要な値 (秒) に設定するか、`-1` を使用して無限のリテンション期間にします。 この設定は後で変更できます。

* [承認のための登録](/cli/azure/feature/registration)には `az feature registration create --namespace Microsoft.DocumentDB --name AnalyticalStoreMigration` を使用します。 
* [要求の状態の確認](/cli/azure/feature/registration)には `az feature registration show --namespace Microsoft.DocumentDB --name AnalyticalStoreMigration` を使用します。
* [分析 TTL](/cli/azure/cosmosdb/sql/container?view=azure-cli-latest#az_cosmosdb_sql_container_update&preserve-view=true) は要求の承認後に `-1` に更新します。
* 移行の状態は Azure portal で確認してください。

### <a name="powershell"></a>PowerShell

次のステップを使用して、PowerShell で既存のコンテナーの分析ストアを有効にします。 `-AnalyticalStorageTtl` プロパティを必要な値 (秒) に設定するか、`-1` を使用して無限のリテンション期間にします。 この設定は後で変更できます。

* [承認のための登録](/powershell/module/az.resources/register-azproviderfeature)には `Register-AzProviderFeature -ProviderName "Microsoft.DocumentDB" -FeatureName "AnalyticalStoreMigration"` を使用します。
* [要求の状態を確認します](/powershell/module/az.resources/get-azproviderfeature)。
* [分析 TTL](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer) は要求の承認後に `-1` に更新します。
* 移行の状態は Azure portal で確認してください。



## <a name="optional---update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> 省略可能 - 分析ストアの Time to Live を更新する

特定の TTL 値で分析ストアを有効にしてから、別の有効な値に更新できます。 Azure portal、Azure CLI、PowerShell、Cosmos DB SDK のいずれかを使用して値を更新できます。 さまざまな分析 TTL 構成オプションの詳細については、[分析 TTL でサポートされる値](analytical-store-introduction.md#analytical-ttl)に関する記事を参照してください。


### <a name="azure-portal"></a>Azure portal

Azure portal を使用して分析ストアが有効なコンテナーを作成した場合は、それに `-1` の既定の `analytical TTL` が含まれます。 この値を更新するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) または [Azure Cosmos DB Explorer](https://cosmos.azure.com/) にサインインします。
1. Azure Cosmos DB アカウントに移動して、 **[データ エクスプローラー]** タブを開きます。
1. 分析ストアが有効になっている既存のコンテナーを選択します。 それを展開し、次の値を変更します。
   1. **[Scale & Settings]\(スケールと設定\)** ウィンドウを開きます。
   1. **[設定]** で、 **[Analytical Storage Time to Live]\(分析ストレージの有効期限\)** を探します。
   1. **[オン (既定値なし)]** または **[オン]** を選択し、TTL 値を設定します。
   1. **[保存]** をクリックして変更を保存します。


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

Synapse Spark 3 を使用してクエリを実行する方法については、[Spark 3 を使用して Azure Cosmos DB 分析ストアに対してクエリを実行する方法](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md)に関する記事の手順を参照してください。 この記事では、Synapse ジェスチャから分析ストアを操作する方法について、いくつかの例を紹介しています。 これらのジェスチャは、コンテナーを右クリックすると表示されます。 ジェスチャを使用すると、コードをすばやく生成し、ニーズに合わせて調整することができます。 また、1 回のクリックでデータを検出するのにも最適です。

Spark 2 統合の場合は、「[Spark 2 を使用して分析ストアを Azure Cosmos DB クエリ](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md)」に記載されている手順を使用します。

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Azure Synapse Analytics でサーバーレス SQL プールを使用して分析ストアにクエリを実行する

サーバーレス SQL プールを使用すると、Azure Synapse Link で有効になっている Azure Cosmos DB コンテナー内のデータに対してクエリと分析を行うことができます。 トランザクション ワークロードのパフォーマンスに影響を与えることなく、凖リアルタイムでデータを分析できます。 T-SQL インターフェイスを使用して分析ストアおよび統合された接続からさまざまな BI やアドホック クエリ ツールへのデータのクエリを実行するために、使い慣れた T-SQL 構文が用意されています。 詳細については、[サーバーレス SQL プールを使用した分析ストアのクエリ](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)に関する記事を参照してください。

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>サーバーレス SQL プールを使用して Power BI のデータを分析して視覚化する

Synapse Link for Azure Cosmos DB 上にサーバーレス SQL プール データベースおよびビューを作成できます。 後で、Azure Cosmos DB コンテナーのクエリを実行してから、これらのビュー上で Power BI を使用してモデルを構築して、そのクエリを反映させることができます。 トランザクション ワークロードへのパフォーマンスやコストの影響はなく、ETL パイプライン管理の複雑さもありません。 [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) または[インポート](/power-bi/connect-data/service-dataset-modes-understand#import-mode)のいずれかのモードを使用できます。 詳細については、[Synapse Link でサーバーレス SQL プールを使用して Azure Cosmos DB データを分析する](synapse-link-power-bi.md)方法に関する記事を参照してください。

## <a name="configure-custom-partitioning"></a>カスタム パーティション分割を構成する

カスタム パーティション分割を使用すると、分析クエリでフィルターとして一般的に使用されるフィールドで分析ストア データをパーティション分割して、クエリ パフォーマンスを向上させることができます。詳細については、[カスタム パーティション分割の概要](custom-partitioning-analytical-store.md)および[カスタム パーティション分割を構成する方法](configure-custom-partitioning.md)に関するページを参照してください。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

[Azure Resource Manager テンプレート](./manage-with-templates.md#azure-cosmos-account-with-analytical-store)では、SQL API の Synapse Link が有効な Azure Cosmos DB アカウントを作成します。 このテンプレートでは、分析 TTL を有効にして構成されたコンテナーと、手動または自動スケールのスループットを使用するオプションで、1 つのリージョンにコア (SQL) API アカウントを作成します。 このテンプレートをデプロイするには、readme ページで **[Azure に配置する]** をクリックします。

## <a name="getting-started-with-azure-synapse-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a>Azure Synapse Link の使用を開始する - サンプル

Azure Synapse Link の使用を開始する場合、[GitHub](https://aka.ms/cosmosdb-synapselink-samples) にサンプルが用意されています。 これらは、IoT および小売のシナリオでのエンド ツー エンドのソリューションを紹介しています。 また、MongoDB 用の Azure Cosmos DB API に対応するサンプルは、[MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) フォルダーの下にある同じリポジトリにあります。 

## <a name="next-steps"></a>次のステップ

詳細については、次のドキュメントを参照してください。

* [Azure Cosmos DB の Azure Synapse Link を構成](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/)する方法に関する Learn モジュールを確認する。

* [Azure Cosmos DB 分析ストアの概要。](analytical-store-introduction.md)

* [Azure Cosmos DB の Synapse Link に関してよく寄せられる質問。](synapse-link-frequently-asked-questions.yml)

* [Azure Synapse Analytics での Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)。

* [Azure Synapse Analytics のサーバーレス SQL プール ランタイム サポート](../synapse-analytics/sql/on-demand-workspace-overview.md)。
