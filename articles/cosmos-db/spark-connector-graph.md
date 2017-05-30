---
title: "Azure Cosmos DB: Spark と Apache TinkerPops Gremlin を使用してグラフ分析を実行する | Microsoft Docs"
description: "Azure Cosmos DB と Spark GraphX によるグラフ分析と並列計算を設定して実行するためのセットアップ手順を紹介します。"
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: khdang
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 7a95953fadb3089f60fd55973fdb3410012655af
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-perform-graph-analytics-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: Spark と Apache TinkerPops Gremlin を使用してグラフ分析を実行する

[Azure Cosmos DB](introduction.md) は、世界規模で分散する、Microsoft のマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント データベース、キー/値データベース、グラフ データベースを作成したり照会したりすることができます。 Azure Cosmos DB は、[Apache TinkerPop の Gremlin](graph-introduction.md) を使用して OLTP グラフ ワークロードに対応します。

[Spark](http://spark.apache.org/) は、汎用目的 OLAP データ処理に取り組む Apache Software Foundation のプロジェクトです。 Spark には、Hadoop の MapReduce モデルに似たインメモリ/ディスク ベースのハイブリッド分散コンピューティング モデルが備わっています。 クラウドには、[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) を使って Apache Spark をデプロイすることができます。 Spark には、グラフおよびグラフ並列計算のための [GraphX ライブラリ](http://spark.apache.org/graphx/)が含まれており、そこでも Apache TinkerPop Gremlin が使用されています。

Azure Cosmos DB と Spark を連携させれば、OLTP と OLAP 両方のワークロードを Gremlin を使って実行することができます。 このクイック スタートでは、Azure HDInsight Spark クラスター上の Azure Cosmos DB に対して Gremlin クエリを実行する方法を紹介しています。

## <a name="prerequisites"></a>前提条件

* このサンプルを実行する前に、以下の前提条件を満たしている必要があります。
   * Azure HDInsight Spark クラスター 2.0
   * JDK 1.8 以降 (JDK がない場合は、`apt-get install default-jdk` を実行します)
   * Maven (Maven がない場合は、`apt-get install maven` を実行します)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Azure HDInsight Spark クラスターをプロビジョニングする方法の詳細については、[HDInsight クラスターのプロビジョニング](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

## <a name="create-an-azure-cosmos-db-database-account"></a>Azure Cosmos DB データベース アカウントを作成する

まず、Graph API を含んだデータベース アカウントを作成します。

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="add-a-collection"></a>コレクションの追加

[!INCLUDE [cosmosdb-create-collection](../../includes/cosmosdb-create-collection.md)]

## <a name="get-apache-tinkerpop"></a>Apache TinkerPop を入手する

* HDInsight クラスターのマスター ノードにリモート接続する (`ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`)

* TinkerPop3 のソース コードを複製してローカルでビルドし、Maven キャッシュにインストールする

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

* Spark-Gremlin プラグインをインストールする 

    1. プラグインのインストールは Grape によって処理されます。 まず、Grape がプラグインとその依存関係をダウンロードできるように Grape のリポジトリの情報を入力する必要があります。 

        * grape 構成ファイルが `~/.groovy/grapeConfig.xml` に存在しない場合は、構成ファイルを作成します。 次の設定を使用します。

            ```xml
            <ivysettings>
            <settings defaultResolver="downloadGrapes"/>
            <resolvers>
                <chain name="downloadGrapes">
                <filesystem name="cachedGrapes">
                    <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
                    <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
                </filesystem>
                <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
                <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
                <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
                <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
                <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
                <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
                </chain>
            </resolvers>
            </ivysettings>
            ``` 

    2. Gremlin コンソールを起動します (`bin/gremlin.sh`)。
        
    3. 先ほどの手順でビルドしたバージョン 3.3.0-SNAPSHOT を含む Spark-Gremlin プラグインをインストールします。

        ```bash
        $ bin/gremlin.sh

                \,,,/
                (o o)
        -----oOOo-(3)-oOOo-----
        plugin activated: tinkerpop.server
        plugin activated: tinkerpop.utilities
        plugin activated: tinkerpop.tinkergraph
        gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
        ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
        gremlin> :q
        $ bin/gremlin.sh

                \,,,/
                (o o)
        -----oOOo-(3)-oOOo-----
        plugin activated: tinkerpop.server
        plugin activated: tinkerpop.utilities
        plugin activated: tinkerpop.tinkergraph
        gremlin> :plugin use tinkerpop.spark
        ==>tinkerpop.spark activated
        ```

    4. `Hadoop-Gremlin` がアクティブ化されているかどうかを `:plugin list` でチェックします。このプラグインは、Spark-Gremlin プラグインと干渉する可能性があるため、`:plugin unuse tinkerpop.hadoop` で無効にする必要があります。

## <a name="prepare-tinkerpop3-dependencies"></a>TinkerPop3 の依存関係を準備する

先ほどの手順で TinkerPop3 をビルドしたときに、target ディレクトリには、Spark と Hadoop に必要な jar の依存関係もすべてプルしました。 ここでは HDI と共にプレインストールされている jar を使用し、別途必要な依存関係だけをプルします。

    1. Gremlin コンソールの target ディレクトリに移動します (`tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`)。 

    2. ここから、`ext/` にあるすべての jar を `lib/` に移動します (`find ext/ -name '*.jar' -exec mv {} lib/ \;`)。

    3. `lib/` から、以下のリストにない jar ライブラリをすべて削除します。

        ```bash
        # TinkerPop3
        gremlin-core-3.3.0-SNAPSHOT.jar       
        gremlin-groovy-3.3.0-SNAPSHOT.jar     
        gremlin-shaded-3.3.0-SNAPSHOT.jar     
        hadoop-gremlin-3.3.0-SNAPSHOT.jar     
        spark-gremlin-3.3.0-SNAPSHOT.jar      
        tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

        # Gremlin depedencies
        asm-3.2.jar                                
        avro-1.7.4.jar                             
        caffeine-2.3.1.jar                         
        cglib-2.2.1-v20090111.jar                  
        gbench-0.4.3-groovy-2.4.jar                
        gprof-0.3.1-groovy-2.4.jar                 
        groovy-2.4.9-indy.jar                      
        groovy-2.4.9.jar                           
        groovy-console-2.4.9.jar                   
        groovy-groovysh-2.4.9-indy.jar             
        groovy-json-2.4.9-indy.jar                 
        groovy-jsr223-2.4.9-indy.jar               
        groovy-sql-2.4.9-indy.jar                  
        groovy-swing-2.4.9.jar                     
        groovy-templates-2.4.9.jar                 
        groovy-xml-2.4.9.jar                       
        hadoop-yarn-server-nodemanager-2.7.2.jar   
        hppc-0.7.1.jar                             
        javatuples-1.2.jar                         
        jaxb-impl-2.2.3-1.jar                      
        jbcrypt-0.4.jar                            
        jcabi-log-0.14.jar                         
        jcabi-manifests-1.1.jar                    
        jersey-core-1.9.jar                        
        jersey-guice-1.9.jar                       
        jersey-json-1.9.jar                        
        jettison-1.1.jar                           
        scalatest_2.11-2.2.6.jar                   
        servlet-api-2.5.jar                        
        snakeyaml-1.15.jar                         
        unused-1.0.0.jar                           
        xml-apis-1.3.04.jar                        
        ```

## <a name="get-the-cosmos-db-spark-connector"></a>Cosmos DB Spark コネクタを入手する

1. Cosmos DB Spark コネクタ `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` と Cosmos DB Java SDK `azure-documentdb-1.10.0.jar` を [GitHub の Azure Cosmos DB Spark コネクタ](https://github.com/Azure/azure-documentdb-spark/tree/master/releases/azure-documentdb-spark-0.0.3_2.0.2_2.11)のページから入手します。

2. または、ローカルでビルドしてもかまいません。 最新バージョンの Spark-Gremlin は Spark 1.6.1 でビルドされており、現在 Cosmos DB Spark コネクタで使われている Spark 2.0.2 とは互換性がないため、手動で最新の TinkerPop3 のコードをビルドして、jar をインストールしてください。

    1. Cosmos DB Spark コネクタを複製します。

    2. TinkerPop3 をビルドします (先ほど手順で既に実行済み)。 TinkerPop 3.3.0-SNAPSHOT の jar をすべてローカルにインストールします。
    
    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    2. `tinkerpop.version` `azure-documentdb-spark/pom.xml` を `3.3.0-SNAPSHOT` に更新します。
    
    3. Maven でビルドします。 必要な jar は `target` と `target/alternateLocation` に格納されます。

    ```bash
    git clone https://github.com/Azure/azure-documentdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. 前述の jar をローカル ディレクトリ (~/azure-documentdb-spark) にコピーします。

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Spark ワーカー ノードに依存関係を配布する 

1. グラフ データの変換には TinkerPop3 が使用されるため、関連する依存関係をすべての Spark ワーカー ノードに配布する必要があります。

2. 先ほど列挙した Gremlin の依存関係と、CosmosDB Spark コネクタの jar、CosmosDB Java SDK をワーカー ノードにコピーします。

    1. すべての jar を `~/azure-documentdb-spark` にコピーします。

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    2. すべての Spark ワーカー ノードの一覧を取得します。これは、Ambari Dashboard で確認できます (`Spark2` セクションの `Spark2 Clients` リスト)。

    3. そのディレクトリを各ノードにコピーします。

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>環境変数を設定する

1. Spark クラスターの HDP バージョンを調べます。これは `/usr/hdp/` にあるディレクトリ名 ("2.5.4.2-7" など) に対応します。

2. その hdp.version をすべてのノードに対して設定します。Ambari Dashboard で `YARN section -> Configs -> Advanced` に移動します。 `Custom yarn-site` に新しいプロパティ `hdp.version` を追加し、マスター ノードの HDP バージョンの値を指定します。 この構成を保存します。警告が表示されますが、無視してかまいません。 その後、通知アイコンの表示に従って YARN サービスと Oozie サービスを再起動します。

3. マスター ノードで次の環境変数を設定します (値は適宜置き換えてください)。

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>グラフの構成を準備する

1. Cosmos DB の接続パラメーターと Spark の設定を含んだ構成ファイルを作成し、`tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties` に格納します。

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark Connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. `spark.driver.extraClassPath` と `spark.executor.extraClassPath` を更新して、先ほどの手順で配布した jar のディレクトリを含めます (この場合は `/home/sshuser/azure-documentdb-spark/*`)。

3. Cosmos DB に関して次の詳細を入力します。

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-tinkerpops-graph-and-save-to-cosmos-db"></a>TinkerPop のグラフを読み込んで Cosmos DB に保存する
Cosmos DB にグラフを保存する方法を紹介するために、ここでは、TinkerPop によってあらかじめ定義された "TinkerPop modern" グラフを例として使用します。 このグラフは、Kryo 形式で保存され、tinkerpop リポジトリで提供されます。

1. ここでは Gremlin を YARN モードで実行するため、グラフ データを Hadoop ファイル システムから利用できるようにする必要があります。 次のコマンドでディレクトリを作成し、ローカル グラフ ファイルをそこにコピーします。 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. グラフの読み取りに `GryoInputFormat` を使用するように `gremlin-spark.properties` ファイルを一時的に更新します。 また、次のように、先ほど作成したディレクトリとして `inputLocation` を示します。

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

2. Gremlin コンソールを起動し、構成されている Cosmos DB コレクションにデータを永続化するための計算処理ステップを次のように作成します。  

    1. グラフを作成します (`graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`)。

    2. 書き込みには SparkGraphComputer を使用します。`graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

3. Cosmos DB にデータが取り込まれていることをデータ エクスプローラーから確認できます。

## <a name="load-the-graph-from-cosmos-db-and-run-gremlin-queries"></a>Cosmos DB からグラフを読み込んで Gremlin クエリを実行する

1. グラフの読み込みについては、`gremlin-spark.properties` を編集し、`graphReader` を `DocumentDBInputRDD` に設定します。

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. 次の手順に従い、グラフを読み込んでデータを走査し、それを使って Gremlin クエリを実行します。

    1. Gremlin コンソールを起動します: `bin/gremlin.sh`

    2. 必要な構成を使ってグラフを作成します: `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`

    3. SparkGraphComputer でグラフ走査を作成します: `g = graph.traversal().withComputer(SparkGraphComputer)`

    4. Gremlin グラフ クエリを実行します。

        ```bash
        gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
        ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
        gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
        ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
        gremlin> g.V().count()
        ==>6
        gremlin> g.E().count()
        ==>6
        gremlin> g.V(1).out().values('name')
        ==>josh
        ==>vadas
        ==>lop
        gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
        ==>josh
        ==>peter
        ==>vadas
        ==>marko
        gremlin> g.V().hasLabel('person').
               choose(values('name')).
                 option('marko', values('age')).
                 option('josh', values('name')).
                 option('vadas', valueMap()).
                 option('peter', label())
        ==>josh
        ==>person
        ==>[name:[vadas],age:[27]]
        ==>29
        ```

> [!NOTE]
> より詳細なログを表示出力するには、`conf/log4j-console.properties` でログの詳細レベルを引き上げてください。
>

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB と Spark を使ったグラフの操作方法について説明しました。

> [!div class="nextstepaction"]

