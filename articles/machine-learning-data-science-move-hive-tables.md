<properties 
	pageTitle="データを作成して Azure BLOB ストレージ から Hive テーブルに読み込む | Azure" 
	description="Hive テーブルを作成し、BLOB 内のデータを Hive テーブルに読み込む" 
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

 
# データを作成して Azure BLOB ストレージから Hive テーブルに読み込む
 
このドキュメントには Hive テーブルを作成し、Azure BLOB ストレージからデータを読み込む汎用の Hive クエリがあります。Hive テーブルをパーティション分割する方法や、Optimized Row Columnar (ORC) 形式を使用してクエリのパフォーマンスを向上させる方法についてのガイダンスも提供されます。

Hive クエリは、[Github リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql)内で共有されており、そこからダウンロードすることが可能です。

「[データ サイエンス用に Azure 仮想マシンを設定する](machine-learning-data-science-setup-virtual-machine.md)」の指示に従って Azure 仮想マシンを作成している場合、このスクリプト ファイルは仮想マシンの  *C:\Users\<user name>\Documents\Data Science Scripts* ディレクトリにダウンロードされているはずです。これらの Hive クエリに必要なことは、独自のデータ スキーマと Azure BLOB ストレージの構成を適切なフィールドに接続し、送信できるようにすることだけです。

ここでは、Hive テーブルのデータが**圧縮されていない**表形式であることと、Hadoop クラスターが使用するストレージ アカウントの既定の (または追加の) コンテナーにデータがアップロードされていることを想定しています。ユーザーが _NYCタクシーの乗車データで実行する場合、まず [24 のファイルすべてをダウンロード](http://www.andresmh.com/nyctaxitrips/)し (12 のトリップ ファイル、および 12 の運賃ファイル)、すべてのファイルを .csv ファイルに**解凍**し、「[データ サイエンス用の Azure HDInsight Hadoop クラスターをカスタマイズする](machine-learning-data-science-customize-hadoop-cluster.md)」トピックで概説されている手順で使用された Azure ストレージ アカウントの既定コンテナー (または適切なコンテナー) にアップロードする必要があります。 

Hive クエリは、Hadoop クラスターのヘッド ノード上の Hadoop コマンド ライン コンソールから送信できます。そのためには、Hadoop クラスターのヘッド ノードにログインし、Hadoop コマンド ライン コンソールを開き、そこから Hive クエリを送信します。これを行う方法の詳細については、「[クラウド データ サイエンス プロセスで Hive クエリを HDInsight Hadoop クラスターに送信する](machine-learning-data-science-process-hive-tables.md)」を参照してください。

ユーザーは、クエリのコンソール (Hive エディター) を使用するために、URL  

https://&#60;Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor

を Web ブラウザーに入力することもできます。ログインする場合、Hadoop クラスターの資格情報の入力を求められることに注意してください。代わりに、 [PowerShell を使用して Hive ジョブを送信](hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell)することもできます。 

## 前提条件
この記事では、以下のことを前提としています。
 
* Azure のストレージ アカウントが作成されている。手順を知りたい場合は、「[Azure のストレージ アカウントの作成](hdinsight-get-started.md#storage)」を参照してください。 
* HDInsight サービスでカスタマイズされた Hadoop クラスターがプロビジョニングされている。手順を知りたい場合は、「[データ サイエンス用に Azure HDInsight Hadoop クラスターをカスタマイズする](machine-learning-data-science-customize-hadoop-cluster.md)」を参照してください。
* クラスターへのリモート アクセスを有効にし、ログインし、Hadoop コマンド ライン コンソールを開いている。手順を知りたい場合は、「[Hadoop クラスターのヘッド ノードにアクセスする](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess)」を参照してください。 


## <a name="create-tables"></a>Hive データベースとテーブルの作成
Hive テーブルを作成する Hive クエリを次に示します。

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

- `<データベース名>`: ユーザーが作成するデータベースの名前。ユーザーが単に既定のデータベースを使用する場合は、クエリ `create database...` は省略してもかまいません。 
- `<table name>`: ユーザーが指定されたデータベース内に作成するテーブルの名前。ユーザーが既定のデータベースを使用する場合は、`<table name>` を直接参照するのに `<データベース名>` は必要ありません。
- `<フィールド区切り記号>`: Hive テーブルにアップロードするデータ ファイル内のフィールドを区切る区切り記号です。 
- `<line separator>`: データ ファイル内の行を区切る区切り文字。 
- `<ストレージの場所>`: Hive テーブルのデータを保存する Azure ストレージの場所です。ユーザーが `LOCATION '<ストレージの場所>'` を指定しなかった場合、既定では、データベースとテーブルは、Hive クラスターの既定のコンテナー内の `hive/warehouse/` ディレクトリに格納されます。ユーザーがストレージの場所を指定する場合、ストレージの場所は、データベースとテーブルの既定のコンテナー内でなければなりません。この場所は、クラスターの既定のコンテナーに対して相対的に、`'wasb:///<ディレクトリ 1>/'` または`'wasb:///<ディレクトリ 1>/<ディレクトリ 2>/'` などの形式で参照される必要があります。クエリが実行されると、既定のコンテナー内に相対ディレクトリが作成されます。 
- `TBLPROPERTIES("skip.header.line.count"="1")`:データ ファイルにヘッダー行が含まれる場合、ユーザーはこのプロパティを  `create table` クエリの**最後**に追加する必要があります。それ以外の場合、ヘッダー行はテーブルへのレコードとして読み込まれます。データ ファイルにヘッダー行が含まれない場合は、クエリでこの構成を省略することができます。 

## <a name="load-data"></a>Hive テーブルへのデータの読み込み
Hive テーブルにデータを読み込む Hive クエリを次に示します。

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<BLOB データへのパス>`:Hive テーブルにアップロードする BLOB ファイルが HDInsight Hadoop クラスターの既定のコンテナーに存在する場合、 `<BLOB データへのパス>` は、`' wasb:///< このコンテナー内のディレクトリ >/<BLOB ファイル名>'` の形式にする必要があります。BLOB ファイルは、HDInsight Hadoop クラスターの追加コンテナーに配置することもできます。この場合、 `<BLOB データへのパス>` は、`'wasb://<コンテナー名>@<ストレージ アカウント名>.blob.windows.core.net/<BLOB ファイル名>'` の形式にする必要があります。

>[AZURE.NOTE] Hive テーブルにアップロードする BLOB データは、Hadoop クラスターのストレージ アカウントの既定のコンテナーまたは追加のコンテナーに配置されている必要があります。それ以外の場合、 `LOAD DATa` クエリはデータにアクセスできないために失敗します。 


## <a name="partition-orc"></a>拡張トピック: パーティション分割テーブルと ORC 形式での Hive データの格納

データのサイズが大きい場合、テーブルのいくつかのパーティションのみをスキャンすればよいクエリでは、テーブルをパーティション分割すると効果的です。たとえば、Web サイトのログ データを日付別にパーティション分割することができます。 

Hive テーブルをパーティション分割することに加え、Optimized Row Columnar (ORC) 形式で Hive データを格納することも効果的です。ORC 形式の詳細については、「[ORC ファイルを使用すると、Hive が読み取り、書き込み、およびデータの処理を行う場合のパフォーマンスが向上する](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles)」を参照してください。

### パーティション テーブル
パーティション テーブルを作成し、そこにデータを読み込む Hive クエリを次に示します。

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

パーティション テーブルのクエリを行う場合、検索の効率が大幅に向上するため、 `where` 句の**先頭**にパーティション条件を追加することをお勧めします。 

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>ORC 形式での Hive データの格納

ユーザーは ORC 形式で格納されているデータを直接 BLOB ストレージから Hive テーブルに読み込むことはできません。以下に、ORC 形式で格納されているデータを Azure BLOB から Hive テーブルに読み込むためにユーザーが実行する必要がある手順を示します。 

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

2. 手順 1 の外部テーブルと同じスキーマを持つ内部テーブルを、同じフィールド区切り記号を使用して作成し、ORC 形式で Hive データを格納します。

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

[AZURE.NOTE] TEXTFILE テーブル `<データベース名>.<外部テキスト ファイルのテーブル名>` にパーティションが含まれている場合、手順 3 で、 `SELECT * FROM <database name>.<external textfile table name>` は、返されたデータ セット内のフィールドとしてパーティション変数を選択します。`<データベース名>.<ORC テーブル名>` にはテーブル スキーマのフィールドとしてパーティション変数が含まれないため、これを `<データベース名>.<ORC テーブル名>` に挿入すると失敗します。この場合は、ユーザーは`<データベース名>.<ORC テーブル名>` に挿入するフィールドを次のように具体的に選択する必要があります。

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. すべてのデータが `<データベース名>.<ORC テーブル名>` に挿入された後に、以下のクエリを使用して `<外部テキスト ファイルのテーブル名>` を削除するのが安全です。

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

この手順が終了すれば、すぐに使用できる ORC 形式のデータを含むテーブルが手に入ったことになります。 

<!--HONumber=49-->