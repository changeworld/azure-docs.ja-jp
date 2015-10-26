<properties 
	pageTitle="高度な分析プロセスで HDInsight Hadoop クラスターに Hive クエリを送信する | Microsoft Azure" 
	description="ハイブ テーブルからのデータの処理" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="hangzh;bradsev" />

#<a name="heading"></a>高度な分析プロセスで HDInsight Hadoop クラスターに Hive クエリを送信する

このドキュメントでは、Azure の HDInsight サービスが管理する Hadoop クラスターに Hive クエリを送信するさまざまな方法について説明します。Hive クエリは、以下のものを使用して送信できます。

* クラスターのヘッドノードでの Hadoop コマンド ライン
* IPython Notebook 
* Hive エディター
* Azure PowerShell スクリプト 

データを調査するため、または埋め込みハイブ ユーザー定義関数 (UDF) を使用する機能を生成するために使用できる汎用的なハイブ クエリが提供されます。

クエリの例 ([NYC Taxi Trip Data](http://chriswhong.com/open-data/foil_nyc_taxi/) シナリオに固有) は、[Github リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)でも提供されます。これらのクエリには、指定されたデータ スキーマが既にあり、すぐに送信してこのシナリオのために実行できる状態になっています。

最後のセクションでは、Hive クエリのパフォーマンスを向上させるためにユーザーが調整できるパラメーターについて説明します。

## 前提条件
この記事では、以下のことを前提としています。
 
* Azure のストレージ アカウントを作成している。この作業の手順については、「[Azure ストレージ アカウントの作成](../hdinsight-get-started.md#storage)」をご覧ください。 
* HDInsight サービスで Hadoop クラスターをプロビジョニングしている。手順については、[HDInsight クラスターのプロビジョニング](../hdinsight-get-started.md#provision)に関するページをご覧ください。
* Azure HDInsight Hadoop クラスターのハイブ テーブルにデータがアップロードされている。アップロードされていない場合は、まず「[データを作成して Hive テーブルに読み込む](machine-learning-data-science-hive-tables.md)」にある指示に従って Hive テーブルにデータをアップロードします。
* クラスターへのリモート アクセスが有効にされている。手順については、「[Hadoop クラスターのヘッド ノードへのアクセス](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess)」をご覧ください。 


## <a name="submit"></a>Hive クエリを送信する方法

1. [Hadoop クラスターのヘッド ノードで Hadoop コマンド ラインを使用して Hive クエリを送信する](#headnode)
2. [Hive エディターで Hive クエリを送信する](#hive-editor)
3. [Azure PowerShell コマンドで Hive クエリを送信する](#ps)
 
###<a name="headnode"></a> 1.Hadoop クラスターのヘッドノードで Hadoop コマンド ラインを使用して Hive クエリを送信する

Hive クエリが複雑な場合、Hadoop クラスターのヘッド ノードに直接クエリを送信することにより、通常、Hive エディターまたは Azure PowerShell スクリプトを使用して送信するよりも速度が上がります。

Hadoop クラスターのヘッド ノードにログインし、ヘッド ノードのデスクトップで Hadoop コマンド ラインを開き、コマンド `cd %hive_home%\bin` を入力します。

ユーザーには Hadoop コマンド ラインでハイブ クエリを送信する 3 つの方法があります。

* 直接
* .hql ファイルの使用
* Hive コマンド コンソールで

#### Hadoop コマンド ラインでハイブ クエリを直接送信する 

ユーザーは、`hive -e "<your hive query>;` のようなコマンドを実行することで、Hadoop コマンド ラインで単純な Hive クエリを直接送信できます。次に例を示します。ここで赤いボックスは Hive クエリを送信するコマンドを囲んでいます。緑色のボックスは Hive クエリの出力を囲んでいます。

![Create workspace][10]

#### .hql ファイルで Hive クエリを送信する

ハイブ クエリがより複雑で、複数の行が存在する場合、コマンド ラインやハイブ コマンド コンソールでクエリを編集することは実際的ではありません。別の方法として、Hadoop クラスターのヘッド ノードでテキスト エディターを使用して、ヘッド ノードのローカル ディレクトリの中の .hql ファイルにハイブ クエリを保存します。次のように `-f` 引数を使用すると、.hql ファイルの Hive クエリを送信できます。
	
	`hive -f "<path to the .hql file>"`

![Create workspace][15]


**Hive クエリの進行状況ステータス画面の出力を抑制する**

既定では、Hadoop コマンド ラインでハイブ クエリを送信した後に、マップ/縮小ジョブの進捗が画面に出力されます。マップ/縮小ジョブの進捗の画面出力を抑制するには、次のように、コマンド ラインで引数 `-S` ("S" は大文字) を使用します。

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Hive コマンド コンソールで Hive クエリを送信する。

ユーザーが Hadoop コマンド ラインで `hive` コマンドを実行すると、まず Hive コマンド コンソールに入力できるようになります。その後、Hive コマンド コンソールで Hive クエリを送信します。たとえば次のようになります。この例では、2 つの赤いボックスは、それぞれ Hive コマンド コンソールに入るために使用するコマンドと、Hive コマンド コンソールで送信された Hive クエリを強調表示しています。緑色のボックスは、Hive クエリからの出力を強調表示しています。

![Create workspace][11]

前の例では、画面上に直接 Hive クエリの結果を出力しています。ユーザーは、ヘッド ノードにあるローカル ファイルや Azure BLOB に出力を書き込むこともできます。次に、ユーザーはその他のツールを使用して、ハイブ クエリの出力をさらに分析することができます。

**Hive クエリの結果をローカル ファイルに出力する。**

Hive クエリの結果をヘッド ノード上のローカル ディレクトリに出力するには、次のように Hadoop コマンド ラインで Hive クエリを送信する必要があります。

	`hive -e "<hive query>" > <local path in the head node>`

次の例では、Hive クエリの出力が `C:\apps\temp` ディレクトリ内の `hivequeryoutput.txt` ファイルに書き込まれます。

![Create workspace][12]

**Hive クエリの結果を Azure BLOB に出力する**

ユーザーは、Hadoop クラスターの既定のコンテナー内にある Azure BLOB に Hive クエリの結果を出力することもできます。ハイブ クエリは、次のように指定する必要があります。

	`insert overwrite directory wasb:///<directory within the default container> <select clause from ...>`

次の例では、Hive クエリの出力が、Hadoop クラスターの既定のコンテナー内にある BLOB ディレクトリ `queryoutputdir` に書き込まれます。ここでは、ディレクトリ名のみを指定する必要があります (BLOB 名は必要ありません)。`wasb:///queryoutputdir/queryoutput.txt` のようにディレクトリと BLOB の両方の名前を指定すると、エラーがスローされます。

![Create workspace][13]

Azure ストレージ エクスプローラーなどのツールを使用して Hadoop クラスターの既定のコンテナーを開く場合、ハイブ クエリの出力が次のように表示されます。フィルター (赤色の四角形によって示されています) を適用して、名前に指定された文字を持つ BLOB のみを取得できます。

![Create workspace][14]

###<a name="hive-editor"></a> 2.Hive エディターで Hive クエリを送信する

ユーザーは Web ブラウザーに URL (`https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor`) を入力することで、クエリ コンソール (Hive エディター) を使用することもできます (ログインするために Hadoop クラスターの資格情報を入力するよう求められます)。

###<a name="ps"></a> 3.Azure PowerShell コマンドで Hive クエリを送信する

ユーザーは PowerShell を使用して Hive クエリを送信することもできます。手順については、「[PowerShell を使用して Hive ジョブを送信する](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell)」を参照してください。

## <a name="explore"></a>データ探索、特徴エンジニアリング、Hive パラメーターの調整

このセクションでは、Azure HDInsight Hadoop クラスターの Hive を使用した次のデータ処理タスクについて説明します。

1. [データの探索](#hive-dataexploration)
2. [特徴の生成](#hive-featureengineering)

> [AZURE.NOTE]サンプルのハイブ クエリでは、Azure HDInsight Hadoop クラスターのハイブ テーブルにデータがアップロードされていることを前提としています。アップロードされていない場合は、まず[データを作成して Hive テーブルに読み込む](machine-learning-data-science-hive-tables.md)に従って Hive テーブルにデータをアップロードします。

###<a name="hive-dataexploration"></a>データの探索
Hive テーブルでデータを探索するために使用する、いくつかのサンプル Hive スクリプトは次のとおりです。

1. パーティションごとの所見の数を取得する `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. 1 日ごとの所見の数を取得する `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. カテゴリ列内のレベルを取得する `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. 2 つのカテゴリ列の組み合わせ内のレベル数を取得する `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. 数値型列の分布を取得する `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

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

###<a name="hive-featureengineering"></a>特徴の生成

このセクションでは、ハイブ クエリを使用して機能を生成する方法について説明します。
  
1. [頻度ベースの特徴の生成](#hive-frequencyfeature)
2. [二項分類におけるカテゴリ変数のリスク](#hive-riskfeature)
3. [[Datetime] フィールドからの特徴の抽出](#hive-datefeature)
4. [[Text] フィールドからの特徴の抽出](#hive-textfeature)
5. [GPS 座標間の距離の計算](#hive-gpsdistance)

> [AZURE.NOTE]追加の機能を生成したら、それらの機能を既存のテーブルに列として追加するか、追加の機能と主キーで新しいテーブルを作成します。新しいテーブルは、元のテーブルと結合することができます。

####<a name="hive-frequencyfeature"></a> 頻度ベースの特徴の生成

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
	

####<a name="hive-riskfeature"></a> 二項分類におけるカテゴリ変数のリスク

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

この例では、データから計算されたリスク値をスムースにするため、変数 `smooth_param1` と `smooth_param2` が設定されています。リスクは -Inf と Inf の間の範囲になります。Risks>0 は、ターゲットが 1 に等しいことの確率が 0.5 より大きいことを意味します。

リスクのテーブルが計算されると、リスクの値をリスクのテーブルと結合して、リスクの値をテーブルに割り当てることができます。ハイブ結合クエリについては、前のセクションで扱われました。

####<a name="hive-datefeature"></a> [Datetime] フィールドからの特徴の抽出

ハイブには、日時フィールドを処理するための一連の UDF があります。ハイブでは、既定の日時形式は 'yyyy-MM-dd 00:00:00' (例: '1970-01-01 12:21:32') となります。このセクションでは、日時フィールドから月を抽出し、その月から日を抽出する例を示します。また、既定以外の形式の日時文字列を既定の形式の日時文字列に変換する例も示します。

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

この Hive クエリは、`<datetime field>` が既定の datetime 形式であることを前提としています。

日時フィールドが既定の形式でない場合、まず日付タイトル フィールドを Unix のタイム スタンプに変換し、Unix のタイム スタンプを既定の形式の日時文字列に変換する必要があります。日時を既定の形式にした後、ユーザーは埋め込み日時 UDF を抽出機能に適用できます。

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

このクエリでは、`<datetime field>` が `03/26/2015 12:04:39` のようなパターンの場合、`'<pattern of the datetime field>'` は `'MM/dd/yyyy HH:mm:ss'` である必要があります。これをテストするために、ユーザーは次を実行できます。

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

このクエリの `hivesampletable` は、Azure HDInsight Hadoop クラスターをプロビジョニングしたときに、すべてのクラスターに既定で付属しています。


####<a name="hive-textfeature"></a> [Text] フィールドからの特徴の抽出

ハイブ テーブルには、スペースで区切られた単語の文字列で成るテキスト フィールドがあることが想定されています。次のクエリでは、文字列の長さ、および文字列内の単語の数の長さを抽出します。

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

####<a name="hive-gpsdistance"></a> GPS 座標間の距離の計算

このセクションで説明されるクエリは、NYC Taxi Trip Data に直接適用できます。このクエリの目的は、機能を生成するために、ハイブの埋め込み数学関数を適用する方法を示すことにあります。

このクエリで使用するフィールドは、ピックアップとドロップオフする場所の GPS 座標で、それぞれ pickup\_longitude、pickup\_latitude、dropoff\_longitude、dropoff\_latitude という名前が付けられています。ピックアップとドロップオフの座標間の直線距離を計算するクエリは、次のとおりです。

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

2 つの GPS 座標間の距離を計算する数式は、[ここ](http://www.movable-type.co.uk/scripts/latlong.html) (Peter Lapisu によって作成されました) にあります。彼の Javascript で、関数 toRad() は単に `lat_or_lon*pi/180` であり、これは、角度をラジアンに変換します。ここで、`lat_or_lon` は緯度または経度です。Hive には関数 `atan2` はありませんが、関数 `atan` はあるので、上記の Hive クエリでは、`atan2` 関数は [Wikipedia](http://en.wikipedia.org/wiki/Atan2) に記載された定義を使用して、`atan` 関数により実装されています。

![Create workspace][1]

Hive 埋め込み UDF のリストについては、[こちら](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions)をご覧ください。

## <a name="tuning"></a>高度なトピック: Hive パラメーターを調整してクエリ速度を向上させる

ハイブ クラスターの既定のパラメーター設定では、ハイブ クエリやクエリが処理するデータに適さない場合があります。このセクションでは、ハイブ クエリのパフォーマンスが向上するようにユーザーが調整できるいくつかのパラメーターについて説明します。ユーザーは、データ処理のクエリの前に、パラメーター調整クエリを追加する必要があります。

1. Java ヒープ スペース: 大規模なデータセットの結合または長いレコードの処理を伴うクエリの場合、一般的なエラーとして、**ヒープ領域の不足**があります。これを調整するには、パラメーター `mapreduce.map.java.opts` と `mapreduce.task.io.sort.mb` を必要な値に設定します。たとえば次のようになります。

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	このパラメーターでは、Java ヒープ スペースに 4 GB のメモリが割り当てられ、より多くのメモリを割り当てることで並べ替えも効率化しています。ヒープ領域に関連するエラーでジョブが失敗する場合に、この調整をすることをお勧めします。

2. DFS ブロック サイズ: このパラメーターでは、ファイル システムに保存されるデータの最小単位を設定します。たとえば、DFS ブロック サイズが 128 MB の場合、サイズが 128 MB 未満のデータは 1 つのブロックに保存されますが、128 MB を超えるデータでは追加のブロックが割り当てられます。非常に小さいブロック サイズを選ぶと、 Hadoop に大きいオーバーヘッドが生じます。これは、NameNode がファイルに付随する関連ブロックを検索するためにさらに多くの要求を処理する必要があるためです。ギガバイト (またはそれ以上) のデータを処理する場合に推奨される設定は、次のとおりです。

		set dfs.block.size=128m;

3. Hive で結合操作を最適化する: 通常、マップ/縮小フレームワークでの結合操作は縮小フェーズで発生しますが、マップ フェーズ ("mapjoins" と呼ばれることもあります) で結合をスケジュールすることで大幅な向上を実現できる場合があります。これをいつでも実行できる場合にハイブを設定するには、次のように設定できます。

		set hive.auto.convert.join=true;

4. Hive にマッパーの数を指定する: Hadoop ではユーザーがレジューサの数を設定できますが、マッパーの数をユーザーが設定することは、通常はできません。hadoop 変数 (mapred.min.split.size と mapred.max.split.size) を選択することによって、この数をある程度制御することができます。各マップ タスクのサイズは、以下によって決まります。

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	通常、mapred.min.split.size の既定値は 0 で、mapred.max.split.size の既定値は Long.MAX で、dfs.block.size の既定値は 64 MB です。お分かりのとおり、指定されたデータのサイズでは、これらのパラメーターを「設定」して調整すると、使用するマッパーの数を調整できるようになります。

5. Hive のパフォーマンスを最適化する他の高度なオプションのいくつかについて以下に説明します。これらのオプションは、マップおよび縮小タスクに割り当てるメモリを設定したり、パフォーマンスの調整するために使用したりできます。`mapreduce.reduce.memory.mb` は、Hadoop クラスターの各ワーカー ノードの物理メモリのサイズを超えることはできないことに注意してください。

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


 

<!---HONumber=Oct15_HO3-->