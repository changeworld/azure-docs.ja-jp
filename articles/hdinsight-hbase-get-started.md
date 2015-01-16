<properties urlDisplayName="Get Started" pageTitle="HDInsight の Hadoop を使用した HBase テーブルの設定および照会 | Azure" metaKeywords="" description="HDInsight の Hadoop 環境での HBase の使用についてです。HBase テーブルを作成し、Hive を使用してそのデータを照会する方法について説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="Set up HBase clusters and query them using Hive on Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/9/2014" ms.author="bradsev" />



# HDInsight の Hadoop で Hive を使用して HBase クラスターを設定および照会する

HDInsight の Hadoop で Hive を使用して、HBase テーブルを作成および照会する方法について説明します。 

##この記事の内容

* [HBase とは](#hbaseintro)
* [前提条件](#prerequisites)
* [Azure 管理ポータルを使用して HBase クラスターをプロビジョニングする](#create-hbase-cluster)
* [HBase シェルを使用して HBase テーブルを管理する](#create-sample-table)
* [Hive を使用して HBase テーブルを照会する](#hive-query)
* [Microsoft HBase REST クライアント ライブラリを使用して HBase テーブルを管理する](#hbase-powershell)
* [関連項目](#seealso)

##<a name="hbaseintro"></a>HBase とは

HBase は、ビッグ データのオンライン トランザクション処理を可能にする遅延時間の短い NoSQL データベースです。HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。クラスターは、Azure BLOB ストレージにデータを直接格納するよう構成されるため、遅延時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよび利用統計情報のデータを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。HBase の詳細、および HBase を利用できるシナリオについては、「[HDInsight HBase の概要]」を参照してください(http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hbase-overview/)。

> [WACOM.NOTE] HBase (バージョン 0.98.0) は、(Apache Hadoop および YARN 2.4.0 を基盤とする) HDInsight の HDInsight 3.1 クラスターでのみ使用できます。バージョン情報については、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][hdinsight-versions]」を参照してください。

##<a name="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション。**サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。
- **Azure ストレージ アカウント。**詳しい手順については、「[ストレージ アカウントの作成方法][azure-create-storageaccount]」を参照してください。
- **Visual Studio 2013 がインストールされている**ワークステーション。手順については、「[Visual Studio のインストール]」を参照してください(http://msdn.microsoft.com/ja-jp/library/e2h7fzkw.aspx)。
- [Microsoft HBase REST Client Library for .NET] をダウンロードします(https://github.com/hdinsight/hbase-sdk-for-net)。 


##<a name="create-hbase-cluster"></a>Azure ポータルで HBase クラスターをプロビジョニングする

このセクションでは、Azure ポータルを使用して HBase クラスターをプロビジョニングする方法について説明します。


[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Azure ポータルで HDInsight クラスターをプロビジョニングするには** 


1. [Azure 管理ポータル][azure-management-portal]にサインインします。 

2. 左側にある **[HDInsight]** をクリックして、アカウント内のクラスターの状態を一覧表示し、左下にある **[+新規]** アイコンをクリックします。 

	![Creating an HBase cluster in HDInsight in the Azure portal.](http://i.imgur.com/PmGynKZ.jpg)

3. 左から 2 番目の列にある [HDInsight] アイコンをクリックし、次の列の [HBase] オプションをクリックします。[クラスター名] および [クラスター サイズ] の値、ストレージ アカウントの名前、および新しい HBase クラスターのパスワードを指定します。
 
	![Choosing and HBase cluster type and entering cluster login credentials.](http://i.imgur.com/ecxbB9K.jpg)

4. 左下にあるチェック アイコンをクリックして、HBase クラスターを作成します。


##<a name="create-sample-table"></a>HBase シェルから HBase サンプル テーブルを作成する
このセクションでは、HBase シェルにアクセスするためにリモート デスクトップ プロトコル (RDP) を有効にして使用する方法と、HBase シェルを使用して HBase サンプル テーブルを作成し、行を追加し、テーブルの行を一覧表示する方法を説明します。

ここでは、最初のセクションで説明した手順を完了し、HBase クラスターが既に正しく作成されていることを前提としています。

**HBase クラスターに対する RDP 接続を有効にする**

1. HDInsight クラスターに対するリモート デスクトップ接続を有効にするには、作成した HBase クラスターを選択して、**[構成]** タブをクリックします。ページの下部にある **[リモートを有効にする]** ボタンをクリックして、クラスターに対する RDP 接続を有効にします。
2. **リモート デスクトップの構成**ウィザードで資格情報と有効期限を入力し、右下の丸で囲まれたチェック マークをクリックします (処理が完了するまで数分かかる場合があります)。
3. HDInsight クラスターに接続するには、**[構成]** タブの下部にある **[接続]** ボタンをクリックします。

 
**HBase シェルを開く**

1. RDP セッション内で、デスクトップの **[Hadoop コマンド ライン プロンプト]** ショートカットをクリックします。

2. 次のようにフォルダーを HBase ホーム ディレクトリに変更します。
		
		cd %HBASE_HOME%\bin

3. 次のように HBase シェルを開きます。

		hbase shell


**サンプル テーブルを作成し、データを追加、取得する**

1. 次のようにサンプル テーブルを作成します。

		create 'sampletable', 'cf1'

2. 次のように行をサンプル テーブルに追加します。

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. 次のようにサンプル テーブルの行を一覧表示します。
	
		scan 'sampletable'

##<a name="hive-query"></a>Hive を使用して HBase テーブルを照会する

これで、HBase クラスターをプロビジョニングし、テーブルを作成したので、Hive を使用してテーブルを照会できます。このセクションでは、HBase テーブルにマッピングする Hive テーブルを作成し、作成した Hive テーブルを使用して HBase テーブルのデータを照会します。

**クラスターのダッシュボードを開くには**

1. [Azure 管理ポータル][azure-management-portal]にサインインします。 
2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。先のセクションで作成したクラスターなど、作成したクラスターが一覧表示されます。
3. Hive ジョブを実行するクラスター名をクリックします。
4. ページの下部にある **[クラスターの管理]** をクリックし、クラスターのダッシュボードを開きます。これにより、別のブラウザー タブで Web ページが開きます。   
5. Hadoop ユーザー アカウントとパスワードを入力します。デフォルトのユーザー名は **admin** で、パスワードはプロビジョニング処理中に入力したパスワードです。ダッシュボードは、次のようになります。

	![HDInsight cluster dashboard.](http://i.imgur.com/tMwXlj9.jpg)


**Hive クエリを実行するには**

1. HBase テーブルにマッピングする Hive テーブルを作成するには、以下の HiveQL スクリプトを Hive コンソール ウィンドウに入力し、**[送信]** ボタンをクリックします。ここで、HBase シェルを使用して、参照するサンプル テーブルを作成したことを確認してから、このステートメントを実行します。
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

 
2. HBase のデータに対して Hive クエリを実行するには、以下の HiveQL スクリプトを Hive コンソール ウィンドウに入力し、**[送信]** ボタンをクリックします。

     	SELECT count(*) FROM hbasesampletable;
 
4. Hive クエリの結果を取得するには、ジョブが実行を終了するときに、**[ジョブ セッション]** ウィンドウで **[詳細の表示]** リンクをクリックします。


注:HBase シェル リンクにより、タブが **[HBase シェル]** に切り替えられます。



**出力ファイルを参照するには**

1. クラスターのダッシュボードで、上部にある **[ファイル]** をクリックします。
2. **Templeton-Job-Status** をクリックします。
3. 以前書き留めたジョブの開始時刻よりも少し後の最終変更時刻の GUID 番号をクリックします。この GUID をメモしておきます。これは、次のセクションで必要になります。
4. **stdout** ファイルには、次のセクションで必要なデータが含まれています。必要な場合は、**stdout** をクリックして、このデータ ファイルのコピーをダウンロードできます。

	
##<a name="hbase-powershell"></a>HBase REST Client Library for .NET C# APIs を使用して HBase テーブルを作成し、テーブルからデータを取得する

Microsoft HBase REST Client Library for .NET プロジェクトを GitHub からダウンロードして、HBase .NET SDK を使用してプロジェクトをビルドする必要があります。このタスクを実行する手順は、次のとおりです。

1. [Microsoft HBase REST Client Library for .NET] をダウンロードします (https://github.com/hdinsight/hbase-sdk-for-net) (この前提条件が満たされていない場合)。

2. ダウンロード先の場所から Visual Studio で Marlin.sln を開きます (**[ファイル]**、**[開く]**、**[プロジェクト/ソリューション...]**)。**[表示]**、**[ソリューション エクスプローラー]** の順に選択して "Marlin" ソリューションと Microsoft.HBase.Client プロジェクトを表示します。**Marlin** プロジェクトをビルドします (**[ソリューション エクスプローラー]** でこのプロジェクトを右クリックし、**[ソリューションのビルド]** を選択します)。 

4. Visual C# の新しいコンソール アプリケーションを作成します。この結果作成される Microsoft.HBase.Client.dll と protobuf.dll を (...\bin\debug\Microsoft.HBase.Client ディレクトリから) 取得し、自分の C# プロジェクトに追加します。**[参照]** を右クリックして **[参照の追加...]** を選択し、2 つのアセンブリを参照してからアップロードします。[protobuf-net] (http://code.google.com/p/protobuf-net/) は、データ通信に使用する Google の Protocol Buffer (プロトコル バッファ) バイナリ シリアライザーを .NET に実装したものです。

5. 次の using ステートメントをファイルの先頭に追加します。
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. 次のように、クラスターの資格情報を使用して HBase クライアントの新しいインスタンスを作成し、クラスター バージョンを取得します。

		// Create a new instance of an HBase client.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		// Retrieve the cluster version
		var version = client.GetVersion();
		Console.WriteLine(version);

7. 新しい HBase テーブルを作成するには、次のコードを使用します。

		// Create a new HBase table.
		var testTableSchema = new TableSchema();
		testTableSchema.name = "mytablename";
		testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
		testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
		client.CreateTable(testTableSchema);

8. 次のコードを使用してデータをテーブルに挿入します。

		// Insert data into a table.
		var tableName = "mytablename";
		var testKey = "content";
		var testValue = "the force is strong in this column";
		var set = new CellSet();
		var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
		set.rows.Add(row);

		var value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
		row.values.Add(value);
		client.StoreCells(tableName, set);

9. セルをキーと一緒に取得するには、次のコードを使用します。 

		// Retrieve a cell with its key.
		var testKey = "content";
		var tableName = "mytablename";

		var cells = client.GetCells(tableName, testKey);
		// get the first value from the row.
		.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
		// with the previous insert, it should yield: "the force is strong in this column"

10. 次のコードによりテーブルの行をスキャンします。

		//Scan over rows in a table.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		var tableName = "mytablename";

		// assume the table has integer keys and we want data between keys 25 and 35
		var scanSettings = new Scanner()
		{
    		batch = 10,
    		startRow = BitConverter.GetBytes(25),
    		endRow = BitConverter.GetBytes(35)
		};

		var scannerInfo = client.CreateScanner(tableName, scanSettings);
		CellSet next = null;
		while ((next = client.ScannerGetNext(scannerInfo)) != null)
		{
    		foreach (var row in next.rows)
    		{
        		// ... read the rows
    		}
		}



##<a name="summary"></a>まとめ
このチュートリアルでは、HBase クラスターのプロビジョニング方法、テーブルの作成方法、および作成したテーブルのデータを HBase シェルから表示する方法について学習しました。また、Hive を使用して HBase テーブルのデータを照会する方法、および HBase C# API を使用して HBase テーブルを作成し、テーブルからデータを取得する方法についても学習しました。

##<a name="next"></a> 参照トピック

[HDInsight HBase の概要][hdinsight-hbase-overview]:
HBase は、Hadoop 上に構築された Apache オープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。

[Azure Virtual Network での HBase クラスターのプロビジョニング][hdinsight-hbase-provision-vnet]:
アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。

[HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する][hbase-twitter-sentiment]:
HDInsight の Hadoop クラスターで HBase を使用して、ビッグ データの[センチメント分析]を(http://en.wikipedia.org/wiki/Sentiment_analysis) リアルタイムで実行する方法について説明します。

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/



[azure-purchase-options]: http://azure.microsoft.com/ja-jp/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ja-jp/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png







<!--HONumber=35.1-->
