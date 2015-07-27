<properties 
	pageTitle="データを作成して BLOB ストレージ から Hive テーブルに読み込む | Microsoft Azure" 
	description="Hive テーブルを作成し、BLOB 内のデータを Hive テーブルに読み込む" 
	services="machine-learning" 
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
	ms.date="07/02/2015" 
	ms.author="hangzh;bradsev" />

 
#データを作成して Azure BLOB ストレージから Hive テーブルに読み込む
 
このドキュメントには Hive テーブルを作成し、Azure BLOB ストレージからデータを読み込む汎用の Hive クエリがあります。これらのハイブ クエリは GitHub リポジトリで共有されます。[Github リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql)。


「IPython Notebook サーバーでの Azure 仮想マシンの設定」の手順に従って Azure 仮想マシンを作成する場合、このスクリプト ファイルは仮想マシンの `C:\Users<user name>\Documents\Data Science Scripts` ディレクトリにダウンロードされています。独自のデータ スキーマを組み込む必要があります。また、これらのクエリおよびハイブ クエリの対応するフィールドで Azure BLOB ストレージ構成を、送信できる状態にする必要があります。

ここでは、Hive テーブルのデータが**圧縮されていない**表形式であること、そのデータが Hadoop クラスターで使用されるストレージ アカウントの既定またはその他のコンテナーにアップロードされていることを想定しています。ユーザーが _NYC Taxi Trip Data_ で実践しようとする場合、最初に [24 ファイルすべてをダウンロード](http://www.andresmh.com/nyctaxitrips/) (12 個の Trip ファイルと 12 個の Fair ファイル) し、すべてのファイルを .csv ファイルに**解凍**して、[Azure HDInsight Hadoop クラスターをカスタマイズする](machine-learning-data-science-customize-hadoop-cluster.html)ときに使用される Azure ストレージ アカウントの既定またはその他のコンテナーにそれらのファイルをアップロードする必要があります。

ハイブ クエリは、Hadoop クラスターのヘッド ノードにける Hadoop コマンド ラインで送信できます。以下を実行する必要があります。

1. [Hadoop クラスターのヘッド ノードへのリモート アクセスを有効にし、ヘッド ノードにログオンします](machine-learning-data-science-customize-hadoop-cluster.md)。
2. [ヘッド ノードの Hadoop コマンド ラインで Hive クエリを送信します](machine-learning-data-science-hive-queries.md)。

ユーザーは Web ブラウザーに URL (https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor) を入力することで、クエリ コンソール (Hive エディター) を使用することもできます (ログインするために Hadoop クラスターの資格情報を入力するよう求められます)。また、[PowerShell を使用して Hive ジョブを送信する](hdinsight-submit-hadoop-jobs-programmatically.md)こともできます。

- [手順 1: Hive データベースとテーブルを作成する](#create-tables)
- [手順 2: Hive テーブルにデータを読み込む](#load-data)
- [高度なトピック: パーティション分割テーブルと ORC 形式での Hive データの格納](#partition-orc)

## <a name="create-tables"></a>Hive データベースとテーブルの作成。

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

ユーザーが接続する必要があるフィールドとその他の構成の説明を次に示します。

- `<database name>`: ユーザーが作成するデータベースの名前。ユーザーが既定のデータベースを使用する場合、クエリ `create database...` は省略してもかまいません。 
- `<table name>`: ユーザーが指定されたデータベース内に作成するテーブルの名前。ユーザーが既定のデータベースを使用する場合、テーブルは `<table name>` で直接参照でき、`<database name>.` は不要です。
- `<field separator>`: ハイブ テーブルにアップロードするデータ ファイルのフィールドを区切る区切り記号。 
- `<line separator>`: データ ファイルの行を区切る区切り記号。 
- `<storage location>`: Hive テーブルのデータを保存する Azure ストレージの場所です。ユーザーが `LOCATION '<storage location>'` を指定しない場合、既定ではデータベースとテーブルは Hive クラスターの既定のコンテナーの `hive/warehouse/` ディレクトリに格納されます。ユーザーがストレージの場所を指定する場合、ストレージの場所は、データベースとテーブルの既定のコンテナー内でなければなりません。この場所は、クラスターの既定のコンテナーを基準として、`'wasb:///<directory 1>/'` や `'wasb:///<directory 1>/<directory 2>/'` などの形式で参照する必要があります。クエリが実行されると、既定のコンテナー内に相対ディレクトリが作成されます。 
- `TBLPROPERTIES("skip.header.line.count"="1")`: データ ファイルにヘッダー行が含まれる場合、ユーザーはこのプロパティを `create table` クエリの**最後**に追加する必要があります。それ以外の場合、ヘッダー行はテーブルへのレコードとして読み込まれます。データ ファイルにヘッダー行が含まれない場合は、クエリでこの構成を省略することができます。 

## <a name="load-data"></a>Hive テーブルへのデータの読み込み

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>`: Hive テーブルにアップロードする BLOB ファイルが HDInsight Hadoop クラスターの既定のコンテナーにある場合、`<path to blob data>` は `'wasb:///<directory in this container>/<blob file name>'` の形式にする必要があります。BLOB ファイルは、HDInsight Hadoop クラスターのその他のコンテナーに配置することもできます。この場合、 `<path to blob data>` は `'wasb://<container name>@<storage account name>.blob.windows.core.net/<blob file name>'` の形式にする必要があります。

> [AZURE.NOTE]Hive テーブルにアップロードする BLOB データは、Hadoop クラスターのストレージ アカウントの既定のコンテナーまたは追加のコンテナーに配置されている必要があります。それ以外の場合、`LOAD DATA` クエリはデータにアクセスできないために失敗します。


## <a name="partition-orc"></a>高度なトピック: パーティション テーブルと ORC 形式での Hive データの格納

データのサイズが大きい場合、テーブルのいくつかのパーティションしかスキャンする必要がないクエリでは、テーブルをパーティション分割することが役立ちます。たとえば、Web サイトのログ データを日付別にパーティション分割することができます。

テーブルのパーティション分割に加え、ORC 形式でハイブ データを格納するのも便利です。ORC は「最適化行多桁式」の略です。詳細については、_[ハイブによるデータの読み取り、書き込み、および処理時における ORC ファイルを使用したパフォーマンスの向上](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles)_に関するページをご覧ください。

### パーティション テーブル

パーティション分割されたテーブルを作成し、そのテーブルへのデータの読み込みを実行するクエリは、次のとおりです。

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

パーティション テーブルの照会時には、検索の有効性が大幅に向上するように、`where` 句の**先頭**にパーティション条件を追加することをお勧めします。

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>ORC 形式での Hive データの格納

ユーザーは BLOB のデータを ORC ストレージ形式のハイブ テーブルに直接読み込むことはできません。以下に、ORC 形式で格納されているデータを Azure BLOB から Hive テーブルに読み込むためにユーザーが実行する必要がある手順を示します。

1. 外部テーブル **STORED AS TEXTFILE** を作成し、データを BLOB ストレージからテーブルに読み込みます。

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. 手順 1 で作成した外部テーブルと同じスキーマを持つ内部テーブルと同じフィールド区切り記号を作成します。ORC 形式でハイブ データを格納します。

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. 手順 1 の外部テーブルからデータを選択し、ORC テーブルに挿入します。

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

> [AZURE.NOTE]TEXTFILE テーブル `<database name>.<external textfile table name>` にパーティションが含まれている場合、手順 3 で、`SELECT * FROM <database name>.<external textfile table name>` は、返されたデータ セット内のフィールドとしてパーティション変数を選択します。`<database name>.<ORC table name>` にはテーブル スキーマのフィールドとしてのパーティション変数が存在しないため、その変数を `<database name>.<ORC table name>` に挿入すると失敗します。この場合、ユーザーは `<database name>.<ORC table name>` に挿入するフィールドを、次のように具体的に選択する必要があります。

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. すべてのデータを `<database name>.<ORC table name>` に挿入した後に、`<external textfile table name>` を省略して次のクエリを使用しても問題ありません。

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

これで ORC 形式のデータを格納したテーブルを使用する準備が整いました。
 

<!---HONumber=July15_HO2-->