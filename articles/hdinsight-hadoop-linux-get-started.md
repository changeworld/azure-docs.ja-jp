<properties 
   pageTitle="Linux 上の HDInsight で Hive と Hadoop を使用する | Azure" 
   description="Linux 上の HDInsight で Hadoop を使用する方法について説明します。Linux で実行する HDInsight Hadoop クラスターをプロビジョニングする方法と、Hive でデータを問い合わせる方法について説明します。" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>

# Linux 上の HDInsight で Hive と Hadoop を使用する (プレビュー)

このチュートリアルでは、Linux 上の HDInsight の基本的な使用方法を紹介するために、Linux 上で HDInsight Hadoop クラスターをプロビジョニングする方法と、Hive クエリを実行して非構造化データから意味のある情報を抽出する方法を説明します。その後、Tableau などの Business Intelligence (BI) ツールで抽出結果を分析します。


> [AZURE.NOTE] Hadoop とビッグ データを初めて扱う方は、 <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>、 <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>、 <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS</a>、および <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>の説明を参照してください。HDInsight によって Azure でどのように Hadoop を利用できるかについては、「[Introduction to Hadoop in HDInsight (HDInsight の Hadoop 入門)](../hdinsight-hadoop-introduction/)」を参照してください。


## このチュートリアルで目的とする操作 ##

構造化されていないデータ セットが大量にあり、このデータ セットに対してクエリを実行して、意味のある情報を抽出したいとします。これを実現するには、次の手順を実行します。

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Tableau.](./media/hdinsight-hadoop-linux-get-started/HDI.Linux.GetStartedFlow.png)


**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。


- Azure サブスクリプション。サブスクリプション取得の詳細については、「 <a href="http://azure.microsoft.com/ pricing/purchase-options/" target="_blank">購入オプション</a>、 <a href="http://azure.microsoft.com/ pricing/member-offers/" target="_blank">メンバー プラン</a>、または <a href="http://azure.microsoft.com/ pricing/free-trial/" target="_blank">無料評価版</a>の説明を参照してください。

**推定所要時間:** 30 分

## このチュートリアルの内容

* [Azure のストレージ アカウントの作成](#storage)
* [HDInsight Linux クラスターのプロビジョニング](#provision)
* [Hive ジョブの送信](#hivequery)
* [次のステップ](#nextsteps)

## <a name="storage"></a>Azure のストレージ アカウントの作成

HDInsight では、データを格納するために Azure BLOB ストレージを使用します。これは、 *WASB* または  *Azure Storage - Blob* と呼ばれます。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[Use Azure Blob storage with HDInsight (HDInsight での Azure BLOB ストレージの使用)](../hdinsight-use-blob-storage/)」を参照してください。

HDInsight クラスターをプロビジョニングするときは、Azure Storage アカウントを指定します。HDFS と同様、このアカウントの特定の BLOB ストレージ コンテナーが、既定のファイル システムとして設定されます。既定では、HDInsight クラスターは、指定されたストレージ アカウントと同じデータ センターにプロビジョニングされます。

このストレージ アカウントに加えて、HDInsight クラスターの構成をカスタマイズするときに他のストレージ アカウントを追加できます。この追加のストレージ アカウント用には、同じ Azure サブスクリプションを使用することも異なる Azure サブスクリプションを使用することもできます。手順については、「[Provision HDInsight Linux clusters using custom options (カスタム オプションを使用した HDInsight Linux クラスターのプロビジョニング)](../hdinsight-hadoop-provision-linux-clusters)」を参照してください。 

このチュートリアルでは、わかりやすくするために、既定の BLOB コンテナーと既定のストレージ アカウントが使用されています。実際には、データ ファイルを専用のストレージ アカウントに格納するのが一般的です。

**Azure Storage アカウントを作成するには**

1.  <a href="https://manage.windowsazure.com/" target="_blank">Azure 管理ポータル</a>にサインインします。
2. 左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

	![Azure portal where you can use Quick Create to set up a new storage account.](./media/hdinsight-hadoop-linux-get-started/HDI.StorageAccount.QuickCreate.png)

3. **[URL]**、**[場所]**、および **[レプリケーション]** に値を入力し、**[ストレージ アカウントの作成]** をクリックします。アフィニティ グループはサポートされていません。新しいストレージ アカウントがストレージ一覧に表示されます。

	>[AZURE.NOTE]  このチュートリアルで使用したような、HDInsight Linux クラスターをプロビジョニングする簡易作成オプションでは、クラスターをプロビジョニングする際に場所をたずねるメッセージが表示されません。クラスターは、既定でストレージ アカウントと同じデータ センターに配置されます。そのため、ストレージ アカウントは、クラスターでサポートされる場所**(東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、**米国中南部**) のみに作成してください。

4. 新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。
5. 一覧の新しいストレージ アカウントを選択し、ページの下部の **[アクセス キーの管理]** をクリックします。
7. **[ストレージ アカウント名]** と **[プライマリ アクセス キー]** (または **[セカンダリ アクセス キー]**) の値をメモします。どちらのキーでもかまいません。この情報は後で必要になります。


詳細については、「
[How to Create a Storage Account (ストレージ アカウントの作成方法)](../storage-create-storage-account/)」と「[Use Azure Blob Storage with HDInsight (HDInsight での Azure BLOB ストレージの使用)](../hdinsight-use-blob-storage/)」を参照してください。
	
## <a name="provision"></a>Linux での HDInsight クラスターのプロビジョニング

HDInsight クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。このセクションでは、簡易作成オプションを使用して、Linux 上に HDInsight クラスターをプロビジョニングします。このオプションでは、既定のユーザー名と Azure ストレージ コンテナーを使用して、Ubuntu 12.04 LTS で実行されている HDInsight Version 3.2 (Hadoop Version 2.5、HDP Version 2.2) をクラスターに構成します。異なる HDInsight バージョンとその SLA については、[HDInsight コンポーネントのバージョン](http://azure.microsoft.com/ documentation/articles/hdinsight-component-versioning/)に関するページを参照してください。

>[AZURE.NOTE]  Windows Server OS を実行する Hadoop クラスターを作成することもできます。手順については、「[Get Started with HDInsight on Windows (Windows での HDInsight の概要)](../hdinsight-get-started/)」を参照してください。


**HDInsight クラスターをプロビジョニングするには** 

1.  <a href="https://manage.windowsazure.com/" target="_blank">Azure 管理ポータル</a>にサインインします。 

2. 左下にある **[新規]** をクリックし、**[データ サービス]**、**[HDInsight]**、**[Hadoop on Linux]** の順にクリックします。

	![Creation of a Hadoop cluster in HDInsight.](./media/hdinsight-hadoop-linux-get-started/HDI.QuickCreateCluster.png)

4. 次の値を入力または選択します。

	<table border="1">
	<tr><th>名前</th><th>値</th></tr>
	<tr><td>クラスター名</td><td>クラスターの名前です。</td></tr>
	<tr><td>クラスター サイズ</td><td>デプロイするデータ ノードの数です。既定値は 4 ですが、ボックスの一覧で 1 つまたは 2 つのデータ ノードを使用するオプションも選択できます。[カスタム作成] オプションを使用すれば、クラスター ノードの数を自由に指定できます。 <strong>カスタム作成</strong> オプションを使用してテーブルを作成する必要があります。各種クラスター サイズの料金を確認するには、このボックスの <strong>[?]</strong> [?] マークをクリックして、表示されるリンクをクリックします。</td></tr>
	<tr><td>パスワード</td><td>admin <i>HTTP</i> アカウント (既定のユーザー名: admin) および <i>SSH</i> アカウント (既定のユーザー名: hdiuser)。これらはクラスターがプロビジョニングされた VM の Windows 管理者アカウントではないことに注意してください。 </td></tr>
	
	<tr><td>ストレージ アカウント</td><td>作成したストレージ アカウントをボックスの一覧から選択します。 <br/>

	ストレージ アカウントは、いったん選択すると、変更することはできません。ストレージ アカウントを削除すると、関連付けたクラスターを使用できなくなります。

	HDInsight クラスターは、ストレージ アカウントと同じデータセンターに配置されます。 
	</td></tr>
	</table>

	クラスター名をメモに記録しておきます。この情報は後で必要になります。

	
5. **[HDInsight クラスターの作成]** をクリックします。プロビジョニングが完了すると、[状態] 列に **[実行中]** と表示されます。

	>[AZURE.NOTE] この手順では、既定の SSH ユーザー名と Azure ストレージ コンテナーを使用する簡易作成オプションを指定して、Linux クラスターを作成します。認証に SSH キーを使用する、追加のストレージ アカウントを使用するなどのカスタム オプションを指定してクラスターを作成するには、「[Provision HDInsight Linux clusters using custom options (カスタム オプションを使用した HDInsight Linux クラスターのプロビジョニング)](../hdinsight-hadoop-provision-linux-clusters)」を参照してください。


## <a name="hivequery"></a>クラスターでの Hive ジョブの送信
HDInsight Linux クラスターがプロビジョニングされたら、HDInsight クラスターに付属するサンプル データ (sample.log) を問い合わせるサンプルの Hive ジョブを実行します。サンプル データには、トレース、警告、情報、エラーなどのログ情報が含まれています。このデータを問い合わせて、特定の重大度を持つエラー ログをすべて取得します。HDInsight Linux クラスターで Hive クエリを実行するには、次の手順を実行する必要があります。

- Linux クラスターへの接続
- Hive ジョブを実行する



### クラスターに接続するには

Linux 上の HDInsight クラスターには、Linux コンピューターまたは Windows コンピューターから SSH を使用して接続できます。

**Linux コンピューターから接続するには**

1. ターミナルを開き、次のコマンドを入力します。

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	簡易作成オプションを使用してクラスターをプロビジョニングしたため、既定の SSH ユーザー名は **hdiuser** です。したがって、コマンドは次のようになります。

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. プロンプトが表示されたら、クラスターのプロビジョニング時に入力したパスワードを入力します。正常に接続されると、プロンプトが次のように変わります。

		hdiuser@headnode-0:~$


**Windows コンピューターから接続するには**

1. Windows クライアント用の **PuTTY** をダウンロードします。次の場所からダウンロードできます。 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

2. **PuTTY** を開きます。**[Category]** で、**[Session]** をクリックします。**[Basic options for your PuTTY session]** 画面で、**[Host name (or IP address)]** フィールドに HDInsight サーバーの SSH アドレスを入力します。SSH アドレスは、クラスター名に続けて「**-ssh.azurehdinsight.net**」と入力します。たとえば、**myhdinsightcluster-ssh.azurehdinsight.net** のようになります。

	![Connect to an HDInsight cluster on Linux using PuTTY](./media/hdinsight-hadoop-linux-get-started/HDI.linux.connect.putty.png)

3. 後で使用するために接続情報を保存するには、**[Saved Sessions]** にこの接続の名前を入力し、**[Save]** をクリックします。接続が、保存済みセッションの一覧に追加されます。

4. **[Open]** をクリックして、クラスターに接続します。ユーザー名の入力を求められたら、「 *hdiuser*」を入力します。パスワードには、クラスターのプロビジョニング時に指定したパスワードを入力します。正常に接続されると、プロンプトが次のように変わります。

		hdiuser@headnode-0:~$

### Hive ジョブを実行するには

SSH を使用してクラスターに接続したら、次のコマンドを使用して Hive クエリを実行します。

1. プロンプトで次のコマンドを入力して、Hive コマンド ライン インターフェイス (CLI) を開始します。

		hive

2. CLI を使用して次のステートメントを入力し、クラスターにあらかじめ用意されているサンプル データを使用して、log4jLogs という名前の新しいテーブルを作成します。

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	これらのステートメントは次のアクションを実行します。

	- **DROP TABLE** - テーブルが既存の場合にテーブルとデータ ファイルを削除します。
	- **CREATE EXTERNAL TABLE** - Hive に新しく  'external' テーブルを作成します。外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。
	- **ROW FORMAT** - Hive にデータの形式を示します。ここでは、各ログのフィールドは、スペースで区切られています。
	- **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data ディレクトリ) と、データはテキストとして格納されていることを示します。
	- **SELECT** - t4 列の値が [ERROR] であるすべての行の数を返します。 

	>[WACOM.NOTE] 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

	返される出力は次のとおりです。

		Query ID = hdiuser_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
		Total jobs = 1
		Launching Job 1 out of 1
		Number of reduce tasks not specified. Estimated from input data size: 1
		In order to change the average load for a reducer (in bytes):
		  set hive.exec.reducers.bytes.per.reducer=<number>
		In order to limit the maximum number of reducers:
		  set hive.exec.reducers.max=<number>
		In order to set a constant number of reducers:
		  set mapreduce.job.reduces=<number>
		Starting Job = job_1421200049012_0006, Tracking URL = <URL>:8088/proxy/application_1421200049012_0006/
		Kill Command = /usr/hdp/2.2.1.0-2165/hadoop/bin/hadoop job  -kill job_1421200049012_0006
		Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
		2015-01-16 00:02:40,823 Stage-1 map = 0%,  reduce = 0%
		2015-01-16 00:02:55,488 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.32 sec
		2015-01-16 00:03:05,298 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 5.62 sec
		MapReduce Total cumulative CPU time: 5 seconds 620 msec
		Ended Job = job_1421200049012_0006
		MapReduce Jobs Launched: 
		Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 5.62 sec   HDFS Read: 0 HDFS Write: 0 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 620 msec
		OK
		[ERROR]    3
		Time taken: 60.991 seconds, Fetched: 1 row(s)

	ここでは、この値を含む列が 3 行あるため、出力には **[ERROR]  3** が返されています。

3. 次のステートメントを使用して、**errorLogs** という名前の新しい "内部" テーブルを作成します。

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	これらのステートメントは次のアクションを実行します。

	- **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。 EXTERNAL キーワードが使用されていないため、これは  'internal' テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。**EXTERNAL**テーブルとは異なり、内部デーブルを削除すると、基盤となるデータは削除されます。
	- **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
	- **INSERT OVERWRITE ... SELECT** - [ERROR] を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。

4. t4 列に **[ERROR]** が含まれる列のみが **errorLogs** テーブルに格納されたことを確認するには、errorLogs からすべての列を返す次のステートメントを使用します。

		SELECT * from errorLogs;

	The following output should be displayed on the console.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	返されるデータはすべて、[ERROR] ログに対応しています。


## <a name="nextsteps"></a>次のステップ
このチュートリアルでは、HDInsight を使用して Hadoop Linux クラスターをプロビジョニングする方法と、SSH を使用してそのクラスターに Hive クエリを実行する方法を確認しました。詳細については、次の記事を参照してください。

- [カスタム オプションを使用した Linux での HDInsight のプロビジョニング](../hdinsight-hadoop-provision-linux-clusters)に関するページ
- [Linux での HDInsight の使用](../hdinsight-hadoop-linux-information)に関するページ
- [Ambari を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari)
- [HDInsight での MapReduce の使用][hdinsight-use-mapreduce]
- [HDInsight での Hive の使用][hdinsight-use-hive]に関するページ
- [HDInsight での Pig の使用][hdinsight-use-pig]に関するページ
- [HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage)
- [HDInsight へのデータのアップロード][hdinsight-upload-data]に関するページ


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install

[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=45--> 
