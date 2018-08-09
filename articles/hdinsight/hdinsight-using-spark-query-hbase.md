---
title: Spark を使用して HBase のデータの読み取りと書き込みを行う - Azure HDInsight
description: Spark HBase コネクタを使用して、Spark クラスターから HBase クラスターに対するデータの読み取りと書き込みを行います。
services: hdinsight
author: maxluk
ms.author: maxluk
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: a21b2dcabd3bd08f6a51699c8ff323f87200ffa2
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592926"
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Spark を使用して HBase データを読み取る/書き込む

通常、Apache HBase は、低レベルの API (スキャン、取得、および配置) または Phoenix を使用した SQL 構文のいずれかでクエリされます。 Apache は Spark HBase コネクタも備えており、これは HBase に格納されたデータをクエリおよび変更するための便利で効率的な代替手段となります。

## <a name="prerequisites"></a>前提条件

* 2 つの別個の HDInsight クラスター。1 つは HBase で、もう 1 つは Spark 2.1 (HDInsight 3.6) がインストールされた Spark です。
* Spark クラスターは最小限の待ち時間で HBase クラスターと直接通信できる必要があります。そのため、両方のクラスターを同じ仮想ネットワークにデプロイする構成をお勧めします。 詳細については、「[Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」をご覧ください。
* 各クラスターへの SSH アクセス。
* 各クラスターの既定のストレージへのアクセス。

## <a name="overall-process"></a>全体的なプロセス

Spark クラスターが HDInsight クラスターのクエリを実行できるようにするためのプロセスの概要は次のとおりです。

1. HBase でいくつかのサンプル データを準備します。
2. HBase クラスターの構成フォルダー (/etc/hbase/conf) から hbase-site.xml ファイルを取得します。
3. hbase-site.xml のコピーを Spark 2 の構成フォルダー (/etc/spark2/conf) に配置します。
4. `packages` オプションで、Maven 座標を使用して Spark HBase コネクターを参照する `spark-shell` を実行します。
5. Spark のスキーマを HBase にマップするカタログを定義します。
6. RDD または DataFrame API を使用して HBase データと対話します。

## <a name="prepare-sample-data-in-hbase"></a>HBase でサンプル データを準備する

この手順では、Spark を使用してクエリできる単純なテーブルを HBase に作成し、データを設定します。

1. SSH を使用して、HBase クラスターのヘッド ノードに接続します。 詳細については、「[SSH を使用して HDInsight (Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。
2. HBase シェルを実行します。

        hbase shell

3. `Personal`、`Office` という列ファミリを持つ `Contacts` テーブルを作成します。

        create 'Contacts', 'Personal', 'Office'

4. いくつかのサンプル データ行を読み込みます。

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>HBase クラスターから hbase-site.xml を取得する

1. SSH を使用して、HBase クラスターのヘッド ノードに接続します。
2. ローカル ストレージから HBase クラスターの既定のストレージのルートに hbase-site.xml をコピーします。

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. [Azure Portal](https://portal.azure.com) を使用して HBase クラスターに移動します。
4. ストレージ アカウントを選択します。 

    ![ストレージ アカウント](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. 一覧で、[既定値] 列の下にチェックマークが付いているストレージ アカウントを選択します。

    ![既定のストレージ アカウント](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. [ストレージ アカウント] ウィンドウで、[BLOB] タイルを選択します。

    ![[BLOB] タイル](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. コンテナーの一覧で、HBase クラスターが使用しているコンテナーを選択します。
8. ファイル リストで `hbase-site.xml` を選択します。

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. [BLOB のプロパティ] パネルで [ダウンロード] を選択し、ローカル コンピューター上の場所に `hbase-site.xml` を保存します。

    ![[ダウンロード]](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Spark クラスターに hbase-site.xml を配置する

1. [Azure Portal](https://portal.azure.com) を使用して Spark クラスターに移動します。
2. ストレージ アカウントを選択します。

    ![ストレージ アカウント](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. 一覧で、[既定値] 列の下にチェックマークが付いているストレージ アカウントを選択します。

    ![既定のストレージ アカウント](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. [ストレージ アカウント] ウィンドウで、[BLOB] タイルを選択します。

    ![[BLOB] タイル](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. コンテナーの一覧で、Spark クラスターが使用しているコンテナーを選択します。
6. [アップロード] を選択します。

    ![アップロード](./media/hdinsight-using-spark-query-hbase/upload.png)

7. 先ほどローカル コンピューターにダウンロードした `hbase-site.xml` ファイルを選択します。

    ![hbase-site.xml のアップロード](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. [アップロード] を選択します。
9. SSH を使用して、Spark クラスターのヘッド ノードに接続します。
10. Spark クラスターの既定のストレージから、クラスターのローカル ストレージの Spark 2 構成フォルダーに `hbase-site.xml` をコピーします。

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark HBase コネクターを参照する Spark Shell を実行する

1. SSH を使用して、Spark クラスターのヘッド ノードに接続します。
2. Spark HBase コネクター パッケージを指定して、Spark シェルを起動します。

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories http://repo.hortonworks.com/content/groups/public/

3. この Spark シェル インスタンスを開いたままで、次の手順に進みます。

## <a name="define-a-catalog-and-query"></a>カタログとクエリを定義する

この手順では、Spark のスキーマを HBase にマップするカタログ オブジェクトを定義します。 

1. 開いている Spark シェルで、次の `import` ステートメントを実行します。

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. HBase で作成した Contacts テーブルのカタログを定義します。
    1. `Contacts` という名前の HBase テーブルのカタログ スキーマを定義します。
    2. rowkey を `key` として指定し、Spark で使用されている列名を、HBase で使用されている列ファミリ、列名、列タイプにマップします。
    3. また、rowkey を、`rowkey` の特定の列ファミリ `cf` を持つ名前付きの列 (`rowkey`) として詳細に定義する必要があります。

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

3. HBase で、`Contacts` テーブルの DataFrame を提供するメソッドを定義します。

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. DataFrame のインスタンスを作成します。

        val df = withCatalog(catalog)

5. DataFrame のクエリを実行します。

        df.show()

6. 2 行のデータが表示されます。

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. 一時テーブルを登録して、Spark SQL を使用して HBase テーブルのクエリを実行できるようにします。

        df.registerTempTable("contacts")

8. `contacts` テーブルに対して SQL クエリを発行します。

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. 次のような結果が表示されます。

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>新しい行を挿入する

1. Contact の新しいレコードを挿入するには、`ContactRecord` クラスを定義します。

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. `ContactRecord` のインスタンスを作成し、配列に挿入します。

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. 新しいデータの配列を HBase に保存します。

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. 結果を確認します。
    
        df.show()

5. 次のような出力結果が表示されます。

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>次の手順

* [Spark HBase コネクタ](https://github.com/hortonworks-spark/shc)
