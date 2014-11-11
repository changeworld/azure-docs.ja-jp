<properties linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Use Hadoop Hive in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight での Hive と Hadoop の使用

[Apache Hive][Apache Hive] では、*HiveQL* と呼ばれる SQL に似たスクリプト言語を使用して MapReduce ジョブを実行します。大規模なデータの集約、照会、分析でも Hive を利用できます。この記事では、HiveQL を使用して、Apache log4j ログ ファイルのデータを照会し、基本的な統計情報レポートを生成します。

**前提条件:**

-   **HDInsight クラスター**のプロビジョニングを終えている必要があります。Azure ポータルを使用してこれを実行する手順については、「[HDInsight の使用][HDInsight の使用]」を参照してください。クラスターを作成するその他のさまざまな方法については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

-   **Azure PowerShell** をコンピューターにインストールしておく必要があります。その手順については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell のインストールおよび構成]」を参照してください。

**所要時間:** 30 分

## この記事の内容

-   [Hive の利用例][Hive の利用例]
-   [Azure BLOB ストレージへのデータ ファイルのアップロード][Azure BLOB ストレージへのデータ ファイルのアップロード]
-   [PowerShell を使用して Hive クエリを実行][PowerShell を使用して Hive クエリを実行]
-   [次のステップ][次のステップ]

## <span id="usage"></span></a>Hive の利用例

データベースは、量がそれほど多くなく、クエリの待ち時間が短いデータを管理する場合に適しています。数テラバイトもあるビッグ データ セットとなると、多くの場合、従来の SQL データベースでは十分に対応できません。これまでデータベース管理者は、このような大規模なデータ セットに対処するためにスケールアップを図り、データベースの負荷が増大してパフォーマンスが低下すると、大型のハードウェアを購入してきました。

Hive は、大規模なデータ セットの照会時にユーザーによるスケールアウトを可能にすることでビッグ データに関連する問題を解決します。Hive は、MapReduce を使用して、増加するノードに対して同時にデータを照会し、負荷が増大したときは複数のホストにデータベースを配布します。

Hive と HiveQL には、データの照会時に Java で MapReduce ジョブを記述する以外の手段も用意されています。単純な SQL 風のラッパーにより、HiveQL でクエリを記述できます。これは HDInsight によって MapReduce にコンパイルされて、クラスター上で実行されます。

さらに、MapReduce フレームワークに慣れているプログラマは mapper と reducer を自分で作成し、HiveQL 言語の標準機能では実現できない高度な分析を実行することもできます。

Hive は、膨大な不変データ (Web ログなど) のバッチ処理に最も適しています。データベース管理システムなど、極めて迅速な応答を求められるトランザクション アプリケーションには適していません。Hive は、優れた柔軟性 (Hadoop クラスターにコンピューターを動的に追加できる)、拡張性 (MapReduce フレームワーク内とその他のプログラミング インターフェイス)、耐障害性を実現するように最適化されています。ただし、遅延時間は設計上の優先事項ではありません。

通常、アプリケーションで発生したエラー、例外、その他のコーディングの問題はログ ファイルに保存されます。管理者はログ ファイルのデータを使用して、発生する可能性のある問題点を確認したり、エラーやパフォーマンスのような問題に関係する指標を生成したりできます。多くの場合、これらのログ ファイルはサイズが非常に大きくなり、さらにその膨大なログ データをアプリケーションで処理、分析しなければなりません。

ログ ファイルはまさにビッグ データの好例といえます。HDInsight に備わっている Hive データ ウェアハウス システムを使用すれば、Azure BLOB ストレージのような Hadoop 互換のファイル システムに保存されたこれらのビッグ データ セットを容易に集約し、非定型クエリを実行して分析することができます。

## <span id="uploaddata"></span></a>BLOB ストレージへのデータ ファイルのアップロード

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

この記事では、HDInsight クラスターと一緒に配布されている log4j サンプル ファイル (*\\example\\data\\sample.log*) を使用します。ファイル内の各ログは 1 行で、ログ タイプと重要度を表す `[LOG LEVEL]` フィールドを含みます。次に例を示します。

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

ファイルにアクセスするには、次の構文を使用します。

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

次に例を示します。

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

*mycontainer* を実際のコンテナー名に置き換え、*mystorage* を BLOB ストレージ アカウント名に置き換えてください。

ファイルは既定のファイル システムに保存されるので、次のように指定してファイルにアクセスすることもできます。

    wasb:///example/data/sample.log
    /example/data/sample.log

独自の log4j ファイルを生成するには、[Apache Log4j ログ ユーティリティ][Apache Log4j ログ ユーティリティ]を使用します。Azure BLOB ストレージにデータをアップロードする方法については、「[データを HDInsight にアップロードする方法][データを HDInsight にアップロードする方法]」を参照してください。

## <span id="runhivequeries"></span></a> PowerShell を使用して Hive クエリを実行

先のセクションでは、sample.log という log4j ファイルを既定のファイル システム コンテナーにアップロードしました。このセクションでは、HiveQL を実行して Hive テーブルを作成し、データを Hive テーブルに読み込み、データを照会してエラー ログの件数を確認します。

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

4.  次のスクリプトを実行して、HiveQL クエリを定義します。

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

    LOAD DATA HiveQL コマンドは、結果としてデータ ファイルを \\hive\\warehouse\\ フォルダーに移動します。DROP TABLE コマンドはテーブルとデータ ファイルを削除します。内部テーブル オプションを使用して、スクリプトを再び実行する場合は、sample.log ファイルを再びアップロードする必要があります。データ ファイルを残す場合は、スクリプトにあるように、CREATE EXTERNAL TABLE コマンドを使用する必要があります。

    データ ファイルが別のコンテナーまたはストレージ アカウントにある場合にも、外部テーブルを使用することができます。

    スクリプトを再実行して、log4jlogs テーブルが既に存在する場合に備えて、まず DROP TABLE を使用します。

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

**Invoke-Hive を使用して Hive クエリを送信するには**

1.  Azure PowerShell コンソール ウィンドウを開きます。
2.  次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

    Azure アカウント資格情報の入力を求められます。

3.  変数を設定し、実行します。

        $clusterName = "<HDInsightClusterName>"

4.  次のスクリプトを実行して、HiveQL クエリを呼び出します。

        Use-AzureHDInsightCluster $clusterName 
        $response = Invoke-Hive -Query @"
            SELECT * FROM hivesampletable
                WHERE devicemake LIKE "HTC%"
                LIMIT 10; 
        "@

        Write-Host $response

    出力は次のようになります。

    ![PowerShell の Invoke-Hive の出力][PowerShell の Invoke-Hive の出力]

    より長い HiveQL クエリの場合は、PowerShell の Here-Strings または HiveQL スクリプト ファイルを使用することをお勧めします。次の例では、Invoke-Hive コマンドレットを使用して HiveQL スクリプト ファイルを実行する方法を示します。HiveQL スクリプト ファイルは、WASB にアップロードする必要があります。

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Here-Strings の詳細については、[Windows PowerShell Here-Strings の使用][Windows PowerShell Here-Strings の使用]を参照してください。

## <span id="nextsteps"></span></a>次のステップ

Hive では、SQL に似たクエリ言語を使用してデータを容易に照会できます。さらに、HDInsight の他のコンポーネントを使用すれば、データ移動や変換などを行うこともできます。詳細については、次の記事を参照してください。

-   [Azure HDInsight の概要][HDInsight の使用]
-   [HDInsight を使用したフライトの遅延データの分析][HDInsight を使用したフライトの遅延データの分析]
-   [Use Oozie with HDInsight (HDInsight での Oozie の使用)][Use Oozie with HDInsight (HDInsight での Oozie の使用)]
-   [プログラムによる Hadoop ジョブの送信][プログラムによる Hadoop ジョブの送信]
-   [HDInsight へのデータのアップロード][データを HDInsight にアップロードする方法]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]
-   [Azure HDInsight SDK のドキュメント][Azure HDInsight SDK のドキュメント]

  [Apache Hive]: http://hive.apache.org/
  [HDInsight の使用]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure PowerShell のインストールおよび構成]: ../install-configure-powershell/
  [Hive の利用例]: #usage
  [Azure BLOB ストレージへのデータ ファイルのアップロード]: #uploaddata
  [PowerShell を使用して Hive クエリを実行]: #runhivequeries
  [次のステップ]: #nextsteps
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage
  [Apache Log4j ログ ユーティリティ]: http://en.wikipedia.org/wiki/Log4j
  [データを HDInsight にアップロードする方法]: ../hdinsight-upload-data/
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [PowerShell の Invoke-Hive の出力]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Windows PowerShell Here-Strings の使用]: http://technet.microsoft.com/ja-jp/library/ee692792.aspx
  [HDInsight を使用したフライトの遅延データの分析]: ../hdinsight-analyze-flight-delay-data/
  [プログラムによる Hadoop ジョブの送信]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
  [Azure HDInsight SDK のドキュメント]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx
