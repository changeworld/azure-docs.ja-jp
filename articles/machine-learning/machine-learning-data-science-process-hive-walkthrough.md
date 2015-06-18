<properties 
	pageTitle="実行中の Azure データ サイエンス プロセス: HDInsight Hadoop クラスターを使用する | Azure" 
	description="公開されている使用可能なデータセットを使用してモデルのビルドと配置を行うための HDInsight Hadoop クラスターを使用したエンド ツー エンド Azure データ サイエンス プロセス。" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

                
# Azure データ サイエンス プロセスの操作: HDInsight Hadoop クラスターを使用する

このチュートリアルでは、Azure HDInsight Hadoop クラスターを使用してエンド ツー エンドで Azure データ サイエンス プロセス マップに従って、[NYC タクシー乗車](http://www.andresmh.com/nyctaxitrips/)データセットという公開されている使用可能なデータセットを操作するモデルのビルドと配置を行います。 


## <a name="dataset"></a>NYC タクシー乗車データセットの説明

NYC タクシー乗車データは、約 20 GB の圧縮されたコンマ区切り値 (CSV) ファイル (非圧縮で最大 48 GB) です。1 億 7300 万以上の個々の乗車と、各乗車に支払われた料金で構成されています。各旅行レコードには、pickup (乗車) と drop-off (降車) の場所と時間、匿名化されたタクシー運転手の (運転) 免許番号、および medallion (タクシーの一意の ID) 番号が含まれています。データには 2013 年のすべての乗車が含まれ、データは月ごとに次の 2 つのデータセットに用意されています。

1.  'trip_data' CSV ファイルには、乗車の詳細 (乗客数、乗車地点、降車地点、乗車時間、乗車距離など) が含まれています。いくつかのサンプル レコードを次に示します。

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2.  'trip_fare' CSV ファイルには、各乗車に対して支払われた料金の詳細 (支払いの種類、料金、追加料金と税、チップ、道路などの通行料、および合計支払金額など) が含まれます。いくつかのサンプル レコードを次に示します。

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

trip_data と trip_fare を結合するための一意のキーは、[medallion]、[hack_licence] および [pickup_datetime} の各フィールドで構成されています。

それぞれ 12 の乗車 'trip_data' および 'trip_fare' ファイルがあります。ファイル名の 1 ～ 12 の番号は、乗車があった月を表しています。 

## <a name="mltasks"></a>予測タスクの例
データ処理に取り掛かる際、分析に基づいて一種の予測を行うことを目標にすると、プロセスに含める必要があるタスクを明確にするのに役立ちます。
このチュートリアルで取り扱う 3 つの予測問題の例を以下に示します。これらの例の公式は、 *tip_amount* に基づいています。

1. **二項分類**:乗車においてチップが支払われたかどうかを予測します。つまり、 *tip_amount* が $0 より大きい場合は肯定的な例で、 *tip_amount* が $0 の場合は否定的な例です。

2. **多クラス分類**:乗車でチップとして支払われた金額の範囲を予測します。 *tip_amount* を次の 5 つの箱つまりクラスに分割します。
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **回帰タスク**:乗車で支払われたチップの金額を予測します。  


## <a name="setup"></a>Azure データ サイエンス環境を設定する

「[環境の計画](machine-learning-data-science-plan-your-environment.md)」ガイドから分かるように、Azure で NYC タクシー乗車データセットを操作する際に実行できるいくつかのアプローチがあります。

- Azure BLOB でデータを操作し、Azure Machine Learning でモデリングする
- データを SQL Server データベースに読み込み、Azure Machine Learning でモデリングする
- データを HDInsight Hive テーブルに読み込み、Azure Machine Learning でモデリングする

データセットのサイズ、データ ソースの場所、および選択された Azure の対象環境に基づくと、このシナリオは[シナリオ #7:ローカル ファイル内の大規模なデータセット、Azure HDInsight Hadoop クラスターの Hive をターゲットにする](../machine-learning-data-science-plan-sample-scenarios.md#largedbtohive) と類似しています。

Azure HDInsight Hadoop クラスターを使用するこのアプローチ用に、より具体的には、データを格納および処理する Hive テーブルとクエリ用に Azure データ サイエンス環境をセット アップするには、以下の手順を実行します。

1. [ストレージ アカウントを作成する](../storage-whatis-account.md)

2. [Azure ML ワークスペースを作成する](machine-learning-create-workspace.md)

3. [データ サイエンス **Windows** 仮想マシンをプロビジョニングする] (machine-learning-data-science-setup-virtual-machine.md)。

	> [AZURE.NOTE] サンプル スクリプトは、仮想マシンのセットアップ プロセス中に、データ サイエンスの仮想マシンにダウンロードされます。仮想マシンのインストール後スクリプトが完了すると、サンプルは  *C:\Users<user_name>\Documents\Data Science Scripts* にある仮想マシンのドキュメント ライブラリに入ります。  以下のように、このサンプル フォルダーは **Sample Scripts** といいます。サンプルの Hive クエリは、**Sample Scripts** フォルダー内の .hql 拡張子が付いたファイルに含まれています。このフォルダーへのパスの中の *<user_name>* は、Azure ユーザー名ではなく仮想マシンの Windows ログイン名であることに注意してください。

4. [データ サイエンス用に Azure HDInsight Hadoop Clusters をカスタマイズする](machine-learning-data-science-customize-hadoop-cluster.md)。この手順では、全ノードに 64 ビットの Anaconda Python 2.7 がインストールされた Azure HDInsight Hadoop クラスターを作成します。カスタマイズされた Hadoop クラスターを作成したら、このカスタマイズのトピックで概説する手順を使用して、Azure ポータルにある Hadoop クラスターのヘッド ノードへのリモート アクセスを有効にします。


## <a name="getdata"></a>公開されているソースからデータを取得する

公開されている場所から [NYC タクシー乗車](http://www.andresmh.com/nyctaxitrips/)データセットを取得するには、「[Azure BLOB ストレージとの間でデータを移動する](machine-learning-data-science-move-azure-blob.md)」で説明するいずれかの方法を使用して、データを新しい仮想マシンにコピーします。

AzCopy を使用してデータをコピーするには

1. 仮想マシン (VM) にログインする

2. VM のデータ ディスクにディレクトリを新規作成する (注:データ ディスクとして仮想マシンに付属する一時ディスクを使用しないでください)。

3. コマンド プロンプト ウィンドウで、次の AzCopy コマンドを実行します。<path_to_data_folder> を、(2) で作成したデータ フォルダーのパスに置き換えます。

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxitrip /Dest:<path_to_data_folder> /S

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxifare /Dest:<path_to_data_folder> /S

	AzCopy が完了すると、合計 24 のZIP CSV ファイル (trip_data に 12 個、trip_fare に 12 個) がデータ フォルダーにあるはずです。

	>[AZURE.NOTE] *Source:https://getgoing.blob.core.windows.net/nyctaxitrip* および *Source:https://getgoing.blob.core.windows.net/nyctaxifare* では、非圧縮の NYC タクシー データをユーザーと共有するために使用する 2 つの Azure パブリック コンテナーを指定します。これらの 2 つの Azure パブリック コンテナーの読み取りには、アクセス キーは不要です。 

## <a name="upload"></a>データを Azure HDInsight Hadoop クラスターの既定のコンテナーにアップロードする

コマンド プロンプトまたは仮想マシンの Windows PowerShell ウィンドウで、次の AzCopy コマンドを実行します。次のパラメーターを、Hadoop クラスターの作成時に指定した値に置き換えます。次のように置き換えます。

* ***&#60;path_to_data_folder>*** を、前のセクションで作成したデータ フォルダーに 
* ***&#60;Hadoop クラスターのストレージ アカウント名>*** を、クラスターで使用するストレージ アカウントに
* ***&#60;Hadoop クラスターの既定のコンテナー>*** を、クラスターで使用する既定のコンテナーに
* ***&#60;ストレージ アカウント キー>*** を、クラスターで使用するストレージ アカウントのキーに

このコマンドで参照する ***nyctaxitripraw*** または ***nyctaxifareraw*** が存在しない場合は、コンテナーに作成されます。 

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

## <a name="#hive-db-tables"></a>Hive データベースと月ごとにパーティション分割されたテーブルを作成する

このチュートリアルで使用する Hive クエリにアクセスして実行するには、Hadoop クラスターのヘッド ノードにログインし、ヘッド ノードのデスクトップで Hadoop コマンド ラインを開き、次のコマンドを入力して Hive ディレクトリに入ります。

    cd %hive_home%\bin

これらの手順または代わりの手順についてさらに支援が必要な場合は、「[Hadoop コマンド ラインから直接 Hive クエリを送信する](machine-learning-data-science-process-hive-tables.md#submit)」セクションを参照してください。 

「[Azure データ サイエンス環境](#setup)」の手順に従って Azure 仮想マシンを作成した場合、Hive クエリの例が **Sample Scripts** フォルダーにある仮想マシンに既にダウンロードされているはずです。代わりに、ユーザーは .hql ファイルを [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) からチェック アウトすることもできます。 

.hql ファイルで Hive クエリを送信するには、.hql ファイルを Hadoop クラスターのヘッド ノードのローカル ディレクトリに転送する必要があります。次の例では、.hql ファイルがヘッド ノードの ***C:\temp&#92;*** ディレクトリに転送されていることを前提としています。

***C:\temp\sample_hive_create_db_and_tables.hql*** ファイルの内容を以下に示します。このファイルでは、Hive データベース ***nyctaxidb*** およびテーブル ***trip*** と ***fare*** が作成されます。

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	( 
	    medallion string, 
	    hack_license string,
	    vendor_id string, 
	    rate_code string, 
	    store_and_fwd_flag string, 
	    pickup_datetime string, 
	    dropoff_datetime string, 
	    passenger_count int, 
	    trip_time_in_secs double, 
	    trip_distance double, 
	    pickup_longitude double, 
	    pickup_latitude double, 
	    dropoff_longitude double, 
	    dropoff_latitude double)  
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare 
	( 
	    medallion string, 
	    hack_license string, 
	    vendor_id string, 
	    pickup_datetime string, 
	    payment_type string, 
	    fare_amount double, 
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

ヘッド ノードの Hadoop コマンド ラインに次のコマンドを入力して、これらの Hive クエリを送信します。
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"


## <a name="#load-data"></a>パーティションごとにテーブルにデータを読み込む
Hadoop コマンド ラインで次の PowerShell コマンドを実行すると、月ごとにパーティション分割されて Hive テーブルにデータが読み込まれます。

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

検査用の  *sample_hive_load_data_by_partitions.hql* ファイルの内容を次に示します。

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/fare_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

### <a name="#show-db"></a>HDInsight Hadoop クラスターのデータベースを表示する

Hadoop コマンド ライン ウィンドウ内に HDInsight Hadoop クラスターで作成されたデータベースを表示するには、Hadoop コマンド ラインで次のコマンドを実行します。

	hive -e "show databases;"

### <a name="#show-tables"></a>nyctaxidb データベースのテーブルを表示する 
	
nyctaxidb データベースのテーブルを表示するには、Hadoop コマンドラインで次のコマンドを実行します。
	
	hive -e "show tables in nyctaxidb;"
   
## <a name="#explore-hive"></a>Hive におけるデータの探索と特徴エンジニアリング
Hive テーブルに読み込まれるデータのデータの探索タスクおよび特徴エンジニアリング タスクは、Hive クエリを使用して実現します。このセクションのチュートリアルで行うこのようなタスクの例を次に示します。

- 両方のテーブルの、上位 10 個のレコードを表示する。
- さまざまな期間で、いくつかのフィールドのデータの分布を探索する。
- [経度] フィールドと [緯度] フィールドのデータの品質を調査する。
- **tip_amount** に基づいて、二項分類および多クラス分類のラベルを生成する。
- 直線乗車距離を計算して、特徴を生成する。
- 2 つのテーブルを結合して、モデルのビルドに使用するランダム サンプルを抽出する。

Hive でこれらのデータ探索と特徴エンジニアリングが完了すると、Azure Machine Learning にすぐに進める状態になっていることが期待できます。最後の Hive クエリを保存してデータの抽出とサンプリングを行い、その後クエリをコピーし、直接 Azure Machine Learning のリーダー モジュールに貼り付けます。これで、このデータのモデルをビルドできる状態になりました。

### 探索:trip テーブルの上位 10 個のレコードを表示する

データ スキーマの特性とデータの外観を把握するために、各テーブルから上位 10 個のレコードを抽出します。レコードを検査するには、Hadoop コマンド ライン コンソールから次の 2 つのクエリを実行します。

 *table _trip_* で上位 10 個のレコードを取得するには:

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
 *table _fare_* で上位 10 個のレコードを取得するには:
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

### 探索:12 個のそれぞれのパーティションのレコードの数を表示する

12 個の月単位のそれぞれのパーティションのレコード数を表示するには、Hadoop コマンド ライン コンソールで次のコマンドを実行します。
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

### 探索:medallion (タクシー番号) ごとの乗車回数の分布

この例では、指定した期間内で乗車回数が 100 を超える medallion (タクシー番号) を識別します。クエリは、パーティション分割されたテーブルのアクセスの恩恵を受けています。これは、パーティション分割されたテーブルが、パーティション変数 **month** によって条件設定されているためです。 

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql"

検査用の  *sample_hive_trip_count_by_medallion.hql* ファイルの内容を次に示します。

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

### 探索:medallion および hack_license ごとの乗車回数の分布

テーブルの複数の列によるグループ化の例を次に示します。この場合は、"medallion" 列と "hack_license" 列です。Hadoop コマンド ライン コンソールから、次のコマンドを実行します。

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql"

検査用の  *sample_hive_trip_count_by_medallion_license.hql* ファイルの内容を次に示します。
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

### データ品質の評価:経度または緯度が無効なレコードを確認する

この例では、[経度] フィールドまたは [緯度] フィールドに無効な値 (ラジアン角は -90 ～ 90 の間である必要があります) または座標 (0, 0) が含まれているかどうかを調査します。

Hadoop コマンド ライン コンソールから、次のコマンドを実行します。

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

このコマンドに含まれている引数 *-S* は、Hive の Map/Reduce ジョブの状態の画面出力を抑制します。このコマンドは、Hive クエリの画面出力を読みやすくするので役立ちます。 

検査用の  *sample_hive_quality_assessment.hql* ファイルの内容を次に示します。

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'));

### 探索:チップが支払われた乗車と支払われなかった乗車の頻度

この例では、特定の期間中 (または、1 年間をカバーする場合は全データセットで) チップが支払われた乗車と支払われなかった乗車の数を確認します。この分布は、後で二項分類モデリングに使用する、二項 ラベル分布を反映しています。

Hadoop コマンド ライン コンソールから、次のコマンドを実行します。

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

検査用の  *sample_hive_tipped_frequencies.hql* ファイルの内容を次に示します。

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

### 探索:チップの範囲の頻度

この例では、指定した期間内に (または、1 年間をカバーする場合は全データセットで) チップの範囲の分布を計算します。これは、後で多クラス分類モデリングに使用されるラベル クラスの分布です。

Hadoop コマンド ライン コンソールから、次のコマンドを実行します。

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

検査用の  *sample_hive_tip_range_frequencies.hql* の内容を次に示します。

	SELECT tip_class, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount=0, 0, 
            if(tip_amount>0 and tip_amount<=5, 1, 
            if(tip_amount>5 and tip_amount<=10, 2, 
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

### 探索:直線距離の計算および乗車距離との比較

この例では、直線乗車距離を (マイル単位で) 計算します。この例では、前述したデータ品質評価のクエリを使用して、結果を有効な座標のみに限定します。

Hadoop コマンド ライン コンソールから、次のコマンドを実行します。

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

検査用の  *sample_hive_trip_direct_distance.hql* ファイルの内容を次に示します。

    set R=3959;
    set pi=radians(180);
	
	insert overwrite directory 'wasb:///queryoutputdir'
    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
        pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
    from nyctaxidb.trip 
    where month=1 
        and pickup_longitude between -90 and -30
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and -30
        and dropoff_latitude between 30 and 90;

クエリが完了すると、結果は Hadoop クラスターの既定のコンテナーにある Azure BLOB ***queryoutputdir/000000_0*** に書き込まれます。Azure ストレージ エクスプローラーを使用すれば、次の図に示すように、Hadoop クラスターの既定のコンテナーのこの BLOB を確認できます。

![Create workspace][2]

名前に指定した文字がある BLOB のみを取得するには、フィルター (赤いボックスで強調表示されている) を適用することに注意してください。 

クエリの完了後、Azure SDK を使用して Azure BLOB からクエリの出力を読み取り、pandas データ フレームに入れることで、さらに探索と処理を行えます。Azure BLOB を pandas データ フレームに読み込む方法の説明については、「[データ サイエンス環境で Azure BLOB データを処理する](machine-learning-data-science-process-data-blob.md)」を参照してください。 
	
### モデルのビルド用にデータを準備する

このセクションに記載されているクエリでは、テーブル **"nyctaxidb.trip"** および **"nyctaxidb.fare"** を結合し、二項分類ラベル **[tipped]** および多クラス分類ラベル **[tip_class]** を生成します。このクエリをコピーして、[Azure Machine Learning Studio](https://studio.azureml.net) リーダー モジュールに直接貼り付けることで、 Azure の **Hive クエリ**から直接データを取り込みます。また、このクエリは、座標の緯度または経度が正しくないレコードを除外します。

このクエリは、Hive の組み込み UDF を直接適用して、Hive レコードから複数の特徴を生成します。Hive レコードにはフィールド "field _pickup_datetime_" の時間、1 年の週、曜日 (1 は月曜日の略、7 は日曜日の略です) などがあります。Hive の組み込み UDF の完全なリストについては、「[LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)」を参照してください。

さらに、このクエリは、データをダウンサンプリングして、クエリの結果が Azure Machine Learning Studio に合うようにします。Studio にインポートされるのは約 1% にすぎません。

Hadoop コマンド ライン コンソールから次のコマンドを実行して、クエリを送信します。

	hive -f "C:\temp\sample_hive_prepare_for_aml.hql"

検査用の  *sample_hive_prepare_for_aml.hql* ファイルの内容を次に示します。
	
    select 
        t.medallion, 
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        f.payment_type, 
        f.fare_amount, 
        f.surcharge, 
        f.mta_tax, 
        f.tip_amount, 
        f.tolls_amount, 
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
            if(tip_amount>0 and tip_amount<=5,1,
            if(tip_amount>5 and tip_amount<=10,2,
            if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
    from
    (
        select medallion, 
            hack_license,
            vendor_id,
            rate_code,
            store_and_fwd_flag,
            pickup_datetime,
            dropoff_datetime,
            passenger_count,
            trip_time_in_secs,
            trip_distance,
            pickup_longitude,
            pickup_latitude,
            dropoff_longitude,
            dropoff_latitude,
            rand() as sample_key 
        from nyctaxidb.trip
        where pickup_latitude between 30 and 60
            and pickup_longitude between -90 and -60
            and dropoff_latitude between 30 and 60
            and dropoff_longitude between -90 and -60
    )t
    join
    (
        select 
            medallion, 
            hack_license, 
            vendor_id, 
            pickup_datetime, 
            payment_type, 
            fare_amount, 
            surcharge, 
            mta_tax, 
            tip_amount, 
            tolls_amount, 
            total_amount
        from nyctaxidb.fare 
    )f
    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
    where t.sample_key<=0.01

## <a name="mlmodel"></a>Azure Machine Learning でモデルをビルドする

これで、[Azure Machine Learning](https://studio.azureml.net) でのモデルの作成とモデルの配置に進む準備が整いました。データも、上で決めた予測問題の対応に使用できるようになりました。

1. **二項分類**:乗車に対してチップが支払われたかどうかを予測します。

2. **多クラス分類**:乗車で支払われたチップの金額の範囲を予測するには、以前定義したクラスを使用します。 

3. **回帰タスク**:乗車で支払われたチップの金額を予測します。  

モデリングの演習を開始するには、Azure Machine Learning ワークスペースにログインします。Machine Learning ワークスペースをまだ作成していない場合は、「[Azure ML ワークスペースを作成する](machine-learning-create-workspace.md)」を参照してください。

1. Azure Machine Learning の使用を開始するには、「[Azure Machine Learning Studio とは](machine-learning-what-is-ml-studio.md)」を参照してください。

2. [Azure Machine Learning Studio](https://studio.azureml.net) にログインします。

3. Studio のホーム ページには、豊富な情報、ビデオ、チュートリアル、モジュール リファレンスへのリンク、およびその他のリソースが用意されています。Azure Machine Learning の詳細については、[Azure Machine Learning ドキュメント センター](http://azure.microsoft.com/documentation/services/machine-learning/) を参照してください。

一般的な実験トレーニングは以下のもので構成されています。

1. 実験の**新規**作成。
2. Azure ML へのデータの取得。
3. 必要に応じたデータの事前処理、変換、および操作。
4. 必要に応じた特徴の生成。
5. トレーニング/検証/テスト データ セットへのデータの分割 (またはそれぞれに個別のデータセットを用意する)。
6. 解決する学習問題によって変わる、1 つ以上の機械学習アルゴリズムの選択。例: 二項分類、多クラス分類、回帰。
7. トレーニング データセットを使用した、1 つ以上のモデルのトレーニング。
8. トレーニング済みのモデルを使用した、検証データセットのスコアリング。
9. 学習問題の関連メトリックを計算するためのモデルの評価。
10. モデルの微調整および配置に最適なモデルの選択。

この演習で、私たちは既に、Hive のデータの探索とエンジニアリングを行い (手順 1 ～ 4)、Azure ML に取り込むサンプルのサイズを決定しました。1 つ以上の予測モデルをビルドするには、

1. **リーダー** モジュール (「**データの入力と出力**」セクションで使用可能) を使用して、 Azure ML にデータを取得します。詳細については、「[リーダー](http://msdn.microsoft.com/library/dn784775) モジュールのリファレンス」ページを参照してください。

	![Create workspace][15]

2. **Hive クエリ** を、**[プロパティ]** パネルの**データ ソース**として選択します。

3. 次のように HDInsight Hadoop クラスターの情報を入力します。**[Azure ストレージ アカウント名]** は、HDInsight Hadoop クラスターの作成に使用するストレージ アカウントである必要があります。**[Azure コンテナー名]** は、Hadoop クラスターの既定のコンテナーである必要があります。 

	![Create workspace][11]

4. **[Hive データベース クエリ]** テキスト編集領域で、必要なデータベース フィールド (ラベルなどの計算フィールドなど) を抽出するクエリを貼り付けてから、データを希望するサンプルのサイズにダウンサンプリングします。

Hive クエリから直接データを読み取る二項分類の実験の例を次の図に示します。同様の実験を、多クラス分類と回帰問題で構築することができます。

![Create workspace][12]

> [AZURE.IMPORTANT] 前のセクションに記載されたモデリング データの抽出とサンプリングのクエリの例では、**3 つのモデリングの演習用のラベルはすべてクエリに含まれています**。各モデリングの演習における重要な (必須の) 手順は、他の 2 つの問題用の不要なラベルと、その他のすべての**ターゲット リーク**を**除外**することです。たとえば、二項分類を使用する場合は、ラベル **"tipped"** を使用し、フィールド **[tip_class]**、**[tip_amount]**、および **[total_amount]** は除外します。使用しないものは支払われたチップを意味しているため、ターゲットのリークになります。

> この実験では、最初の**メタデータ エディター** モジュールが、リーダー モジュールからの出力データに列名を追加します。Hive クエリからデータを読み取るリーダー モジュールは出力データの列名を作成しないため、このモジュールが必要になります。 

> 不要な列またはターゲット リークを除外するために、**プロジェクトの列**モジュールまたは**メタデータ エディター**を使用できます。詳細については、「[プロジェクトの列](http://msdn.microsoft.com/library/dn784740)」および「[メタデータ エディター](http://msdn.microsoft.com/library/dn784761)」リファレンス ページを参照してください。

## <a name="mldeploy"></a>Azure Machine Learning にモデルを配置する

モデルの準備ができたら、実験から直接 Web サービスとして配置することができます。Azure ML Web サービスの発行の詳細については、「[Azure Machine Learning API サービスの操作](../machine-learning-overview-of-azure-ml-process.md)」を参照してください。

新しい Web サービスを配置するには以下のことを実行する必要があります。

1. スコアリングの実験の作成。
2. Web サービスの発行。

**終了した**トレーニング実験からスコアリング実験を作成するには、下部の操作バーにある**[スコアリング実験の作成]** をクリックしてください。

![Azure Scoring][13]

Azure Machine Learning は、トレーニング実験のコンポーネントに基づいて、スコアリング実験の作成を試みます。特に、以下のことを実行します。

1. トレーニング済みのモデルを保存し、モデルのトレーニング モジュールを削除する。
2. 予想される入力データ スキーマを表す論理**入力ポート**を特定する。
3. 予想される Web サービスの出力スキーマを表す論理**出力ポート**を特定する。

スコアリングの実験が作成されたら、確認して、必要に応じて調整します。一般的な調整は、ラベル フィールドを除外した入力データセットまたはクエリを置き換えることです。これらはサービスが呼び出されるときに使用できないためです。入力データセットまたはクエリのサイズを、入力スキーマを示すのに十分な 2、3 個のレコードまで削減することをお勧めします。出力ポートでは、一般的に、すべての入力フィールドを除外して、**プロジェクトの列**モジュールを使用して、出力の**スコアリングしたラベル**および**スコアリングした確率**のみを含めます。

スコアリングの実験のサンプルを次の図に示します。発行できる状態になったら、下部の操作バーにある **[Web サービスの発行]** ボタンをクリックします。

![Create workspace][14]

要点をまとめると、このチュートリアルでは、大規模な公開されているデータセットを操作する Azure データ サイエンス環境を作成しました。データの取得から始め、データ サイエンス プロセスの探索および特徴エンジニアリング タスク、Azure Machine Learning Web サービスのモデルのトレーニングおよび発行へと進みました。

## ライセンス情報

このサンプルのチュートリアルとそれに付随するスクリプトは、MIT ライセンスの下で Microsoft と共有されています。詳細については、GitHub のサンプル コードのディレクトリにある LICENSE.txt ファイルを確認してください。

## 参照

*	[Andrés Monroy NYC タクシー乗車ダウンロード ページ](http://www.andresmh.com/nyctaxitrips/)  
*	[NYC のタクシー乗車データを FOIL する (Chris Whong)](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[ニューヨーク市タクシー&リムジン委員会調査および統計](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!--HONumber=49--> 