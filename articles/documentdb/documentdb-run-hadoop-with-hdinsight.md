<properties 
	pageTitle="DocumentDB と HDInsight を使用した Hadoop ジョブの実行 | Microsoft Azure" 
	description="DocumentDB と Azure HDInsight を使用してシンプルな Hive、Pig、および MapReduce ジョブを実行する方法を説明します。"
	services="documentdb" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="mimig"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="anhoh"/>

#<a name="DocumentDB-HDInsight"></a>DocumentDB と HDInsight を使用した Hadoop ジョブの実行

このチュートリアルでは、DocumentDB の Hadoop コネクタを使用して、 [Apache Hive][apache-hive]、[Apache Pig][apache-pig]、および [Apache Hadoop][apache-hadoop] MapReduce の各ジョブを Azure HDInsight 上で実行する方法について説明します。DocumentDB の Hadoop コネクタを使用すると、DocumentDB が Hive、Pig、および MapReduce の各ジョブに対してソースとシンクの両方として機能します。このチュートリアルでは、Hadoop ジョブのデータ ソースと出力先の両方に DocumentDB を使用します。

このチュートリアルを完了すると、次の項目について説明できるようになります。

- Hive、Pig、または MapReduce ジョブを使用して DocumentDB からどのようにしてデータを読み込むか。
- Hive、Pig、または MapReduce ジョブを使用して DocumentDB にどのようにしてデータを格納するか。

まずは、次のビデオを視聴することをお勧めします。このビデオでは、DocumentDB と HDInsight を使用して Hive ジョブがひととおり実行されます。

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

その後で、この記事に戻ってください。この記事では、DocumentDB データに対して分析ジョブを実行する方法について詳しく説明します。

> [AZURE.TIP]このチュートリアルは、これまでに Apache Hadoop、Hive、Pig の少なくとも 1 つを使用した経験があることを前提としています。Apache Hadoop、Hive、Pig の使用経験がない場合は、[Apache Hadoop のドキュメント][apache-hadoop-doc]を参照することをお勧めします。さらに、このチュートリアルでは、DocumentDB の使用経験があり、DocumentDB アカウントを持っていることを想定しています。DocumentDB を初めて扱う方や DocumentDB アカウントを持っていない方は、[使用の開始に関するページ][getting-started]を参照してください。

チュートリアルを完了する時間がなく、Hive、Pig、および MapReduce の完全なサンプル PowerShell スクリプトが必要な場合は、 [こちら][documentdb-hdinsight-samples]をクリックすると入手できます。ダウンロードには、これらのサンプルの hql、pig、および java の各ファイルも含まれています。

## <a name="NewestVersion"></a>最新バージョン

<table border='1'>
	<tr><th>Hadoop コネクタのバージョン</th>
		<td>1.1.0</td></tr>
	<tr><th>スクリプト URI</th>
		<td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v03.ps1</td></tr>
	<tr><th>更新日時</th>
		<td>07/20/2015</td></tr>
	<tr><th>サポートされる HDInsight のバージョン</th>
		<td>3.1、3.2</td></tr>
	<tr><th>変更履歴</th>
		<td>DocumentDB Java SDK 1.1.0 への更新</br>
			カスタム インデックス作成パスの追加出力パラメーターの削除</br>
			カスタム文字列の有効桁数 (既定で-1) のオプションのパラメーターの追加</br>
			6/11/2015</br>
			コネクタと <a href="https://www.microsoft.com/download/details.aspx?id=40886">Microsoft Hive ODBC ドライバー</a>の互換性修正</br>
			出力コレクションのプランの種類 (既定では S3 プラン) を変更する機能追加</br>
		</td></tr>
</table>

## <a name="Prerequisites"></a>前提条件
このチュートリアルの手順を実行する前に、次のものを備えておく必要があります。

- DocumentDB アカウント、データベース、およびドキュメントが含まれているコレクション詳細については、[DocumentDB の使用に関するページ][getting-started]を参照してください。[DocumentDB インポート ツール][documentdb-import-data]を使用して、DocumentDB アカウントにサンプル データをインポートします。
- スループット。HDInsight の読み取りと書き込みは、コレクションに割り当てられた要求単位に達するまでカウントされます。詳細については、[プロビジョニング済みスループット、要求単位、およびデータベース操作に関するページ][documentdb-manage-throughput]を参照してください。
- 各出力コレクションに含まれる追加のストアド プロシージャ用の容量。ストアド プロシージャは、生成されたドキュメントを転送するために使用されます。詳細については、「[コレクションとプロビジョニング済みスループット][documentdb-manage-document-storage]」を参照してください。
- Hive、Pig、または MapReduce のジョブから生成されたドキュメント用の容量。詳細については、「[DocumentDB の容量とパフォーマンスの管理][documentdb-manage-collections]」を参照してください。
- *オプション*: 追加のコレクション用の容量。詳細については、「[プロビジョニング済みドキュメント ストレージとインデックス オーバーヘッド][documentdb-manage-document-storage]」を参照してください。
	
> [AZURE.WARNING]何かのジョブの実行中に新しいコレクションが作成されることを避けるために、stdout に結果を書き出して出力を WASB コンテナーに格納するか、既存のコレクションを指定することができます。既存のコレクションを指定した場合、新しいドキュメントはコレクション内に作成され、*ID* に競合がない限り既存のドキュメントに影響は及びません。**ID の競合がある場合は、既存のドキュメントがコネクタによって自動的に上書きされます**。この機能を無効にするには、upsert オプションを false に設定します。upsert が false に設定された状態で競合が発生すると、Hadoop ジョブは失敗し、ID 競合エラーが報告されます。

## <a name="CreateStorage"></a>手順 1: Azure のストレージ アカウントを作成する

> [AZURE.IMPORTANT]**既に** Azure ストレージ アカウントを所有していて、そのアカウント内に BLOB コンテナーを新規作成する場合は、この手順を省略して「[手順 2: カスタマイズした HDInsight クラスターを作成する](#ProvisionHDInsight)」に進んでください。

Azure HDInsight では、データの格納に Azure BLOB ストレージを使用します。これは *WASB* または *Azure ストレージ - BLOB* と呼ばれています。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。

HDInsight クラスターをプロビジョニングするときは、Azure ストレージ アカウントを指定します。HDFS と同様、このアカウントの特定の BLOB ストレージ コンテナーが、既定のファイル システムとして設定されます。既定では、HDInsight クラスターは、指定されたストレージ アカウントと同じデータ センターにプロビジョニングされます。

**Azure ストレージ アカウントを作成するには**

1. [Azure 管理ポータル][azure-classic-portal]にサインインします。
	
	> [AZURE.NOTE]現在、Azure HDInsight は Azure の管理ポータルでサポートされていますが、Azure DocumentDB は Microsoft Azure ポータルにしかありません。

2. 左下隅にある **[+ 新規]** をクリックし、**[DATA SERVICES]**、**[STORAGE]**、**[簡易作成]** の順にクリックします。![簡易作成を使用して新しいストレージ アカウントを設定できる Azure ポータル。][image-storageaccount-quickcreate]

3. **URL** を入力し、**[場所]** および** [レプリケーション]** に値を入力して、**[ストレージ アカウントの作成]** をクリックします。アフィニティ グループはサポートされていません。
	
	新しいストレージ アカウントがストレージ一覧に表示されます。

	> [AZURE.IMPORTANT]最適なパフォーマンスが得られるように、お使いのストレージ アカウント、HDInsight クラスター、および DocumentDB アカウントが同一の Azure リージョンに配置されていることを確認してください。3 つのサービスすべてをサポートしている Azure リージョンは、**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、および**米国西部**です。

4. 新しいストレージ アカウントの **[状態]** 列が **[オンライン]** になるまで待ちます。

## <a name="ProvisionHDInsight"></a>手順 2: カスタマイズした HDInsight クラスターを作成する
このチュートリアルでは、Azure 管理ポータルからスクリプト アクションを使用して、HDInsight クラスターをカスタマイズする方法を説明します。このチュートリアルでは、カスタマイズされたクラスターの作成に、Azure 管理ポータルを使用します。PowerShell コマンドレットまたは HDInsight .NET SDK を使用する方法については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズに関するページ][hdinsight-custom-provision]を参照してください。

1. [Azure 管理ポータル][azure-classic-portal]にサインインします。前の手順で既にサインインしている場合もあります。

2. ページ下部の **[+ 新規]** をクリックし、**[DATA SERVICES]**、**[HDINSIGHT]**、**[カスタム作成]** の順にクリックします。

3. **[クラスターの詳細]** ページで、次の値を入力または選択します。

	![Hadoop HDInsight の初期クラスターの詳細を提供][image-customprovision-page1]

	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>クラスター名</td><td>クラスターの名前を指定します。<br/>
		DNS 名の最初と最後の文字は英数字にする必要があります。それ以外ではダッシュ (-) を使用できます。<br/>
		3 ～ 63 文字のクラスター名を入力できます。</td></tr>
	<tr><td>サブスクリプション名</td>
		<td>Azure サブスクリプションが複数ある場合は、<strong>手順 1</strong> で使用したストレージ アカウントに対応するサブスクリプションを選択します。</td></tr>
	<tr><td>クラスターの種類</td>
		<td>クラスターの種類には、<strong>[Hadoop]</strong> を選択します。</td></tr>
	<tr><td>オペレーティング システム</td>
		<td>[オペレーティング システム] で、<strong>[Windows Server 2012 R2 Datacenter]</strong> を選択します。</td></tr>
	<tr><td>HDInsight のバージョン</td>
		<td>バージョンを選択します。</br><Strong>[HDInsight version 3.1]</Strong> を選択します。</td></tr>
	</table><p>表に示されている値を入力または選択し、右矢印をクリックします。</p>

4. **[クラスターの構成]** ページで、次の値を入力または選択します。

	<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td>データ ノード</td><td>デプロイするデータ ノードの数です。</br>HDInsight のデータ ノードはパフォーマンスと料金の両方に関連していることに注意してください。</td></tr>
<tr><td>リージョン/仮想ネットワーク</td><td>新たに作成した<strong>ストレージ アカウント</strong>およびお使いの <strong>DocumentDB アカウント</strong>と同じリージョンを選択します。</br> HDInsight は、同じリージョンに配置されたストレージ アカウントを必要とします。これ以後の構成作業では、ここで指定した地域と同じリージョンにあるストレージ アカウントしか選択できません。</td></tr>
</table>右矢印をクリックします。

5. **[クラスター ユーザーの構成]** ページで、次の情報を指定します。

    <table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>ユーザー名</td>
		<td>HDInsight クラスターのユーザー名を指定します。</td></tr>
	<tr><td>パスワード/パスワードの確認</td>
		<td>HDInsight クラスターのユーザー パスワードを指定します。</td></tr>
</table>右矢印をクリックします。
    
6. **[ストレージ アカウント]** ページで、次の値を指定します。

	![HDInsight Hadoop クラスターに対してストレージ アカウントを指定][image-customprovision-page4]

	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>ストレージ アカウント</td>
		<td>HDInsight クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを指定します。3 つのオプション [既存のストレージを使用する]、[新しいストレージを作成する]、[別のサブスクリプションのストレージを使用する] のいずれかを選択できます。</br></br>
		<strong>[既存のストレージを使用する]</strong> を選択します。
		</td>
		</td></tr>
	<tr><td>アカウント名</td>
		<td>
		<strong>[アカウント名]</strong> で、<strong>手順 1</strong> で作成したアカウントを選択します。ボックスの一覧には、クラスターをプロビジョニングする対象として選択したデータ センターと同じデータ センター内の同じ Azure サブスクリプション下にあるストレージ アカウントのみが表示されます。
		</td></tr>
	<tr><td>既定のコンテナー</td>
		<td>ストレージ アカウントの既定のコンテナーを指定します。既定のコンテナーは、HDInsight クラスターの既定のファイル システムとして使用されます。<strong>[ストレージ アカウント]</strong> で <strong>[既存のストレージを使用する]</strong> を選択したにもかかわらずそのアカウントに既存のコンテナーがない場合は、既定でクラスターと同じ名前のコンテナーが作成されます。クラスター名と同じ名前のコンテナーが既に存在する場合は、コンテナー名に連番が付加されます。
    </td></tr>
	<tr><td>追加のストレージ アカウント</td>
		<td>HDInsight は複数のストレージ アカウントをサポートします。クラスターで使用できる追加ストレージ アカウントの数に制限はありません。ただし、Azure ポータルを使用してクラスターを作成する場合は、UI の制約により 7 が上限になります。ここで指定した追加のストレージ アカウントのそれぞれに対する [ストレージ アカウント] ページがウィザードに追加され、そこでアカウント情報を指定できます。</td></tr>
</table>右矢印をクリックします。

7. **[スクリプト アクション]** ページで **[スクリプト アクションの追加]** をクリックし、クラスターが作成される際に、クラスターのカスタマイズを実行する PowerShell スクリプトに関する詳細を指定します。PowerShell スクリプトによって、クラスターの作成中に DocumentDB Hadoop コネクタが HDInsight クラスターにインストールされます。
	
	![HDInsight クラスターをカスタマイズするスクリプト操作を構成][image-customprovision-page5]

	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。</br></br>
		次の値を入力してください。 </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v03.ps1</strong>.</td></tr>
	<tr><td>ノードの種類</td>
		<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。</br></br>
		<strong>[すべてのノード]</strong> を選択してください。</td></tr>
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。</br></br>
		<strong>パラメーターは必要ありません</strong>。</td></tr>
</table>チェック マークをクリックして、クラスターの作成を完了します。

## <a name="InstallCmdlets"></a>手順 3: Azure PowerShell をインストールおよび構成する

1. Azure PowerShell をインストールします。手順については、[このページ][powershell-install-configure]を参照してください。

	> [AZURE.NOTE]または、Hive クエリに対して HDInsight のオンライン Hive エディターを使用できます。この場合は、[Azure の管理ポータル][azure-classic-portal]にサインインし、左側ウィンドウの **[HDInsight]** をクリックすると、HDInsight クラスターの一覧が表示されます。Hive クエリを実行するクラスターをクリックし、**[クエリ コンソール]** をクリックします

2. Azure PowerShell Integrated Scripting Environment を開きます。
	- Windows 8 または Windows Server 2012 以降を実行しているコンピューターでは、組み込みの検索機能を使用できます。スタート画面で、「**powershell ise**」と入力し、**Enter** キーを押します。 
	- Windows 8 よりも前、または Windows Server 2012 より前のバージョンを実行しているコンピューターでは、[スタート] メニューを使用します。[スタート] メニューの検索ボックスに「**コマンド プロンプト**」と入力し、結果の一覧で、**[コマンド プロンプト]** をクリックします。コマンド プロンプトで、「**powershell\_ise**」と入力し、**Enter** キーを押します。

3. Azure アカウントを追加します。
	1. コンソール ウィンドウで、「**Add-AzureAccount**」と入力し、**Enter** キーを押します。 
	2. お使いの Azure サブスクリプションに関連付けられている電子メール アドレスを入力し、**[続行]** をクリックします。 
	3. Azure サブスクリプションのパスワードを入力します。 
	4. **[サインイン]** をクリックします。

4. 次の図は、Azure PowerShell Scripting Environment の重要な要素を示しています。

	![図: Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>手順 4: DocumentDB と HDInsight を使用して Hive ジョブを実行する

> [AZURE.IMPORTANT]< > で囲まれている変数はすべて、構成設定を使用して入力する必要があります。

1. PowerShell スクリプト ウィンドウで次の変数を設定します。

		# Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide the HDInsight cluster name where you want to run the Hive job.
		$clusterName = "<HDInsightClusterName>"

2. 
	<p>クエリ文字列の作成から始めましょう。作成する Hive クエリでは、DocumentDB コレクションからすべてのドキュメントのシステム生成のタイムスタンプ (_ts) と一意の ID (_rid) を取得し、すべてのドキュメントを分単位で集計して、その結果を新しい DocumentDB コレクションに格納します。</p>

	<p>まず、DocumentDB コレクションから Hive テーブルを作成します。次のコード スニペットを PowerShell スクリプト ウィンドウの #1 から始まっているコード スニペットの<strong>後に</strong>追加します。_ts および _rid に合わせてドキュメントをトリミングするためのオプションの DocumentDB.query パラメーターが含まれていることを確認してください。</p>

    > [AZURE.NOTE]**DocumentDB.inputCollections という名前は誤りではありません。** 次のように、複数のコレクションを 1 つの入力として追加することができます。</br>
    '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' </br>コレクション名は、間にスペースを入れずにコンマだけで区切ります。


		# Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
		$queryStringPart1 = "drop table DocumentDB_timestamps; "  + 
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " + 
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
 
3.  次に、出力コレクション用に Hive テーブルを作成します。出力ドキュメントのプロパティは、月、日、時間、分、および発生した合計回数です。

	> [AZURE.NOTE]**ここでも、DocumentDB.outputCollections という名前は誤りではありません。** 次のように、複数のコレクションを 1 つの入力として追加することができます。</br> '*DocumentDB.outputCollections*' = '*<DocumentDB Output Collection Name 1>*,*<DocumentDB Output Collection Name 2>*' </br>コレクション名は、間にスペースを入れずにコンマだけで区切ります。</br></br> ドキュメントは複数のコレクションに対してラウンドロビン形式で分散されます。ドキュメントの 1 つ目のバッチが 1 つのコレクションに格納され、2 つ目のバッチが次のコレクションに格納されて、以降、同様に処理されます。

		# Create a Hive table for the output data to DocumentDB.
	    $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " + 
                              "tblproperties ( " + 
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. 最後は、ドキュメントを月単位、日単位、時間単位、および分単位で集計し、結果を出力 Hive テーブルに格納します。

		# GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. 次のスクリプト スニペットを追加して、前のクエリから Hive ジョブ定義を作成します。

		# Create a Hive job definition.
		$queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	-File スイッチを使用して、HDFS 上の HiveQL スクリプト ファイルを指定することもできます。

6. 次のスニペットを追加して、開始時刻を格納し、Hive ジョブを送信します。

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. 次のスニペットを追加して、Hive ジョブが完了するのを待機します。

		# Wait for the Hive job to complete.
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. 次のスニペットを追加して、標準出力および開始時刻と終了時刻を表示します。

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. 作成できたスクリプトを**実行しましょう**。 緑色の実行ボタンを**クリックします**。

10. 結果を確認します。[Azure プレビュー ポータル][azure-portal]にサインインします。
	1. 左側ウィンドウの <strong>[参照]</strong> をクリックします。</br>
	2. 参照ウィンドウの右上にある <strong>[すべて]</strong> をクリックします。</br>
	3. <strong>[DocumentDB アカウント]</strong> を見つけてクリックします。</br>
	4. 次に、自分の <strong>DocumentDB アカウント</strong>、<strong>DocumentDB データベース</strong>、Hive クエリで指定した出力コレクションに関連付けられた <strong>DocumentDB コレクション</strong>を見つけます。</br>
	5. 最後に、<strong>[開発者ツール]</strong> の下にある <strong>[ドキュメント エクスプローラー]</strong> をクリックします。</br></p>

	Hive クエリの結果が表示されます。

	![Hive クエリの結果][image-hive-query-results]

## <a name="RunPig"></a>手順 5: DocumentDB と HDInsight を使用して Pig ジョブを実行する

> [AZURE.IMPORTANT]< > で囲まれている変数はすべて、構成設定を使用して入力する必要があります。

1. PowerShell スクリプト ウィンドウで次の変数を設定します。

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>クエリ文字列の作成から始めましょう。作成する Pig クエリでは、DocumentDB コレクションからすべてのドキュメントのシステム生成のタイムスタンプ (_ts) と一意の ID (_rid) を取得し、すべてのドキュメントを分単位で集計して、その結果を新しい DocumentDB コレクションに格納します。</p>
    <p>まず、DocumentDB から HDInsight にドキュメントを読み込みます。次のコード スニペットを PowerShell スクリプト ウィンドウの #1 から始まっているコード スニペットの<strong>後に</strong>追加します。_ts および _rid に合わせてドキュメントをトリミングするためのオプションの DocumentDB.query パラメーターが DocumentDB クエリに追加されていることを確認してください。</p>

    > [AZURE.NOTE]次のように、複数のコレクションを 1 つの入力として追加することができます。</br>
    '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*'</br>コレクション名は、間にスペースを入れずにコンマだけで区切ります。 </b>

	ドキュメントは複数のコレクションに対してラウンドロビン形式で分散されます。ドキュメントの 1 つ目のバッチが 1 つのコレクションに格納され、2 つ目のバッチが次のコレクションに格納されて、以降、同様に処理されます。

		# Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  次に、ドキュメントを月単位、日単位、時間単位、分単位、および発生した合計回数で集計します。

		# GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. 最後に、その結果を新しい出力コレクションに格納します。

    > [AZURE.NOTE] 次のように、複数のコレクションを 1 つの入力として追加することができます。</br>
    '\<DocumentDB Output Collection Name 1\>,\<DocumentDB Output Collection Name 2\>'</br> コレクション名は、間にスペースを入れずにコンマだけで区切ります。</br>
    ドキュメントは複数のコレクションに対してラウンドロビン形式で分散されます。ドキュメントの 1 つ目のバッチが 1 つのコレクションに格納され、2 つ目のバッチが次のコレクションに格納されて、以降、同様に処理されます。

		# Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. 次のスクリプト スニペットを追加して、前のクエリから Pig ジョブ定義を作成します。

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

	-File スイッチを使用して、HDFS 上の Pig スクリプト ファイルを指定することもできます。

6. 次のスニペットを追加して、開始時刻を格納し、Pig ジョブを送信します。

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. 次のスニペットを追加して、Pig ジョブが完了するのを待機します。

		# Wait for the Pig job to complete.
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. 次のスニペットを追加して、標準出力および開始時刻と終了時刻を表示します。

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
		
9. 作成できたスクリプトを**実行しましょう**。 緑色の実行ボタンを**クリックします**。

10. 結果を確認します。[Azure プレビュー ポータル][azure-portal]にサインインします。
	1. 左側ウィンドウの <strong>[参照]</strong> をクリックします。</br>
	2. 参照ウィンドウの右上にある <strong>[すべて]</strong> をクリックします。</br>
	3. <strong>[DocumentDB アカウント]</strong> を見つけてクリックします。</br>
	4. 次に、自分の <strong>DocumentDB アカウント</strong>、<strong>DocumentDB データベース</strong>、Pig クエリで指定した出力コレクションに関連付けられた <strong>DocumentDB コレクション</strong>を見つけます。</br>
	5. 最後に、<strong>[開発者ツール]</strong> の下にある <strong>[ドキュメント エクスプローラー]</strong> をクリックします。</br></p>

	Pig クエリの結果が表示されます。

	![Pig クエリの結果][image-pig-query-results]

## <a name="RunMapReduce"></a>手順 6: DocumentDB と HDInsight を使用して MapReduce ジョブを実行する

1. PowerShell スクリプト ウィンドウで次の変数を設定します。
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
2. DocumentDB コレクションから各ドキュメント プロパティの発生回数を集計する MapReduce ジョブを実行します。このスクリプト スニペットを上記のスニペットの**後に**追加します。

		# Define the MapReduce job.
		$TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

	> [AZURE.NOTE]TallyProperties-v01.jar は、DocumentDB Hadoop コネクタのカスタム インストールに付属しています。

3. 次のコマンドを追加して、MapReduce ジョブを送信します。

		# Save the start time and submit the job.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	MapReduce ジョブ定義に加えて、MapReduce ジョブを実行する HDInsight クラスター名、および資格情報も指定します。Start-AzureHDInsightJob は非同期呼び出しです。ジョブの完了を確認するには、*Wait-AzureHDInsightJob* コマンドレットを使用します。

4. 次のコマンドを追加して、MapReduce ジョブの実行中に発生したエラーを確認します。
	
		# Get the job output and print the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green 

5. 作成できたスクリプトを**実行しましょう**。 緑色の実行ボタンを**クリックします**。

6. 結果を確認します。[Azure プレビュー ポータル][azure-portal]にサインインします。
	1. 左側ウィンドウの <strong>[参照]</strong> をクリックします。
	2. 参照ウィンドウの右上にある <strong>[すべて]</strong> をクリックします。
	3. <strong>[DocumentDB アカウント]</strong> を見つけてクリックします。
	4. 次に、自分の <strong>DocumentDB アカウント</strong>、<strong>DocumentDB データベース</strong>、MapReduce ジョブで指定した出力コレクションに関連付けられた <strong>DocumentDB コレクション</strong>を見つけます。
	5. 最後に、<strong>[開発者ツール]</strong> の下にある <strong>[ドキュメント エクスプローラー]</strong> をクリックします。

	MapReduce ジョブの結果が表示されます。

	![MapReduce クエリの結果][image-mapreduce-query-results]

## <a name="NextSteps"></a>次のステップ

ご利用ありがとうございます。 Azure DocumentDB および HDInsight を使用して最初の Hive、Pig、および MapReduce ジョブを実行しました。

Microsoft では Hadoop コネクタをオープン ソース化しています。関心がある場合は、[GitHub][documentdb-github] に投稿できます。

詳細については、次の記事を参照してください。

- [DocumentDB を使用した Java アプリケーションの開発][documentdb-java-application]
- [HDInsight での Hadoop 用 Java MapReduce プログラムの開発][hdinsight-develop-deploy-java-mapreduce]
- [HDInsight で Hive と Hadoop を使用し、モバイル ハンドセットの使用状況を分析する][hdinsight-get-started]
- [HDInsight での MapReduce の使用][hdinsight-use-mapreduce]
- [HDInsight での Hive の使用][hdinsight-use-hive]
- [HDInsight での Pig の使用][hdinsight-use-pig]
- [Script Action を使って HDInsight クラスターをカスタマイズする][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-classic-portal]: https://manage.windowsazure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png
[azure-portal]: https://portal.azure.com/

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-customprovision-page4]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page4.png
[image-customprovision-page5]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page5.png
[image-storageaccount-quickcreate]: ./media/documentdb-run-hadoop-with-hdinsight/storagequickcreate.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../install-configure-powershell.md
 

<!---HONumber=Oct15_HO3-->