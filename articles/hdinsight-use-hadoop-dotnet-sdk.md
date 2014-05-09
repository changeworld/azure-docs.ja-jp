<properties linkid="manage-services-hdinsight-howto-sdk" urlDisplayName="HDInsight SDK" pageTitle="HDInsight .NET ライブラリの使用方法 | Azure" metaKeywords="" description="HDInsight NuGet パッケージを入手し、それらを .NET アプリケーションから使用する方法について説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="Hadoop .NET SDK と HDInsight の使用" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />





#Hadoop .NET SDK と HDInsight の使用#

Hadoop .NET SDK は、.NET から Hadoop を簡単に操作できる .NET クライアント ライブラリを提供します。このチュートリアルでは、Hadoop .NET SDK の入手方法と、それを使って、Azure HDInsight サービスを利用して Hive クエリを実行する単純な .NET ベースのアプリケーションをビルドする方法について説明します。actors.txt ファイルが与えられ、受賞数が最も多い俳優または女優を検索するアプリケーションを作成します。

HDInsight を有効にするには、[ここ](https://account.windowsazure.com/PreviewFeatures)をクリックしてください。

## この記事の内容

* [Hadoop .NET SDK のダウンロードとインストール](#install)
* [チュートリアルの準備](#prepare)
* [アプリケーションを作成する](#create)
* [アプリケーションの実行](#run)
* [次のステップ](#nextsteps)

##<a id="install"></a> Hadoop .NET SDK のダウンロードとインストール##

公開されている最新の SDK を [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。SDK には次のコンポーネントが含まれています。

* **MapReduce ライブラリ** - Hadoop ストリーミング インターフェイスを使用して、.NET 言語で MapReduce ジョブを簡単に記述できます。

* **LINQ to Hive クライアント ライブラリ** -  C# または F# の LINQ クエリを HiveQL クエリに変換し、それを Hadoop クラスター上で実行します。このライブラリを使用すれば、.NET アプリケーションから任意の HiveQL クエリを実行することもできます。

* **WebClient ライブラリ** - *WebHDFS* および *WebHCat* のクライアント ライブラリが含まれています。

	* **WebHDFS** クライアント ライブラリ -  HDFS および Azure BLOB ストレージのファイルを操作します。

	* **WebHCat クライアント ライブラリ** -  HDInsight クラスターでのジョブのスケジュールと実行を管理します。
	
これらのライブラリをインストールする NuGet 構文は次のとおりです。
	
	install-package Microsoft.Hadoop.MapReduce
	install-package Microsoft.Hadoop.Hive 
	install-package Microsoft.Hadoop.WebClient 
			
これらのコマンドは、ライブラリと参照を現在の Visual Studio プロジェクトに追加します。

##<a id="prepare"></a> チュートリアルの準備

このチュートリアルを実行するには、[Azure サブスクリプション][free-trial]と [Azure ストレージ アカウント][create-storage-account]が必要です。Azure ストレージのアカウント名とアカウント キーも確認しておく必要があります。この情報を取得する方法については、「[ストレージ アカウントの管理方法](/ja-jp/manage/services/storage/how-to-manage-a-storage-account/)」の「*方法: ストレージ アクセス キーの表示、コピーおよび再生成*」を参照してください。


このチュートリアルで使用する Actors.txt ファイルもダウンロードする必要があります。このファイルを開発環境にダウンロードする手順は次のとおりです。

1. ローカル コンピューター上に C:\Tutorials フォルダーを作成します。

2. [Actors.txt](http://www.microsoft.com/ja-jp/download/details.aspx?id=37003) をダウンロードして、C:\Tutorials フォルダーに保存します。

##<a id="create"></a>アプリケーションを作成する

このセクションでは、プログラムを使用して Hadoop クラスターにファイルをアップロードする方法、および LINQ to Hive を使用して Hive ジョブを実行する方法を説明します。

1. Visual Studio 2012 を開きます。

2. [ファイル] メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. [新しいプロジェクト] で、次の値を入力または選択します。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">プロパティ</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">値</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カテゴリ</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">テンプレート/Visual C##/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">テンプレート</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">コンソール アプリケーション</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SimpleHiveJob</td></tr>
	</table>

4. **[OK]** をクリックしてプロジェクトを作成します。


5. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		install-package Microsoft.Hadoop.Hive 
		install-package Microsoft.Hadoop.WebClient 

	これらのコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

7. ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8. 次の using ステートメントをファイルの先頭に追加します。

		using Microsoft.Hadoop.WebHDFS.Adapters;
		using Microsoft.Hadoop.WebHDFS;
		using Microsoft.Hadoop.Hive;
	
9. Main() 関数で、次のコードをコピーしてファイルに貼り付けます。
		
		// Upload actors.txt to Blob Storage
		var asvAccount = [Storage-account-name.blob.core.windows.net];
		var asvKey = [Storage account key];
		var asvContainer = [Container name];
		var localFile = "C:/Tutorials/Actors.txt";
		var hadoopUser = [Hadoop user name]; // The HDInsight cluster user
		var hadoopUserPassword = [Hadoop user password]; // The HDInsight cluster user password
		var clusterURI = [HDInsight cluster URL]; //"https://HDInsightCluster Name.azurehdinsight.net:563";
		
		var storageAdapter = new BlobStorageAdapter(asvAccount, asvKey, asvContainer, true);
		var HDFSClient = new WebHDFSClient(hadoopUser, storageAdapter);
		
		Console.WriteLine("Creating MovieData directory and uploading actors.txt...");
		HDFSClient.CreateDirectory("/user/hadoop/MovieData").Wait();
		HDFSClient.CreateFile(localFile, "/user/hadoop/MovieData/Actors.txt").Wait();
		
		// Create Hive connection
		var hiveConnection = new HiveConnection(
		  new System.Uri(clusterURI),
		  hadoopUser, 
		  hadoopUserPassword,
		  asvAccount, 
		  asvKey);
		
		// Drop any existing tables called Actors
		// Only needed if you wish to rerun this application
		// and drop a previous Actors table.
		//hiveConnection.GetTable<HiveRow>("Actors").Drop();

		Console.WriteLine("Creating a Hive table named 'Actors'...");
		string command =
		  @"CREATE TABLE Actors(
		            MovieId string, 
		            ActorId string,
		            Name string, 
		            AwardsCount int) 
		            row format delimited fields 
		        terminated by ',';";
		hiveConnection.ExecuteHiveQuery(command).Wait();
		
		Console.WriteLine("Load data from Actors.txt into the 'Actors' table in Hive...");
		command =
		  @"LOAD DATA INPATH 
		        '/user/hadoop/MovieData/Actors.txt'
		    OVERWRITE INTO TABLE Actors;";
		hiveConnection.ExecuteHiveQuery(command).Wait();
		
		Console.WriteLine("Performing Hive query...");
		var result = hiveConnection.ExecuteQuery(@"select name, awardscount
		          from actors sort by awardscount desc
		          limit 1");
		result.Wait();

		Console.WriteLine("The results are: {0}", result.Result.ReadToEnd());
        Console.WriteLine("\nPress any key to continue.");
        Console.ReadKey();

10. アプリケーションの定数を更新します。Azure HDInsight サービスは、既定のファイル システムとして Azure BLOB ストレージを使用します。HDInsight のプロビジョン処理中に、BLOB が既定のファイル システムとして指定されます。既定のファイル システム コンテナーまたは別の BLOB ストレージのコンテナーを使用することもできます。詳細については、「[HDInsight での Azure BLOB ストレージの使用](/ja-jp/manage/services/hdinsight/howto-blob-store/)」を参照してください。

	既定のファイル システム コンテナーを使う場合は、クラスターにリモート接続して、*c:\apps\dist\hadoop-1.1.0-SNAPSHOT\conf>core-site.xml* 構成ファイルからストレージ アカウント名、ストレージ キー、コンテナー名を取得することができます。既定のファイル システムとして使用するコンテナーは、*fs.default.name* を検索すると確認できます。ストレージ アカウント名およびアカウント キーは *fs.azure.account.key* を検索すると確認できます。
	
##<a id="run"></a>アプリケーションの実行

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの実行している手順が表示されます。データがアップロードされ、Hive テーブルに格納され、最後に照会されます。アプリケーションが完了して、クエリ結果が返されたら、任意のキーを押してアプリケーションを終了します。

![HDI.HadoopSDKOutput](./media/hdinsight-use-hadoop-dotnet-sdk/HDI.HadoopSDKOutput.PNG "Console Application")

<div class="dev-callout">
<strong>注</strong>
<p>アプリケーションによって実行される各手順は、完了するまでに数秒、ときには数分かかる場合があります。Actors.txt ファイルをアップロードする時間は、Azure データ センターに対するインターネット接続の状態に応じて変動し、他の手順にかかる時間はクラスター サイズに左右されます。</p>
</div>

<div class="dev-callout">
<strong>注</strong>
<p>LOAD DATA INPATH 操作は、Hive が制御するファイル システム名前空間に Actors.txt データを移動する移動操作です。これにより事実上、アップロード場所から Actors.txt ファイルは削除されます。このため、Actors.txt ファイルはこのアプリケーションを実行するたびにアップロードする必要があります。</p>
<p>Hive へのデータ読み取りの詳細については、「<a href="https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-DMLOperations">Hive GettingStarted (Hive の概要)</a>」を参照してください。</p>
</div>

##<a id="nextsteps"></a>次のステップ
ここでは、Hadoop .NET SDK を使用して .NET アプリケーションを作成する方法を学習しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要](/ja-jp/manage/services/hdinsight/get-started-hdinsight/)
* [HDInsight での Pig の使用][hdinsight-pig]
* [HDInsight での MapReduce の使用][hdinsight-mapreduce]
* [HDInsight での Hive の使用](/ja-jp/manage/services/hdinsight/using-hive-with-hdinsight/)

[hdinsight-pig]: /ja-jp/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-mapreduce]: /ja-jp/manage/services/hdinsight/using-mapreduce-with-hdinsight/



[free-trial]: http://www.windowsazure.com/ja-jp/pricing/free-trial/
[create-storage-account]: http://www.windowsazure.com/ja-jp/manage/services/storage/how-to-create-a-storage-account/



