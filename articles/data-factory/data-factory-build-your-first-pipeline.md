<properties
	pageTitle="Data Factory のチュートリアル: 初めてのデータ パイプライン | Microsoft Azure"
	description="この Azure Data Factory のチュートリアルでは、Hadoop クラスターで Hive スクリプトを使用してデータを処理するデータ ファクトリを作成およびスケジュールする方法を示します。"
	services="data-factory"
	keywords="Azure Data Factory のチュートリアル, Hadoop クラスター, Hadoop Hive"
	documentationCenter=""
	authors="spelluru"
	manager=""
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="09/06/2016"
	ms.author="spelluru"/>

# チュートリアル: Hadoop クラスターを使用してデータを処理する最初のパイプラインを作成する 
> [AZURE.SELECTOR]
- [概要と前提条件](data-factory-build-your-first-pipeline.md)
- [Azure ポータル](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager テンプレート](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

このチュートリアルでは、Azure HDInsight (Hadoop) クラスターで Hive スクリプトを実行してデータを処理するデータ パイプラインを備えた最初の Azure データ ファクトリを構築します。

> [AZURE.NOTE] この記事は、Azure Data Factory の概念の概要を説明するものではありません。このサービスの概念の概要については、[Azure Data Factory の概要](data-factory-introduction.md)に関する記事をご覧ください。Data Factory の学習用コンテンツを読むお勧めの順番については、[Data Factory のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/data-factory/)に関するページをご覧ください。

## このチュートリアルの内容	
**Azure Data Factory** では、データ駆動型ワークフロー (データ パイプラインとも呼ばれます) として、データ**移動**タスクやデータ**処理**タスクを構成できます。データ処理 (またはデータ変換) アクティビティを含む最初のデータ パイプラインを構築する方法について説明します。このアクティビティでは、サンプルの Web ログの変換と分析に HDInsight Hadoop クラスターを使用します。

このチュートリアルでは、以下の手順を実行します。

1.	**Data Factory** を作成します。データ ファクトリには、データを移動および処理するデータ パイプラインを 1 つ以上含めることができます。
2.	**リンクされたサービス**を作成します。データ ストアまたはコンピューティング サービスをデータ ファクトリにリンクする、リンクされたサービスを作成します。Azure Storage などのデータ ストアには、パイプラインのアクティビティの入力データや出力データが保持されます。HDInsight Hadoop クラスターなどのコンピューティング サービスがデータを処理または変換します。
3.	入力**データセット**と出力データセットを作成する。入力データセットはパイプラインのアクティビティの入力を表し、出力データセットはアクティビティの出力を表します。
3.	**パイプライン**を作成する。パイプラインには、1 つまたは複数のアクティビティを含めることができます (例: コピー アクティビティ、HDInsight Hive アクティビティ)。このサンプルでは、HDInsight Hadoop クラスターで Hive スクリプトを実行する HDInsight Hive アクティビティを使用します。このスクリプトでは、まず Azure BLOB ストレージに格納されている生の Web ログ データを参照するテーブルを作成し、その後、年月別に生データを分割します。

	Azure Data Factory では 2 種類のアクティビティがサポートされています。それは、[データ移動アクティビティ](data-factory-data-movement-activities.md)と[データ変換アクティビティ](data-factory-data-transformation-activities.md)です。データ移動アクティビティは 1 つしかありません。それがコピー アクティビティです。このチュートリアルでは、コピー アクティビティは使用しません。コピー アクティビティを使用するチュートリアルについては、[Azure BLOB から Azure SQL へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するチュートリアルを参照してください。このチュートリアルで使用する HDInsight Hive アクテビティは、Data Factory でサポートされるデータ変換アクティビティの 1 つです。
 
次に示すのは、このチュートリアルで構築するサンプル データ ファクトリの**ダイアグラム ビュー**です。

![Diagram view in Data Factory tutorial](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

このチュートリアルでは、**adfgetstarted** Azure BLOB コンテナーの **inputdata** フォルダーに、input.log という名前のファイルが 1 つ含まれています。このログ ファイルには、2016 年の 1 月、2 月、および 3 月の 3 か月間のエントリが含まれています。入力ファイル内の各月のサンプル行を次に示します。

	2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
	2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
	2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

HDInsight Hive アクティビティを含むパイプラインによってファイルが処理されると、アクティビティによって HDInsight クラスターで Hive スクリプトが実行され、入力データが年月別に分割されます。このスクリプトでは、各月のエントリが含まれているファイルを格納した 3 つの出力フォルダーが作成されます。

	adfgetstarted/partitioneddata/year=2016/month=1/000000_0
	adfgetstarted/partitioneddata/year=2016/month=2/000000_0
	adfgetstarted/partitioneddata/year=2016/month=3/000000_0

上記のサンプル行の最初の行 (2016-01-01) は、month=1 フォルダーの 000000\_0 ファイルに書き込まれます。同様に、2 行目は month=2 フォルダーのファイルに書き込まれ、3 行目は month=3 フォルダーのファイルに書き込まれます。


## 前提条件
このチュートリアルを開始する前に、以下の前提条件を満たしている必要があります。

1.	**Azure サブスクリプション** - Azure サブスクリプションがない場合は、無料試用版アカウントを数分で作成することができます。無料試用版アカウントの取得方法については、「[無料試用版](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。

2.	**Azure Storage** – このチュートリアルのデータは、Azure ストレージ アカウントを使用して格納します。Azure ストレージ アカウントがない場合は、「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」を参照してください。ストレージ アカウントを作成したら、**アカウント名**と**アクセス キー**をメモしておきます。「[ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)」を参照してください。

### チュートリアル用に Azure Storage にファイルをアップロードする
チュートリアルを開始する前に、チュートリアル用のサンプル ファイルと Azure ストレージ アカウントを準備する必要があります。

1. **adfgetstarted** BLOB コンテナーの **script** フォルダーに Hive クエリ ファイル (HQL) をアップロードします。
2. **adfgetstarted** BLOB コンテナーの **inputdata** フォルダーに入力ファイルをアップロードします。

#### HQL スクリプト ファイルを作成する 

1. **メモ帳**を起動し、次の HQL スクリプトを貼り付けます。この Hive スクリプトでは、**WebLogsRaw** と **WebLogsPartitioned** の 2 つのテーブルを作成します。メニューの **[ファイル]** をクリックし、**[名前を付けて保存]** を選択します。ハード ドライブの **C:\\adfgetstarted** フォルダーを参照します。**[ファイルの種類]** フィールドで **[すべてのファイル (*.*)]** を選択します。**[ファイル名]** に「**partitionweblogs.hql**」と入力します。ダイアログ ボックスの下部にある **[エンコード]** フィールドが **[ANSI]** に設定されていることを確認します。そうでない場合は、**[ANSI]** に設定します。

		set hive.exec.dynamic.partition.mode=nonstrict;
		
		DROP TABLE IF EXISTS WebLogsRaw; 
		CREATE TABLE WebLogsRaw (
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		LINES TERMINATED BY '\n' 
		tblproperties ("skip.header.line.count"="2");
		
		LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
		
		DROP TABLE IF EXISTS WebLogsPartitioned ; 
		create external table WebLogsPartitioned (  
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		partitioned by ( year int, month int)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
		STORED AS TEXTFILE 
		LOCATION '${hiveconf:partitionedtable}';
		
		INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
		SELECT
		  date,
		  time,
		  ssitename,
		  csmethod,
		  csuristem,
		  csuriquery,
		  sport,
		  susername,
		  cipcsUserAgent,
		  csCookie,
		  csReferer,
		  cshost,
		  scstatus,
		  scsubstatus,
		  scwin32status,
		  scbytes,
		  csbytes,
		  timetaken,
		  year(date),
		  month(date)
		FROM WebLogsRaw

実行時に、Data Factory パイプラインの Hive アクティビティは、次のスニペットで **inputtable** パラメーターと **partitionedtable** パラメーターの値を渡します。

		"inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		"partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

**storageaccountname** は Azure ストレージ アカウントの名前です。
 
#### サンプル入力ファイルを作成する
メモ帳を使用して、以下の内容を含む **input.log** という名前のファイルを **c:\\adfgetstarted** に作成します。

	#Software: Microsoft Internet Information Services 8.0
	#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### 入力ファイルと HQL ファイルを Azure Blob Storage にアップロードする

このセクションでは、**AzCopy** ツールを使用して Azure BLOB ストレージに input.log ファイルと partitionweblogs.hql ファイルをコピーする手順を説明します。好みのツール ([Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com/)や [ClumsyLeaf Software の CloudXPlorer](http://clumsyleaf.com/products/cloudxplorer) など) を使用して、このタスクを実行できます。
	 
1. [最新バージョンの **AzCopy**](http://aka.ms/downloadazcopy) または[最新のプレビュー バージョン](http://aka.ms/downloadazcopypr)をダウンロードします。ユーティリティを使用する手順については、[AzCopy を使用する方法](../storage/storage-use-azcopy.md)に関するページを参照してください。
2. c:\\adfgetstarted フォルダーに移動し、次のコマンドを実行します。

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

	このコマンドは、**input.log** ファイルをストレージ アカウント (**adfgetstarted** コンテナーと **inputdata** フォルダー) にアップロードします。**storageaccountname** はストレージ アカウントの名前に、**storageaccesskey** はストレージ アクセス キーに置き換えます。

	> [AZURE.NOTE] このコマンドは、**adfgetstarted** という名前のコンテナーを Azure BLOB ストレージに作成し、**input.log** ファイルをローカル ドライブからコンテナーの **inputdata** フォルダーにコピーします。
	
3. ファイルが正常にアップロードされると、AzCopy から次のような出力が表示されます。
	
		Finished 1 of total 1 file(s).
		[2015/12/16 23:07:33] Transfer summary:
		-----------------
		Total files transferred: 1
		Transfer successfully:   1
		Transfer skipped:        0
		Transfer failed:         0
		Elapsed time:            00.00:00:01
5. 次のコマンドを実行して、**partitionweblogs.hql** ファイルを **adfgetstarted** コンテナーの **script** フォルダーにアップロードします。次にコマンドを示します。
	
		AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

<!---HONumber=AcomDC_0921_2016-->