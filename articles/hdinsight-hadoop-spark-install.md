<properties 
	pageTitle="HDInsight で Script Action を使用して Hadoop クラスターに Spark をインストールする| Azure" 
	description="Spark をインストールする HDInsight クラスターをカスタマイズする方法について説明します。Spark をインストールするスクリプトを使用するために、Script Action の構成オプションを使用します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# HDInsight Hadoop クラスターで Spark 1.0 をインストールして使用する

**Script Action** クラスター カスタマイズを使用して、HDInsight の Hadoop に任意の種類の Spark をインストールできます。Script Action により、クラスターの作成時のみ、スクリプトを実行してクラスターをカスタマイズできます。詳細については、[Script Action を使用した HDInsight のカスタマイズ][hdinsight-cluster-customize]に関するページを参照してください。

このトピックでは、Script Action を使用して、Spark 1.0 をインストールする方法を学習します。Spark をインストールした後で、HDInsight クラスターで Spark クエリを実行する方法も学習します。


## この記事の内容

- [Spark とは](#whatis)
- [Spark のインストール方法](#install)
- [HDInsight で Spark を使用する方法](#usespark)
- [PowerShell を使用した HDInsight Hadoop クラスターでの Spark のインストール](#usingPS)
- [.NET SDK を使用した HDInsight Hadoop クラスターでの Spark のインストール](#usingSDK) 


## <a name="whatis"></a>Spark とは

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。Spark のメモリ内の計算能力により、機械学習とグラフ計算における反復的なアルゴリズムに対して、Spark は適切な選択肢となります。

Spark を使用して、従来のディスク ベースのデータ処理を実行することもできます。Spark は、中間段階でのディスクへの書き込みを回避することで、従来の MapReduce フレームワークよりも向上しています。また、Spark は HDFS および WASB と互換性があるため、Spark を使用して既存のデータを簡単に処理できます。 

このトピックでは、HDInsight クラスターをカスタマイズして Spark をインストールする方法について説明します。   

## <a name="install"></a>Spark のインストール方法

HDInsight クラスターに Spark をインストールするためのサンプル スクリプトは、[https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1) の読み取り専用の Azure ストレージ BLOB から入手できます。このセクションでは、Azure の管理ポータルを使用してクラスターをプロビジョニングする際に、サンプル スクリプトを使用する方法について説明します。 

> [AZURE.NOTE] サンプル スクリプトは、HDInsight クラスター version 3.1 でのみ機能します。  HDInsight クラスター バージョンの詳細については、[HDInsight クラスター バージョン](http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-component-versioning/)に関するページを参照してください。

1. [カスタム オプションを使用したクラスターのプロビジョニング](http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-provision-clusters/#portal)に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。 
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、Script Action の詳細を指定します。

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>名前</td>
			<td>Script Action の名前を指定します。たとえば、<b>Install Spark</b> です。</td></tr>
		<tr><td>スクリプト URI</td>
			<td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。たとえば、次のように入力します。 <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1</i></td></tr>
		<tr><td>ノードの種類</td>
			<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
		<tr><td>パラメーター</td>
			<td>スクリプトで必要な場合は、パラメーターを指定します。Spark をインストールするスクリプトではパラメーターは必要ないため、ここは空白のままにできます。</td></tr>
	</table>	

	複数の Script Action を追加して、複数のコンポーネントをクラスターにインストールできます。スクリプトの追加後、チェックマークをクリックしてクラスターのプロビジョニングを開始します。

PowerShell または HDInsight .NET SDK を使用して HDInsight に Spark をインストールするのにスクリプトを使用することもできます。これらの手順については、このトピックの後半で説明します。

## <a name="usespark"></a>HDInsight で Spark を使用する方法
Spark は Scala、Python、および Java で API を提供します。対話型の Spark シェルを使用して Spark クエリを実行することもできます。このセクションでは、これら 2 つの方法を使用して、Spark を操作する方法の概要を説明します。

- [Spark シェル  の使用](#sparkshell)
- [スタンドアロンの Scala プログラムの使用](#standalone)

###<a name="sparkshell"></a>Spark シェルの使用
次の手順を実行して、対話型の Spark シェルから Spark クエリを実行します。このセクションでは、既定で HDInsight クラスターに用意されているサンプル データ ファイル (/example/data/gutenberg/davinci.txt) に対して Spark クエリを実行します。

1. Azure 管理ポータルから、Spark をインストールして作成されたクラスターに対してリモート デスクトップを有効にし、クラスターにリモート接続します。手順については、 <a href="http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>を参照してください。

2. RDP セッションで、デスクトップから Hadoop コマンドラインを開き、Spark がインストールされている場所、たとえば **C:\apps\dist\spark-1.0.2** に移動します。


3. 次のコマンドを実行して、Spark シェルを起動します。

		 .\bin\spark-shell --master yarn

	コマンドの実行が完了すると、Scala プロンプトが表示されます。

		 scala>

5. Scala プロンプトで、下記のように Spark クエリを入力します。このクエリは、クラスターに関連付けられている WASB のストレージの /example/data/gutenberg/ で利用可能な davinci.txt ファイル内の各単語が出現する回数をカウントします。

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. 出力は次のようになります。

	![Output from running Scala interactive shell in an HDInsight cluster][img-hdi-scala-interactive]
		

7. :q を入力して、Scala プロンプトを終了します。

		:q

### <a name="standalone"></a>スタンドアロンの Scala プログラムの使用

このセクションでは、既定で HDInsight クラスターに用意されているサンプル データ ファイル (/example/data/gutenberg/davinci.txt) 内の文字  'a' and 'b' を含む行の数をカウントする Scala アプリケーションを作成します。Spark のインストールによりカスタマイズされたクラスターで、スタンドアロンの Scala プログラムの作成して使用するには、次の手順を実行する必要があります。

- Scala プログラムを作成する
- プログラムをビルドして、.jar ファイルを取得する
- クラスター上でジョブを実行する

#### Scala プログラムを作成する
このセクションでは、サンプル データ ファイル内で、 'a' and 'b' を含む行の数をカウントする Scala プログラムを作成します。 

1. テキスト エディターを開き、次のコードを貼り付けます。


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//location of sample data file on WASB
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

2. ファイルに **SimpleApp.scala** という名前を付けて保存します。

#### Scala プログラムをビルドする
このセクションでは、 <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (sbt) を使用して Scala プログラムをビルドします。sbt には Java 1.6 またはそれ以降が必要なので、このセクションの作業を進める前に適切なバージョンの Java がインストールされていることを確認してください。

1. http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html から sbt をインストールします。
2. **SimpleScalaApp** という名前のフォルダーを作成し、このフォルダー内に **simple.sbt** という名前のファイルを作成します。これは Scala バージョン、ライブラリの依存関係などに関する情報を含む構成ファイルです。次のデータを simple.sbt ファイルに貼り付け、保存します。


		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.0.2"



	>[AZURE.NOTE] 必ずファイル内に空の行を保持してください。

	
3. **SimpleScalaApp** フォルダーの下にディレクトリ構造 **\src\main\scala** を作成し、前に作成した Scala プログラム (**SimpleApp.scala**) を \src\main\scala フォルダーの下に貼り付けます。
4. コマンド プロンプトを開き、SimpleScalaApp ディレクトリに移動して、次のコマンドを入力します。


		sbt package


	アプリケーションがコンパイルされると、ルート SimpleScalaApp フォルダー内の **\target\scala-2.10** ディレクトリの下に **simpleapp_2.10-1.0.jar** が作成されます。


#### クラスター上でジョブを実行する
このセクションでは、Spark がインストールされているクラスターにリモート接続し、SimpleScalaApp プロジェクトの対象フォルダーをコピーします。次に、**spark-submit** コマンドを使用して、クラスター上のジョブを送信します。

1. Spark がインストールされているクラスターにリモート接続します。SimpleApp.scala プログラムを作成し、ビルドしたコンピューターから **SimpleScalaApp\target**フォルダーをコピーし、クラスター上の場所に貼り付けます。
2. RDP セッションで、デスクトップから Hadoop コマンドラインを開き、**target** フォルダーを貼り付けた場所に移動します。
3. 次のコマンドを入力して、SimpleApp.scala プログラムを実行します。


		C:\apps\dist\spark-1.0.2\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. プログラムの実行が完了すると、コンソールに出力が表示されます。


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>PowerShell を使用した HDInsight Hadoop クラスターでの Spark のインストール

このセクションでは、Script Action を使用してクラスターのカスタマイズを行うスクリプトを呼び出すコマンドレット **<a href = "http://msdn.microsoft.com/ja-jp/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** を使用します。次に進む前に、PowerShell をインストールして構成したことを確認します。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、[Azure PowerShell のインストールおよび構成][powershell-install-configure]に関するページを参照してください。

次の手順に従います。

1. Azure PowerShell ウィンドウを開き、次の変数を宣言します。

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. クラスター内のノードや使用する既定のストレージなどの構成値を指定します。

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. **Add-AzureHDInsightScriptAction** コマンドレットを使用して、Script Action をクラスター構成に追加します。その後、クラスターの作成時に、Script Action が実行されます。 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1

	**Add-AzureHDInsightScriptAction** コマンドレットには次のパラメーターが必要です。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">パラメーター</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定義</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Script Action 情報が追加される構成オブジェクト</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Script Action の名前</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カスタマイズ スクリプトが実行されるノードを指定します。有効な値は HeadNode (ヘッドノードにインストールする場合) または DataNode (すべてのデータ ノードにインストールする場合) です。いずれかまたは両方の値を使用することができます。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">実行されるスクリプトへの URI を指定します。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">パラメーター</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">スクリプトで必要なパラメーター。このトピックで使用するサンプル スクリプトでは、パラメーターは必要ありません。そのため、このパラメーターは上記のスニペットに含まれません。
	</td></tr>
	</table>
	
4. 最後に、Spark がインストールされているカスタマイズされたクラスターのプロビジョニングを開始します。  
	
		# START PROVISIONING A CLUSTER WITH SPARK INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

入力を求められたら、クラスターの資格情報を入力します。クラスターが作成されるまでに数分かかる場合があります。


## <a name="usingSDK"></a>.NET SDK を使用した HDInsight Hadoop クラスターでの Spark のインストール

HDInsight .NET SDK は、.NET アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。このセクションでは、SDK からの Script Action を使用して、Spark がインストールされているクラスターをプロビジョニングする方法について説明します。次の手順を実行する必要があります。

- HDInsight .NET SDK のインストール
- 自己署名証明書の作成
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**

公開されている SDK の最新のビルドを [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**自己署名証明書を作成するには**

自己署名証明書を作成し、それをコンピューターにインストールして、さらに、Azure サブスクリプションにアップロードします。手順については、「[自己署名証明書の作成](http://go.microsoft.com/fwlink/?LinkId=511138)」を参照してください。 


**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio 2013 を開きます。

2. [ファイル] メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. [新しいプロジェクト] で、次の値を入力または選択します。
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">プロパティ</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">値</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カテゴリ</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">テンプレート/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">テンプレート</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">コンソール アプリケーション</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
	</table>

4. **[OK]** をクリックしてプロジェクトを作成します。

5. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトから追加します。

7. ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8. 次の using ステートメントをファイルの先頭に追加します。

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Main() 関数で、次のコードのコピーおよび貼り付けを行い、変数の値を指定します。
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. [ScriptAction](http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) クラスを使用して、Spark をインストールするカスタム スクリプトを呼び出すために、次のコードを Main() 関数に追加します。

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1"), // Location of the script to install Spark
		  null //because the script used does not require any parameters.
        ));

11. 最後に、クラスターを作成します。

		client.CreateCluster(clusterInfo);

11. アプリケーションに変更を保存し、ソリューションをビルドします。 

**アプリケーションを実行するには**

PowerShell コンソールを開き、Visual Studio プロジェクトを保存した場所に移動し、プロジェクト内の \bin\debug ディレクトリに移動して、次のコマンドを実行します。

	.\CreateSparkCluster <cluster-name>

クラスター名を指定し、ENTER を押して、Spark がインストールされているクラスターをプロビジョニングします。


## 関連項目##
- [HDInsight Hadoop クラスターでの R のインストール][hdinsight-install-r]に関するページでは、HDInsight Hadoop クラスターで R をインストールして使用するためのクラスター カスタマイズの使用法に関する手順が説明されています。R は統計計算用のオープン ソースの言語および環境で、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を組み合わせた独自のプログラミング言語を提供します。また、広範なグラフィカル機能も提供します。
- [HDInsight クラスターでの Giraph のインストール](../hdinsight-hadoop-giraph-install):クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。
- [HDInsight クラスターでの Solr のインストール](../hdinsight-hadoop-solr-install):クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Solr をインストールします。Solr は、格納されたデータに対して強力な検索操作を実行することができます。





[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[powershell-install-configure]: ../install-configure-powershell/
<!--HONumber=42-->
