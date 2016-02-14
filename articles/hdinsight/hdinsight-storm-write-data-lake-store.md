<properties
pageTitle="Azure HDInsight で Apache Storm によって Azure Data Lake Store を使用する"
description="HDInsight 上の Apache Storm トポロジから Azure Data Lake Store にデータを書き込む方法について説明します。このドキュメントおよび関連する例では、Data Lake Store への書き込みに HdfsBolt コンポーネントを使用する方法をデモンストレーションします。"
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="01/28/2016"
ms.author="larryfr"/>

#HDInsight で Apache Storm によって Azure Data Lake Store を使用する

Azure Data Lake Store は、データの高スループット、可用性、耐久性、および信頼性を提供する、HDFS 互換のクラウド ストレージ サービスです。このドキュメントでは、Java ベースの Storm トポロジを使用し、[HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) コンポーネントでデータを Azure Data Lake Store に書き込む方法について説明します。このコンポーネントは、Apache Storm の一部として提供されます。

> [AZURE.IMPORTANT] このドキュメントで使用されるトポロジの例は、HDInsight クラスターの Storm に含まれているコンポーネントに依存しており、他の Apache Storm クラスターで使用する場合には Azure Data Lake Store を動作させるための修正が必要になることがあります。

##前提条件

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 以上
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Azure サブスクリプション
* HDInsight クラスターの Storm。Azure Data Lake Store を使用できるクラスターの作成方法は、このドキュメントに含まれています。

###環境変数を構成する

開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* __JAVA\_HOME__ - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。たとえば、Unix や Linux ディストリビューションの場合は、`/usr/lib/jvm/java-7-oracle` のような値になります。Windows の場合は、`c:\Program Files (x86)\Java\jre1.7` のような値になります。

* __PATH__ - 次のパスを含む必要があります。

    * __JAVA\_HOME__ または同等のパス
    
    * __JAVA\_HOME\\bin__ または同等のパス
    
    * Maven がインストールされているディレクトリ

##トポロジの実装

このドキュメントで使用される例は、Java で記述され、以下のコンポーネントを使用します。

* __TickSpout__: トポロジ内の他のコンポーネントによって使用されるデータを生成します。

* __PartialCount__: TickSpout によって生成されたイベントをカウントします。

* __FinalCount__: PartialCount からのカウント データを集計します。

* __ADLStoreBolt__: [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) コンポーネントを使用して Azure Data Lake Store にデータを書き込みます。

このトポロジを含むプロジェクトは、[https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) からダウンロードして利用できます。

###ADLStoreBolt について

ADLStoreBolt は、Azure Data Lake への書き込みを行うトポロジで HdfsBolt インスタンスのために使用される名前です。これは、Microsoft によって作成された特別なバージョンの HdfsBolt ではありません。しかし、core-site 構成値に依存しており、Data Lake と通信するために Azure HDInsight に含まれている Hadoop コンポーネントにも依存しています。

具体的には、HDInsight クラスターを作成するときに、それを Azure Data Lake Store と関連付けることができます。そうすることで、選択した Data Lake Store の core-site にエントリが書き込まれます。これらのエントリは、Data Lake Store との通信を可能にするために、hadoop クライアントや hadoop hdfs などのコンポーネントによって使用されます。

> [AZURE.NOTE] Microsoft は、Azure Data Lake Store および Azure Blob Storage との通信を可能にするコードを Apache Hadoop および Storm プロジェクトに提供していますが、この機能は他の Hadoop および Storm の配布には既定では含まれていない場合があります。

トポロジ内の HdfsBolt の構成は、次のとおりです。

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
		.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
      	.withRecordFormat(recordFormat)
      	.withFileNameFormat(fileNameFormat)
      	.withRotationPolicy(rotationPolicy)
      	.withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");
      
HdfsBolt を使い慣れていればわかるように、URL 以外のすべてが標準的な構成です。URL は、Azure Data Lake Store のルートへのパスです。

Data Lake Store への書き込みは、HdfsBolt を使用し、URL を変更するだけで済むため、HDFS または WASB への書き込みに HdfsBolt を使用する既存の任意のトポロジを利用することができ、Azure Data Lake Store を使用するための変更も簡単です。

##HDInsight クラスターと Data Lake Store を作成する

[Azure での HDInsight と Data Lake Store の使用](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)に関するドキュメントでの手順に従って、HDInsight クラスターに新しい Storm を作成します。このドキュメントの手順では、新しい HDInsight クラスターと Azure Data Lake Store の作成手順を説明します。

> [AZURE.IMPORTANT] HDInsight クラスターを作成するときに、クラスターの種類として __Storm__ を選択する必要があります。OS には、Windows または Linux を指定できます。

##トポロジをビルドおよびパッケージ化する

1. サンプル プロジェクトを [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) から開発環境にダウンロードします。

2. エディターで `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` ファイルを開き、`.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")` という記述がある行を見つけます。__MYDATALAKE__ を、HDInsight サーバーの作成時に使用した Azure Data Lake Store の名前に変更します。

3. コマンド プロンプト、ターミナル、またはシェル セッションで、ダウンロードしたプロジェクトのルート ディレクトリに移動し、次のコマンドを実行してトポロジをビルドおよびパッケージ化します。

        mvn compile
        mvn package
    
    ビルドとパッケージ化が完了すると、`target` という名前の新しいディレクトリができます。このディレクトリには、`StormToDataLakeStore-1.0-SNAPSHOT.jar` という名前のファイルが含まれています。このファイルに、コンパイルされたトポロジが含まれています。

##Linux ベースの HDInsight でデプロイおよび実行する

HDInsight クラスターで Linux ベースの Storm を作成した場合は、次の手順に従って、トポロジをデプロイおよび実行します。

1. 次のコマンドを使用して、トポロジを HDInsight クラスターにコピーします。__USER__ を、クラスター作成時に使用した SSH ユーザー名に置き換えます。__CLUSTERNAME__ をクラスターの名前に置き換えます。

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    メッセージが表示されたら、クラスターの SSH ユーザーの作成時に使用したパスワードを入力します。パスワードではなく公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーのパスを指定することが必要な場合があります。
    
    > [AZURE.NOTE] 開発に Windows クライアントを使用している場合、`scp` コマンドがないことがあります。その場合は、`pscp` を使用できます。このコマンドは、[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) で入手できます。

2. アップロードが完了したら、次のようにして、SSH を使用して HDInsight クラスターに接続します。__USER__ を、クラスター作成時に使用した SSH ユーザー名に置き換えます。__CLUSTERNAME__ をクラスターの名前に置き換えます。

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    メッセージが表示されたら、クラスターの SSH ユーザーの作成時に使用したパスワードを入力します。パスワードではなく公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーのパスを指定することが必要な場合があります。
    
    > [AZURE.NOTE] 開発に Windows クライアントを使用している場合は、クラスターへの接続に PuTTY クライアントを使用するための情報について、[SSH による Windows から Linux ベースの HDInsight への接続](hdinsight-hadoop-linux-use-ssh-windows.md)に関するページを参照してください。
    
3. 接続したら、次のコマンドを使用してトポロジを開始します。

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    `datalakewriter` のフレンドリ名でトポロジが開始されます。

##Windows ベースの HDInsight でデプロイおよび実行する

1. Web ブラウザーを開き、HTTPS://CLUSTERNAME.azurehdinsight.net に移動します。__CLUSTERNAME__ は、HDInsight クラスターの名前です。メッセージが表示されたら、管理者ユーザー名 (`admin`) と、クラスターの作成時にこのアカウントのために使用したパスワードを指定します。

2. Storm ダッシュボードの __[Jar ファイル]__ ドロップダウンで __[参照]__ を選択し、`target` ディレクトリの StormToDataLakeStore-1.0-SNAPSHOT.jar ファイルを選択します。フォームの他のエントリには、以下の値を使用します。

    * クラス名: com.microsoft.example.StormToDataLakeStore
    * 追加のパラメーター: datalakewriter
    
    ![image of storm dashboard](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. __[送信]__ ボタンを選択して、トポロジをアップロードおよび開始します。トポロジが開始されると、__[送信]__ ボタンの下にある結果フィールドに、次のような情報が表示されます。

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##出力データを表示する

データを表示するには、いくつかの方法があります。このセクションでは、Azure ポータルと `hdfs` コマンドを使用して、データを表示します。

> [AZURE.NOTE] Azure Data Lake Store のいくつかのファイルにデータが同期されるように、トポロジを数分間実行してから出力データを確認します。

* __[Azure ポータル](https://portal.azure.com)から__: ポータルで、HDInsight で使用した Azure Data Lake Store を選択します。

    > [AZURE.NOTE] Data Lake Store を Azure ポータル ダッシュボードにピン留めしなかった場合は、左側の一覧の下部にある __[参照]__、__[Data Lake Store]__ の順に選択し、ストアを探して選択することができます。
    
    Data Lake Store の上部にあるアイコンで、__[データ エクスプローラー]__ を選択します。
    
    ![data explore icon](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    次に、__stormdata__ フォルダーを選択します。テキスト ファイルの一覧が表示されます。
    
    ![text files](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    いずれかのファイルを選択して、その内容を表示します。

* __クラスターから__: HDInsight クラスターへの接続に SSH (Linux クラスター) またはリモート デスクトップ (Windows クラスター) を使用した場合は、以下の方法でデータを表示できます。__DATALAKE__ を Data Lake Store の名前に置き換えます。

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    これで、ディレクトリに格納されているテキスト ファイルが連結され、次のような情報が表示されます。
    
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000
        
##トポロジを停止する

Storm トポロジは、停止されるか、クラスターが削除されるまで実行され続けます。トポロジを停止するには、次の情報を使用します。

__Linux ベースの HDInsight の場合__:

クラスターへの SSH セッションで、次のコマンドを使用します。

    storm kill datalakewriter

__Windows ベースの HDInsight の場合__:

1. Storm ダッシュボード (https://CLUSTERNAME.azurehdinsight.net,) で、ページの上部にある __[Storm UI]__ リンクを選択します。

2. Storm UI が読み込まれたら、__[datalakewriter]__ リンクを選択します。

    ![link to datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. __[トポロジのアクション]__ セクションで __[強制終了]__ を選択し、表示されるダイアログ ボックスで [OK] を選択します。

    ![topology actions](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

##次のステップ

これで、Storm を使用して Azure Data Lake Store に書き込む方法を学習できました。他の [HDInsight 用 Storm サンプル](hdinsight-storm-example-topology.md)もご覧ください。

<!---HONumber=AcomDC_0204_2016-->