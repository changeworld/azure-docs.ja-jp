---
title: Azure HDInsight での Apache Kafka と Confluent スキーマ レジストリ
description: エッジ ノードを含む HDInsight のマネージド Kafka クラスターを仮想ネットワーク内にデプロイしてから、Confluent スキーマ レジストリをそのエッジ ノードにインストールします。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 30cdc9924d41fdbe27156fcf90688d4baf440487
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209363"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Azure HDInsight での Apache Kafka と Confluent スキーマ レジストリ

Kafka スキーマ レジストリは、Avro 形式で送信される Kafka メッセージのメッセージ スキーマの格納と取得を処理する Apache Kafka クライアントにプラグインされるシリアライザーを提供します。 以前は Confluent による OSS プロジェクトの 1 つでしたが、現在は [Confluent Community License](https://www.confluent.io/blog/license-changes-confluent-platform/) の下にあります。 さらに、このスキーマ レジストリは以下の目的で使用されます。

* プロデューサーとコンシューマーのスキーマを格納および取得します。
* トピックに対して下位、上位、または完全互換性を適用します。
* Kafka に送信されるペイロードのサイズを小さくします。

HDInsight のマネージド Kafka クラスターでは、ヘッド ノードからのコンピューティングの分離が可能になるように、スキーマ レジストリは通常、エッジ ノードにデプロイされます。

スキーマ レジストリを HDInsight クラスターにデプロイする方法の代表的なアーキテクチャを以下に示します。 スキーマ レジストリでは、それに対する操作用の REST API をネイティブに公開します。  プロデューサーとコンシューマーは、VNet 内から、または [Kafka REST プロキシ](rest-proxy.md)を使用して、スキーマ レジストリとやり取りできます。

![HDInsight の Kafka スキーマ レジストリ](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>HDInsight のマネージド Kafka を Confluent スキーマ レジストリと共にデプロイする

このセクションでは、エッジ ノードを含む HDInsight のマネージド Kafka クラスターを仮想ネットワーク内にデプロイしてから、Confluent スキーマ レジストリをそのエッジ ノードにインストールします。  

1. 下の **[Azure へのデプロイ]** ボタンを選択して Azure にサインインし、Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/schema-registry/hdi-deploy-to-azure1.png"/></a>

1. カスタム デプロイ テンプレートで、以下に示すようにフィールドに値を入力します。

    |プロパティ |説明 |
    |---|---|
    |サブスクリプション|ドロップダウン リストから、このクラスターに使用する Azure サブスクリプションを選択します。|
    |Resource group|ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
    |リージョン|クラスターの作成先となるリージョンをドロップダウン リストから選択します。|
    |クラスター名|グローバルに一意の名前を入力します。 あるいは、既定の名前をそのまま使用します。|
    |[Cluster Login User Name]\(クラスター ログイン ユーザー名\)|ユーザー名を指定します。既定値は **admin** です。|
    |[クラスター ログイン パスワード]|パスワードを指定します。|
    |SSH ユーザー名|ユーザー名を指定します。既定値は **sshuser** です。|
    |SSH パスワード|パスワードを指定します。|

    その他のフィールドはそのままにしておきます。 **[Review + create]\(確認と作成\)** を選択して続行します。

1. デプロイの詳細を確認した後、 **[作成]** を選択してデプロイを初期化します。 デプロイが完了するまで 45 分ほどかかる場合があります。

## <a name="configure-the-confluent-schema-registry"></a>Confluent スキーマ レジストリを構成する

このセクションでは、エッジ ノードにインストールした Confluent Kafka スキーマ レジストリを構成します。 Confluent スキーマ レジストリは `/etc/schema-registry/schema-registry.properties` にあり、サービス実行可能ファイルを開始および停止するメカニズムは `/usr/bin/` フォルダーにあります。

スキーマの登録では、Zookeeper サービスが HDInsight Kafka クラスターとやり取りできることを認識している必要があります。 ZooKeeper クォーラムの詳細を取得するには、以下の手順に従います。

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用して、クラスターのエッジ ノードに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. パスワード変数を設定します。 PASSWORD をクラスターのログインパスワードに置き換え、次のコマンドを入力します。

    ```bash
    export password='PASSWORD'
    ```

1. 大文字と小文字が正しく区別されたクラスター名を抽出します。 次のコマンドを実行します。

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Kafka Zookeeper ホストを抽出します。 次のコマンドを実行します。

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    この値は、後で使用するので書き留めておいてください。

1. Kafka ブローカー ホストを抽出します。 次のコマンドを実行します。

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. 編集モードでスキーマ レジストリのプロパティ ファイルを開きます。 次のコマンドを実行します。

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. 先ほど特定した Zookeeper の文字列で `kafkastore.connection.url` の値を更新します。
    1. `debug` の値を `true` に更新します。

    これで、プロパティ ファイルは次のようになります。  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. ファイルを保存するには、 **Ctrl + X** キー、 **Y** キー、 **Enter** キーの順に押します。

1. スキーマ レジストリを起動し、更新されたスキーマ レジストリのプロパティ ファイルを使用するように指定します。 次のコマンドを実行します。

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. スキーマ レジストリが 1 つの SSH セッションで動作している状態で、別の SSH ウィンドウを起動します。  サブジェクト "Kafka-key" の下にスキーマの新しいバージョンを登録し、出力を記録しておきます。

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. サブジェクト "Kafka-value" の下にスキーマの新しいバージョンを登録し、出力を記録しておきます。

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. すべてのサブジェクトを一覧表示し、出力を確認します。

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

必要に応じて、[こちらに示されているその他の高度なコマンド](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart)も試してみてください。

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>スキーマ レジストリを使用して Kafka から Avro データを送信して使用する

このセクションでは、標準入力からデータを読み取り、ある形式で Kafka トピックに書き込みます。 次に、Avro フォーマッタを持つコンシューマーを使用して Kafka トピックからデータを読み取り、そのデータを読み取り可能な形式に変換します。

1. Kafka トピック `agkafkaschemareg` を作成します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. **Kafka Avro コンソール プロデューサー** を使用してスキーマを作成し、そのスキーマをトピックに割り当て、そのトピックへの Avro 形式でのデータの送信を開始します。 前の手順の Kafka トピックが正常に作成されていること、および **$KAFKABROKERS** に値が含まれていることを確認します。

    送信しているスキーマは、キーと値のペアです

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    以下のコマンドを使用して **Kafka Avro コンソール プロデューサー** を起動します。

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. プロデューサーがメッセージを受け入れる準備ができたら、定義済みの Avro スキーマ形式でのメッセージの送信を開始します。 キーと値の間にスペースを設けるには、Tab キーを使用しします。

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. コンソール プロデューサーにスキーマ以外のランダムなデータを入力してみることで、定義済みの Avro スキーマに準拠していないデータがプロデューサーでどのように許可されるかを確認します。

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. 別の画面で、Kafka Avro コンソール コンシューマーを起動します

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    以下の出力の表示が開始されます。

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>次の手順

* [Apache Kafka Producer および Consumer API の使用](apache-kafka-producer-consumer-api.md)