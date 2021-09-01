---
title: Azure Cosmos DB 用 Kafka Connect - ソース コネクタ
description: Azure Cosmos DB ソース コネクタを使用すると、Azure Cosmos DB の変更フィードからデータを読み取り、そのデータを Kafka トピックに発行することができます。 Azure Cosmos DB 用 Kafka Connect は、Azure Cosmos DB に対してデータの読み取りと書き込みを行うコネクタです。
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 99879c5fa14cc91e8a39b99ff1a428f31bfde744
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113937"
---
# <a name="kafka-connect-for-azure-cosmos-db---source-connector"></a>Azure Cosmos DB 用 Kafka Connect - ソース コネクタ
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB 用 Kafka Connect は、Azure Cosmos DB に対してデータの読み取りと書き込みを行うコネクタです。 Azure Cosmos DB ソース コネクタを使用すると、Azure Cosmos DB の変更フィードからデータを読み取り、そのデータを Kafka トピックに発行することができます。

## <a name="prerequisites"></a>前提条件

* 最初に、[Confluent Platform のセットアップ](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)を行います。これにより、使用する完全な環境が提供されるからです。 Confluent Platform を使用しない場合は、Zookeeper、Apache Kafka、Kafka Connect を自分でインストールして構成する必要があります。 Azure Cosmos DB コネクタを手動でインストールして構成する必要もあります。
* Azure Cosmos DB アカウント、コンテナーを作成します ([セットアップ ガイド](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md))。
* GitHub Codespaces、Mac、Ubuntu、WSL2 搭載の Windows 上でテストされた Bash シェル。 このシェルは、Cloud Shell または WSL1 では機能しません。
* [Java 11+](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) をダウンロードします。
* [Maven](https://maven.apache.org/download.cgi) をダウンロードします。

## <a name="install-the-source-connector"></a>ソース コネクタのインストール

推奨されている [Confluent Platform のセットアップ](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)を使用している場合は、インストールに Azure Cosmos DB ソース コネクタが含まれているため、この手順をスキップできます。

それ以外の場合は、最新[リリース](https://github.com/microsoft/kafka-connect-cosmosdb/releases)の JAR ファイルを使用してコネクタを手動でインストールできます。 詳細については、こちらの[手順](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually)を参照してください。 ソース コードから新しい JAR ファイルをパッケージ化することもできます。

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic"></a>Kafka トピックの作成

Confluent Control Center を使用して Kafka トピックを作成します。 このシナリオでは、"apparels" という名前の Kafka トピックを作成し、スキーマが埋め込まれていない JSON データをそのトピックに書き込みます。 Control Center 内でトピックを作成する方法については、[Kafka トピックの作成に関するドキュメント](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics)を参照してください。

## <a name="create-the-source-connector"></a>ソース コネクタの作成

### <a name="create-the-source-connector-in-kafka-connect"></a>Kafka Connect でソース コネクタを作成する

kafka Connect で Azure Cosmos DB ソース コネクタを作成するには、次の JSON 構成を使用します。`connect.cosmos.connection.endpoint` および `connect.cosmos.master.key` プロパティのプレースホルダー値を、前提条件で示した Azure Cosmos DB セットアップ ガイドで保存しておいた値に必ず置き換えてください。

```json
{
  "name": "cosmosdb-source-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector",
    "tasks.max": "1",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "connect.cosmos.task.poll.interval": "100",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "apparels#kafka",
    "connect.cosmos.offset.useLatest": false,
    "value.converter.schemas.enable": "false",
    "key.converter.schemas.enable": "false"
  }
}
```

上記の各構成プロパティの詳細については、[ソース プロパティ](#source-configuration-properties)に関するセクションを参照してください。 すべての値を入力したら、JSON ファイルをローカルの任意の場所に保存します。 このファイルを使用すると、REST API を使用してコネクタを作成できます。

#### <a name="create-connector-using-control-center"></a>Control Center を使用してコネクタを作成する

コネクタを作成する簡単な方法の 1 つは、Confluent Control Center ポータルから作成する方法です。 [Confluent セットアップ ガイド](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data)に従って、Control Center からコネクタを作成します。 設定時には、`DatagenConnector` オプションを使用する代わりに、`CosmosDBSourceConnector` タイルを使用します。 ソース コネクタを構成するときは、JSON ファイルに入力したときと同様に値を入力します。

または、コネクタ ページで **[Upload connector config file]\(コネクタ構成ファイルのアップロード\)** を使用して、前のセクションで作成した JSON ファイルをアップロードすることもできます。

:::image type="content" source="./media/kafka-connector-source/upload-connector-config.png" alt-text="[Upload connector config file]\(コネクタ構成ファイルのアップロード\)":::

#### <a name="create-connector-using-rest-api"></a>REST API を使用してコネクタを作成する

Connect REST API を使用して、ソース コネクタを作成します。

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors
```

## <a name="insert-document-into-azure-cosmos-db"></a>Azure Cosmos DB へのドキュメントの挿入

1. [Azure portal](https://portal.azure.com/learn.docs.microsoft.com) にサインインし、Azure Cosmos DB アカウントに移動します。
1. **[Data Explore]\(データ探索\)** タブを開き、 **[データベース]** を選択します。
1. 前の手順で作成した "kafkaconnect" データベースと "kafka" コンテナーを開きます。
1. 新しい JSON ドキュメントを作成するには、[SQL API] ペインで "kafka" コンテナーを展開して **[項目]** を選択し、ツール バーで **[新しい項目]** を選択します。
1. ここで、次の構造のドキュメントをコンテナーに追加します。 次のサンプル JSON ブロックを [項目] タブに貼り付けて、現在の内容を上書きします。

   ``` json
 
   {
     "id": "2",
     "productId": "33218897",
     "category": "Women's Outerwear",
     "manufacturer": "Contoso",
     "description": "Black wool pea-coat",
     "price": "49.99",
     "shipping": {
       "weight": 2,
       "dimensions": {
         "width": 8,
         "height": 11,
         "depth": 3
       }
     }
   }
 
   ```

1. **[保存]** を選択します。
1. 左側のメニューの項目を見て、ドキュメントが保存されていることを確認します。

### <a name="confirm-data-written-to-kafka-topic"></a>Kafka トピックに書き込まれたデータの確認

1. `<http://localhost:9000>` で Kafka トピック UI を開きます。
1. 作成した Kafka トピック "apparels" を選択します。
1. 先ほど Azure Cosmos DB に挿入したドキュメントが Kafka トピックに表示されていることを確認します。

### <a name="cleanup"></a>クリーンアップ

Confluent Control Center からコネクタを削除するには、作成したソース コネクタに移動し、 **[削除]** アイコンを選択します。

:::image type="content" source="./media/kafka-connector-source/delete-source-connector.png" alt-text="Confluent Center からのコネクタの削除":::

または、コネクタの REST API を使用します。

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-source-connector
```

作成した Azure Cosmos DB サービスとそのリソース グループを Azure CLI を使用して削除する方法については、[こちらの手順](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup)を参照してください。

## <a name="source-configuration-properties"></a>ソース構成プロパティ

Kafka ソース コネクタの構成には、次の設定が使用されます。 これらの構成値により、使用される Azure Cosmos DB コンテナー、Kafka トピックの書き込み元のデータ、データをシリアル化する形式が決定されます。 既定値を含む例については、こちらの[構成ファイル](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/source.example.json)を参照してください。

| 名前 | Type | Description | 必須/省略可能 |
| :--- | :--- | :--- | :--- |
| connector.class | String | Azure Cosmos DB ソースのクラス名。 これは `com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector` に設定する必要があります。 | 必須 |
| connect.cosmos.databasename | String | 読み取り元のデータベースの名前。 | 必須 |
| connect.cosmos.master.key | String | Azure Cosmos DB の主キー。 | 必須 |
| connect.cosmos.connection.endpoint | URI | アカウント エンドポイント。 | 必須 |
| connect.cosmos.containers.topicmap | String | トピックからコンテナーへのコンマ区切りのマッピング。 例： topic1#coll1、topic2#coll2 | 必須 |
| connect.cosmos.messagekey.enabled | ブール値 | この値は、Kafka メッセージ キーを設定する必要があるかどうかを表します。 既定値は `true` です | 必須 |
| connect.cosmos.messagekey.field | String | ドキュメントのフィールドの値をメッセージ キーとして使用します。 既定値は `id` です。 | 必須 |
| connect.cosmos.offset.useLatest | ブール値 |  最新のソース オフセットを使用する場合は `true` に設定します。 最も早く記録されたオフセットを使用する場合は `false` に設定します。 既定値は `false` です。 | 必須 |
| connect.cosmos.task.poll.interval | int | 変更フィード コンテナーの変更をポーリングする間隔。 | 必須 |
| key.converter | String | Kafka トピックに書き込まれたキー データのシリアル化形式。 | 必須 |
| value.converter | String | Kafka トピックに書き込まれた値データのシリアル化形式。 | 必須 |
| key.converter.schemas.enable | String | キー データにスキーマが埋め込まれている場合は `true` に設定します。 | 省略可能 |
| value.converter.schemas.enable | String | キー データにスキーマが埋め込まれている場合は `true` に設定します。 | 省略可能 |
| tasks.max | int | コネクタ ソース タスクの最大数。 既定値は `1` です。 | 省略可能 |

## <a name="supported-data-types"></a>サポートされるデータ型

Azure Cosmos DB ソース コネクタは JSON ドキュメントをスキーマに変換し、次の JSON データ型をサポートします。

| JSON データ型 | スキーマの種類 |
| :--- | :--- |
| Array | Array |
| Boolean | ブール値 | 
| Number | Float32<br>Float64<br>Int8<br>Int16<br>Int32<br>Int64|
| [Null] | String |
| オブジェクト (JSON)| 構造体|
| String | String |

## <a name="next-steps"></a>次のステップ

* Azure Cosmos DB 用 Kafka Connect [シンク コネクタ](kafka-connector-sink.md)