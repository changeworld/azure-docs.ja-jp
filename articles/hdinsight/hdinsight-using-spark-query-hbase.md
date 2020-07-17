---
title: Spark を使用して HBase のデータの読み取りと書き込みを行う - Azure HDInsight
description: Spark HBase コネクタを使用して、Spark クラスターから HBase クラスターに対するデータの読み取りと書き込みを行います。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 3ddb8734a3d15a6cd5f4a43ee069d6364f7523ed
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087488"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Apache Spark を使用した Apache HBase データの読み取り/書き込み

通常、Apache HBase は、低レベルの API (スキャン、取得、および配置) または Apache Phoenix を使用した SQL 構文のいずれかでクエリされます。 Apache には、Apache Spark HBase コネクタも用意されています。 そのコネクタは、HBase に格納されたデータのクエリおよび変更を行うための便利で効率的な代替手段となります。

## <a name="prerequisites"></a>前提条件

* 2 つの異なる HDInsight クラスターが、同じ[仮想ネットワーク](./hdinsight-plan-virtual-network-deployment.md)にデプロイされていること。 一方は HBase で、もう一方は Spark 2.1 (HDInsight 3.6) 以降がインストールされた Spark です。 詳細については、「[Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」をご覧ください。

* クラスターのプライマリ ストレージの URI スキーム。 このスキーマは、Azure Blob Storage では wasb://、Azure Data Lake Storage Gen2 では `abfs://`、Azure Data Lake Storage Gen1 では adl:// になります。 Blob Storage で安全な転送が有効になっている場合、URI は `wasbs://` になります。  [安全な転送](../storage/common/storage-require-secure-transfer.md)に関するページも参照してください。

## <a name="overall-process"></a>全体的なプロセス

Spark クラスターが HDInsight クラスターのクエリを実行できるようにするためのプロセスの概要は次のとおりです。

1. HBase でいくつかのサンプル データを準備します。
2. HBase クラスターの構成フォルダー (/etc/hbase/conf) から hbase-site.xml ファイルを取得します。
3. hbase-site.xml のコピーを Spark 2 の構成フォルダー (/etc/spark2/conf) に配置します。
4. `packages` オプションで、Maven 座標を使用して Spark HBase コネクターを参照する `spark-shell` を実行します。
5. Spark のスキーマを HBase にマップするカタログを定義します。
6. RDD または DataFrame API を使用して HBase データと対話します。

## <a name="prepare-sample-data-in-apache-hbase"></a>Apache HBase でサンプル データを準備する

この手順では、Spark を使用してクエリできるテーブルを Apache HBase に作成し、データを設定します。

1. `ssh` コマンドを使用して HBase クラスターに接続します。 次のコマンドを編集して `HBASECLUSTER` を HBase クラスターの名前に置き換えてから、コマンドを入力します。

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. `hbase shell` コマンドを使用して、HBase 対話型シェルを起動します。 SSH 接続で次のコマンドを入力します。

    ```bash
    hbase shell
    ```

3. `create` コマンドを使用して、2 つの列ファミリを持つ HBase テーブルを作成します。 次のコマンドを入力します。

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. `put` コマンドを使用して、特定のテーブルの指定行の指定列に値を挿入します。 次のコマンドを入力します。

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. `exit` コマンドを使用して、HBase 対話型シェルを停止します。 次のコマンドを入力します。

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>hbase-site.xml を Spark クラスターにコピーする

ローカル ストレージから Spark クラスターの既定ストレージのルートに hbase-site.xml をコピーします。  以下のコマンドを編集して、構成を反映します。  次に、開いている HBase クラスターへの SSH セッションから、以下のコマンドを入力します。

| 構文の値 | 新しい値|
|---|---|
|[URI スキーム](hdinsight-hadoop-linux-information.md#URI-and-scheme) | ストレージを反映するように変更します。  以下の構文は、安全な転送が有効になった Blob ストレージ用です。|
|`SPARK_STORAGE_CONTAINER`|Spark クラスターで使用される既定のストレージ コンテナー名に置き換えます。|
|`SPARK_STORAGE_ACCOUNT`|Spark クラスターで使用される既定のストレージ アカウント名に置き換えます。|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

次に、HBase クラスターへの SSH 接続を終了します。

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Spark クラスターに hbase-site.xml を配置する

1. SSH を使用して、Spark クラスターのヘッド ノードに接続します。 次のコマンドの `SPARKCLUSTER` を Spark クラスターの名前に置き換えて編集した後、コマンドを入力します。

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. 以下のコマンドを入力して、Spark クラスターの既定のストレージから、クラスターのローカル ストレージの Spark 2 構成フォルダーに `hbase-site.xml` をコピーします。

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark HBase コネクターを参照する Spark Shell を実行する

前の手順を完了すると、適切なバージョンの Spark HBase コネクタを参照して、Spark シェルを実行できるようになります。 自分のクラスター シナリオに適した最新の Spark HBase コネクタ コア バージョンを確認するには、[SHC コア リポジトリ](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/)のページを参照してください。

例として、次の表では、HDInsight チームが現在使用している 2 つのバージョンと対応するコマンドを示します。 表で示されているように HBase と Spark のバージョンが同じ場合は、クラスターに同じバージョンを使用できます。 


1. 開いている Spark クラスターへの SSH セッションで、下のコマンドを入力して、Spark シェルを開始します。

    |Spark のバージョン| HDI HBase のバージョン  | SHC のバージョン    |  コマンド  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3.6 (HBase 1.1) | 1.1.0.3.1.2.2-1    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2.4    | HDI 4.0 (HBase 2.0) | 1.1.1-2.1-s_2.11  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. この Spark シェル インスタンスを開いたままで、[カタログとクエリを定義します](#define-a-catalog-and-query)。 SHC コア リポジトリ内のバージョンに対応する jar が見つからない場合は、引き続き読んでください。 

jar は、[spark-hbase-connector](https://github.com/hortonworks-spark/shc) GitHub ブランチから直接ビルドできます。 たとえば、Spark 2.3 と HBase 1.1 を使用してを実行している場合は、次の手順を実行します。

1. リポジトリを複製します。

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. ブランチ - 2.3 にアクセスします。

    ```bash
    git checkout branch-2.3
    ```

3. ブランチからビルドします (.jar ファイルを作成します)。

    ```bash
    mvn clean package -DskipTests
    ```
    
3. 次のコマンドを実行します (作成した .jar ファイルに対応するよう .jar の名前を変更してください)。

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. この Spark シェル インスタンスを開いたままで、次のセクションに進みます。 



## <a name="define-a-catalog-and-query"></a>カタログとクエリを定義する

この手順では、Apache Spark のスキーマを Apache HBase にマップするカタログ オブジェクトを定義します。  

1. 開いている Spark シェルで、次の `import` ステートメントを入力します。

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. 以下のコマンドを入力して、HBase で作成した Contacts テーブルのカタログを定義します。

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    コード:  

    1. `Contacts` という名前の HBase テーブルのカタログ スキーマを定義します。  
    1. rowkey を `key` として指定し、Spark で使用されている列名を、HBase で使用されている列ファミリ、列名、列タイプにマップします。  
    1. `rowkey` の特定の列ファミリ `cf` を持つ名前付きの列 (`rowkey`) として、rowkey を詳細に定義します。  

1. 以下のコマンドを入力して、HBase で `Contacts` テーブルの DataFrame を提供するメソッドを定義します。

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. DataFrame のインスタンスを作成します。

    ```scala
    val df = withCatalog(catalog)
    ```  

1. DataFrame のクエリを実行します。

    ```scala
    df.show()
    ```

    2 行のデータが表示されます。

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. 一時テーブルを登録して、Spark SQL を使用して HBase テーブルのクエリを実行できるようにします。

    ```scala
    df.createTempView("contacts")
    ```

1. `contacts` テーブルに対して SQL クエリを発行します。

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    次のような結果が表示されます。

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>新しい行を挿入する

1. Contact の新しいレコードを挿入するには、`ContactRecord` クラスを定義します。

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. `ContactRecord` のインスタンスを作成し、配列に挿入します。

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. 新しいデータの配列を HBase に保存します。

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. 結果を確認します。

    ```scala  
    df.show()
    ```

    次のような出力結果が表示されます。

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. 次のコマンドを入力して、Spark シェルを閉じます。

    ```scala
    :q
    ```

## <a name="next-steps"></a>次のステップ

* [Apache Spark HBase コネクタ](https://github.com/hortonworks-spark/shc)
