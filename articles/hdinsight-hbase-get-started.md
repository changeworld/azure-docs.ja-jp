<properties 
	pageTitle="HDInsight の Hadoop を使用した HBase テーブルの設定および照会 | Azure" 
	description="HDInsight の Hadoop 環境での HBase の使用についてです。HBase テーブルを作成し、Hive を使用してそのデータを照会する方法について説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/7/2015" 
	ms.author="bradsev"/>



# HDInsight の Hadoop で Hive を使用して HBase クラスターを設定および照会する

HDInsight の Hadoop で Hive を使用して、HBase テーブルを作成および照会する方法について説明します。 

##この記事の内容

* [HBase とは](#hbaseintro)
* [前提条件](#prerequisites)
* [Azure の管理ポータルを使用して HBase クラスターをプロビジョニングする](#create-hbase-cluster)
* [HBase シェルを使用して HBase テーブルを管理する](#create-sample-table)
* [HiveQL を使用して HBase テーブルを照会する](#hive-query)
* [Microsoft HBase REST クライアント ライブラリを使用して HBase テーブルを管理する](#hbase-powershell)
* [関連項目](#seealso)

##<a name="hbaseintro"></a>HBase とは

HBase は、ビッグ データのオンライン トランザクション処理を可能にする遅延時間の短い NoSQL データベースです。HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。クラスターは、Azure BLOB ストレージにデータを直接格納するよう構成されるため、遅延時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよび利用統計情報のデータを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。HBase の詳細、および HBase を利用できるシナリオについては、「[HDInsight HBase の概要][hdinsight-hbase-overview]」を参照してください。

> [AZURE.NOTE] HBase (バージョン 0.98.0) は、(Apache Hadoop および YARN 2.4.0 を基盤とする) HDInsight の HDInsight 3.1 クラスターでのみ使用できます。バージョン情報については、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][hdinsight-versions]」を参照してください。

##<a name="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。
- **Azure ストレージ アカウント**。詳しい手順については、「[ストレージ アカウントの作成方法][azure-create-storageaccount]」を参照してください。
- Visual Studio 2013 がインストールされている**ワークステーション**。手順については、「[Visual Studio のインストール](http://msdn.microsoft.com/library/e2h7fzkw.aspx)」を参照してください。

##<a name="create-hbase-cluster"></a>Azure ポータルで HBase クラスターをプロビジョニングする

このセクションでは、Azure の管理ポータルを使用して HBase クラスターをプロビジョニングする方法について説明します。


[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Azure の管理ポータルで HDInsight クラスターをプロビジョニングするには** 


1. [Azure の管理ポータル][azure-management-portal]にサインインします。 
2. 左下にある **[新規]** をクリックしてから、**[データ サービス]**、**[HDINSIGHT]**、**[HBASE]** の順にクリックします。 
3. **[クラスター名]**、**[クラスター サイズ]**、[クラスター ユーザー パスワード]、および **[ストレージ アカウント]** にそれぞれ値を入力します。
 
	![Choosing and HBase cluster type and entering cluster login credentials.][img-hdinsight-hbase-cluster-quick-create]

4. 左下にあるチェック アイコンをクリックして、HBase クラスターを作成します。


##<a name="create-sample-table"></a>HBase シェルから HBase サンプル テーブルを作成する
このセクションでは、HBase シェルにアクセスするためにリモート デスクトップ プロトコル (RDP) を有効にして使用する方法と、HBase シェルを使用して HBase サンプル テーブルを作成し、行を追加し、テーブルの行を一覧表示する方法を説明します。

ここでは、最初のセクションで説明した手順を完了し、HBase クラスターが既に正しく作成されていることを前提としています。

**HBase クラスターに対する RDP 接続を有効にするには**

1. 管理ポータルで、左側にある **[HDINSIGHT]** をクリックして既存クラスターの一覧を表示します。
2. HBase シェルを開く HBase クラスターをクリックします。
3. 上部にある **[構成]** をクリックします。
4. 下部にある **[リモートを有効にする]** をクリックします。
5. RDP ユーザー名とパスワードを入力します。  このユーザー名は、クラスターをプロビジョニングするときに使用したクラスターのユーザー名と異なる名前にする必要があります。**[期限]** データには、今日から 7 日先までの日付を指定できます。
6. 右下にあるチェック ボックスをクリックして、リモート デスクトップを有効にします。
7. RPD が有効になったら、**[構成]** タブの下部にある **[接続]** をクリックし、手順に従います。

 
**HBase シェルを開くには**

1. RDP セッション内で、デスクトップにある **[Hadoop コマンド ライン]** ショートカットをクリックします。

2. 次のようにフォルダーを HBase ホーム ディレクトリに変更します。
		
		cd %HBASE_HOME%\bin

3. 次のように HBase シェルを開きます。

		hbase shell


**サンプル テーブルを作成し、データを追加、取得するには**

1. 次のようにサンプル テーブルを作成します。

		create 'sampletable', 'cf1'

2. 次のように行をサンプル テーブルに追加します。

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. 次のようにサンプル テーブルの行を一覧表示します。
	
		scan 'sampletable'

**HBase WebUI でクラスターの状態を確認する**
	
HBase には、要求の統計情報や領域に関する情報などを提供することでクラスターの監視に役立つ WebUI も付属しています。HBase クラスターでは、zookeepernode というアドレスに WebUI があります。


	http://zookeepernode:60010/master-status
	
高可用性 (HA) クラスターの場合は、WebUI をホストしている現在アクティブな HBase マスター ノードへのリンクがあります。

**サンプル テーブルの一括読み込み**

1. 次のデータを含む samplefile1.txt を作成して、Azure BLOB ストレージの /tmp/ にこの samplefile1.txt をアップロードします。

		row1	c1	c2
		row2	c1	c2
		row3	c1	c2
		row4	c1	c2
		row5	c1	c2
		row6	c1	c2
		row7	c1	c2
		row8	c1	c2
		row9	c1	c2
		row10    c1	c2

2. 次のようにフォルダーを HBase ホーム ディレクトリに変更します。
		
		cd %HBASE_HOME%\bin

3. ImportTsv を実行します。

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,a:b,a:c" -Dimporttsv.bulk.output=/tmpOutput sampletable2 /tmp/samplefile1.txt

4. 前のコマンドからの出力を HBase に読み込みます。

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput sampletable2



##<a name="hive-query"></a>Hive を使用して HBase テーブルを照会する

これで、HBase クラスターをプロビジョニングし、HBase テーブルを作成したので、Hive を使用してテーブルを照会できます。このセクションでは、HBase テーブルにマッピングする Hive テーブルを作成し、作成した Hive テーブルを使用して HBase テーブルのデータを照会します。

**クラスターのダッシュボードを開くには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。 
2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。先のセクションで作成したクラスターなど、作成したクラスターが一覧表示されます。
3. Hive ジョブを実行するクラスター名をクリックします。
4. ページの下部にある **[クエリ コンソール]** をクリックし、クラスターのダッシュボードを開きます。これにより、別のブラウザー タブで Web ページが開きます。   
5. Hadoop ユーザー アカウントとパスワードを入力します。既定のユーザー名は **admin** で、パスワードはプロビジョニング処理中に入力したパスワードです。新しいブラウザーのタブが開きます。 
6. 上部の **[Hive エディター]** をクリックします。次のような Hive エディターが開きます。

	![HDInsight cluster dashboard.][img-hdinsight-hbase-hive-editor]





























**Hive クエリを実行するには**

1. 下の HiveQL スクリプトを Hive エディターに入力し、**[送信]** をクリックして、HBase テーブルへの Hive テーブル マッピングを作成します。ここで、HBase シェルを使用して、ここに示す sampletable テーブルが HBase に作成されたことを確認してから、このステートメントを実行してください。
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

	**状態**が **[完了]** に更新されるまで待機します。
 
2. 下の HiveQL スクリプトを Hive エディターに入力してから、**[送信]** をクリックします。次のように Hive クエリが HBase テーブル内のデータを照会します。

     	SELECT count(*) FROM hbasesampletable;
 
4. Hive クエリの結果を取得するには、ジョブが実行を終了したときに、**[ジョブ セッション]** ウィンドウで **[詳細の表示]** リンクをクリックします。HBase テーブルに指定したレコードは 1 つのみのため、ジョブの出力は 1 になるはずです。




**出力ファイルを参照するには**

1. クエリ コンソールの上部にある **[ファイル ブラウザー]** をクリックします。
2. HBase クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントをクリックします。
3. HBase クラスター名をクリックします。既定の Azure ストレージ アカウントのコンテナーでこのクラスター名が使用されます。
4. **[ユーザー]** をクリックします。
5. **[admin]** をクリックします。これが Hadoop ユーザー名です。
6. Hive クエリ SELECT が実行された時刻と **[最終更新]** の時刻が一致しているジョブ名をクリックします。
4. **[stdout]** をクリックします。ファイルを保存し、メモ帳でファイルを開きます。出力は 1 になるはずです。

	![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]
	
##<a name="hbase-powershell"></a>HBase REST Client Library for .NET C# APIs を使用して HBase テーブルを作成し、テーブルからデータを取得する

Microsoft HBase REST Client Library for .NET プロジェクトを GitHub からダウンロードして、HBase .NET SDK を使用してプロジェクトをビルドする必要があります。このタスクを実行する手順は、次のとおりです。

1. 新しい C# Visual Studio Windows デスクトップ コンソール アプリケーションを作成します。
2. **[ツール]** メニュー、**[Nuget パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックして NuGet のパッケージ マネージャー コンソールを開きます。
3. コンソールで次の NuGet コマンドを実行します。

	Install-Package Microsoft.HBase.Client

5. 次の using ステートメントをファイルの先頭に追加します。
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Main 関数を次のコードに置き換えます。

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35. 
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Main 関数の最初の 3 つの変数を設定します。
8. **F5** キーを押してアプリケーションを実行します。



##<a name="next"></a> 参照トピック
このチュートリアルでは、HBase クラスターのプロビジョニング方法、テーブルの作成方法、および作成したテーブルのデータを HBase シェルから表示する方法について学習しました。また、Hive を使用して HBase テーブルのデータを照会する方法、および HBase C# API を使用して HBase テーブルを作成し、テーブルからデータを取得する方法についても学習しました。 

詳細については、次を参照してください。

- [HDInsight HBase の概要][hdinsight-hbase-overview]:
HBase は、Hadoop 上に構築された Apache オープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。
- [Azure Virtual Network での HBase クラスターのプロビジョニング][hdinsight-hbase-provision-vnet]:
アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。
- [HDInsight 環境の HBase で Twitter のセンチメントを分析する][hbase-twitter-sentiment]:
HDInsight の Hadoop クラスターで HBase を使用してリアルタイムでビッグ データの[センチメントを分析](http://en.wikipedia.org/wiki/Sentiment_analysis)する方法について学習します。

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/
[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/ 

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png








<!--HONumber=42-->
