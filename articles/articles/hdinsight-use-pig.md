<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="HDInsight での Pig の使用" pageTitle="HDInsight での Pig の使用 | Azure" metaKeywords="" description="HDInsight で Pig を使用する方法を学習します。Pig Latin ステートメントを記述してアプリケーション ログ ファイルを分析し、データに対してクエリを実行して分析用の出力を生成します。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight での Pig の使用" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />



# HDInsight での Pig の使用#


[Apache *Pig*][apachepig-home] には *MapReduce* ジョブを実行するためのスクリプト言語が備わっており、Java コードの代わりに使用できます。このチュートリアルでは、PowerShell を使用して Pig Latin ステートメントを実行して Apache log4j ログ ファイルを分析し、データに対してさまざまなクエリを実行して出力を生成します。このチュートリアルでは Pig の利点を紹介し、Pig を使用することで MapReduce ジョブがどのように簡略化されるかを実証します。

Pig のスクリプト言語を *Pig Latin* といいます。Pig Latin ステートメントは一般的なフローに従います。

- **Load**: 操作対象のデータをファイル システムから読み込みます。
- **Transform**: データを操作します。
- **Dump または store**: データを画面に出力します。または、処理できるように保存します。

Pig Latin の詳細については、「[Pig Latin Reference Manual 1][piglatin-manual-1]」および「[Pig Latin Reference Manual 2][piglatin-manual-2]」を参照してください。

**前提条件**

この記事を読み始める前に、次の要件に気を付けてください。

* Azure HDInsight クラスター。手順については、「[Azure HDInsight の概要][hdinsight-getting-started]」または「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」を参照してください。
* Azure PowerShell のインストールおよび構成。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install-configure]を参照してください。

**所要時間: **30 分

##この記事の内容

* [Pig の利用例](#usage)
* [Azure BLOB ストレージへのデータ ファイルのアップロード](#uploaddata)
* [Pig Latin について](#understand)
* [PowerShell を使用して Pig Latin を実行](#powershell)
* [次の手順](#nextsteps)
 
##<a id="usage"></a>Pig の利用例
データベースは、データ量がそれほど多くなく、クエリの待ち時間が短い場合に適しています。数テラバイトもある大規模なデータセットやビッグ データとなると、従来の SQL データベースでは十分に対応できません。これまでデータベース管理者は、データベースの負荷が増大し、パフォーマンスが低下したとき、より大型のハードウェアを購入することでスケールアップを図ってきました。

通常、アプリケーションで発生したエラー、例外、その他のコーディング問題はすべてログ ファイルに保存されます。管理者はログ ファイルのデータを参照して問題点を確認したり、指標を生成したりします。多くの場合、これらのログ ファイルはサイズが非常に大きくなり、さらにその膨大なログ データを処理、分析しなければなりません。

ログ ファイルはまさにビッグ データといえます。リレーショナル データベースや統計/視覚化パッケージではビッグ データを適切に処理できません。こうした膨大なデータを実用的な速度で処理するには、数十台、数百台、あるいは数千台のサーバー上でソフトウェアを同時に実行する必要があります。Hadoop は、膨大な構造化データと非構造化データを処理するアプリケーションを記述するための MapReduce フレームワークです。クラスター化された多数のコンピューターでこれらのデータを並列処理することで、高い信頼性と耐障害性を実現します。

Pig を使用すると、mapper プログラムと reducer プログラムの記述に要する時間を短縮できます。つまり、Java は不要であり、従来のようなコードも必要ありません。Java コードと Pig を組み合わせることもできます。多くの複雑なアルゴリズムを、人間による判読が可能な数行の Pig コードで記述できます。

次の 2 つの図は、この記事で実行する処理を視覚化したものです。これらの図はデータセットの一部を例にとり、スクリプト内の Pig コード行を実行したときデータがどように処理されるかを示しています。最初の図は log4j ファイルのサンプルです。

![サンプル ファイル全体][image-hdi-log4j-sample]

2 つ目の図はデータ変換を示しています。

![HDI.PIG.Data.Transformation][image-hdi-pig-data-transformation]




























##<a id="uploaddata"></a>BLOB ストレージへのデータ ファイルのアップロード

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。この記事では、HDInsight クラスターと一緒に配布されている log4j サンプル ファイル (*\example\data\sample.log*) を使用します。データのアップロードについては、「[データを HDInsight へアップロードする方法][hdinsight-upload-data]」を参照してください。

ファイルにアクセスするには、次の構文を使用します。

		wasb[s]://[<containerName>@<storageAccountName>.blob.core.windows.net]/<path>/<filename>

次に例を示します。

		wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

*mycontainer* を実際のコンテナー名に置き換え、*mystorage* を BLOB ストレージ アカウント名に置き換えてください。

ファイルは既定のファイル システムに保存されるので、次のように指定してファイルにアクセスすることもできます。

		wasb:///example/data/sample.log
		/example/data/sample.log



	
##<a id="understand"></a> Pig Latin について

ここでは、いくつかの Pig Latin ステートメントと、その実行結果を確認します。次のセッションでは、PowerShell を使用して Pig ステートメントを実行します。

1. ファイル システムからデータを読み込んで、結果を表示します。

		LOGS = LOAD 'wasb:///example/data/sample.log';
		DUMP LOGS;
	
	次のように出力されます。
	
		(2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
		(2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
		(2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
		(2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
		(2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
		(2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
		...

2. データ ファイルの各行を調べ、6 つのログ レベルと照合します。

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
 
3. 一致しない行を除去します。たとえば、空の行です。

		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		DUMP FILTEREDLEVELS;

	次のように出力されます。

		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		...

4. ログ レベルのすべてをそれぞれ独自の行にまとめます。

		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		DUMP GROUPEDLEVELS;

	次のように出力されます。

		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE), ...


5. グループごとにログ レベルの出現回数をカウントします。これらは各ログ レベルの頻度になります。

		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		DUMP FREQUENCIES;
 
	次のように出力されます。

		(INFO,3355)
		(WARN,361)
		(DEBUG,15608)
		(ERROR,181)
		(FATAL,37)
		(TRACE,29950)

6. 出現頻度が多い順に並べ替えます。

		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;   

	次のように出力されます。

		(TRACE,29950)
		(DEBUG,15608)
		(INFO,3355)
		(WARN,361)
	 	(ERROR,181)
		(FATAL,37)


##<a id="powershell"></a>PowerShell を使用して Pig Latin を実行

ここでは PowerShell コマンドレットの使用手順を示します。このセクションを読み進める前に、まずローカル環境をセットアップし、Azure への接続を構成する必要があります。詳細については、「[Azure HDInsight の概要][hdinsight-getting-started]」および「[PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]」を参照してください。


**PowerShell を使用して Pig Latin を実行するには**

1. Azure PowerShell コンソール ウィンドウを開きます。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install-configure]を参照してください。
2. 次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。
2. 次のスクリプトで変数を設定して、スクリプトを実行します。

		$clusterName = "<HDInsightClusterName>" 

3. 次のコマンドを実行して、Pig Latin クエリ文字列を定義します。

		# Create the Pig job definition
		$0 = '$0';
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;" 
		
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString 

	-File スイッチを使用して、HDFS 上の Pig スクリプト ファイルを指定することもできます。

4. 次のスクリプトを実行して、Pig ジョブを送信します。
		
		# Submit the Pig job
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition 

5. 次のスクリプトを実行して、Pig ジョブを送信し、ジョブの完了を待ちます。		

		# Wait for the Pig job to complete
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

6. 次のスクリプトを実行して、Pig ジョブの出力を表示します。
		
		# Print the standard error and the standard output of the Pig job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

	![HDI.Pig.PowerShell][image-hdi-pig-powershell]

	Pig ジョブがログの種類の出現回数を計算します。




















 

##<a id="nextsteps"></a>次のステップ

Pig ではデータを分析できますが、HDInsight には便利なその他の言語も用意されています。Hive は SQL に似たクエリ言語であり、HDInsight に保存されているデータに対して簡単にクエリを実行できます。また、Java で記述された MapReduce ジョブでは複雑なデータ分析を実行できます。詳細については、次のトピックを参照してください。


* [Azure HDInsight の概要][hdinsight-getting-started]
* [データを HDInsight へアップロードする方法][hdinsight-upload-data]
* [HDInsight での Hive の使用][hdinsight-using-hive]



[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: /ja-jp/manage/services/hdinsight/howto-blob-store/
[hdinsight-upload-data]: /ja-jp/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-getting-started]: /ja-jp/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-admin-powershell]: /ja-jp/manage/services/hdinsight/administer-hdinsight-using-powershell/

[hdinsight-using-hive]: /ja-jp/manage/services/hdinsight/using-hive-with-hdinsight/

[hdinsight-provision]: /ja-jp/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-configure-powershell]: /ja-jp/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 

[Powershell-install-configure]: /ja-jp/documentation/articles/install-configure-powershell/

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png  


