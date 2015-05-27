<properties
	pageTitle="HDInsight の Hadoop を使用した HBase テーブルの設定および照会 | Azure"
	description="HDInsight の Hadoop 環境での HBase の使用についてです。HBase テーブルを作成し、Hive を使用してそのデータを照会する方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2015"
	ms.author="jgao"/>



# HDInsight での Apache HBase の使用

HDInsight で Hive を使用して、HBase テーブルを作成し、照会する方法について説明します。

HBase は、ビッグ データのオンライン トランザクション処理を可能にする遅延時間の短い NoSQL データベースです。HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。クラスターは、Azure BLOB ストレージにデータを直接格納するよう構成されるため、遅延時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよび利用統計情報のデータを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。HBase の詳細や HBase を利用できるシナリオについては、「[HDInsight HBase の概要][hdinsight-hbase-overview]」をご覧ください。

> [AZURE.NOTE]HBase (バージョン 0.98.0) は、(Apache Hadoop および YARN 2.4.0 を基盤とする) HDInsight の HDInsight 3.1 クラスターでのみ使用できます。バージョン情報については、「[What's new in the Hadoop cluster versions provided by HDInsight? (HDInsight で提供される Hadoop クラスター バージョンの新機能)][hdinsight-versions]」を参照してください。

## 前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**: サブスクリプションの入手方法の詳細については、「[購入オプション][azure-purchase-options]」、「[メンバー プラン][azure-member-offers]」、「[無料評価版][azure-free-trial]」に関するページをご覧ください。
- **Azure ストレージ アカウント**: 詳しい手順については、「[ストレージ アカウントの作成方法][azure-create-storageaccount]」をご覧ください。
- Visual Studio 2013 がインストールされている**ワークステーション**: 手順については、「[Visual Studio のインストール](http://msdn.microsoft.com/library/e2h7fzkw.aspx)」をご覧ください。

## HBase クラスターのプロビジョニング

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Azure ポータルを使用して HBase クラスターをプロビジョニングするには**


1. [Azure ポータル][azure-management-portal]にサインインします。
2. 左下にある **[新規]** をクリックしてから、**[データ サービス]**、**[HDINSIGHT]**、**[HBASE]** の順にクリックします。

	カスタム作成オプションも使用できます。
3. **[クラスター名]**、**[クラスター サイズ]**、[クラスター ユーザー パスワード]、**[ストレージ アカウント]** にそれぞれ値を入力します。

	![HBase クラスターの種類を選択して、クラスターのログイン資格情報を入力します。][img-hdinsight-hbase-cluster-quick-create]

	既定の HTTP ユーザー名は admin です。名前は、[カスタム作成] オプションを使用してカスタマイズできます。

	> [AZURE.WARNING]HBase サービスの高可用性には、最低 **3 つ** のノードを含むクラスターをプロビジョニングする必要があります。これで 1 つのノードがダウンしても、HBase データ領域は他のノードで利用できます。

4. 右下にあるチェックマーク アイコンをクリックして、HBase クラスターを作成します。

>[AZURE.NOTE]HBase クラスターを削除したら、同じ既定の BLOB を使用して別の HBase クラスターを作成できます。新しいクラスターでは、元のクラスターで作成した HBase テーブルを選択します。

## HBase シェルから HBase サンプル テーブルを作成する
このセクションでは、HBase テーブルを作成し、行を追加して一覧表示する HBase シェルの使用方法について説明します。HBase シェルにアクセスするには、まずリモート デスクトップ プロトコル (RDP) を有効にし、HBase クラスターに RDP を接続する必要があります。手順については、「[Azure ポータルを使用した HDInsight での Hadoop クラスターの管理][hdinsight-manage-portal]」をご覧ください。


**HBase シェルを使用するには**

1. RDP セッション内で、デスクトップにある **[Hadoop コマンド ライン]** ショートカットをクリックします。
2. 次のようにフォルダーを HBase ホーム ディレクトリに変更します。

		cd %HBASE_HOME%\bin
3. 次のように HBase シェルを開きます。

		hbase shell

4. 1 つの列のファミリで HBase を作成し、行を挿入します。

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	Hbase テーブル スキーマの詳細については、「[Introduction to HBase Schema Design (HBase スキーマの設計の概要)][hbase-schema]」をご覧ください。HBase コマンドについての詳細は、「[Apache HBase reference guide (Apache HBase リファレンス ガイド)][hbase-quick-start]」をご覧ください。
5. HBase テーブルを一覧表示します。

		list

**HBase WebUI でクラスターの状態を確認する**

HBase には WebUI も付属されるので、クラスターの監視に役立ちます。たとえば、統計情報や領域についての情報を要求できます。HBase クラスターでは、zookeepernode というアドレスに WebUI があります。


	http://zookeepernode:60010/master-status

高可用性クラスターの場合は、WebUI をホストしている現在アクティブな HBase マスター ノードへのリンクがあります。

**サンプル テーブルの一括読み込み**

1. HBase シェルから、2 つの列ファミリで HBase テーブルを作成します。

		create 'Contacts', 'Personal', 'Office'


3. 次のデータを含む連絡先リストを作成し、Azure BLOB ストレージの /tmp/contacts.txt という名前のフォルダーにアップロードします。手順については、「[HDInsight での Hadoop ジョブ用データのアップロード][hdinsight-upload-data]」をご覧ください。

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

2. Hadoop のコマンド ラインでフォルダーを HBase ホーム ディレクトリに変更します。

		cd %HBASE_HOME%\bin

3. ImportTsv を実行します。ImportTsv とは、データを TSV 形式で HBase に読み込むツールで、Hadoop 分散ファイル システム (HDFS) の TSV 形式から HBase にデータを読み込み、読み込むファイルを準備します。詳細については、「[Apache HBase reference guide (Apache HBase リファレンス ガイド)][hbase-reference]」をご覧ください。

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt

4. 前のコマンドからの出力を HBase に読み込みます。

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## Hive を使用して HBase テーブルを照会する

これで HBase クラスターはプロビジョニングされ、HBase テーブルが作成されたので、Hive を使用してテーブル内のデータを照会できるようになりました。このセクションでは、HBase テーブルにマッピングする Hive テーブルを作成し、作成した Hive テーブルを使用して HBase テーブルのデータを照会します。

**クラスターのダッシュボードを開くには**

1. [Azure ポータル][azure-management-portal]にサインインします。
2. 左側のウィンドウで **[HDInsight]** をクリックします。先のセクションで作成したクラスターなど、クラスターが一覧表示されます。
3. Hive ジョブを実行するクラスター名をクリックします。
4. ページの下部にある **[クエリ コンソール]** をクリックし、クラスターのダッシュボードを開きます。別のブラウザー タブで Web ページが開きます。
5. Hadoop ユーザー アカウントとパスワードを入力します。デフォルトのユーザー名は **admin** で、パスワードはプロビジョニング処理中に入力したパスワードです。新しいブラウザーのタブが開きます。
6. ページ上部の **[Hive エディター]** をクリックします。次のような Hive エディターが開きます。

	![HDInsight クラスターのダッシュボード][img-hdinsight-hbase-hive-editor]





























**Hive クエリを実行するには**

1. 次の HiveQL スクリプトを Hive エディターに入力し、**[送信]** をクリックして、HBase テーブルへの Hive テーブル マッピングを作成します。ここで、HBase シェルを使用して、先ほど参照したサンプル テーブルが HBase に作成されたことを確認してから、このステートメントを実行してください。

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	**[状態]** が **[完了]** に更新されるまで待機します。

2. 次の HiveQL スクリプトを Hive エディターに入力してから、**[送信]** をクリックします。次のように Hive クエリが HBase テーブル内のデータを照会します。

     	SELECT count(*) FROM hbasecontactstable;

4. Hive クエリの結果を取得するには、ジョブが実行を終了するときに、**[ジョブ セッション]** ウィンドウで **[詳細の表示**] リンクをクリックします。HBase テーブルに挿入したレコードは 1 つなので、ジョブの出力ファイルは 1 つだけになります。




**出力ファイルを参照するには**

1. クエリ コンソールで **[ファイル ブラウザー**] をクリックします。
2. HBase クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントをクリックします。
3. HBase クラスター名をクリックします。既定の Azure ストレージ アカウントのコンテナーでこのクラスター名が使用されます。
4. **[ユーザー]**、**[Admin]** と順にクリックします (これが Hadoop ユーザー名です)。
6. Hive クエリ SELECT が実行された時刻と **[最終更新]** の時刻が一致しているジョブ名をクリックします。
4. **[stdout]** をクリックします。ファイルを保存し、メモ帳でファイルを開きます。出力ファイルは 1 つになります。

	![HDInsight HBase Hive エディター ファイル ブラウザー][img-hdinsight-hbase-file-browser]

## HBase REST Client Library for .NET C# を使用して HBase テーブルを作成し、テーブルからデータを取得する

GitHub から HBase REST API Client Library for .NET をダウンロードして HBase .NET SDK が使用できるように、プロジェクトをビルドする必要があります。このタスクを実行する手順は、次のとおりです。

1. 新しい C# Visual Studio Windows デスクトップ コンソール アプリケーションを作成します。
2. **[ツール]** メニュー、**[Nuget パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックして NuGet のパッケージ マネージャー コンソールを開きます。
3. コンソールで次の NuGet コマンドを実行します。

		Install-Package Microsoft.HBase.Client

5. 次の **using** ステートメントをファイルの先頭に追加します。

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. **Main** 関数を次のコードに置き換えます。

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

7. **Main** 関数の最初の 3 つの変数を設定します。
8. **F5** キーを押してアプリケーションを実行します。



## 次の手順
このチュートリアルでは、HBase クラスターのプロビジョニング方法、テーブルの作成方法、作成したテーブルのデータを HBase シェルから表示する方法について学習しました。また、Hive を使用して HBase テーブルのデータを照会する方法、HBase C# REST API を使用して HBase テーブルを作成し、テーブルからデータを取得する方法についても学習しました。

詳細については、次を参照してください。

- 「[HDInsight HBase の概要][hdinsight-hbase-overview]」: HBase は、Hadoop 上に構築された Apache オープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。
- 「[Azure Virtual Network での HBase クラスターのプロビジョニング][hdinsight-hbase-provision-vnet]」: アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。
- 「[Configure HBase replication in HDInsight (HDInsight での HBase レプリケーションの構成)](hdinsight-hbase-geo-replication.md)」: 2 つの Azure データ センター間の HBase レプリケーションを構成する方法を説明します。 
- 「[HDInsight 環境の HBase で Twitter のセンチメントを分析する][hbase-twitter-sentiment]」: HDInsight の Hadoop クラスターで HBase を使用してリアルタイムでビッグ データの[センチメントを分析する](http://en.wikipedia.org/wiki/Sentiment_analysis)方法について説明します。

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!--HONumber=54-->