---
title: Apache Atlas Spark コネクタからのメタデータとデータ系列
description: この記事では、Atlas Spark コネクタを使用して Spark からデータ系列を抽出する方法について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/28/2021
ms.openlocfilehash: aebe227c8f2a278131d38143c43c38ee4687e701
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579055"
---
# <a name="how-to-use-apache-atlas-connector-to-collect-spark-lineage"></a>Apache Atlas コネクタを使用して Spark データ系列を収集する方法

Apache Atlas Spark Connector は、Spark SQL/DataFrame のデータ移動を追跡し、メタデータの変更を Purview Atlas エンドポイントにプッシュするためのフックです。 

## <a name="supported-scenarios"></a>サポートされるシナリオ

このコネクタでは、次の追跡がサポートされています。
1.  SQL DDL: "CREATE/ALTER DATABASE"、"CREATE/ALTER TABLE" など。
2.  SQL DML: "CREATE TABLE HelloWorld AS SELECT"、"INSERT INTO..."、"LOAD DATA [LOCAL] INPATH"、"INSERT OVERWRITE [LOCAL] DIRECTORY" など。
3.  入力と出力のある DataFrame の移動。

このコネクタは、クエリ リスナーを利用してクエリを取得し、影響を調べます。 Hive や HDFS などの他のシステムと関連付けて、Atlas 内のデータのライフサイクルを追跡します。
Purview は Atlas API と Atlas ネイティブ フックをサポートしているため、コネクタは Spark で構成された後、Purview にデータ系列をレポートできます。 コネクタは、ジョブごとに構成することも、クラスターの規定の設定として構成することもできます。 

## <a name="configuration-requirement"></a>構成要件

コネクタには、Spark のバージョン 2.4.0+ が必要です。 ただし、Spark バージョン 3 はサポートされていません。 Spark は、設定する必要のある次の 3 種類のリスナーをサポートしています。  

| リスナー |    Spark 最小バージョン|
| ------------------- | ------------------- | 
| spark.extraListeners | 1.3.0 |
| spark.sql.queryExecutionListeners | 2.3.0 |
| spark.sql.streaming.streamingQueryListeners | 2.4.0 |

Spark クラスターのバージョンが 2.4.0 未満の場合、Stream のクエリ データ系列と大部分のクエリ データ系列は取得されません。

### <a name="step-1-prepare-spark-atlas-connector-package"></a>手順 1. Spark Atlas コネクタ パッケージを準備します
以下の手順は、DataBricks の場合の例です。

1.  パッケージを生成します
    1. GitHub からコードをプルします: https://github.com/hortonworks-spark/spark-atlas-connector
    2. (Windows の場合) spark-atlas-connector\pom.xml で **maven-enforcer-plugin** をコメント アウトし、Unix 上の依存関係を除去します。

    ```web
    <requireOS>
        <family>unix</family>
    </requireOS>
    ```
    
    c. ビルドするプロジェクト ルートで **mvn package -DskipTests** コマンドを実行します。 
    
    d. *~\spark-atlas-connector-assembly\target\spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar* から jar を取得します。
    
    e. Spark クラスターがアクセスできる場所にパッケージを置きます。 DataBricks クラスターの場合、パッケージを dbfs フォルダー (/FileStore/jars など) にアップロードできます。

2. コネクタ構成を準備します
    1. Purview アカウントの Azure portal で Kafka エンドポイントと資格情報を取得します
        1. アカウントに *“Purview データ キュレーター”* 権限を付与します 

        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/assign-purview-data-curator-role.png" alt-text="データ キュレーター ロールの割り当てを示すスクリーンショット" lightbox="./media/how-to-lineage-spark-atlas-connector/assign-purview-data-curator-role.png":::

        1. エンドポイント: *Atlas Kafka エンドポイント プライマリ接続ストリング* から取得します。 エンドポイント部分
        1. 資格情報: *Atlas Kafka エンドポイント プライマリ接続ストリング* 全体
        
        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/atlas-kafka-endpoint.png" alt-text="Atlas Kafka エンドポイントを示すスクリーンショット" lightbox="./media/how-to-lineage-spark-atlas-connector/atlas-kafka-endpoint.png":::        
        
    1. *atlas-application.properties* ファイルを準備します。*atlas.kafka.bootstrap.servers* と、*atlas.kafka.sasl.jaas.config* 内のパスワード値を置き換えます。

    ```script
    atlas.client.type=kafka
    atlas.kafka.sasl.mechanism=PLAIN
    atlas.kafka.security.protocol=SASL_SSL
    atlas.kafka.bootstrap.servers= atlas-46c097e6-899a-44aa-9a30-6ccd0b2a2a91.servicebus.windows.net:9093
    atlas.kafka.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="<connection string got from your Purview account>";
    ```
    
    c.  Atlas 構成ファイルが、[上記の手順 1「パッケージの生成」セクション](../purview/how-to-lineage-spark-atlas-connector.md#step-1-prepare-spark-atlas-connector-package)で生成された Driver のクラスパスにあることを確認します。 クラスター モードで、この構成ファイルをリモート ドライブの *--files atlas-application.properties* に送付します


### <a name="step-2-prepare-your-purview-account"></a>手順 2. Purview アカウントを準備します
Atlas Spark モデル定義が正常に作成されたら、次の手順に従います
1. GitHub から Spark 型定義を取得します https://github.com/apache/atlas/blob/release-2.1.0-rc3/addons/models/1000-Hadoop/1100-spark_model.json

2. ロールの割り当て:
    1. Purview アカウントに移動し、[アクセス制御 (IAM)] を選択します 
    1. ユーザーを追加し、サービス プリンシパル *Purview データ ソース管理者* ロールを付与します
3. 認証トークンの取得:
    1. "postman" または同様のツールを開きます 
    1. 前の手順で使用したサービス プリンシパルを使用した、ベアラー トークンの取得:
        * エンドポイント: https://login.windows.net/microsoft.com/oauth2/token
        * grant_type: client_credentials
        * client_id: {service principal ID}
        * client_secret: {service principal key}
        * resource: `https://purview.azure.net`

        :::image type="content" source="./media/how-to-lineage-spark-atlas-connector/postman-examples.png" alt-text="postman を使用した例を示すスクリーンショット" lightbox="./media/how-to-lineage-spark-atlas-connector/postman-examples.png":::      

4. Spark Atlas モデル定義を Purview アカウントに投稿:
    1.  Purview アカウントの Atlas エンドポイントを Azure portal のプロパティ セクションから取得します。
    1. Purview アカウントへの Spark 型定義の投稿:
       * Post: {{endpoint}}/api/atlas/v2/types/typedefs
       * 生成されたアクセス トークンを使用します 
       * Body: raw を選択し、GitHub からすべてのコンテンツをコピーします https://github.com/apache/atlas/blob/release-2.1.0-rc3/addons/models/1000-Hadoop/1100-spark_model.json

:::image type="content" source="./media/how-to-lineage-spark-atlas-connector/postman-example-type-definition.png" alt-text="型定義に postman を使用する例を示すスクリーンショット" lightbox="./media/how-to-lineage-spark-atlas-connector/postman-example-type-definition.png":::

### <a name="step-3-prepare-spark-job"></a>手順 3. Spark ジョブを準備します
1. Spark ジョブを通常どおりに作成します
2. Spark ジョブのソース コードにコネクタの設定を追加します。 *'atlas.conf'* システム プロパティ値を以下のようなコード内に設定し、*atlas-application.properties* ファイルがあることを確認します。

    **System.setProperty("atlas.conf", "/dbfs/FileStore/jars/")**

3. Spark ジョブのソース コードを作成して、jar ファイルを生成します。 
4. クラスターがアクセスできる場所に Spark アプリケーションの jar ファイルを置きます。 たとえば、 *"/dbfs/FileStore/jars/"DataBricks* に jar ファイルを置きます。 

### <a name="step-4-prepare-to-run-job"></a>手順 4. ジョブの実行を準備します
 
1. 各ジョブ設定の手順を以下に示します。特定のジョブのデータ系列を取得するには、spark-submit を使用し、パラメーターを使用してジョブを開始します。 

    ジョブのパラメーターで、次を設定します。
* コネクタ Jar ファイルのパス。 
* 3 つのリスナー: extraListeners、queryExecutionListeners、streamingQueryListeners (コネクタとして使用)。 

| リスナー | 詳細 |
| ------------------- | ------------------- | 
| spark.extraListeners  | com.hortonworks.spark.atlas.SparkAtlasEventTracker|
| spark.sql.queryExecutionListeners | com.hortonworks.spark.atlas.SparkAtlasEventTracker
| spark.sql.streaming.streamingQueryListeners | com.hortonworks.spark.atlas.SparkAtlasStreamingQueryEventTracker |

* Spark ジョブ アプリケーション Jar ファイルのパス。

Databricks ジョブのセットアップ: spark-submit を使用し、リスナーを適切にセットアップしてジョブを実行することが重要です。 task パラメーターにリスナー情報を設定します。   

Spark ジョブのパラメーターの例を次に示します。

```script
["--jars","dbfs:/FileStore/jars/spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar ","--conf","spark.extraListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker","--conf","spark.sql.queryExecutionListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker","--conf","spark.sql.streaming.streamingQueryListeners=com.hortonworks.spark.atlas.SparkAtlasStreamingQueryEventTracker","--class","com.microsoft.SparkAtlasTest","dbfs:/FileStore/jars/08cde51d_34d8_4913_a930_46f376606d7f-sparkatlas_1_6_SNAPSHOT-17452.jar"]
```

コマンド ラインからの spark submit の例を次に示します。

```script
spark-submit --class com.microsoft.SparkAtlasTest --master yarn --deploy-mode --files /data/atlas-application.properties --jars /data/ spark-atlas-connector-assembly-0.1.0-SNAPSHOT.jar 
--conf spark.extraListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker 
--conf spark.sql.queryExecutionListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker 
--conf spark.sql.streaming.streamingQueryListeners=com.hortonworks.spark.atlas.SparkAtlasEventTracker
/data/worked/sparkApplication.jar
```

2. 以下の手順はクラスターの設定用です。コネクタの jar とリスナーの設定は、Spark クラスターの *conf/spark-defaults.conf* に入れる必要があります。 Spark-submit は、*conf/spark-defaults.conf* でオプションを読み取り、それらをアプリケーションに渡します。 
 
### <a name="step-5-run-and-check-lineage-in-purview-account"></a>手順 5. Purview アカウントでデータ系列を実行およびチェックします
Spark ジョブを開始し、Purview アカウントのデータ系列情報を確認します。 

:::image type="content" source="./media/how-to-lineage-spark-atlas-connector/purview-with-spark-lineage.png" alt-text="Spark データ系列を持つ Purview を示すスクリーンショット" lightbox="./media/how-to-lineage-spark-atlas-connector/purview-with-spark-lineage.png":::

## <a name="known-limitations-with-the-connector-for-spark-lineage"></a>Spark データ系列用コネクタの既知の制限事項
1. SQL/DataFrame API がサポートされています (言い換えれば、RDD はサポートされていません)。 このコネクタは、クエリ リスナーを利用してクエリを取得し、影響を調べます。
    
2. 複数のクエリからのすべての "入力" と "出力" は、単一の "spark_process" エンティティに結合されます。
    
    "spark_process" は、Spark の "applicationId" にマップされます。 これにより、管理者は、アプリケーションの一部として発生したすべての変更を追跡できます。 ただし、"spark_process" の lineage/relationship グラフは複雑で意義が薄れたものになります。
3. ストリーミング クエリで追跡されるのは、入力の一部のみです。

* Kafka ソースでは、"pattern" を使用したサブスクライブがサポートされていますが、このコネクタによって既存のすべての一致するトピック、あるいは考えられるすべてのトピックが列挙されるわけではありません。 
 
* "executed plan" には、(マイクロ) バッチの読み取りと処理を含む実際のトピックが用意されています。 このため、(マイクロ) バッチに含まれている入力のみが、"spark_process" エンティティの "inputs" として含まれます。
    
4. このコネクタでは、列レベルのデータ系列はサポートされていません。

5. 削除されたテーブル (Spark モデル) は追跡されません。

    Spark からの "drop table" イベントによって提供されるのはデータベースとテーブルの名前のみであり、これは、テーブルを認識するための一意のキーを作成するのに十分ではありません。

    コネクタは、Spark カタログを読み取ることで、テーブル情報を取得します。 このコネクタがテーブルの削除を認識するときには Spark によってテーブルはすでに削除されているため、drop table は機能しません。


## <a name="next-steps"></a>次のステップ

- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- [プッシュ自動系列への Azure Data Factory のリンク](how-to-link-azure-data-factory.md)
