<properties 
	pageTitle="クラウド データ サイエンス プロセスで HDInsight Hadoop クラスターに Hive クエリを送信する | Azure" 
	description="Hive クエリで Hive テーブルからのデータを処理します。" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="hangzh;bradsev" /> 

#<a name="heading"></a> クラウド データ サイエンス プロセスで HDInsight Hadoop クラスターに Hive クエリを送信する

このドキュメントでは、Azure の HDInsight サービスが管理する Hadoop クラスターに Hive クエリを送信するさまざまな方法について説明します。いくつかのデータを処理するタスク (データの探索および特徴の生成) について説明します。Azure の HDInsight Hadoop クラスターで Hive を使用してデータの探索や特徴の生成を行う方法を示す汎用の Hive クエリが取り上げられます。これらの Hive クエリでは、用意されている埋め込みの Hive のユーザー定義関数 (UDF) を使用します。 

[NYC タクシー乗車データ](http://chriswhong.com/open-data/foil_nyc_taxi/)シナリオ固有のクエリの例も、[Github リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)に用意されています。これらのクエリには、指定されたデータ スキーマが既にあり、すぐに送信して実行できる状態になっています。 

最後のセクションでは、Hive クエリのパフォーマンスを向上させるためにユーザーが調整できるパラメーターについて説明します。

## 前提条件
この記事では、以下のことを前提としています。
 
* Azure のストレージ アカウントが作成されている。手順を知りたい場合は、「[Azure のストレージ アカウントの作成](hdinsight-get-started.md#storage)」を参照してください。 
* HDInsight サービスでカスタマイズされた Hadoop クラスターがプロビジョニングされている。手順を知りたい場合は、「[データ サイエンス用に Azure HDInsight Hadoop クラスターをカスタマイズする](machine-learning-data-science-customize-hadoop-cluster.md)」を参照してください。
* データが Azure HDInsight Hadoop クラスターの Hive テーブルにアップロードされている。アップロードされていない場合は、まず「[データを作成して Hive テーブルに読み込む](machine-learning-data-science-move-hive-tables.md)」に従って Hive テーブルにデータをアップロードします。
* クラスターへのリモート アクセスが有効になっている。手順を知りたい場合は、「[Hadoop クラスターのヘッド ノードにアクセスする](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess)」を参照してください。 


## <a name="submit"></a>Hive クエリを送信する方法
Hive クエリは、以下のものを使用して送信できます。 

* クラスターのヘッドノードでの Hadoop コマンド ライン
* IPython Notebook 
* Hive エディター
* Azure PowerShell スクリプト 

Hive クエリの送信時、Hive クエリの出力先を、画面上、ヘッド ノード上のローカル ファイル、または Azure BLOB のどれにするか制御できます。

### Hadoop クラスターのヘッド ノードにある Hadoop コマンド ライン コンソールを介して

クエリが複雑な場合は、一般に、Hadoop クラスターのヘッド ノードから直接 Hive クエリを送信すれば、Hive エディターで送信したり、Azure PowerShell スクリプトを使用した場合よりもターンアラウンド時間が短くなります。 

Hadoop クラスターのヘッド ノードにログインし、ヘッド ノードのデスクトップで Hadoop コマンド ラインを開き、次のコマンドを入力します。

    cd %hive_home%\bin

Hadoop コマンド ライン コンソールで Hive クエリを送信する方法は 3 つあります。

* 直接 Hadoop コマンドラインから
* .hql ファイルの使用
* Hive コマンド コンソールから

####Hive クエリを直接 Hadoop コマンドラインから送信する 

ユーザーは、次のようなコマンドを実行して、 

	hive -e "<your hive query>; 

Hadoop コマンド ラインで簡単な Hive クエリを直接送信できます。次に例を示します。ここで赤いボックスは Hive クエリを送信するコマンドを囲んでいます。緑色のボックスは Hive クエリの出力を囲んでいます。

![Create workspace][10]

####.hql ファイルで Hive クエリを送信する

Hive クエリがより複雑で、複数の行がある場合、Hadoop コマンド ラインまたは Hive コマンド コンソールでクエリを編集することは実用的ではありません。別の方法は、Hadoop クラスターのヘッド ノードでテキスト エディターを使用して、ヘッド ノードのローカル ディレクトリにある .hql ファイルに Hive クエリを保存することです。続いて、次のように、.hql ファイル内の Hive クエリを、 `hive` コマンドの `-f` 引数を使用して送信します。
	
	`hive -f "<path to the .hql file>"`

![Create workspace][15]


####Hive クエリの進行状況ステータス画面の出力を抑制する

既定では、Hive クエリが Hadoop コマンド ライン コンソールで送信されると、 Map/Reduce ジョブの進行状況が画面に出力されます。Map/educe ジョブの進行状況の画面出力を抑制するには、次のように、コマンド ラインで引数 `-S` (「S」は大文字である必要があります) を使用します。

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

####Hive コマンド コンソールで Hive クエリを送信する。

Hadoop コマンドラインから `hive` コマンドを実行して Hive コマンド コンソールに入った後、Hive コマンド コンソールから Hive クエリを送信することもできます。たとえば次のようになります。  

![Create workspace][11]

この例では、2 つの赤いボックスは、それぞれ Hive コマンド コンソールに入るために使用するコマンドと、Hive コマンド コンソールで送信された Hive クエリを強調表示しています。緑色のボックスは、Hive クエリからの出力を強調表示しています。

前の例では、画面上に直接 Hive クエリの結果を出力しています。ユーザーは、ヘッド ノードにあるローカル ファイルや Azure BLOB に出力を書き込むこともできます。その後、他のツールを使用して Hive クエリからの出力をさらに分析することもできます。

####Hive クエリの結果をローカル ファイルに出力する。 

Hive クエリの結果をヘッド ノード上のローカル ディレクトリに出力するには、次のように Hadoop コマンド ラインで Hive クエリを送信する必要があります。

	`hive -e "<hive query>" > <local path in the head node>`

次の例では、Hive クエリの出力が  `C:\apps\temp` ディレクトリ内のファイル  `hivequeryoutput.txt` に書き込まれます。

![Create workspace][12]

####Hive クエリの結果を Azure BLOB に出力する

ユーザーは、Hadoop クラスターの既定のコンテナー内にある Azure BLOB に Hive クエリの結果を出力することもできます。これを行う Hive クエリは次のようになります。

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

次の例では、Hive クエリの出力が、Hadoop クラスターの既定のコンテナー内にある BLOB ディレクトリ  `queryoutputdir` に書き込まれます。ここでは、ディレクトリ名のみを指定する必要があります。BLOB 名を指定する必要はありません。ディレクトリ名と BLOB 名の両方を指定する ( `wasb:///queryoutputdir/queryoutput.txt` など) と、エラーがスローされます。 

![Create workspace][13]

Azure ストレージ エクスプローラーの (またはそれと同等の) ツールを使用して、Hadoop クラスターの既定のコンテナーを開くと、Hive クエリの出力を BLOB ストレージで確認できます。指定された文字が名前に含まれる BLOB のみを取得する場合は、フィルター (赤いボックスで強調表示) を適用できます。

![Create workspace][14]

### Hive エディターまたは Azure の PowerShell コマンドを介して

ユーザーは、クエリのコンソール (Hive エディター) を使用するために、URL 
 
https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor  

を Web ブラウザーに入力することもできます。ログインする場合、Hadoop クラスターの資格情報の入力を求められることに注意してください。代わりに、 [PowerShell を使用して Hive ジョブを送信](hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell)することもできます。 


##<a name="hive-dataexploration"></a>データの探索
Hive テーブルでデータを探索するために使用する、いくつかのサンプル Hive スクリプトは次のとおりです。

1. パーティションごとの所見の数を取得する
	`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. 1 日ごとの所見の数を取得する
	`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);` 

3. カテゴリ列内のレベルを取得する  
	`SELECT  distinct <column_name> from <databasename>.<tablename>`

4. 2 つのカテゴリ列の組み合わせ内のレベルの数を取得する 
	`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. 数値型列の分布を取得する  
	`SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. 2 つのテーブルの結合からレコードを抽出する 

	    SELECT 
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>, 
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>, 
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

##<a name="hive-featureengineering"></a>特徴の生成

このセクションでは、Hive クエリを使用して特徴を生成する方法について説明します。 

> [AZURE.NOTE] このセクションのサンプルの Hive クエリでは、Azure HDInsight Hadoop クラスターの Hive テーブルにデータがアップロードされていることを前提としています。アップロードされていない場合は、まず「[データを作成して Hive テーブルに読み込む](machine-learning-data-science-hive-tables.md)」に従って Hive テーブルにデータをアップロードします。

追加の特徴を生成すると、既存のテーブルに列として追加するか、追加の特徴と主キーを持つ新しいテーブルを作成して元のテーブルと結合することができます。  

1. [頻度ベースの特徴の生成](#hive-frequencyfeature)
2. [二項分類におけるカテゴリ変数のリスク](#hive-riskfeature)
3. [[Datetime] フィールドからの特徴の抽出](#hive-datefeatures)
4. [[Text] フィールドからの特徴の抽出](#hive-textfeatures)
5. [GPS 座標間の距離の計算](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>頻度ベースの特徴の生成

カテゴリ変数のレベルの出現頻度、または複数のカテゴリ変数のレベルの特定の組み合わせの出現頻度を計算することが役立つことがよくあります。これらの頻度を計算するには、次のスクリプトを使用できます。

		select 
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select 
				<column_name1>,<column_name2>, count(*) as sub_count 
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;
	

###<a name="hive-riskfeature"></a>二項分類におけるカテゴリ変数のリスク

二項分類では、使用中のモデルが数値の特徴のみを処理する場合、数値以外の分類変数を数値の特徴に変換する必要があります。これを行うには、各数値以外のレベルを数値のリスクに置き換えます。このセクションでは、カテゴリ変数のリスクの値 (対数オッズ) を計算するいくつかの汎用 Hive クエリについて説明します。 


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select 
	    	<column_name1>,<column_name2>, 
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select 
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select 
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename> 
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b 

この例では、データから計算されたリスク値をスムースにするため、変数 `smooth_param1` と `smooth_param2` が設定されています。リスクの範囲は -Inf から Inf までです。リスク > 0 は、ターゲット = 1 である確率が 0.5 よりも大きいことを示します。 

リスクのテーブルが計算されると、リスクの値をリスクのテーブルと結合して、リスクの値をテーブルに割り当てることができます。Hive 結合クエリは、前のセクションで用意されました。

###<a name="hive-datefeature"></a>[Datetime] フィールドから特徴を抽出する

Hive には、[datetime] フィールドを処理するための UDF のセットが付属します。Hive では、既定の datetime 形式は 'yyyy-MM-dd 00:00:00' (例: '1970-01-01 12:21:32') です。このセクションでは、[datetime] フィールドから日と月を抽出する例、および既定の形式以外の datetime 文字列を既定の形式の datetime 文字列に変換する例を示します。 

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

この Hive クエリは、`<datetime フィールド>` が既定の datetime 形式であることを前提としています。

[datetime] フィールドが既定の形式でない場合、まず [datetime] フィールドを Unix のタイムスタンプに変換してから、Unix のタイムスタンプを既定の形式の datetime 文字列に変換する必要があります。datetime が既定の形式の場合は、特徴を抽出するために組み込みの datetime ユーザー定義関数を適用することができます。

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

このクエリでは、`<datetime field>` が `03/26/2015 12:04:39` のようなパターンであった場合、`'<pattern of the datetime field>'` は `'MM/dd/yyyy HH:mm:ss'` である必要があります。これをテストするために、ユーザーは次を実行できます。

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

このクエリの  `hivesampletable` は、クラスターがプロビジョニングされるときに、既定ですべての Azure HDInsight Hadoop クラスターにプレインストールされます。 


###<a name="hive-textfeature"></a>[Text] フィールドから特徴を抽出する

Hive テーブルに、スペースで区切られた単語から成る文字列を含む [Text] フィールドがある場合、次のクエリは、文字列の長さと文字列内の単語数を抽出します。

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>GPS 座標のセット間の距離を計算する

このセクションで指定されたクエリは、NYC タクシー乗車データに直接適用できます。このクエリの目的は、特徴を生成する Hive の組み込みの数学関数を適用する方法を示すことです。 

このクエリで使用されているフィールドは、pickup\_longitude、 pickup\_latitude、 dropoff\_longitude、および dropoff\_latitude という名前の 乗車 (pickup) と降車 (dropoff) の位置を示す GPS 座標です。pickup 座標と dropoff 座標間の直線距離を計算するクエリは次のとおりです。

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, 
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
		from nyctaxi.trip 
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10; 

2 つの GPS 座標の距離を計算する方程式は、Peter Lapisu による [Movable Type Scripts](http://www.movable-type.co.uk/scripts/latlong.html) サイトにあります。彼の Javascript で、関数 toRad() は単に  `lat_or_lon*pi/180`, which converts degrees to radians. Here,  であり、これは、角度をラジアンに変換します。ここで、`lat_or_lon` は緯度または経度です。Hive には関数  `atan2`, but provides the function  はありませんが関数 `atan` はあるので、上記の Hive クエリでは、 `atan2` function is implemented by  関数は [Wikipedia](http://en.wikipedia.org/wiki/Atan2) に記載された定義を使用して、`atan` 関数により実装されています。 

![Create workspace][1]

Hive の組み込み UDF の完全なリストは、「[Apache Hive Wiki](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions)」の「**組み込み関数**」セクションにあります。 

## <a name="tuning"></a> 高度なトピック:Hive のパラメーターを調整してクエリの速度を向上させる

Hive クラスターの既定のパラメーター設定は、Hive クエリおよびクエリが処理するデータに適していないことがあります。このセクションでは、Hive クエリのパフォーマンスを向上させるためにユーザーが調整できるパラメーターのいくつかについて説明します。ユーザーは、データ処理のクエリの前に、パラメーター調整クエリを追加する必要があります。 

1. **Java ヒープ スペース**:大規模なデータセットの結合または長いレコードの処理が関係するクエリで一般的なエラーは、**ヒープ領域の不足**です。これを調整するには、パラメーター  `mapreduce.map.java.opts` および  `mapreduce.task.io.sort.mb` を希望する値に設定します。たとえば次のようになります。

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	このパラメーターでは、Java ヒープ スペースに 4 GB のメモリが割り当てられ、より多くのメモリを割り当てることで並べ替えも効率化しています。ヒープ スペース関連のジョブ失敗のエラーが発生する場合、これらの割り当てを試してみるとよいでしょう。

2. **DFS ブロック サイズ**:このパラメーターでは、ファイル システムに保存されるデータの最小単位を設定します。たとえば、DFS ブロック サイズが 128 MB の場合、サイズが 128 MB 未満のデータは 1 つのブロックに保存されますが、128 MB を超えるデータでは追加のブロックが割り当てられます。非常に小さいブロック サイズを選ぶと、 Hadoop に大きいオーバーヘッドが生じます。これは、NameNode がファイルに付随する関連ブロックを検索するためにさらに多くの要求を処理する必要があるためです。数ギガバイト (またはそれ以上) のデータを処理する際の推奨設定:

		set dfs.block.size=128m;

3. **Hive で結合操作を最適化する**:一般的には Map/Reduce フレームワークの結合操作は Reduce フェーズで実行されますが、Map フェーズで結合する (「mapjoins」ともいう) ようスケジュールすることでパフォーマンスを非常に向上させられることがあります。可能な場合に Hive でこれを行うようにするには、次のように設定します。

		set hive.auto.convert.join=true;

4. **Hive にマッパーの数を指定する** :Hadoop では、ユーザーにレジューサの数の設定を許可していますが、一般にマッパーの数はユーザーによっては設定されません。マッパーの数をある程度制御できるようにするトリックは、Hadoop 変数  *mapred.min.split.size* および  *mapred.max.split.size* の値を選択することです。これは、各マップ タスクのサイズが次の式によって決定されるためです。

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	一般に、 *mapred.min.split.size* の既定値は 0、 *mapred.max.split.size* の既定値は **Long.MAX**、そして  *dfs.block.size* の既定値は 64MB です。お分かりのとおり、指定されたデータのサイズでは、これらのパラメーターを「設定」して調整すると、使用するマッパーの数を調整できるようになります。 

5. Hive のパフォーマンスを最適化する他の**高度なオプション**のいくつかについて以下に説明します。これらのオプションにより、タスクをマップおよび削減するためのメモリの割り当てを設定でき、パフォーマンスの調整に役立ちます。 `mapreduce.reduce.memory.mb` は、Hadoop クラスターの各ワーカー ノードの物理メモリのサイズを超えられないことに注意してください。

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-process-hive-tables/atan2new.png
[10]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-3.png



<!--HONumber=49-->