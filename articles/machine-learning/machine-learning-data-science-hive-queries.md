<properties 
	pageTitle="クラウド データの科学プロセスにおける HDInsight Hadoop クラスターへのハイブ クエリの送信 |Azure" 
	description="ハイブ テーブルからのデータの処理" 
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
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

#<a name="heading"></a> クラウド データの科学プロセスにおける HDInsight Hadoop クラスターへのハイブ クエリの送信

このドキュメントでは、Azure の HDInsight サービスで管理される Hadoop クラスターにハイブ クエリを送信するさまざまな方法について説明します。以下を使用してハイブ クエリを送信できます。 

* クラスターのヘッドノードでの Hadoop コマンド ライン
* IPython Notebook 
* ハイブ エディター
* Azure PowerShell スクリプト 

データを調査するため、または埋め込みハイブ ユーザー定義関数 (UDF) を使用する機能を生成するために使用できる汎用的なハイブ クエリが提供されます。 

クエリの例 ([NYC Taxi Trip Data](http://chriswhong.com/open-data/foil_nyc_taxi/) シナリオに固有) は、[Github リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)でも提供されます。これらのクエリではデータ スキーマが既に指定されていて、実行するために送信する準備が整っています。 

最後のセクションでは、ハイブ クエリのパフォーマンスが向上するようにユーザーが調整できるパラメーターについて説明します。

## 前提条件
この記事では、以下を前提としています。
 
* Azure のストレージ アカウントを作成している。手順を確認する必要がある場合は、[Azure のストレージ アカウントの作成](../hdinsight-get-started.md#storage)を参照してください。 
* HDInsight サービスで Hadoop クラスターをプロビジョニングしている。手順を確認する必要がある場合は、[HDInsight クラスターのプロビジョニング](../hdinsight-get-started.md#provision)を参照してください。
* Azure HDInsight Hadoop クラスターのハイブ テーブルにデータがアップロードされている。アップロードされていない場合は、[ハイブ テーブルを作成してデータをハイブ テーブルに読み込む](machine-learning-data-science-hive-tables.md)の手順に従い、まずデータをハイブ テーブルにアップロードします。
* クラスターへのリモート アクセスが有効にされている。手順を確認する必要がある場合は、[Hadoop クラスターのヘッド ノードへのアクセス](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess)を参照してください。 


- [ハイブ クエリを送信する方法](#submit)
- [データ探索および機能エンジニアリング](#explore)
- [高度なトピック:ハイブ パラメーターを調整してクエリ速度を向上させる](#tuning)

## <a name="submit"></a>ハイブ クエリを送信する方法

###1. Hadoop クラスターのヘッド ノードでの Hadoop コマンド ラインの使用

クエリが複雑な場合、Hadoop クラスターのヘッド ノードに直接ハイブ クエリを送信することにより、通常、ハイブ エディターまたは Azure PowerShell スクリプトを使用して送信するよりも速度が上がります。 

Hadoop クラスターのヘッド ノードにログインし、ヘッド ノードのデスクトップで Hadoop コマンド ラインを開き、コマンド `cd %hive_home%\bin` を入力します。

ユーザーには Hadoop コマンド ラインでハイブ クエリを送信する 3 つの方法があります。 

####Hadoop コマンド ラインでハイブ クエリを直接送信する 

ユーザーは `hive -e "<your hive query>;` のようなコマンドを実行することによって、Hadoop コマンド ラインで単純なハイブ クエリを直接送信できます。次に例を示します。ここで、赤色の四角形はハイブ クエリを送信するコマンドを示し、緑色の四角形はハイブ クエリからの出力を示します。

![Create workspace][10]

####.hql ファイルでハイブ クエリを送信する

ハイブ クエリがより複雑で、複数の行が存在する場合、コマンド ラインやハイブ コマンド コンソールでクエリを編集することは実際的ではありません。別の方法として、Hadoop クラスターのヘッド ノードでテキスト エディターを使用して、ヘッド ノードのローカル ディレクトリの中の .hql ファイルにハイブ クエリを保存します。`-f` 引数を以下のように使用することによって、.hql ファイルでハイブ クエリを送信できます。
	
	`hive -f "<path to the .hql file>"`

![Create workspace][15]


####ハイブ クエリの進捗状況画面出力を抑制する

既定では、Hadoop コマンド ラインでハイブ クエリを送信した後に、マップ/縮小ジョブの進捗が画面に出力されます。マップ/縮小ジョブの進捗の画面出力を抑制するには、引数 `-S` ("S" は大文字) をコマンド ラインで以下のように使用します。

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

####ハイブ コマンド コンソールでハイブ クエリを送信します。

ユーザーが Hadoop コマンド ラインで `hive` コマンドを実行することによって、まずハイブ コマンド コンソールに入力できるようになります。その後、ハイブ コマンド コンソールでハイブ クエリを送信します。たとえば次のようになります。この例の 2 つの赤色の四角形は、ハイブ コマンド コンソールの入力に使用するコマンドとハイブ コマンド コンソールで送信されたハイブ クエリをそれぞれ示しています。緑色の四角形は、ハイブ クエリからの出力を示します。 

![Create workspace][11]

前の例では、画面上にハイブ クエリの結果を直接出力しています。ユーザーはヘッド ノードのローカル ファイルまたは Azure BLOB に出力を記述することもできます。次に、ユーザーはその他のツールを使用して、ハイブ クエリの出力をさらに分析することができます。

####ハイブ クエリの結果をローカル ファイルに出力する 

ヘッド ノードのローカル ディレクトリにハイブ クエリの結果を出力するには、ユーザーは Hadoop コマンド ラインでハイブ クエリを次のように送信する必要があります。

	`hive -e "<hive query>" > <local path in the head node>`

次の例では、ハイブ クエリの出力は、ディレクトリ `C:\apps\temp` のファイル `hivequeryoutput.txt` に記述されます。

![Create workspace][12]

####Azure BLOB にハイブ クエリの結果を出力する

ユーザーはハイブ クエリの結果を Azure BLOB (Hadoop クラスターの既定のコンテナー内) に出力することもできます。ハイブ クエリは、次のように指定する必要があります。

	`insert overwrite directory wasb:///<directory within the default container> <select clause from ...>`

次の例では、ハイブ クエリの出力は、Hadoop クラスターの既定のコンテナー内の BLOB ディレクトリ `queryoutputdir` に書き込まれます。ここでは、ディレクトリ名のみを指定する必要があります (BLOB 名は必要ありません)。`wasb:///queryoutputdir/queryoutput.txt` のようにディレクトリと BLOB の両方の名前を指定すると、エラーがスローされます。 

![Create workspace][13]

Azure ストレージ エクスプローラーなどのツールを使用して Hadoop クラスターの既定のコンテナーを開く場合、ハイブ クエリの出力が次のように表示されます。フィルター (赤色の四角形によって示されています) を適用して、名前に指定された文字を持つ BLOB のみを取得できます。

![Create workspace][14]

###2. ハイブ エディターまたは Azure PowerShell コマンドの使用

ユーザーは Web ブラウザー ( `https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor`) に URL を入力してクエリ コンソール (ハイブ エディター) を使用できます (ログインするために、Hadoop クラスターの資格情報を入力するように求められます)。または、[PowerShell を使用してハイブ ジョブを送信する](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell) こともできます。 

## <a name="explore"></a>データ探索、エンジニアリング機能、およびハイブ パラメーターの調整

このセクションでは、Azure HDInsight Hadoop クラスターのハイブを使用して、data wrangling タスクについて、またハイブ クエリのパフォーマンスを向上させるための一部のハイブ パラメーターの調整に関するより高度なトピックについて説明します。

1. [データ探索](#hive-dataexploration)
2. [機能生成](#hive-featureengineering)
3. [高度なトピック: ハイブ パラメーターを調整してクエリ速度を向上させる](#tune-parameters)

> [AZURE.NOTE] サンプルのハイブ クエリでは、Azure HDInsight Hadoop クラスターのハイブ テーブルにデータがアップロードされていることを前提としています。アップロードされていない場合は、[ハイブ テーブルを作成してデータをハイブ テーブルに読み込む](machine-learning-data-science-hive-tables.md) の手順に従い、まずデータをハイブ テーブルにアップロードします。

###<a name="hive-dataexploration"></a>データ探索
ハイブ テーブルでデータを探索するために使用できるいくつかのサンプルハイブ スクリプトを以下に示します。

1. パーティションごとの監視数を取得します。
	`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. 1 日の監視数を取得します。
	`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);` 

3. カテゴリ列のレベルを取得します。  
	`SELECT  distinct <column_name> from <databasename>.<tablename>`

4. 2 つのカテゴリ列の組み合わせでレベル数を取得します。 
	`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. 数値型列の分布を取得します。  
	`SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. 結合する 2 つのテーブルからレコードを抽出します。 

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

###<a name="hive-featureengineering"></a>機能生成

このセクションでは、ハイブ クエリを使用して機能を生成する方法について説明します。 

> [AZURE.NOTE]
> 追加の機能を生成したら、それらの機能を既存のテーブルに列として追加するか、追加の機能と主キーで新しいテーブルを作成します。新しいテーブルは、元のテーブルと結合することができます。  

1. [頻度ベースの機能生成](#hive-frequencyfeature)
2. [バイナリ分類におけるカテゴリ変数のリスク](#hive-riskfeature)
3. [日時フィールドからの機能の抽出](#hive-datefeatures)
4. [テキスト フィールドからの機能の抽出](#hive-textfeatures)
5. [GPS 座標間の距離の計算](#hive-gpsdistance)

####<a name="hive-frequencyfeature"></a>頻度ベースの機能生成

場合によっては、カテゴリ変数のレベルの頻度や複数のカテゴリ変数のレベルの組み合わせの頻度を計算することが重要になります。ユーザーは頻度を計算するのに、次のスクリプトを使用できます。

		select 
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select 
				<column_name1>,<column_name2>, count(*) as sub_count 
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;
	

####<a name="hive-riskfeature"></a>バイナリ分類におけるカテゴリ変数のリスク

バイナリ分類では、数値以外のレベルを数値のリスクで置き換えることによって、数値以外のカテゴリ変数を数値の機能に変換する必要がある場合があります (一部のモデルでは数値機能しか受け入れられないため)。このセクションでは、カテゴリ変数のリスク値 (ログ オッズ) を計算するいくつかの汎用的なハイブ クエリについて説明します。 


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

この例では、変数 `smooth_param1` と `smooth_param2` は、リスク値をデータから円滑に計算できるように設定されます。リスクは -Inf と Inf の間の範囲になります。Risks>0 は、ターゲットが 1 に等しいことの確率が 0.5 より大きいことを意味します。 

リスク テーブルが計算されると、ユーザーはリスク値をリスク テーブルと結合することによって、そのリスク値をテーブルに割り当てることができます。ハイブ結合クエリについては、前のセクションで扱われました。

####<a name="hive-datefeature"></a>日時フィールドからの機能の抽出

ハイブには、日時フィールドを処理するための一連の UDF があります。ハイブでは、既定の日時形式は 'yyyy-MM-dd 00:00:00' (例: '1970-01-01 12:21:32') となります。このセクションでは、日時フィールドから月を抽出し、その月から日を抽出する例を示します。また、既定以外の形式の日時文字列を既定の形式の日時文字列に変換する例も示します。 

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

このハイブ クエリでは、`<datetime field>` は既定の日時形式であることが想定されています。

日時フィールドが既定の形式でない場合、まず日付タイトル フィールドを Unix のタイム スタンプに変換し、Unix のタイム スタンプを既定の形式の日時文字列に変換する必要があります。日時を既定の形式にした後、ユーザーは埋め込み日時 UDF を抽出機能に適用できます。

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

このクエリでは、`<datetime field>` を `03/26/2015 12:04:39` のようなパターンにする場合、`'<pattern of the datetime field>'` を `'MM/dd/yyyy HH:mm:ss'` にする必要があります。これをテストするために、ユーザーは次を実行できます。

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

このクエリでは、クラスターをプロビジョニングする場合、`hivesampletable` は既定ですべての Azure HDInsight Hadoop クラスターに付属しています。 


####<a name="hive-textfeature"></a>テキスト フィールドからの機能の抽出

ハイブ テーブルには、スペースで区切られた単語の文字列で成るテキスト フィールドがあることが想定されています。次のクエリでは、文字列の長さ、および文字列内の単語の数の長さを抽出します。

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

####<a name="hive-gpsdistance"></a>GPS 座標間の距離の計算

このセクションで説明されるクエリは、NYC Taxi Trip Data に直接適用できます。このクエリの目的は、機能を生成するために、ハイブの埋め込み数学関数を適用する方法を示すことにあります。 

このクエリで使用するフィールドは、ピックアップおよびドロップオフする場所の GPS 座標で、それぞれ pickup_longitude、pickup_latitude、dropoff_longitude、dropoff_latitude という名前が付けられています。ピックアップとドロップオフの座標間の直線距離を計算するクエリは、次のとおりです。

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

2 つの GPS 座標間の距離を計算する数式は、[ここ](http://www.movable-type.co.uk/scripts/latlong.html) (Peter Lapisu によって作成されました) にあります。この Javascript では、関数 toRad() は、度をラジアンに変換する `lat_or_lon*pi/180` になります。ここで、`lat_or_lon` は緯度または経度になります。, which converts degrees to radians. Here, ハイブでは関数 `atan2` は提供されませんが、`atan` は提供されます。上記のハイブ クエリでは、`atan2` は `atan` 関数によって実装されています ([Wikipedia](http://en.wikipedia.org/wiki/Atan2) の定義に基づいています)。, but provides function  function is implemented by  

![Create workspace][1]

ハイブ埋め込み UDF の完全なリストは、[ここ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions)を参照してください。 

## <a name="tuning"></a> 高度なトピック:ハイブ パラメーターを調整してクエリ速度を向上させる

ハイブ クラスターの既定のパラメーター設定では、ハイブ クエリやクエリが処理するデータに適さない場合があります。このセクションでは、ハイブ クエリのパフォーマンスが向上するようにユーザーが調整できるいくつかのパラメーターについて説明します。データを処理するクエリの前に、ユーザーは、クエリを調整するパラメーターを追加する必要があります。 

1. Java ヒープ領域:大規模なデータセットの結合、または長いレコードの処理が関係するクエリの一般的なエラーに、**[ヒープ領域が不足しています]** というエラーあります。これは、パラメーター `mapreduce.map.java.opts` と `mapreduce.task.io.sort.mb` を目的の値に設定することによって調整できます。たとえば次のようになります。

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	このパラメーターは、Java ヒープ領域に 4 GB のメモリを割り当てます。また、より多くのメモリを割り当てることによって、並べ替えをより効率的にします。ヒープ領域に関連するエラーでジョブが失敗する場合に、この調整をすることをお勧めします。

2. DFS ブロック サイズ:このパラメーターは、ファイル システムに格納されるデータの最小単位を設定します。例のように、DFS ブロック サイズが 128 MB である場合、サイズが 128 MB 以下のすべてのデータが単一ブロックに格納されます。一方、128 MB より大きいデータは余分のブロックに割り当てられます。非常に小さいブロック サイズを選択すると、ファイルに関連するブロックを検索するため名前ノードがより多くの要求を処理する必要が生じるため、Hadoop でオーバーヘッドが大きくなります。ギガバイト (またはそれ以上) のデータを処理する場合に推奨される設定は、次のとおりです。

		set dfs.block.size=128m;

3. ハイブでの結合操作の最適化:マップ/縮小フレームワークでの結合操作は通常縮小フェーズで発生しますが、マップ フェーズ ("mapjoins" と呼ばれることもあります) で結合をスケジュールすることによって大幅な向上を実現できる場合があります。これをいつでも実行できる場合にハイブを設定するには、次のように設定できます。

		set hive.auto.convert.join=true;

4. ハイブへのマッパーの数の指定:Hadoop ではユーザーがレジューサの数を設定できますが、マッパーの数をユーザーが設定することは、通常はできません。hadoop 変数 (mapred.min.split.size と mapred.max.split.size) を選択することによって、この数をある程度制御することができます。各マップ タスクのサイズは、以下によって決まります。

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	通常、mapred.min.split.size の既定値は 0 で、mapred.max.split.size の既定値は Long.MAX で、dfs.block.size の既定値は 64 MB です。これらから分かるように、データ サイズを指定し、それらを「設定」することによってこれらのパラメーターを調整することで、使用されるマッパーの数を調整できます。 

5. ハイブのパフォーマンスを最適化するためのその他のいくつか詳細オプションについては、以下で説明します。これらのオプションは、マップおよび縮小タスクに割り当てるメモリを設定したり、パフォーマンスの調整するために使用したりできます。 `mapreduce.reduce.memory.mb` は、Hadoop クラスター内の各ワーカー ノードの物理メモリのサイズ以下でなければならないことに注意してください。

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-hive-queries/atan2new.png
[10]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-hive-queries/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-hive-queries/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-hive-queries/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-3.png



<!--HONumber=49--> 