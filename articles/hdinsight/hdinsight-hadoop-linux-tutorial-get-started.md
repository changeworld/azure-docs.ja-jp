<properties
   pageTitle="Linux のチュートリアル: Hadoop と Hive の使用 | Microsoft Azure"
   description="HDInsight で Hadoop を使用するには、この Linux チュートリアルに従います。Linux のクラスターをプロビジョニングする方法、および Hive でデータを照会する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/13/2015"
   ms.author="nitinme"/>

# Hadoop チュートリアル: Linux 上の HDInsight で Hive と Hadoop を使用する (プレビュー)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

この Hadoop チュートリアルでは、Linux 上の Azure HDInsight の基本的な使用方法を紹介するために、Linux 上で Hadoop クラスターをプロビジョニングする方法と、Hive クエリを実行して非構造化データから意味のある情報を抽出する方法を説明します。


> [AZURE.NOTE]Hadoop とビッグ データを初めて扱う方は、<a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>、<a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>、<a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">Hadoop Distributed File System (HDFS)</a>、<a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a> に関するトピックをご覧ください。HDInsight によって Azure でどのように Hadoop を利用できるかについては、「[Introduction to Hadoop in HDInsight (HDInsight の Hadoop 入門)](hdinsight-hadoop-introduction.md)」を参照してください。


## このチュートリアルで目的とする操作 ##

構造化されていないデータ セットが大量にあり、このデータ セットに対してクエリを実行して、意味のある情報を抽出したいとします。これを実現するには、次の手順を実行します。

   ![Hadoop チュートリアルの手順: Storage アカウントを作成します。Hadoop クラスターをプロビジョニングします。Hive でデータを照会します。](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.Linux.GetStartedFlow.png)


## 前提条件

Linux でこの Hadoop チュートリアルを開始する前に、以下の条件を満たしている必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

**推定所要時間:** 30 分

## このチュートリアルの内容

* [Azure のストレージ アカウントの作成](#storage)
* [HDInsight Linux クラスターのプロビジョニング](#provision)
* [クラスターでの Hive ジョブの送信](#hivequery)
* [次のステップ](#nextsteps)

## <a name="storage"></a>Azure ストレージ アカウントの作成

HDInsight では、データを格納するために Azure BLOB ストレージを使用します。詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」をご覧ください。

HDInsight クラスターをプロビジョニングするときは、Azure Storage アカウントを指定します。HDFS と同様、このアカウントの特定の BLOB ストレージ コンテナーが、既定のファイル システムとして設定されます。既定では、HDInsight クラスターは、指定されたストレージ アカウントと同じデータ センターにプロビジョニングされます。

このストレージ アカウントに加えて、HDInsight クラスターの構成をカスタマイズするときに他のストレージ アカウントを追加できます。これらの追加のストレージ アカウント用には、同じ Azure サブスクリプションを使用することも異なる Azure サブスクリプションを使用することもできます。手順については、「[カスタム オプションを使用した HDInsight での Linux クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。

このチュートリアルでは、わかりやすくするために、既定の BLOB コンテナーと既定のストレージ アカウントが使用されています。実際には、データ ファイルを専用のストレージ アカウントに格納するのが一般的です。

**Azure ストレージ アカウントを作成するには**

1. <a href="https://manage.windowsazure.com/" target="_blank">Azure ポータル</a>にサインインします。
2. 左下隅にある **[新規]** をクリックし、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

	![簡易作成を使用して新しいストレージ アカウントを設定できる Azure ポータル。](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.StorageAccount.QuickCreate.png)

3. **[URL]**、**[場所]**、**[レプリケーション]** に情報を入力し、**[ストレージ アカウントの作成]** をクリックします。アフィニティ グループはサポートされていません。新しいストレージ アカウントがストレージ一覧に表示されます。

	>[AZURE.NOTE]このチュートリアルで使用したような、HDInsight Linux クラスターをプロビジョニングする簡易作成オプションでは、クラスターをプロビジョニングする際に場所をたずねるメッセージが表示されません。クラスターは、既定でストレージ アカウントと同じデータ センターに配置されます。そのため、ストレージ アカウントは、クラスターでサポートされる場所 (**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、**米国中南部**) に作成してください。

4. 新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。
5. 一覧の新しいストレージ アカウントを選択し、ページの下部の **[アクセス キーの管理]** をクリックします。
7. **[ストレージ アカウント名]** と **[プライマリ アクセス キー]** (または **[セカンダリ アクセス キー]** の動作しているいずれかのキー) の情報を書き留めます。この情報は後で必要になります。


詳細については、「[ストレージ アカウントの作成方法](../storage-create-storage-account.md)」と「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)￼」に関するページをご覧ください。

## <a name="provision"></a>Linux での HDInsight クラスターのプロビジョニング

HDInsight クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。このセクションでは、簡易作成オプションを使用して、Linux 上に HDInsight クラスターをプロビジョニングします。このオプションでは、既定のユーザー名と Azure ストレージ コンテナーを使用して、Ubuntu 12.04 Long Term Support (LTS) で実行されている HDInsight バージョン 3.2 (Hadoop バージョン 2.6、HDP バージョン 2.2) をクラスターに構成します。異なる HDInsight バージョンとそのサービス レベル アグリーメントについては、「[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md)」に関するページをご覧ください。

>[AZURE.NOTE]Windows Server オペレーティング システムを実行する Hadoop クラスターを作成することもできます。手順については、「[HDInsight の概要](../hdinsight-get-started.md)」に関するページをご覧ください


**HDInsight クラスターをプロビジョニングするには**

1. <a href="https://manage.windowsazure.com/" target="_blank">Azure ポータル</a>にサインインします。

2. 左下にある **[新規]** をクリックし、**[データ サービス]**、**[HDInsight]**、**[Hadoop on Linux]** の順にクリックします。

	![HDInsight での Hadoop クラスターの作成。](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.QuickCreateCluster.png)

4. 次の値を入力または選択します。

	<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td>クラスター名</td><td>クラスターの名前です。</td></tr>
<tr><td>クラスター サイズ</td><td>デプロイするデータ ノードの数です。既定値は 4 ですが、ボックスの一覧で 1 つまたは 2 つのデータ ノードを使用するオプションも選択できます。<strong>[カスタム作成]</strong> オプションを使用すれば、クラスター ノードの数を自由に指定できます。各種クラスター サイズの料金を確認するには、このボックスのすぐ上にある <strong>[?]</strong> マークをクリックして、表示されるリンクをクリックします。</td></tr>
<tr><td>パスワード</td><td><i>HTTP</i> アカウント (既定のユーザー名: admin) と <i>SSH</i> アカウント (既定のユーザー名: hdiuser) のパスワードです。これらはクラスターがプロビジョニングされた仮想マシンの管理者アカウントではないことに注意してください。</td></tr>

<tr><td>ストレージ アカウント</td><td>作成したストレージ アカウントをボックスの一覧から選択します。<br/>

ストレージ アカウントは、いったん選択すると、変更することはできません。ストレージ アカウントを削除すると、関連付けたクラスターを使用できなくなります。HDInsight クラスターは、ストレージ アカウントと同じデータセンターに配置されます。
</td></tr>
</table>クラスター名をメモに記録しておきます。この情報は後で必要になります。


5. **[HDInsight クラスターの作成]** をクリックします。プロビジョニングが完了すると、[状態] 列に **[実行中]** と表示されます。

	>[AZURE.NOTE]この手順では、既定の SSH ユーザー名と Azure ストレージ コンテナーを使用する簡易作成オプションを指定して、Linux クラスターを作成します。認証に SSH キーを使用する、追加のストレージ アカウントを使用するなどのカスタム オプションを指定してクラスターを作成するには、「[カスタム オプションを使用した HDInsight Linux クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。


## <a name="hivequery"></a>クラスターでの Hive ジョブの送信
HDInsight Linux クラスターがプロビジョニングされたら、HDInsight クラスターに付属するサンプル データ (sample.log) を問い合わせるサンプルの Hive ジョブを実行します。サンプル データには、トレース、警告、情報、エラーなどのログ情報が含まれています。このデータを問い合わせて、特定の重大度を持つエラー ログをすべて取得します。HDInsight Linux クラスターで Hive クエリを実行するには、次の手順を実行する必要があります。

- Linux クラスターへの接続
- Hive ジョブを実行する



### クラスターに接続するには

Linux 上の HDInsight クラスターには、Linux コンピューターまたは Windows ベースのコンピューターから SSH を使用して接続できます。

**Linux コンピューターから接続するには**

1. ターミナルを開き、次のコマンドを入力します。

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	簡易作成オプションを使用してクラスターをプロビジョニングしたため、既定の SSH ユーザー名は **hdiuser** です。したがって、コマンドは次のようになります。

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. プロンプトが表示されたら、クラスターのプロビジョニング時に入力したパスワードを入力します。正常に接続されると、プロンプトが次のように変わります。

		hdiuser@headnode-0:~$


**Windows ベースのコンピューターから接続するには**

1. Windows ベースのクライアント用の <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a> をダウンロードします。

2. PuTTY を開きます。**[Category]** で、**[Session]** をクリックします。**[PuTTY セッションの基本設定]** 画面で、**[ホスト名 (または IP アドレス)]** フィールドに HDInsight サーバーの SSH アドレスを入力します。SSH アドレスは、クラスター名に続けて「-ssh.azurehdinsight.net」と入力します (**myhdinsightcluster-ssh.azurehdinsight.net** など)。

	![PuTTY を使用した Linux での HDInsight クラスターへの接続。](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. 後で使用するために接続情報を保存するには、**[保存されたセッション]** にこの接続の名前を入力し、**[保存]** をクリックします。接続が、保存済みセッションの一覧に追加されます。

4. **[開く]** をクリックして、クラスターに接続します。ユーザー名の入力を求められたら、「**hdiuser**」と入力します。パスワードには、クラスターのプロビジョニング時に指定したパスワードを入力します。正常に接続されると、プロンプトが次のように変わります。

		hdiuser@headnode-0:~$

### Hive ジョブを実行するには

SSH を使用してクラスターに接続したら、次のコマンドを使用して Hive クエリを実行します。

1. プロンプトで次のコマンドを入力して、Hive コマンド ライン インターフェイス (CLI) を開始します。

		hive

2. CLI を使用して次のステートメントを入力し、クラスターにあらかじめ用意されているサンプル データを使用して、**log4jLogs** という名前の新しいテーブルを作成します。

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	これらのステートメントは次のアクションを実行します。

	- **DROP TABLE** - テーブルが既存の場合にテーブルとデータ ファイルを削除します。
	- **CREATE EXTERNAL TABLE** - Hive に新しく ”外部” テーブルを作成します。外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。
	- **ROW FORMAT** - Hive にデータの形式を示します。ここでは、各ログのフィールドは、スペースで区切られています。
	- **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data ディレクトリ) と、データはテキストとして格納されていることを示します。
	- **SELECT** - t4 列の値が [ERROR] であるすべての行の数を指定します。

	>[AZURE.NOTE]基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。外部テーブルを削除しても、データは削除*されません*。テーブル定義のみが削除されます。

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

	ここでは、この値を含む行が 3 行あるため、出力には **[ERROR] 3** が返されています。

3. 次のステートメントを使用して、**errorLogs** という名前の新しい "内部" テーブルを作成します。

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	これらのステートメントは次のアクションを実行します。

	- **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。**EXTERNAL** キーワードが使用されていないため、これは内部テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。外部テーブルとは異なり、内部テーブルを削除すると、基盤となるデータも削除されます。
	- **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
	- **INSERT OVERWRITE ...SELECT** - [ERROR] を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。

4. **errorLogs** テーブルに格納された、t4 列に [ERROR] を含む列のみを確認するには、次のステートメントを使用して、**errorLogs** 列からすべての列を返します。

		SELECT * from errorLogs;

	次の出力がコンソールに表示されます。

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	返されるデータはすべて、[ERROR] ログに対応しています。


## <a name="nextsteps"></a>次のステップ
この Linux チュートリアルでは、HDInsight を使用して Linux での Hadoop クラスターをプロビジョニングする方法と、SSH を使用してそのクラスターに Hive クエリを実行する方法を確認しました。詳細については、次の記事を参照してください。

- [カスタム オプションを使用した Linux での HDInsight のプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)
- [Linux での HDInsight の使用](hdinsight-hadoop-linux-information.md)
- [Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)
- [HDInsight での MapReduce の使用][hdinsight-use-mapreduce]
- [HDInsight での Hive の使用][hdinsight-use-hive]
- [HDInsight での Pig の使用][hdinsight-use-pig]
- [HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)
- [HDInsight へのデータのアップロード][hdinsight-upload-data]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
 

<!---HONumber=July15_HO2-->