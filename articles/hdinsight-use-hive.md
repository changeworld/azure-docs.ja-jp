<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="HDInsight での Hadoop Hive の使用 | Azure" metaKeywords="" description="HDInsight で Hive を使用する方法について説明します。HDInsight テーブルへの入力としてログ ファイルを使用します。また、HiveQL を使用してデータを照会し、基本的な統計情報を取得します。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight での Hadoop Hive の使用" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# HDInsight での Hive と Hadoop の使用

[Apache Hive][Apache Hive] では、*HiveQL* と呼ばれる SQL に似たスクリプト言語を使用して MapReduce ジョブを実行します。Hive は Hadoop 対応のデータ ウェアハウス システムで、大規模なデータを集約、照会、および分析することができます。この記事では、HiveQL を使用して、HDInsight クラスター プロビジョニングの一部として提供されたサンプル データ ファイルを照会します。

**前提条件:**

-   **HDInsight クラスター**のプロビジョニングを終えている必要があります。Azure ポータルを使用してこれを実行する手順については、「[HDInsight の使用][HDInsight の使用]」を参照してください。クラスターを作成するその他のさまざまな方法については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

-   **Azure PowerShell** をコンピューターにインストールしておく必要があります。その手順については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell のインストールおよび構成]」を参照してください。

**所要時間:** 30 分

## この記事の内容

-   [Hive の利用例][Hive の利用例]
-   [Hive テーブルのデータのアップロード][Hive テーブルのデータのアップロード]
-   [PowerShell を使用して Hive クエリを実行][PowerShell を使用して Hive クエリを実行]
-   [パフォーマンスを改善するための Tez の使用方法][パフォーマンスを改善するための Tez の使用方法]
-   [次のステップ][次のステップ]

## <span id="usage"></span></a>Hive の利用例

![HDI.HIVE.Architecture][HDI.HIVE.Architecture]

Hive は、大規模な非構造化データ セットにある種の構造を与え (すべての Hadoop で同様に)、ユーザーが HiveQL を使用してデータを照会できるようにします。Hive は、Java ベースの MapReduce フレームワーク上に抽象化レイヤーを提供しており、ユーザーはそのレイヤーを使用して、Java や MapReduce の知識なしで、データを照会できます。単純な SQL 風のラッパーにより、HiveQL でクエリを記述できます。これは HDInsight によって MapReduce にコンパイルされて、クラスター上で実行されます。Hive にはその他にも次の利点があります。

-   MapReduce フレームワークに慣れているプログラマは mapper と reducer を自分で作成し、HiveQL 言語の標準機能では実現できない高度な分析を実行することもできます。
-   Hive は、膨大な不変データ (Web ログなど) のバッチ処理に最も適しています。データベース管理システムなど、極めて迅速な応答を求められるトランザクション アプリケーションには適していません。
-   Hive は、優れた柔軟性 (Hadoop クラスターにコンピューターを動的に追加できる)、拡張性 (MapReduce フレームワーク内とその他のプログラミング インターフェイス)、耐障害性を実現するように最適化されています。ただし、遅延時間は設計上の優先事項ではありません。

## <span id="uploaddata"></span></a>Hive テーブルのデータのアップロード

HDInsight は、Hadoop クラスター対応の既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。クラスター プロビジョニングの一部としていくつかのサンプル データ ファイルが BLOB ストレージに追加されています。これらのサンプル データ ファイルを使用して、クラスター上で Hive クエリを実行できます。必要な場合は、クラスターに関連付けられた BLOB ストレージ アカウントにユーザー専用のデータ ファイルをアップロードすることもできます。手順については、「[データを HDInsight にアップロードする方法][データを HDInsight にアップロードする方法]」を参照してください。HDInsight と共に Azure BLOB ストレージを使用する方法の詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

この記事では、*log4j* サンプル ファイルを使用します。このファイルは、HDInsight クラスターと共に配布され、BLOB ストレージ コンテナーの下の *\\example\\data\\sample.log* に格納されています。また、[Apache Log4j][Apache Log4j] ログ ユーティリティを使用して独自の log4j ファイルを生成し、それを BLOB コンテナーにアップロードすることもできます。ファイル内の各ログは 1 行で、ログ タイプと重要度を表す `[LOG LEVEL]` フィールドを含みます。次に例を示します。

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

前の例では、ログ レベルは ERROR です。

お使いのアプリケーション内から次の構文を使用してファイルにアクセスできます。

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

sample.log ファイルにアクセスするには、次の構文を使用します。

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

*mycontainer* を Azure BLOB コンテナー名に置き換え、*mystorage* を Azure ストレージ アカウント名に置き換えてください。

ファイルは既定のファイル システムに保存されるので、次のように指定してファイルにアクセスすることもできます。

    wasb:///example/data/sample.log
    /example/data/sample.log

## <span id="runhivequeries"></span></a> PowerShell を使用して Hive クエリを実行

前のセクションで、Hive クエリを実行するために使用する *sample.log* ファイルを特定しました。このセクションでは、HiveQL を実行して Hive テーブルを作成し、サンプル データを Hive テーブルに読み込み、データを照会してファイル内に存在するエラー ログの件数を確認します。

この記事では、Azure PowerShell コマンドレットを使用して Hive クエリを実行する手順を示します。このセクションを読み進める前に、このトピックの冒頭にある「**前提条件**」セクションで説明されているように、まずローカル環境をセットアップし、Azure への接続を構成する必要があります。

Hive クエリは、**Start-AzureHDInsightJob** コマンドレットまたは **Invoke-Hive** コマンドレットを使用して PowerShell で実行できます。

**Start-AzureHDInsightJob を使用して Hive クエリを実行するには**

1.  Azure PowerShell コンソール ウィンドウを開きます。手順については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell のインストールおよび構成]」を参照してください。
2.  次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

    Azure アカウント資格情報の入力を求められます。

3.  次のスクリプトで変数を設定して、スクリプトを実行します。

        # Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  次のスクリプトを実行して、HiveQL クエリを定義します。*内部*または*外部* のどちらの Hive テーブルを作成するか選択できます。

    -   内部テーブルでは、使用するサンプル データが既存の場所から \\hive\\warehouse\\\<*tablename\>* フォルダーに移動されます。そのため、内部テーブルを削除すると、関連付けられたデータも削除されます。内部テーブルを使用して、スクリプトを再び実行する場合は、*sample.log* ファイルを BLOB ストレージに再びアップロードする必要があります。
    -   外部テーブルでは、データは元の場所から移動されません。また、データ ファイルが異なるコンテナーやストレージ アカウントにある状態でも外部テーブルを使用できます。

            # HiveQL queries
            # Use the internal table option. 
            $queryString = "DROP TABLE log4jLogs;" +
                           "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
                           "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
                           "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

            # Use the external table option. 
            $queryString = "DROP TABLE log4jLogs;" +
                            "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
                            "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

    DROP TABLE コマンドは、テーブルが既存の場合にテーブルとデータ ファイルを削除します。LOAD DATA HiveQL コマンドは、データ ファイルを \\example\\data から \\hive\\warehouse\\\<*tablename\>* フォルダーに移動します。

5.  次のスクリプトを実行して、Hive ジョブ定義を作成します。

        # Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

    -File スイッチを使用して、HDFS 上の HiveQL スクリプト ファイルを指定することもできます。

6.  次のスクリプトを実行して、Hive ジョブを送信します。

        # Submit the job to the cluster 
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  次のスクリプトを実行して、Hive ジョブを送信し、ジョブの完了を待ちます。

        # Wait for the Hive job to complete
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  次のスクリプトを実行して、標準出力を表示します。

        # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    結果は次のようになります。

        [ERROR] 3

    つまり、*sample.log* ファイルには 3 件の ERROR ログがありました。

必要な場合は、クエリの出力を Microsoft Excel にインポートしてさらに分析できます。手順については、「[Power Query を使用した Excel から Hadoop への接続][Power Query を使用した Excel から Hadoop への接続]」を参照してください。

**Invoke-Hive を使用して Hive クエリを送信するには**

1.  Azure PowerShell コンソール ウィンドウを開きます。
2.  次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

    Azure アカウント資格情報の入力を求められます。

3.  次のスクリプトで変数を設定して、スクリプトを実行します。

        # Provide Azure subscription name, Azure Storage account, and container.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  HDInsight クラスターへ接続します。

        # Connect to the cluster
        Use-AzureHDInsightCluster $clusterName

5.  次のスクリプトを実行して、HiveQL クエリを呼び出します。

        # Run the query to create an internal Hive table, load sample data
        $response = Invoke-Hive -Query @"
        >> DROP TABLE log4jLogs;
        >> CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW   
            FORMAT DELIMITED FIELDS TERMINATED BY ' ';
        >> LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/
            sample.log' OVERWRITE INTO TABLE log4jLogs;
        >> SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4; 
        >> "@

6.  出力をコンソールに表示します。

        # print the output on the console
        Write-Host $response

    出力は次のようになります。

    ![PowerShell の Invoke-Hive の出力][PowerShell の Invoke-Hive の出力]

    より長い HiveQL クエリの場合は、PowerShell の Here-Strings または HiveQL スクリプト ファイルを使用できます。次のスニペットでは、*Invoke-Hive* コマンドレットを使用して HiveQL スクリプト ファイルを実行する方法を示します。HiveQL スクリプト ファイルは、WASB にアップロードする必要があります。

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Here-Strings の詳細については、[Windows PowerShell Here-Strings の使用][Windows PowerShell Here-Strings の使用]を参照してください。

必要な場合は、クエリの出力を Microsoft Excel にインポートしてさらに分析できます。手順については、「[Power Query を使用した Excel から Hadoop への接続][Power Query を使用した Excel から Hadoop への接続]」を参照してください。

## <span id="usetez"></span></a>パフォーマンスを改善するための Tez の使用方法

[Apache Tez][Apache Tez] は、Hive などの大量のデータを扱うアプリケーションを同じ規模で遥かに効率的に実行可能にするフレームワークです。HDInsight の最新リリースでは、Hive を Tez 上で実行できます。この機能は、現在、既定では無効のため、有効にする必要があります。将来のクラスター バージョンでは、既定で有効に設定されます。Tez を活用するために、Hive クエリに次の値を設定する必要があります。

        set hive.execution.engine=tez;
        

これは、クエリの先頭に配置することで、クエリ単位で送信できます。また、クラスターの作成時に構成値を設定することで、この機能が既定で有効になるようにクラスターを設定できます。詳細については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

[「Hive on Tez」設計ドキュメント][「Hive on Tez」設計ドキュメント]には、実装の選択肢および構成の調整に関する詳細が記載されています。

## <span id="nextsteps"></span></a>次のステップ

Hive では、SQL に似たクエリ言語を使用してデータを容易に照会できます。さらに、HDInsight の他のコンポーネントを使用すれば、データ移動や変換などを行うこともできます。詳細については、次の記事を参照してください。

-   [Use Oozie with HDInsight (HDInsight での Oozie の使用)][Use Oozie with HDInsight (HDInsight での Oozie の使用)]
-   [プログラムによる Hadoop ジョブの送信][プログラムによる Hadoop ジョブの送信]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]
-   [HDInsight を使用したフライトの遅延データの分析][HDInsight を使用したフライトの遅延データの分析]
-   [Azure HDInsight SDK のドキュメント][Azure HDInsight SDK のドキュメント]
-   [HDInsight へのデータのアップロード][データを HDInsight にアップロードする方法]
-   [Azure HDInsight の概要][HDInsight の使用]

  [Apache Hive]: http://hive.apache.org/
  [HDInsight の使用]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure PowerShell のインストールおよび構成]: ../install-configure-powershell/
  [Hive の利用例]: #usage
  [Hive テーブルのデータのアップロード]: #uploaddata
  [PowerShell を使用して Hive クエリを実行]: #runhivequeries
  [パフォーマンスを改善するための Tez の使用方法]: #usetez
  [次のステップ]: #nextsteps
  [HDI.HIVE.Architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
  [データを HDInsight にアップロードする方法]: ../hdinsight-upload-data/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [Power Query を使用した Excel から Hadoop への接続]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-connect-excel-power-query/
  [PowerShell の Invoke-Hive の出力]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Windows PowerShell Here-Strings の使用]: http://technet.microsoft.com/ja-jp/library/ee692792.aspx
  [Apache Tez]: http://tez.apache.org
  [「Hive on Tez」設計ドキュメント]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Use Oozie with HDInsight (HDInsight での Oozie の使用)]: ../hdinsight-use-oozie/
  [プログラムによる Hadoop ジョブの送信]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
  [HDInsight を使用したフライトの遅延データの分析]: ../hdinsight-analyze-flight-delay-data/
  [Azure HDInsight SDK のドキュメント]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx
