<properties 
	pageTitle="HDInsight での Hadoop Hive の使用 | Azure" 
	description="HDInsight で Hive を使用する方法について説明します。HDInsight テーブルへの入力としてログ ファイルを使用します。また、HiveQL を使用してデータを照会し、基本的な統計情報を取得します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="jgao"/>

# HDInsight での Hive と Hadoop の使用

[Apache Hive][apache-hive]では、 *HiveQL* と呼ばれる SQL に似たスクリプト言語を使用して MapReduce ジョブを実行します。Hive は Hadoop 対応のデータ ウェアハウス システムで、大規模なデータを集約、照会、および分析することができます。この記事では、HiveQL を使用して、HDInsight クラスター プロビジョニングの一部として提供されたサンプル データ ファイルを照会します。


**前提条件:**

- **HDInsight クラスター**のプロビジョニングを終えている必要があります。Azure ポータルを使用してこれを実行する手順については、[HDInsight の使用][hdinsight-get-started].を参照してください。クラスターを作成するその他のさまざまな方法については、[HDInsight クラスターのプロビジョニング][hdinsight-provision]を参照してください。

- **Azure PowerShell**をワークステーションにインストールしておく必要があります。その手順については、[Azure PowerShell のインストールおよび構成][powershell-install-configure]を参照してください。

## この記事の内容

* [Hive の利用例](#usage)
* [Hive テーブルのデータをアップロード](#uploaddata)
* [PowerShell を使用して Hive クエリを実行](#runhivequeries)
* [Visual Studio の HDInsight のツールを使用して HIve クエリを実行](#runhivefromvs)
* [Tez を使用してパフォーマンスを向上](#usetez)
* [次のステップ](#nextsteps)

## <a id="usage"></a>Hive の利用例

![HDI.HIVE.Architecture][image-hdi-hive-architecture]

Hive の目的は、大規模な非構造化データを構造化し、ユーザーがデータを照会できるようにすることです。Hive は、Java ベースの MapReduce フレームワーク上に抽象化レイヤーを提供しており、ユーザーはそのレイヤーを使用して、Java や MapReduce の知識なしで、データを照会できます。Hive のクエリ言語である HiveQL により、T-SQL と同様にステートメントを使用してクエリを記述することができます。HiveQL クエリは、HDInsight によって MapReduce にコンパイルされ、クラスターで実行されます。Hive にはその他にも次の利点があります。

- MapReduce フレームワークに慣れているプログラマは mapper と reducer を自分で作成し、HiveQL 言語の標準機能では実現できない高度な分析を実行することもできます。
- Hive は、膨大な不変データ (Web ログなど) のバッチ処理に最も適しています。データベース管理システムなど、極めて迅速な応答を求められるトランザクション アプリケーションには適していません。
- Hive は、優れた柔軟性 (Hadoop クラスターにコンピューターを動的に追加できる)、拡張性 (MapReduce フレームワーク内とその他のプログラミング インターフェイス)、耐障害性を実現するように最適化されています。ただし、遅延時間は設計上の優先事項ではありません。

## <a id="uploaddata"></a>Hive テーブルのデータのアップロード

HDInsight は、Hadoop クラスター対応の既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。クラスター プロビジョニングの一部としていくつかのサンプル データ ファイルが BLOB ストレージに追加されています。この記事では、 *log4j*サンプル ファイルを使用します。このファイルは、HDInsight クラスターと共に配布され、BLOB ストレージ コンテナーの下の **/example/data/sample.log** に格納されています。ファイル内の各ログは 1 行で、ログの種類と重要度を表す `[LOG LEVEL]` フィールドを含みます。次に例を示します。

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

前の例では、ログ レベルは ERROR です。

> [AZURE.NOTE] また、 [Apache Log4j][apache-log4j]ログ ユーティリティを使用して独自の log4j ファイルを生成し、それを BLOB コンテナーにアップロードすることもできます。手順については、[HDInsight でのデータのアップロード][hdinsight-upload-data]を参照してください。HDInsight と共に Azure BLOB ストレージを使用する方法の詳細については、[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage].を参照してください。

HDInsight では、**wasb** プレフィックスを使用して、BLOB ストレージに格納されたファイルにアクセスすることができます。たとえば、sample.log ファイルにアクセスするには、次の構文を使用します。

	wasb:///example/data/sample.log

WASB が HDInsight の既定のストレージであるため、 **/example/data/sample.log** を使用してファイルにアクセスできます。

> [AZURE.NOTE] 上の構文、**wasb:///** は HDInsight クラスターの既定のストレージ コンテナーに格納されたファイルにアクセスするために使用します。クラスターをプロビジョニングするときに追加のストレージ アカウントを指定し、その追加のアカウントに格納されたファイルにアクセスする必要がある場合、コンテナー名とストレージ アカウント アドレスを指定することによって、データにアクセスすることができます。たとえば、 **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**です。

## <a id="runhivequeries"></a> PowerShell を使用して Hive クエリを実行

Hive クエリは、**Start-AzureHDInsightJob** コマンドレットまたは **Invoke-Hive** コマンドレットを使用して PowerShell で実行できます。

* **Start-AzureHDInsightJob** はジョブの実行に汎用的に使用することができ、Hive、Pig、および MapReduce ジョブを HDInsight クラスターで開始するために使用します。**Start-AzureHDInsightJob** は非同期であり、ジョブが完了する前に返します。ジョブに関する情報が返されると、**Wait-AzureHDInsightJob**、**Stop-AzureHDInsightJob**、および **Get-AzureHDInsightJobOutput** コマンドレットで使用できます。  **Get-AzureHDInsightJobOutput** は、**STDOUT** または **STDERR** にジョブによって記述された情報を取得するために使用する必要があります。

* **Invoke-Hive** は Hive クエリを実行し、クエリが完了するのを待ちます。次に、1 つのアクションとしてクエリの出力を取得します。

1. Azure PowerShell コンソール ウィンドウを開きます。手順については、 [Azure PowerShell のインストールおよび構成][powershell-install-configure]を参照してください。
2. 次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。

2. 次のスクリプトで変数を設定して、スクリプトを実行します。

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. サンプル データを使用して、次のスクリプトを実行すると **log4jLogs** という名前の新しいテーブルが作成されます。

		# HiveQL
		# Create an EXTERNAL table
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	HiveQL ステートメントは次のアクションを実行します。

	* **DROP TABLE** - テーブルが既存の場合にテーブルとデータ ファイルを削除します。
	* **CREATE EXTERNAL TABLE** - Hive に新しく  'external' テーブルを作成します。外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。
	* **ROW FORMAT** - Hive にデータの形式を示します。ここでは、各ログのフィールドは、スペースで区切られています。
	* **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data directory) と、データはテキストとして格納されていることを示します。
	* **SELECT** - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。ここでは、この値を含む列が 3 行あるため、**3** という値が返されています。

	> [AZURE.NOTE] 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
	>
	> 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。


4. 次のスクリプトを実行して、前のクエリから、Hive ジョブ定義を作成します。

		# Create a Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	-File スイッチを使用して、HDFS 上の HiveQL スクリプト ファイルを指定することもできます。

5. 次のスクリプトを実行して、Hive ジョブを送信します。

		# Submit the job to the cluster
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

6. 次のスクリプトを実行して、Hive ジョブを送信し、ジョブの完了を待ちます。

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

7. 次のスクリプトを実行して、標準出力を表示します。

		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	結果は次のようになります。

		[ERROR] 3

	つまり、 *sample.log* ファイルには 3 件の ERROR ログがありました。

4. **Invoke-Hive** を使用するには、まず、使用するクラスターを設定する必要があります。

		# Connect to the cluster
		Use-AzureHDInsightCluster $clusterName

4. 新規作成するには、次のスクリプトを使用して 'internal'という名前のテーブル**エラー ログ**を使用して、 **Invoke-hive**コマンドレットです。

		# Run a query to create an 'internal' Hive table
		$response = Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		"@
		# print the output on the console
		Write-Host $response

	これらのステートメントは次のアクションを実行します。

	* **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。 **EXTERNAL** キーワードが使用されていないため、これは  'internal' テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。 
	* **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
	* **INSERT OVERWRITE ... SELECT** -、 **[エラー]**を含む**log4jLogs**テーブルから行を選択、**エラー ログ**テーブルへデータの挿入

	> [AZURE.NOTE] **EXTERNAL**テーブルとは異なり、内部デーブルを削除すると、基盤となるデータは削除されます。

	出力は次のようになります。

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	> [AZURE.NOTE] より長い HiveQL クエリの場合は、PowerShell の Here-Strings または HiveQL スクリプト ファイルを使用できます。次のスニペットでは、 *Invoke-Hive* コマンドレットを使用して HiveQL スクリプト ファイルを実行する方法を示します。HiveQL スクリプト ファイルは、WASB にアップロードする必要があります。
	>
	> `呼び出すには. - ファイル"wasb://< ContainerName > @< StorageAccountName >/< パス >/query.hql"`
	>
	> Here-Strings の詳細については、[Windows PowerShell Here-Strings の使用][powershell-here-strings]を参照してください。

5. **errorLogs** テーブルに格納された、t4 列に **[ERROR]** を含む列のみを確認するには、次のステートメントを使用して、**errorLogs**列からすべての列を返します。　

		#Select all rows
		$response = Invoke-Hive -Query "SELECT * from errorLogs;"
		Write-Host $response

	3 つのデータ行が返され、各行の t4 列には **[ERROR]** が含まれます。


> [AZURE.NOTE] 必要な場合は、クエリの出力を Microsoft Excel にインポートしてさらに分析できます。手順については、[Power Query を使用した Excel から Hadoop への接続][import-to-excel]を参照してください。

## <a id="runhivefromvs"></a>Visual Studio を使用して Hive クエリを実行
HDInsight Tools for Visual Studio は、Azure SDK for .NET バージョン 2.5 以降に付属しています。  Visual Studio からこのツールを使用すると、HDInsight クラスターに接続し、Hive テーブルを作成し、Hive クエリを実行できます。  詳細については、[Get started using HDInsight Hadoop Tools for Visual Studio (Visual Studio 用 HDInsight Hadoop ツールの使用)][1]を参照してください。



## <a id="usetez"></a>パフォーマンスを改善するための Tez の使用方法

[Apache Tez][apache-tez]　は、Hive などの大量のデータを扱うアプリケーションを同じ規模で遥かに効率的に実行可能にするフレームワークです。HDInsight の最新リリースでは、Hive を Tez 上で実行できます。  この機能は、現在、既定では無効のため、有効にする必要があります。  将来のクラスター バージョンでは、既定で有効に設定されます。Tez を活用するために、Hive クエリに次の値を設定する必要があります。

		set hive.execution.engine=tez;

これは、クエリの先頭に配置することで、クエリ単位で送信できます。  また、クラスターの作成時に構成値を設定することで、この機能が既定で有効になるようにクラスターを設定できます。  詳細については、  [HDInsight クラスターのプロビジョニング][hdinsight-provision]を参照してください。

[「Hive on Tez」設計ドキュメント][hive-on-tez-wiki]には、実装の選択肢および構成の調整に関する詳細が記載されています。


## <a id="nextsteps"></a>次のステップ

Hive では、SQL に似たクエリ言語を使用してデータを容易に照会できます。さらに、HDInsight の他のコンポーネントを使用すれば、データ移動や変換などを行うこともできます。詳細については、次の記事を参照してください。

* [HDInsight Hadoop Tools for Visual Studio の使用開始][1]
* [HDInsight での Oozie の使用][hdinsight-use-oozie]
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]
* [HDInsight での Pig の使用](../hdinsight-use-pig/)
* [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-data]
* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation]
* [データを HDInsight へアップロード][hdinsight-upload-data]
* [Azure の HDInsight の概要](../hdinsight-get-started/)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
<!--HONumber=42-->
