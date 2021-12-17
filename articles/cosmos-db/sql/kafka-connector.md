---
title: Azure Cosmos DB 用 Kafka Connect を使用してデータの読み取りと書き込みを行う
description: Azure Cosmos DB 用 Kafka Connect は、Azure Cosmos DB に対してデータの読み取りと書き込みを行うコネクタです。 Kafka Connect は、Apache Kafka と他のシステムとの間でスケーラブルに、かつ高い信頼性を持ってデータをストリーム配信するためのツールです
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 02489a961edf3c9044209da64882d0ab60d20bb4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114176"
---
# <a name="kafka-connect-for-azure-cosmos-db"></a>Azure Cosmos DB 用 Kafka Connect
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

[Kafka Connect](http://kafka.apache.org/documentation.html#connect) は、Apache Kafka と他のシステムとの間でスケーラブルに、かつ高い信頼性を持ってデータをストリーム配信するためのツールです。 Kafka Connect を使用すると、大規模なデータセットを Kafka 内外に移動するコネクタを定義できます。 Azure Cosmos DB 用 Kafka Connect は、Azure Cosmos DB に対してデータの読み取りと書き込みを行うコネクタです。

## <a name="source--sink-connectors-semantics"></a>ソースおよびシンク コネクタのセマンティクス

* **ソース コネクタ** - 現在、このコネクタは複数のタスクで 1 回以上、および 1 つのタスクで正確に 1 回のみサポートしています。

* **シンク コネクタ** - このコネクタは、正確に 1 つのセマンティクスを完全にサポートしています。

## <a name="supported-data-formats"></a>サポートされるデータ形式

ソース コネクタとシンク コネクタは、次のデータ形式をサポートするように構成できます。

| Format | 説明 |
| :----------- | :---------- |
| プレーン JSON  | スキーマがアタッチされていない JSON レコード構造。 |
| スキーマのある JSON | 必要な形式にデータを確実に一致させるための明示的なスキーマ情報を含む JSON レコード構造。 |
| AVRO | Apache の Hadoop プロジェクト内で作成された、行指向のリモート プロシージャ コールとデータ シリアル化フレームワーク。 データ型やプロトコルを定義するために JSON を使用し、コンパクトなバイナリ形式にデータをシリアル化します。

形式やシリアル化などのキーと値の設定を、Kafka で個別に構成できます。 このため、キーと値に対してそれぞれ異なるデータ形式で処理できます。 異なるデータ形式に対応するために、`key.converter` と `value.converter` の両方に対してコンバーター構成があります。

## <a name="converter-configuration-examples"></a>コンバーター構成の例

### <a name="plain-json"></a><a id="json-plain"></a>プレーン JSON

接続データにスキーマ レジストリなしの JSON を使用する必要がある場合、Kafka でサポートされている `JsonConverter` を使用します。 次の例は、構成に追加される `JsonConverter` キーと値のプロパティを示しています。

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=false
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=false
  ```

### <a name="json-with-schema"></a><a id="json-with-schema"></a>スキーマのある JSON

プロパティ `key.converter.schemas.enable` と `value.converter.schemas.enable` を true に設定し、キーまたは値が、内部スキーマとデータの両方を含む複合 JSON オブジェクトとして扱われるようにします。 これらのプロパティがない場合、キーまたは値はプレーン JSON として扱われます。 

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=true
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=true
  ```

Kafka に対して結果として表示されるメッセージは次の例のようになります。スキーマとペイロードは JSON の最上位要素になります。

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
      ],
      "optional": false,
      "name": "ksql.users"
    },
    "payload": {
      "userid": 123,
      "name": "user's name"
    }
  }
  ```

> [!NOTE]
> Azure Cosmos DB に書き込まれるメッセージは、スキーマとペイロードで構成されます。 メッセージのサイズに加えて、ペイロードとスキーマで構成されている割合を確認してください。 このスキーマは、Kafka に書き込むすべてのメッセージで繰り返されます。 このようなシナリオでは、JSON スキーマや AVRO などのシリアル化形式を使用することができます。この場合、スキーマは個別に格納され、メッセージにはペイロードのみが保持されます。

### <a name="avro"></a><a id="avro"></a>AVRO

Kafka コネクタは、AVRO データ形式をサポートしています。 AVRO 形式を使用するには、Kafka Connect が AVRO データの処理方法を認識できるように `AvroConverter` を構成します。 Azure Cosmos DB Kafka Connect は、Apache 2.0 のライセンスに基づいて、Confluent によって提供される [AvroConverter](https://www.confluent.io/hub/confluentinc/kafka-connect-avro-converter) を使用してテストされています。 必要に応じて、別のカスタムコンバーターを使用することもできます。

Kafka では、キーと値は別個に取り扱われます。 ワーカー構成で必要な場合、`key.converter` プロパティと `value.converter` プロパティを指定します。 `AvroConverter` を使用する場合は、スキーマ レジストリの URL を提供する追加のコンバータープロパティを追加します。 次の例は、構成に追加される AvroConverter キーと値のプロパティを示しています。

  ```java
  key.converter=io.confluent.connect.avro.AvroConverter
  key.converter.schema.registry.url=http://schema-registry:8081
  value.converter=io.confluent.connect.avro.AvroConverter
  value.converter.schema.registry.url=http://schema-registry:8081
  ```

## <a name="choose-a-conversion-format"></a>変換形式の選択

変換形式を選択する際の考慮事項を次に示します。

* **ソース コネクタ** を構成する場合:

  * Kafka Connect で、Kafka に書き込むメッセージにプレーン JSON が含まれるようにするには、[プレーン JSON](#json-plain) 構成を設定します。

  * Kafka Connect で、Kafka に書き込むメッセージにスキーマが含まれるようにするには、[スキーマのある JSON](#json-with-schema) 構成を設定します。

  * Kafka Connect で、Kafka に書き込むメッセージに AVRO 形式が含まれるようにするには、[AVRO](#avro) 構成を設定します。

* Kafka トピックからの JSON データを **シンクコネクタ** に使用している場合、JSON が Kafka トピックに書き込まれたときにどのようにシリアル化されたかを把握してください。

  * JSON シリアライザーを使用して書き込まれた場合、JSON コンバーター `(org.apache.kafka.connect.json.JsonConverter)` を使用するように Kafka Connect を設定します。

    * JSON データがプレーン文字列として書き込まれた場合、入れ子になったスキーマまたはペイロードがデータに含まれているかを判別します。 含まれている場合、[スキーマのある JSON](#json-with-schema) 構成に設定します。
    * ただし、JSON データを使用している場合に、そのデータにスキーマまたはペイロード構造がないときは、[プレーン JSON](#json-plain) 構成ごとに `schemas.enable=false` を設定して、スキーマを検索 **しない** ように Kafka Connect に伝達する必要があります。

  * AVRO シリアライザーを使用して書き込まれた場合、[AVRO](#avro) 構成ごとに AVRO コンバーター `(io.confluent.connect.avro.AvroConverter)` を使用するように Kafka Connect を設定します。

## <a name="configuration"></a>構成

### <a name="common-configuration-properties"></a>一般的な構成プロパティ

ソース コネクタとシンク コネクタは、次の一般的な構成プロパティを共有します。

| 名前 | Type | 説明 | 必須/省略可能 |
| :--- | :--- | :--- | :--- |
| connect.cosmos.connection.endpoint | uri | Cosmos endpoint URI string | 必須 |
| connect.cosmos.master.key | string | シンクが接続する Azure Cosmos DB 主キー。 | 必須 |
| connect.cosmos.databasename | string | シンクが書き込む Azure Cosmos データベースの名前。 | 必須 |
| connect.cosmos.containers.topicmap | string | Kafka トピックと Azure Cosmos DB コンテナー間のマッピング。 CSV を使用して `topic#container,topic2#container2` のように形式設定されます | 必須 |

シンク コネクタ固有の構成については、[シンク コネクタのドキュメント](kafka-connector-sink.md)を参照してください

ソース コネクタ固有の構成については、[ソース コネクタのドキュメント](kafka-connector-source.md)を参照してください

## <a name="common-configuration-errors"></a>一般的な構成エラー

Kafka Connect でコンバーターの構成を間違うと、エラーが発生する可能性があります。 Kafka に既に格納されているメッセージを逆シリアル化しようとすることになるため、これらのエラーは Kafka Connector シンクで発生します。 シリアル化はソース側で設定されているため、通常、Converter での問題はソースで派生することはありません。

詳細については、[一般的な構成エラー](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/#common-errors)の文章を参照してください。

## <a name="project-setup"></a>プロジェクトの設定

初期セットアップ手順については、[開発者用チュートリアルとプロジェクトのセットアップ](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Developer_Walkthrough.md)に関するドキュメントを参照してください。

## <a name="performance-testing"></a>パフォーマンス テスト

シンク コネクタおよびソース コネクタに対して実行されるパフォーマンス テストの詳細については、[パフォーマンス テストのドキュメント](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Performance_Testing.md)を参照してください。

コネクタのパフォーマンス テスト環境を配置するための正確な手順については、[パフォーマンス環境のセットアップ](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/perf/README.md)を参照してください。

## <a name="resources"></a>リソース

* [Kafka Connect](http://kafka.apache.org/documentation.html#connect)
* [Kafka Connect の詳細情報 – コンバーターとシリアル化について説明されています](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/)

## <a name="next-steps"></a>次のステップ

* Azure Cosmos DB 用 Kafka Connect [ソース コネクタ](kafka-connector-source.md)
* Azure Cosmos DB 用 Kafka Connect [シンク コネクタ](kafka-connector-sink.md)