---
title: Azure Cosmos DB 用 Kafka Connect - シンク コネクタ
description: Azure Cosmos DB シンク コネクタを使用すると、Apache Kafka トピックから Azure Cosmos DB データベースにデータをエクスポートすることができます。 コネクタにより、Kafka からのデータがポーリングされ、トピックのサブスクリプションに基づいてデータベース内のコンテナーに書き込まれます。
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: aa4b870a76d9d346116a48633bf3baddc7c44ef4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056735"
---
# <a name="kafka-connect-for-azure-cosmos-db---sink-connector"></a>Azure Cosmos DB 用 Kafka Connect - シンク コネクタ
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB 用 Kafka Connect は、Azure Cosmos DB に対してデータの読み取りと書き込みを行うコネクタです。 Azure Cosmos DB シンク コネクタを使用すると、Apache Kafka トピックから Azure Cosmos DB データベースにデータをエクスポートすることができます。 コネクタにより、Kafka からのデータがポーリングされ、トピックのサブスクリプションに基づいてデータベース内のコンテナーに書き込まれます。

## <a name="prerequisites"></a>前提条件

* 最初に、[Confluent Platform のセットアップ](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)を行います。これにより、使用する完全な環境が提供されるからです。 Confluent Platform を使用しない場合は、Zookeeper、Apache Kafka、Kafka Connect を自分でインストールして構成する必要があります。 Azure Cosmos DB コネクタを手動でインストールして構成する必要もあります。
* Azure Cosmos DB アカウント、コンテナーを作成します ([セットアップ ガイド](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md))。
* GitHub Codespaces、Mac、Ubuntu、WSL2 搭載の Windows 上でテストされた Bash シェル。 このシェルは、Cloud Shell または WSL1 では機能しません。
* [Java 11+](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) をダウンロードします。
* [Maven](https://maven.apache.org/download.cgi) をダウンロードします。

## <a name="install-sink-connector"></a>シンク コネクタをインストールする

推奨されている [Confluent Platform のセットアップ](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)を使用している場合は、インストールに Azure Cosmos DB シンク コネクタが含まれているため、この手順をスキップできます。  

それ以外の場合は、最新の[リリース](https://github.com/microsoft/kafka-connect-cosmosdb/releases)から JAR ファイルをダウンロードするか、このリポジトリをパッケージ化して新しい JAR ファイルを作成できます。 JAR ファイルを使用して手動でコネクタをインストールするには、こちらの[手順](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually)を参照してください。 ソース コードから新しい JAR ファイルをパッケージ化することもできます。

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Kafka Connect installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic-and-write-data"></a>Kafka トピックの作成とデータの書き込み

Confluent Platform を使用している場合、Kafka トピックを作成する最も簡単な方法は、付属の Control Center UX を使用することです。 それ以外の場合は、次の構文を使用して手動で Kafka トピックを作成できます。

```bash
./kafka-topics.sh --create --zookeeper <ZOOKEEPER_URL:PORT> --replication-factor <NO_OF_REPLICATIONS> --partitions <NO_OF_PARTITIONS> --topic <TOPIC_NAME>
```

このシナリオでは、"hotels" という名前の Kafka トピックを作成し、スキーマが埋め込まれていない JSON データをそのトピックに書き込みます。 Control Center 内でトピックを作成するには、[Confluent のガイド](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics)を参照してください。

次に、Kafka コンソール プロデューサーを起動し、"hotels" トピックにいくつかのレコードを書き込みます。

```powershell
# Option 1: If using Codespaces, use the built-in CLI utility
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 2: Using this repo's Confluent Platform setup, first exec into the broker container
docker exec -it broker /bin/bash
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 3: Using your Confluent Platform setup and CLI install
<path-to-confluent>/bin/kafka-console-producer --broker-list <kafka broker hostname> --topic hotels
```

コンソール プロデューサーで、次のように入力します。

```json
{"id": "h1", "HotelName": "Marriott", "Description": "Marriott description"}
{"id": "h2", "HotelName": "HolidayInn", "Description": "HolidayInn description"}
{"id": "h3", "HotelName": "Motel8", "Description": "Motel8 description"}
```

入力された 3 つのレコードは、JSON 形式で Kafka トピック "hotels" に発行されます。

## <a name="create-the-sink-connector"></a>シンク コネクタを作成する

Kafka Connect で Azure Cosmos DB シンク コネクタを作成します。 次の JSON 本文には、シンク コネクタの構成が定義されています。 `connect.cosmos.connection.endpoint` と `connect.cosmos.master.key` (Azure Cosmos DB 設定ガイドの前提条件で保存したプロパティ) の値は必ず置き換えてください。

これらの各構成プロパティの詳細については、[シンクのプロパティ](#sink-configuration-properties)に関するセクションを参照してください。

```json
{
  "name": "cosmosdb-sink-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector",
    "tasks.max": "1",
    "topics": [
      "hotels"
    ],
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "hotels#kafka"
  }
}
```

すべての値を入力したら、JSON ファイルをローカルの任意の場所に保存します。 このファイルを使用すると、REST API を使用してコネクタを作成できます。

### <a name="create-connector-using-control-center"></a>Control Center を使用してコネクタを作成する

コネクタを作成する簡単なオプションは、Control Center の Web ページを利用することです。 この[インストール ガイド](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data)に従って、Control Center からコネクタを作成してください。 `DatagenConnector` オプションを使用する代わりに、`CosmosDBSinkConnector` タイルを使用します。 シンク コネクタを構成するときは、JSON ファイルに入力したときと同様に値を入力します。

または、コネクタ ページで **[Upload connector config file]\(コネクタ構成ファイルのアップロード\)** を使用して、先ほど作成した JSON ファイルをアップロードすることもできます。

:::image type="content" source="./media/kafka-connector-sink/upload-connector-config.png" alt-text="[Upload connector config file]\(コネクタ構成ファイルのアップロード\)":::

### <a name="create-connector-using-rest-api"></a>REST API を使用してコネクタを作成する

Connect REST API を使用してシンク コネクタを作成します。

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors

```

## <a name="confirm-data-written-to-cosmos-db"></a>Cosmos DB に書き込まれたデータを確認する

[Azure portal](https://portal.azure.com/learn.docs.microsoft.com) にサインインし、Azure Cosmos DB アカウントに移動します。 "hotels" トピックの 3 つのレコードが自分のアカウントに作成されていることを確認します。

## <a name="cleanup"></a>クリーンアップ

Control Center からコネクタを削除するには、作成したシンク コネクタに移動し、 **[削除]** アイコンをクリックします。

:::image type="content" source="./media/kafka-connector-sink/delete-connector.png" alt-text="コネクタを削除します。":::

または、Connect REST API を使用して削除します。

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-sink-connector
```

作成した Azure Cosmos DB サービスとそのリソース グループを Azure CLI を使用して削除する方法については、[こちらの手順](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup)を参照してください。

## <a name="sink-configuration-properties"></a><a id="sink-configuration-properties"></a>シンクの構成プロパティ

Cosmos DB Kafka シンク コネクタの構成には、次の設定を使用します。 これらの構成値により、消費される Kafka トピック データ、書き込まれる Azure Cosmos DB コンテナーのデータ、データをシリアル化する形式が決まります。 既定値が指定された構成ファイルの例については、[こちらの構成]( https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/sink.example.json)を参照してください。

| 名前 | 型 | 説明 | 必須/省略可能 |
| :--- | :--- | :--- | :--- |
| トピック | list | 監視する Kafka トピックの一覧。 | 必須 |
| connector.class | string | Azure Cosmos DB シンクのクラス名。 これは `com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector` に設定する必要があります。 | 必須 |
| connect.cosmos.connection.endpoint | uri | Azure Cosmos エンドポイント URI 文字列。 | 必須 |
| connect.cosmos.master.key | string | シンクが接続する Azure Cosmos DB 主キー。 | 必須 |
| connect.cosmos.databasename | string | シンクが書き込む Azure Cosmos データベースの名前。 | 必須 |
| connect.cosmos.containers.topicmap | string | Kafka トピックと Azure Cosmos DB コンテナー間のマッピング。`topic#container,topic2#container2` のように、書式設定には CSV が使用されています。 | 必須 |
| key.converter | string | Kafka トピックに書き込まれたキー データのシリアル化形式。 | 必須 |
| value.converter | string | Kafka トピックに書き込まれた値データのシリアル化形式。 | 必須 |
| key.converter.schemas.enable | string | キー データにスキーマが埋め込まれている場合は "true" に設定します。 | オプション |
| value.converter.schemas.enable | string | キー データにスキーマが埋め込まれている場合は "true" に設定します。 | 省略可能 |
| tasks.max | INT | コネクタ シンク タスクの最大数。 既定値は `1` です | 省略可能 |

データは、常にスキーマなしの JSON として Azure Cosmos DB に書き込まれます。

## <a name="supported-data-types"></a>サポートされるデータ型

Azure Cosmos DB シンク コネクタにより、シンク レコードは次のスキーマの種類をサポートする JSON ドキュメントに変換されます。

| スキーマの種類 | JSON データ型 |
| :--- | :--- |
| Array | Array |
| Boolean | ブール値 |
| Float32 | Number |
| Float64 | Number |
| Int8 | Number |
| Int16 | Number |
| Int32 | Number |
| Int64 | Number|
| マップ | オブジェクト (JSON)|
| String | String<br> Null |
| 構造体 | オブジェクト (JSON) |

シンク コネクタは、次の AVRO 論理型もサポートしています。

| Schema Type | JSON データ型 |
| :--- | :--- |
| 日付 | Number |
| Time | Number |
| Timestamp | Number |

> [!NOTE]
> 現在、Azure Cosmos DB シンク コネクタでは、バイトの逆シリアル化はサポートされていません。

## <a name="single-message-transformssmt"></a>シングル メッセージ変換 (SMT)

シンク コネクタの設定と共に、シングル メッセージ変換 (SMT) の使用を指定して、Kafka Connect プラットフォームを流れるメッセージを変更することができます。 詳細については、[Confluent SMT のドキュメント](https://docs.confluent.io/platform/current/connect/transforms/overview.html)を参照してください。

### <a name="using-the-insertuuid-smt"></a>InsertUUID SMT の使用

InsertUUID SMT を使用すると、項目 ID を自動的に追加することができます。 カスタム `InsertUUID` SMT を使用すると、Azure Cosmos DB に書き込まれる前に、各メッセージに対してランダムな UUID 値を持つ `id` フィールドを挿入することができます。

> [!WARNING]
> この SMT は、メッセージに `id` フィールドが含まれていない場合にのみ使用してください。 そうしないと、`id` の値が上書きされ、データベース内の項目が重複する可能性があります。 UUID をメッセージ ID として使用すると、すばやく簡単に使用できますが、Azure Cosmos DB で使用する[理想的なパーティション キーではありません](https://stackoverflow.com/questions/49031461/would-using-a-substring-of-a-guid-in-cosmosdb-as-partitionkey-be-a-bad-idea)。

### <a name="install-the-smt"></a>SMT をインストールする

`InsertUUID` SMT を使用する前に、この変換を Confluent Platform の設定にインストールする必要があります。 このリポジトリから Confluent Platform の設定を使用している場合、変換は既にインストールに含まれているため、この手順をスキップできます。

また、新しい JAR ファイルを作成するために、[InsertUUID ソース](https://github.com/confluentinc/kafka-connect-insert-uuid)をパッケージ化することもできます。 JAR ファイルを使用して手動でコネクタをインストールするには、こちらの[手順](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually)を参照してください。

```bash
# clone the kafka-connect-insert-uuid repo
https://github.com/confluentinc/kafka-connect-insert-uuid.git
cd kafka-connect-insert-uuid

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*.jar
```

### <a name="configure-the-smt"></a>SMT を構成する

シンク コネクタの構成内で、次のプロパティを追加して `id` を設定します。

```json
"transforms": "insertID",
"transforms.insertID.type": "com.github.cjmatta.kafka.connect.smt.InsertUuid$Value",
"transforms.insertID.uuid.field.name": "id"
```

この SMT の使用に関する詳細については、[InsertUUID リポジトリ](https://github.com/confluentinc/kafka-connect-insert-uuid)のページを参照してください。

### <a name="using-smts-to-configure-time-to-live-ttl"></a>SMT を使用した Time to Live (TTL) の構成

`InsertField` と `Cast` の両方の SMT を使用して、Azure Cosmos DB で作成された各項目に TTL を構成できます。 項目レベルで TTL を有効にする前に、コンテナーで TTL を有効にします。 詳細については、[Time to Live](how-to-time-to-live.md#enable-time-to-live-on-a-container-using-azure-portal) のドキュメントを参照してください。

シンク コネクタの構成内で、次のプロパティを追加すると、秒単位で TTL を設定できます。 次の例では、TTL が 100 秒に設定されています。 メッセージに `TTL` フィールドが既に含まれている場合、`TTL` の値はこれらの SMT によって上書きされます。

```json
"transforms": "insertTTL,castTTLInt",
"transforms.insertTTL.type": "org.apache.kafka.connect.transforms.InsertField$Value",
"transforms.insertTTL.static.field": "ttl",
"transforms.insertTTL.static.value": "100",
"transforms.castTTLInt.type": "org.apache.kafka.connect.transforms.Cast$Value",
"transforms.castTTLInt.spec": "ttl:int32"
```

これらの SMT の使用方法については、[InsertField](https://docs.confluent.io/platform/current/connect/transforms/insertfield.html) と [Cast](https://docs.confluent.io/platform/current/connect/transforms/cast.html) のドキュメントを参照してください。

## <a name="troubleshooting-common-issues"></a>一般的な問題のトラブルシューティング

ここでは、Kafka シンク コネクタを使用するときに直面する可能性のある一般的な問題の解決策について説明します。

### <a name="read-non-json-data-with-jsonconverter"></a>JsonConverter を使用して JSON 以外のデータを読み取る

Kafka のソース トピックに JSON 以外のデータがあり、`JsonConverter` を使用して読み取ろうとすると、次のような例外が表示されます。

```console
org.apache.kafka.connect.errors.DataException: Converting byte[] to Kafka Connect data failed due to serialization error:
…
org.apache.kafka.common.errors.SerializationException: java.io.CharConversionException: Invalid UTF-32 character 0x1cfa7e2 (above 0x0010ffff) at char #1, byte #7)

```

このエラーは、ソース トピックのデータが Avro または CSV 文字列などの別の形式でシリアル化されていることが原因と考えられます。

**解決策**: トピック データが AVRO 形式の場合は、次に示すように、`AvroConverter` を使用するように Kafka Connect シンク コネクタを変更します。

```json
"value.converter": "io.confluent.connect.avro.AvroConverter",
"value.converter.schema.registry.url": "http://schema-registry:8081",
```

### <a name="read-non-avro-data-with-avroconverter"></a>AvroConverter を使用して Avro 以外のデータを読み取る

このシナリオは、Avro コンバーターを使用して、Avro 形式ではないトピックからデータを読み取る場合に適用されます。 これには、Confluent Schema Registry の Avro シリアライザー以外の Avro シリアライザーによって書かれたデータが含まれ、このようなデータには独自のワイヤー形式があります。

```console
org.apache.kafka.connect.errors.DataException: my-topic-name
at io.confluent.connect.avro.AvroConverter.toConnectData(AvroConverter.java:97)
…
org.apache.kafka.common.errors.SerializationException: Error deserializing Avro message for id -1
org.apache.kafka.common.errors.SerializationException: Unknown magic byte!

```

**解決策**: ソース トピックのシリアル化形式を確認します。 次に、正しいコンバーターを使用するように Kafka Connect のシンク コネクタを切り替えるか、アップストリーム形式を Avro に切り替えます。

### <a name="read-a-json-message-without-the-expected-schemapayload-structure"></a>期待されるスキーマおよびペイロード構造を持たない JSON メッセージを読み取る

Kafka Connect では、次のようなペイロードとスキーマの両方を含む JSON メッセージの特殊な構造がサポートされます。

```json
{
  "schema": {
    "type": "struct",
    "fields": [
      {
        "type": "int32",
        "optional": false,
        "field": "userid"
      },
      {
        "type": "string",
        "optional": false,
        "field": "name"
      }
    ]
  },
  "payload": {
    "userid": 123,
    "name": "Sam"
  }
}
```

この構造に含まれていない JSON データを読み取ろうとすると、次のようなエラーが発生します。

```none
org.apache.kafka.connect.errors.DataException: JsonConverter with schemas.enable requires "schema" and "payload" fields and may not contain additional fields. If you are trying to deserialize plain JSON data, set schemas.enable=false in your converter configuration.
```

明確にするために説明すると、前述のようなスキーマとペイロードのフィールドを最上位の要素として含むのは、`schemas.enable=true` に有効な JSON 構造だけです。 エラー メッセージに示されているように、プレーンな JSON データしかない場合は、コネクタの構成を次のように変更する必要があります。

```json
"value.converter": "org.apache.kafka.connect.json.JsonConverter",
"value.converter.schemas.enable": "false",
```

## <a name="limitations"></a>制限事項

* Azure Cosmos DB でのデータベースとコンテナーの自動作成はサポートされていません。 データベースとコンテナーは既に存在し、正しく構成されている必要があります。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB の変更フィードについては、次のドキュメントを参照してください。

* [変更フィードの入門](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
* [変更フィードからの読み取り](read-change-feed.md)