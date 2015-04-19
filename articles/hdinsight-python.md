<properties 
	pageTitle="Azure HDInsight での Python と Hive および Pig の使用" 
	description="Azure HDInsight での Hive および Pig の Python ユーザー定義関数の使用方法について説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>

#HDInsight における Python と Hive および Pig の使用

Hive と Pig は HDInsight でデータを処理する場合にきわめて有益ですが、より汎用的な言語が必要になる場合もあります。Hive と Pig では、さまざまなプログラミング言語を使用してユーザー定義関数 (UDF) を作成できます。この記事では、Hive と Pig で Python UDF を使用する方法について説明します。

> [AZURE.NOTE] この記事で説明する手順は、HDInsight クラスター Version 2.1、3.0、および 3.1 に適用されます。

##目次

* [HDInsight の Python](#python)
*   [Hive と Python](#hivepython)
*   [Pig と Python](#pigpython)
* [例を実行する](#running)
* [トラブルシューティング](#troubleshooting)
* [次のステップ](#next)

##<a name="python"></a>HDInsight の Python

Python2.7 は HDInsight 3.0 クラスターに既定でインストール済みです。Python は D:\Python フォルダーにインストールされます。このバージョンの Python と共に Hive を使用することで、ストリームを処理できます (Hive と Python の間のデータの受け渡しには STDOUT/STDIN を使用します)。

HDInsight には、Java で記述された Python 実装である Jython も付属しています。Pig ではストリーミングを用いずに Jython と対話できるため、Pig を使用している場合に有効です。

###<a name="hivepython"></a>Hive と Python

Python は、HiveQL の **TRANSFORM** ステートメントを通じて Hive から UDF として使用することができます。たとえば、次の HiveQL は **streaming.py** ファイルに格納されている Python スクリプトを呼び出します。

	add file wasb:///streaming.py;
	
	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

この例では以下のように処理されます。

1. ファイルの先頭の **add file** ステートメントで **streaming.py** ファイルが分散キャッシュに追加されます。これによってクラスター内のすべてのノードからのアクセスが可能になります。

2.   **SELECT TRANSFORM ...USING 'D:\Python27\python.exe streaming.py'** ステートメントを使用して、**hivesampletable** からデータを選択し、clientid、devicemake、および devicemodel を **streaming.py** スクリプトに渡します。

	> [AZURE.NOTE] **USING** 句は、パスに指定されていない python.exe への完全なパスを指定します。

3. **AS** 句は、**streaming.py** から返されるフィールドを記述します。


<a name="streamingpy"></a>
次に、HiveQL で使用される **streaming.py** ファイルの例を示します。

	import sys
	import string
	import hashlib
	
	while True:
	  line = sys.stdin.readline()
	  if not line:
	    break
	
	  line = string.strip(line, "\n ")
	  clientid, devicemake, devicemodel = string.split(line, "\t")
	  phone_label = devicemake + ' ' + devicemodel
	  print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

ストリーミングを使用しているため、このスクリプトは以下の処理が必要です。

1. STDIN のデータの読み取り。この例では、 `sys.stdin.readline()` を使用してデータを読み取ります。

2. ここではテキスト データのみが必要であり、行末のインジケーターは不要のため、 `string.strip(line, "\n ")` で末尾の改行文字が削除されます。

2. ストリームの処理中は、すべての値が 1 つの行に含まれ、値と値の間はタブ文字で区切られます。それにより、 `string.split(line, "\t")` を使用してタブごとに入力を分割し、フィールドのみを返すことができます。

3. 処理の完了時には、フィールド間がタブで区切られた単一の行として、STDOUT に出力が書き出される必要があります。この処理は、 `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])` を使用して実現します。

4. これはすべて  `while` ループ内で実行され、 `line` が読み込まれなくなるまで繰り返されます。読み込まれなくなった時点で  `break` がループを終了し、スクリプトは終了します。

これ以外にも、スクリプトは  `devicemake` と  `devicemodel` の入力値を連結し、連結後の値のハッシュを計算します。Hive から呼び出された Python スクリプトは、ループ、入力がなくなるまでの読み込み、タブでの各入力行の区切り、処理、タブで区切った単一行への出力の書き出しを実行します。単純ですがこれが基本的な動作の説明です。

ご使用の HDInsight クラスターでこの例を実行する方法については、[例を実行する](#running) を参照してください。

###<a name="pigpython"></a>Pig と Python

Python スクリプトは、**GENERATE** ステートメントを通じて Pig から UDF として使用できます。たとえば、次の例では **jython.py** ファイルに格納されている Python スクリプトを使用します。

	Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

以下に、このスクリプトの動作の例を示します。

1. **Jython** を使用して Python スクリプト (j**ython.py**) が含まれるファイルを登録し、**myfuncs** として Pig に公開します。Jython は、Java による Python 実装であり、Pig と同じ Java 仮想マシンで動作します。この方法によって、Hive で使用するストリーミング手法ではなく、従来の関数呼び出しのように Python スクリプトを扱うことができます。

2. 次の行は、サンプルのデータ ファイルである、**sample.log** を **LOGS** に読み込みます。このファイルには一貫したスキーマがないことから、各レコード (この場合は **LINE**) を **chararray** として定義します。Chararray は本質的には文字列です。

3. 3 行目はすべての null 値を除去し、操作の結果を **LOG** に格納します。

4. 次に、**LOG** のレコードを反復処理し、**GENERATE** を使用して、**myfuncs** として読み込まれた **jython.py** スクリプトに格納されている **create_structure** メソッドを呼び出します。  **LINE** は現在のレコードを関数に渡すために使用されます。

5. 最後に、**DUMP** コマンドで出力が STDOUT にダンプされます。この例は、操作完了後の結果をすぐに示すためのものであり、実際のスクリプトでは通常、**STORE** を通じて新しいファイルにデータを保存します。

<a name="jythonpy"></a>
次に、Pig の例で使用する **jython.py** ファイルを示します。

	@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
	def create_structure(input):
	  if (input.startswith('java.lang.Exception')):
	    input = input[21:len(input)] + ' - java.lang.Exception'
	  date, time, classname, level, detail = input.split(' ', 4)
	  return date, time, classname, level, detail

先ほど、入力の一貫したスキーマがないために **LINE** 入力を chararray として定義したことを覚えているでしょうか。これから **jython.py** が行うのは、出力用に、データを一貫したスキーマに変換することです。次のような処理です。

1. **@outputSchema** ステートメントは、Pig に返されるデータ形式を定義します。この場合、Pig のデータ型である、**data bag** になります。この bag には以下のフィールドが含まれ、すべて chararray (文字列) です。

	* date - ログ エントリが作成された日付
	* time - ログ エントリが作成された時刻
	* classname - エントリが作成されたクラスの名前
	* level - ログ レベル
	* detail - ログ エントリの詳細説明

2. 次に、**def create_structure(input)** で Pig が行項目を渡す関数が定義されます。

3. 例のデータである **sample.log** は通常、日付、時刻、クラス名、レベル、および詳細説明のデータを返すスキーマに従います。しかし、ここにはスキーマとの一致を図るために、'*java.lang.Exception*' という文字列で始まるいくつかの行を変更する必要があります。**if** ステートメントでそれらを確認した後、想定される出力スキーマと一致するように、入力データの '*java.lang.Exception*' 文字列を末尾に移動します。

4. 次に、**split** コマンドを使用して、最初の 4 つの空白文字でデータを分割します。その結果 5 つの値が生成され、それぞれ **date**、**time**、**classname**、**level**、および **detail** に割り当てられます。

5. 最後に、値が Pig に返されます。

データが Pig に返された時点で、このデータは、**@outputSchema** ステートメントで定義された一貫したスキーマを持ちます。

ご使用の HDInsight クラスターでこの例を実行する方法については、[例を実行する](#running) を参照してください。

##<a name="running"></a>例を実行する

次の手順では、Windows Azure PowerShell を使用します。まだインストールと構成が終了していない場合は、以下の手順に進む前に「[Azure PowerShell のインストールおよび構成方法](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)」を参照してください。


1. Python の例 [streaming.py](#streamingpy) と [jython.py](#jythonpy)を使用して、開発用コンピューター上のファイルのローカル コピーを作成します。

2.   次の PowerShell スクリプトを使用して、**streaming.py** ファイルと **jython.py** ファイルをサーバーにアップロードします。スクリプトの最初の 3 行に、Azure HDInsight クラスターの名前と、**streaming.py** ファイルおよび **jython.py** ファイルへのパスを入力します。

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\jython.py"

		$hdiStore = get-azurehdinsightcluster -name $clusterName
		$storageAccountName = $hdiStore.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		$storageAccountKey = $hdiStore.defaultstorageaccount.storageaccountkey
		$defaultContainer = $hdiStore.DefaultStorageAccount.StorageContainerName
		
		$destContext = new-azurestoragecontext -storageaccountname $storageAccountName -storageaccountkey $storageAccountKey
		set-azurestorageblobcontent -file $pathToStreamingFile -Container $defaultContainer -Blob "streaming.py" -context $destContext
		set-azurestorageblobcontent -file $pathToJythonFile -Container $defaultContainer -Blob "jython.py" -context $destContext

	このスクリプトは、HDInsight クラスターの情報を取得してから、既定のストレージ アカウント用にアカウントとキーを抽出します。その後、ファイルをコンテナーのルートにアップロードします。

	> [AZURE.NOTE] スクリプトをアップロードする他の方法については、ドキュメント「[HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」で確認できます。

###Hive ダッシュボードの使用 (Hive の例のみ)

1. ファイルのアップロード後、ブラウザーを開き、https://YourClusterName.azurehdinsight.net/ に移動します。資格情報の入力を要求されたら、クラスターの管理者名とパスワードを入力します。

	> [AZURE.NOTE] Azure の管理ポータルの [**ダッシュボード**] の下部にある [**クラスターの管理**] リンクを使用しても Hive ダッシュボードを起動できます。

2. **Hive エディター**を使用して、 `select * from hivesampletable` 行を次の HiveQL に置き換えます。

		add file wasb:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'D:\Python27\python.exe streaming.py' AS
		  (clientid string, phoneLable string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. **[送信]** をクリックしてジョブを送信します。HDInsight のクラスター バージョンによっては、[ジョブの詳細] ページにリダイレクトされる場合があります。リダイレクトされない場合、ページ下部の **[ジョブ セッション]** 領域で **[詳細の表示]** を選択します。

4. **[ジョブの詳細]** ページはジョブが完了するたびに更新されます。完了すると、出力とジョブに関する情報が表示されます。

###PowerShell の使用 (Hive と Pig の例)

ファイルのアップロード後、次の PowerShell スクリプトを使用してジョブを開始します。ジョブが完了すると、PowerShell コンソールに出力が書き込まれます。

**Hive ジョブを実行するには**
    
    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$HiveQuery = "add file wasb:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'D:\Python27\python.exe streaming.py' AS " +
	               "(clientid string, phoneLable string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"
	
	$jobDefinition = New-AzureHDInsightHiveJobDefinition -Query $HiveQuery -StatusFolder '/hivepython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

**Hive** ジョブの出力は次のように表示されます。

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

**Pig ジョブを実行するには**

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"
	
	$jobDefinition = New-AzureHDInsightPigJobDefinition -Query $PigQuery -StatusFolder '/pigpython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

**Pig** ジョブの出力は次のように表示されます。

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>トラブルシューティング

例の実行に使用した両方の PowerShell スクリプトには、ジョブのエラー出力を表示するコメント行が含まれています。ジョブについて想定された出力が確認できない場合は、次の行をコメント解除し、エラー情報で問題が示されるかどうかを確認してください。

	# Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName

エラー情報 (STDERR) およびジョブの結果 (STDOUT) は、クラスターの既定の BLOB コンテナーの以下の場所にも記録されます。

<table>
<tr>
<td>ジョブ</td><td>BLOB コンテナーで確認するファイル</td>
</tr>
<td>Hive</td><td>/HivePython/stderr</br>/HivePython/stdout</td>
</tr>
<td>Pig</td><td>/PigPython/stderr</br>/PigPython/stdout</td>
</tr>
</table>

##<a name="next"></a>次のステップ

既定では提供されない Python モジュールを読み込む必要がある場合は、方法を「[How to deploy a Python module to Windows Azure HDInsight (Python モジュールを Windows Azure HDInsight にデプロイする方法)](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)」で参照してください。

PowerShell を使用せずに HDInsight のジョブをリモートで実行する場合は、「[How to use HDInsight from Linux (Linux から HDInsight を使用する方法)](http://blogs.msdn.com/b/benjguin/archive/2014/02/18/how-to-use-hdinsight-from-linux.aspx)」で、WebHCat REST API を通じて Python を使用する例を参照してください。
<!--HONumber=42-->
