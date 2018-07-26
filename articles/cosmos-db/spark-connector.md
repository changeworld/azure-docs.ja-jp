---
title: Apache Spark を Azure Cosmos DB に接続する | Microsoft Docs
description: このチュートリアルでは、Apache Spark を Azure Cosmos DB に接続して、クラウド向けに設計されたマルチテナント グローバル分散データベース システムで Microsoft から分散集計やデータ サイエンスを実行できる、Azure Cosmos DB 用 Spark コネクタについて説明します。
keywords: Apache Spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: cae66a40882231f7762af29cdeaaf658dd2aa968
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113949"
---
# <a name="accelerate-real-time-big-data-analytics-by-using-the-spark-to-azure-cosmos-db-connector"></a>Spark-Azure Cosmos DB コネクタを使用したリアルタイムのビッグ データ分析の高速化
 
Spark-Azure Cosmos DB コネクタを使用すると、Azure Cosmos DB は Apache Spark ジョブの入力または出力として機能することができます。 [Spark](http://spark.apache.org/) を [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) に接続すると、Azure Cosmos DB を使用してデータをすばやく保持し、クエリを実行できるため、目まぐるしく変化するデータ サイエンスの問題を迅速に解決できます。 この Spark-Cosmos DB コネクタは、Azure Cosmos DB で管理されるネイティブのインデックスを効率的に活用し、 モノのインターネット (IoT)、データ サイエンス、分析などのシナリオにおいて、急速に変化するグローバル分散データに対して、分析の実行時に更新可能な列や、述語によるプッシュダウン フィルターを利用できるようにします。

このビデオでは、Spark-Azure Cosmos DB コネクタについて詳しく説明します。

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>コネクタ コンポーネント

Spark-Azure Cosmos DB コネクタでは、次のコンポーネントを使用します。

* [Azure Cosmos DB](http://documentdb.com): 多数の地理的リージョン間でスループットとストレージをプロビジョニングし、弾力的に拡大縮小できます。  

* [Apache Spark](http://spark.apache.org/): 速度、使いやすさ、高度な分析に重点を置いて構築された強力なオープン ソース処理エンジンです。  

* [Azure Databricks の Apache Spark クラスター](https://docs.azuredatabricks.net/getting-started/index.html): Spark クラスターで Spark ジョブを実行します。

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Apache Spark を Azure Cosmos DB に接続する

Apache Spark と Azure Cosmos DB は次の 2 とおりの方法で接続できます。

1. [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python) を使用する。これは、Python ベースの Spark-Cosmos DB コネクタであり、"pyDocumentDB" とも呼ばれます。  

2. [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java) を使用する。これは、Java ベースの Spark-Cosmos DB コネクタです。


**サポートされているバージョン**

| コンポーネント | Version |
|---------|-------|
|Apache Spark| 2.1.x、2.2.x、2.3.x |
| Scala|2.11|
| Databricks ランタイムのバージョン | 3.4 より新しいバージョン |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Python または pyDocumentDB SDK を使用して接続する

次の図は、pyDocumentDB SDK 実装のアーキテクチャを示しています。

![pyDocumentDB DB を介した Spark と Azure Cosmos DB 間のデータ フロー](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Data flow

pyDocumentDB 実装のデータ フローは次のとおりです。

* Spark のマスター ノードは、pyDocumentDB を介して Azure Cosmos DB ゲートウェイ ノードに接続されます。 ユーザーが指定するのは、Spark と Azure Cosmos DB 接続のみです。 それぞれのマスター ノードとゲートウェイ ノードへの接続は、ユーザーに対して透過的に行われます。  

* ゲートウェイ ノードにより、Azure Cosmos DB に対するクエリが作成されます。その後、このクエリはデータ ノード内のコレクションのパーティションに対して実行されます。 これらのクエリに対する応答がゲートウェイ ノードに送信され、その結果セットが Spark マスター ノードに返されます。  

* 後続のクエリ (Spark データ フレームに対するクエリなど) が Spark worker ノードに送信され、処理されます。  

Spark と Azure Cosmos DB 間の通信が、Spark マスター ノードと Azure Cosmos DB ゲートウェイ ノードに制限されます。 クエリは、この 2 つのノード間のトランスポート層で許可される速度で実行されます。

PyDocumentDB SDK を使用して Spark を Azure Cosmos DB に接続するには、次の手順を行います。

1. [Azure Databricks ワークスペース](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace)を作成します。次に、そのワークスペース内に [Spark クラスター](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Databricks ランタイム バージョン: 4.0 (Apache Spark 2.3.0、Scala 2.11 を含む)) を作成します。  

2. クラスターが作成され、実行されたら、**[ワークスペース]** > **[作成]** > **[ライブラリ]** の順に移動します。  
3. [新しいライブラリ] ダイアログ ボックスで、ソースとして **[Upload Python Egg or PyPi]\(Python Egg または PyPi のアップロード\)** を選び、名前として **pydocumentdb** を指定して **[ライブラリのインストール]** を選択します。 PyDocumentdb SDK は pip パッケージに既に発行されているため、それを見つけてインストールできます。 

   ![ライブラリを作成してアタッチする](./media/spark-connector/create-library.png)

4. ライブラリがインストールされたら、先ほど作成したクラスターにアタッチします。  

5. 次に、**[ワークスペース]** > **[作成]** > **[Notebook]** の順に移動します。  

6. **[ノートブックの作成]** ダイアログ ボックスで、わかりやすい名前を入力し、言語として **[Python]** を選びます。 ドロップダウンから先ほど作成したクラスターを選び、**[作成]** を選択します。  

7. 通信転送がシンプルであるため、pyDocumentDB を使用した Spark から Azure Cosmos DB へのクエリの実行が比較的シンプルになります。 次に、パブリックにアクセス可能な "doctorwho" Cosmos DB アカウントでホストされているフライト サンプル データを使用して、Spark クエリをいくつか実行します。 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub リポジトリでは、HTML バージョンのノートブックがホストされています。 リポジトリ ファイルをダウンロードし、`\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html` に移動する必要があります。そうすれば、ノートブックをご利用の Azure Databricks アカウントにインポートして実行することができます。 次のセクションでは、コード ブロックの機能を詳しく説明します。

次のコード スニペットは、pyDocumentDB SDK をインポートし、Spark コンテキストでクエリを実行する方法を示しています。 コード スニペットに示されているとおり、pyDocumentDB SDK には、Azure Cosmos DB アカウントへの接続に必要な接続パラメーターが含まれています。 必要なライブラリがインポートされ、Azure Cosmos DB クライアント (pydocumentdb.document_client) を作成するようにマスター キーとホストが構成されます。


```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]

# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)

```

次に、クエリを実行できます。以下のコード スニペットでは、DoctorWho アカウント内の airports.codes コレクションに接続して、ワシントン州の空港都市を抽出するクエリを実行します。 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

クエリが実行されると、結果は "query_iterable.QueryIterable" になります。これは、Python リストに変換されてから、Spark データ フレームに変換されます。 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

## <a name="considerations-when-using-pydocumentdb-sdk"></a>pyDocumentDB SDK を使用する場合の考慮事項
次のシナリオでは、PyDocumentDB SDK を使用して Spark を Azure Cosmos DB に接続することをお勧めします。

* Python を使用する場合。  

* Azure Cosmos DB から比較的小規模の結果セットを Spark に返す場合。 Azure Cosmos DB 内の基になるデータセットは非常に大きい可能性があり、Azure Cosmos DB ソースに対してフィルターを適用するか、述語フィルターを実行することに注意してください。

## <a name="connect-by-using-the-java-sdk"></a>Java SDK を使用して接続する

次の図は、Azure Cosmos DB SQL Java SDK 実装のアーキテクチャを示しており、データは Spark worker ノード間で移動します。

![Spark-Azure Cosmos DB コネクタのデータ フロー](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Data flow

Java SDK 実装のデータ フローは次のとおりです。

* Spark マスター ノードは、Azure Cosmos DB ゲートウェイ ノードに接続して、パーティション マップを取得します。 ユーザーが指定するのは、Spark と Azure Cosmos DB 接続のみです。 それぞれのマスター ノードとゲートウェイ ノードへの接続は、ユーザーに対して透過的に行われます。  

* この情報が Spark マスター ノードに返されます。 この時点で、クエリを解析して、アクセスする必要がある Azure Cosmos DB 内のパーティションとそのパーティションの場所を特定できます。  

* この情報が Spark worker ノードに送信されます。  

* Spark worker ノードは Azure Cosmos DB のパーティションに直接接続してデータを抽出し、そのデータを worker ノードの Spark パーティションに返します。  

データ移動は Spark worker ノードと Azure Cosmos DB データ ノード (パーティション) の間で行われるため、Spark と Azure Cosmos DB の間の通信が大幅に高速化されます。 このドキュメントでは、サンプルの twitter データをいくつか Azure Cosmos DB アカウントに送信し、そのデータを使用して Spark クエリを実行します。 サンプルの Twitter データを Azure Cosmos DB に書き込むには、次の手順を使用します。

1. [Azure Cosmos DB SQL API アカウント](create-sql-api-dotnet.md#create-a-database-account)を作成し、そのアカウントに[コレクションを追加](create-sql-api-dotnet.md#add-a-collection)します。  

2. GitHub から [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) サンプルをダウンロードします。これは、サンプルの Twitter データを Azure Cosmos DB に書き込むために使用されます。  

3. コマンド プロンプトを開き、次のコマンドを実行して Tweepy および pyDocumentdb モジュールをインストールします。

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Twitter フィード サンプルの内容を抽出し、config.py ファイルを開きます。 masterKey、host、databaseId、collectionId、preferredLocations の各値を更新します。  

5. `http://apps.twitter.com/` に移動し、新しいアプリケーションとして Twitter フィード スクリプトを登録します。 ご利用のアプリに対する名前とアプリケーションを選ぶと、**コンシューマー キー、コンシューマー シークレット、アクセス トークン、アクセス トークン シークレット**が提供されます。 これらの値をコピーし、config.py ファイルで更新してアプリケーションから Twitter にプログラムでアクセスできるようにします。   

6. config.py ファイルを保存します。 コマンド プロンプトを開き、次のコマンドを使用して、python アプリケーションを実行します。

   ```bash
   Python driver.py
   ```

7. ポータルで Azure Cosmos DB コレクションに移動し、twitter データがコレクションに書き込まれていることを確認します。

### <a name="find-and-attach-java-sdk-to-the-spark-cluster"></a>Java SDK を見つけて Spark クラスターにアタッチする

1. [Azure Databricks ワークスペース](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace)を作成します。次に、そのワークスペース内に [Spark クラスター](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Databricks ランタイム バージョン: 4.0 (Apache Spark 2.3.0、Scala 2.11 を含む)) を作成します。  

2. クラスターが作成され、実行されたら、**[ワークスペース]** > **[作成]** > **[ライブラリ]** の順に移動します。  

3. [新しいライブラリ] ダイアログ ボックスで、ソースとして **[Maven Coordinate]\(Maven 座標\)** を選び、座標値 **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0** を指定し、**[ライブラリの作成]** を選択します。 Maven の依存関係が解決され、パッケージはワークスペースに追加されます。 上記の maven 座標形式では、2.3.0 は Spark のバージョン、2.11 は Scala のバージョン、1.2.0 は Azure Cosmos DB コネクタのバージョンを表します。 

4. ライブラリがインストールされたら、先ほど作成したクラスターにアタッチします。 

この記事では、次のシナリオでの Spark コネクタ Java SDK の使用を示します。

* Azure Cosmos DB から twitter データを読み取る  

* Azure Cosmos DB にストリーミングされている twitter データを読み取る  

* Azure Cosmos DB に twitter データを書き込む 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Azure Cosmos DB から twitter データを読み取る
 
このセクションでは、Azure Cosmos DB から Twitter データのバッチを読み取る Spark クエリを実行します。 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub リポジトリでは、HTML バージョンのノートブックがホストされています。 リポジトリ ファイルをダウンロードして、`\samples\Documentation_Samples\Read_Batch_Twitter_Data.html` に移動する必要があります。そうすれば、ノートブックを Azure Databricks アカウントにインポートし、アカウント URI、マスター キー、データベース、コレクションの各名前を更新して実行できます。あるいは、次のようにしてノートブックを作成することができます。

1. Azure Databricks アカウントに移動し、**[ワークスペース]** > **[作成]** > **[Notebook]** の順に選択します。 

2. **[ノートブックの作成]** ダイアログ ボックスで、わかりやすい名前を入力し、言語として **[Python]** を選び、ドロップダウンから先ほど作成したクラスターを選んで、**[作成]** を選択します。  

3. エンドポイント、マスター キー、データベース、コレクションの各値を更新してアカウントに接続し、spark.read.format() コマンドを使用してツイートを読み取ります。

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. キャッシュ データのさまざまなハッシュタグでツイート数を取得するクエリを実行します。 

   ```python
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

Java SDK では、構成マッピングのために次の値がサポートされます。 

|Setting  |説明  |
|---------|---------|
|query_maxdegreeofparallelism  | 並列クエリの実行中にクライアント側で実行される同時実行操作数を設定します。 0 より大きい値に設定すると、同時実行操作の数は割り当てられた値に制限されます。 0 未満に設定すると、同時実行操作の数はシステムによって自動的に設定されます。 コネクタは Executor を使用して各コレクション パーティションをマップするため、この値は読み取り操作には影響しません。        |
|query_maxbuffereditemcount     |    並列クエリの実行中にクライアント側でバッファーに格納できる項目の最大数を設定します。 0 より大きい値に設定すると、バッファーに格納される項目の値は割り当てられた値に制限されます。 0 未満に設定すると、バッファーに格納される項目の数はシステムによって自動的に設定されます。     |
|query_enablescan    |   要求されたパスでインデックスの作成がオプトアウトされたために処理できなかったクエリに対するスキャンを有効にするためのオプションを設定します。       |
|query_disableruperminuteusage  |  通常のプロビジョニング RU (要求ユニット)/秒がすべて使用された場合にクエリを処理するには、RU/分の容量を無効にします。       |
|query_emitverbosetraces   |   調査のためにクエリで詳細トレースを出力できるようにするオプションを設定します。      |
|query_pagesize  |   クエリ要求ごとにクエリ結果ページのサイズを設定します。 スループットを最適化するには、大きいページ サイズを使用し、ラウンド トリップの数を減らしてクエリ結果をフェッチします。      |
|query_custom  |  Azure Cosmos DB からデータをフェッチするときに、既定のクエリをオーバーライドするように Azure Cosmos DB クエリを設定します。 この値を指定すると、プッシュ ダウン述語を使用するクエリの代わりにも利用されることに注意してください。     |

シナリオによっては、パフォーマンスとスループットを最適化するためにさまざまな構成値を使用する必要があります。 現時点では、構成キーの大文字と小文字は区別されておらず、構成値は常に文字列であることに注意してください。

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Azure Cosmos DB にストリーミングされている twitter データを読み取る

このセクションでは、ストリーミング twitter データの変更フィードを読み取る Spark クエリを実行します。 このセクションでクエリを実行する間は、Twitter フィード アプリが稼働しており、データを Azure Cosmos DB にポンプしていることを確認してください。 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub リポジトリでは、HTML バージョンのノートブックがホストされています。 リポジトリ ファイルをダウンロードして、`\samples\Documentation_Samples\Read_Stream_Twitter_Data.html` に移動する必要があります。そうすれば、ノートブックを Azure Databricks アカウントにインポートし、アカウント URI、マスター キー、データベース、コレクションの各名前を更新して実行できます。あるいは、次のようにしてノートブックを作成することができます。

1. Azure Databricks アカウントに移動し、**[ワークスペース]** > **[作成]** > **[Notebook]** の順に選択します。  

2. **[ノートブックの作成]** ダイアログ ボックスで、わかりやすい名前を入力し、言語として **[Scala]** を選び、ドロップダウンから先ほど作成したクラスターを選んで、**[作成]** を選択します。  

3. エンドポイント、マスター キー、データベース、コレクションの各値を更新してアカウントに接続します。

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. spark.readStream.format() コマンドを使用して、ストリームとしての変更フィードの読み取りを開始します。

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. コンソールへのストリーミング クエリを開始します。

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Java SDK では、構成マッピングのために次の値がサポートされます。

|Setting  |説明  |
|---------|---------|
|readchangefeed   |  コレクションのコンテンツが CosmosDB 変更フィードからフェッチされていることを示します。 既定値は false です。       |
|changefeedqueryname |   クエリを特定するカスタム文字列。 コネクタは、さまざまな変更フィード クエリのコレクション継続トークンを個別に追跡します。 readchangefeed が true の場合、これは必須の構成であり、値を空にすることはできません。      |
|changefeedcheckpointlocation  |   ノード障害が発生した場合に、継続トークンを保持するためのローカル ファイル ストレージへのパス。      |
|changefeedstartfromthebeginning  |  変更フィードを先頭から開始する (true) か、現在のポイントから開始する (false) かを設定します。 既定では、現在のポイントから開始されます (false)。       |
|rollingchangefeed  |   変更フィードが最後のクエリからのものである必要があるかどうかを示すブール値。 既定値は false です。これは、変更が最初のコレクションの読み取りからカウントされることを意味します。      |
|changefeedusenexttoken  |   障害シナリオの処理をサポートするためのブール値。 現在の変更フィードのバッチが適切に処理されており、RDD で次の継続トークンを使って、後続の変更バッチを取得する必要があることを示すために使用されます。      |
| InferStreamSchema | ストリーミングの開始時に、ストリーミング データのスキーマをサンプリングする必要があるかどうかを示すブール値。 既定では、この値は true に設定されます。 このパラメーターが true に設定され、データのサンプリング後にストリーミング データのスキーマが変更された場合、新たに追加されたプロパティはストリーミング データ フレームにドロップされます。 <br/><br/> ストリーミング データ フレームがスキーマに依存しないようにするには、このパラメーターを false に設定します。 このモードでは、Azure Cosmos DB 変更フィードから読み取られるドキュメントの本文は、システム プロパティ値とは別に結果のストリーミング データ フレームの "body" プロパティにラップされます。
 |

### <a name="connection-settings"></a>接続の設定

Java SDK では、次の接続設定がサポートされます。

|Setting  |説明  |
|---------|---------|
|connectionmode   |  内部の DocumentClient が Azure Cosmos DB との通信で使用する必要がある接続モードを設定します。 使用できる値は **DirectHttps** (既定値) と **Gateway** です。 DirectHttps 接続モードでは、CosmosDB パーティションに直接要求がルーティングされ、ある程度の待機時間に関する利点が得られます。       |
|connectionmaxpoolsize   |  内部の DocumentClient で使用される接続プール サイズの値を設定します。 既定値は 100 です。       |
|connectionidletimeout  |  アイドル接続のタイムアウト値を秒単位で設定します。 既定値は 60 です。       |
|query_maxretryattemptsonthrottledrequests    |  最大再試行回数を設定します。 この値は、クライアントでのレート制限により要求が失敗した場合に使用されます。 指定されていない場合、再試行回数の既定値は 1000 となります。       |
|query_maxretrywaittimeinseconds   |  最大再試行時間を秒単位で設定します。 既定では、1000 秒となります。       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Azure Cosmos DB に twitter データを書き込む 

このセクションでは、twitter データのバッチを、同じデータベース内の新しいコレクションに書き込む Spark クエリを実行します。 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub リポジトリでは、HTML バージョンのノートブックがホストされています。 リポジトリ ファイルをダウンロードして、`\samples\Documentation_Samples\Write_Batch_Twitter_Data.html` に移動する必要があります。そうすれば、ノートブックを Azure Databricks アカウントにインポートし、アカウント URI、マスター キー、データベース、コレクションの各名前を更新して実行できます。あるいは、次のようにしてノートブックを作成することができます。

1. Azure Databricks アカウントに移動し、**[ワークスペース]** > **[作成]** > **[Notebook]** の順に選択します。  

2. **[ノートブックの作成]** ダイアログ ボックスで、わかりやすい名前を入力し、言語として **[Scala]** を選び、ドロップダウンから先ほど作成したクラスターを選んで、**[作成]** を選択します。  

3. エンドポイント、マスター キー、データベース、コレクションの各値を更新し、twitter データの読み取りと書き込みのためにデータベース コレクションに接続します。

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. キャッシュ データのさまざまなハッシュタグでツイート数を取得するクエリを実行します。 

   ```scala
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

5. ツイート タグの新しいデータ フレームを作成し、新しいコレクションにデータを保存します。 次のコードを実行したら、ポータルに戻り、データがコレクションに書き込まれていることを確認できます。 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

Java SDK では、構成マッピングのために次の値がサポートされます。

|Setting  |説明  |
|---------|---------|
| BulkImport | BulkExecutor ライブラリを使用してデータをインポートする必要があるかどうかを示すブール値。 既定では、この値は true に設定されます。 |
|WritingBatchSize  |   Azure Cosmos DB コレクションにデータを書き込むときに使用するバッチ サイズを示します。 <br/><br/> BulkImport パラメーターが true に設定されている場合、WritingBatchSize パラメーターは、BulkExecutor ライブラリの importAll API への入力として指定されたドキュメントのバッチ サイズを示します。 既定では、この値は 100 K に設定されます。 <br/><br/> BulkImport パラメーターが false に設定されている場合、WritingBatchSize パラメーターは、Azure Cosmos DB コレクションへの書き込み時に使用するバッチ サイズを示します。 コネクタは、createDocument/upsertDocument 要求を非同期に一括で送信します。 クラスター リソースが使用可能である限り、バッチ サイズが大きいほど、実現できるスループットが多くなります。 その一方で、レートと RU の消費を制限するために、より小さい数のバッチ サイズを指定します。 既定では、書き込みバッチ サイズは 500 に設定されます。  |
|Upsert   |  Cosmos DB コレクションへの書き込み時に CreateDocument ではなく、upsertDocument を使用する必要があるかどうかを示すブール値の文字列。   |
| WriteThroughputBudget |  コレクションに割り当てられている合計スループットから一括インジェスト Spark ジョブに割り当てる、RU/秒の数を表す整数文字列。 |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Azure Cosmos DB にストリーミングされている twitter データを書き込む 

このセクションでは、ストリーミング twitter データの変更フィードを、同じデータベース内の新しいコレクションに書き込む Spark クエリを実行します。 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub リポジトリでは、HTML バージョンのノートブックがホストされています。 リポジトリ ファイルをダウンロードして、`\samples\Documentation_Samples\Write_Stream_Twitter_Data.html` に移動する必要があります。そうすれば、ノートブックを Azure Databricks アカウントにインポートし、アカウント URI、マスター キー、データベース、コレクションの各名前を更新して実行できます。あるいは、次のようにしてノートブックを作成することができます。

1. Azure Databricks アカウントに移動し、**[ワークスペース]** > **[作成]** > **[Notebook]** の順に選択します。  

2. **[ノートブックの作成]** ダイアログ ボックスで、わかりやすい名前を入力し、言語として **[Scala]** を選び、ドロップダウンから先ほど作成したクラスターを選んで、**[作成]** を選択します。  

3. エンドポイント、マスター キー、データベース、コレクションの各値を更新し、twitter データの読み取りと書き込みのためにデータベース コレクションに接続します。

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. spark.readStream.format() コマンドを使用して、ストリームとしての変更フィードの読み取りを開始します。
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. 移動先コレクションの構成を定義し、writeStream.format() メソッドを使用して、ストリーミング ジョブを開始します。

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
 ```

Java SDK では、構成マッピングのために次の値がサポートされます。

|Setting  |説明  |
|---------|---------|
|Upsert   |  Cosmos DB コレクションへの書き込み時に CreateDocument ではなく、upsertDocument を使用する必要があるかどうかを示すブール値の文字列。   |
|checkpointlocation  |   ノード障害が発生した場合に、継続トークンを保持するためのローカル ファイル ストレージへのパス。   |
|WritingBatchSize  |  Azure Cosmos DB コレクションにデータを書き込むときに使用するバッチ サイズを示します。 コネクタは、createDocument/upsertDocument 要求を非同期に一括で送信します。 クラスター リソースが使用可能である限り、バッチ サイズが大きいほど、実現できるスループットが多くなります。 その一方で、レートと RU の消費を制限するために、より小さい数のバッチ サイズを指定します。 既定では、書き込みバッチ サイズは 500 に設定されます。  |


## <a name="considerations-when-using-java-sdk"></a>Java SDK を使用する場合の考慮事項

次のシナリオでは、Java SDK を使用して Spark を Azure Cosmos DB に接続することをお勧めします。

* Python や Scala を使用する場合。  

* Apache Spark と Azure Cosmos DB 間で転送するデータの量が多く、Java SDK のパフォーマンスが pyDocumentDB の場合と比べて高い場合。 クエリのパフォーマンスの違いについては、[クエリ テストの実行に関する Wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs) をご覧ください。

## <a name="next-steps"></a>次の手順

Spark-Azure Cosmos DB コネクタをまだダウンロードしていない場合は、そのコネクタを [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub リポジトリからダウンロードし、リポジトリでその他のリソースを調べる。

* [分散集計の例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [サンプル スクリプトと Notebook](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

また、[Apache Spark SQL、DataFrames、データセット ガイド](http://spark.apache.org/docs/latest/sql-programming-guide.html)と [Azure HDInsight 上の Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) に関する記事を確認することもできます。
