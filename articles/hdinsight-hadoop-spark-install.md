<properties 
	pageTitle="Script Action を使用して Hadoop クラスターに Spark をインストールする | Microsoft Azure" 
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
	ms.date="03/17/2015" 
	ms.author="nitinme"/>

# HDInsight Hadoop クラスターで Spark をインストールして使用する

**Script Action** クラスター カスタマイズを使用して、Azure HDInsight の Hadoop で任意の種類のクラスターに Spark をインストールできます。Script Action では、クラスターの作成時のみ、スクリプトを実行してクラスターをカスタマイズできます。詳細については、「[Script Action を使って HDInsight をカスタマイズする][hdinsight-cluster-customize]」をご覧ください。

このトピックでは、Script Action を使用して、Spark をインストールする方法を学習します。Spark をインストールした後で、HDInsight クラスターで Spark クエリを実行する方法も学習します。


## <a name="whatis"></a>Spark とは

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。Spark のメモリ内の計算能力により、機械学習とグラフ計算における反復的なアルゴリズムに対して、Spark は適切な選択肢となります。

Spark を使用して、従来のディスク ベースのデータ処理を実行することもできます。Spark は、中間段階でのディスクへの書き込みを回避することで、従来の MapReduce フレームワークを向上しています。また、Spark は Hadoop Distributed File System (HDFS) と Azure BLOB ストレージと互換性があるため、既存のデータは Spark を通じて簡単に処理できます。

このトピックでは、HDInsight クラスターをカスタマイズして Spark をインストールする方法について説明します。

## <a name="whatis"></a>インストールできる Spark のバージョン

このトピックでは、Script Action のカスタム スクリプトを使用して、HDInsight クラスターに Spark をインストールします。このスクリプトではプロビジョニングする HDInsight クラスターのバージョンによって Spark 1.2.0 か Spark 1.0.2 をインストールできます。

- **HDInsight 3.2** クラスターをプロジョニング中にスクリプトを使用する場合、**Spark 1.2.0** をインストールします。
- **HDInsight 3.1** クラスターをプロジョニング中にスクリプトを使用する場合、**Spark 1.0.2** をインストールします。 

このスクリプトを変更するか、独自のスクリプトを作成してその他の Spartk バージョンをインストールできます。


## <a name="install"></a>Spark のインストール方法

HDInsight クラスターに Spark をインストールするためのサンプル スクリプトは、[https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1) の読み取り専用の Azure ストレージ BLOB から入手できます。このセクションでは、Azure の管理ポータルを使用してクラスターをプロビジョニングする際に、サンプル スクリプトを使用する方法について説明します。

> [AZURE.NOTE]サンプル スクリプトは、HDInsight 3.1 と 3.2 クラスターでのみ機能します。HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](hdinsight-component-versioning.md)」をご覧ください。

1. 「[カスタム オプションを使用したクラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)」に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。次に基づいてクラスター バージョンを選択します。

	- **Spark 1.2.0** をインストールする場合、HDInsight 3.2 クラスターをプロビジョニングします。
	- **Spark 1.0.2** をインストールする場合、HDInsight 3.1 クラスターをプロビジョニングします。


2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。たとえば、<b>Install Spark</b> です。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターをカスタマイズするために呼び出すスクリプトの Uniform Resource Identifier (URI) を指定します。例: <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
	<tr><td>ノードの種類</td>
		<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。Spark をインストールするスクリプトではパラメーターは必要ないため、ここは空白のままにできます。</td></tr>
    </table>

	複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。スクリプトの追加後、チェックマークをクリックしてクラスターのプロビジョニングを開始します。

Azure PowerShell や HDInsight .NET SDK を使用して、HDInsight に Spark をインストールするためにスクリプトを使用することもできます。これらの手順については、このトピックの後半で説明します。

## <a name="usespark"></a>HDInsight で Spark を使用する方法
Spark は Scala、Python、および Java で API を提供します。対話型の Spark シェルを使用して Spark クエリを実行することもできます。このセクションでは、さまざまな方法を使用して、Spark を操作する方法の概要を説明します。

- [Spark シェルを使用して、対話型のクエリを実行する](#sparkshell)
- [Spark シェルを使用して、Spark SQL のクエリを実行する](#sparksql) 
- [スタンドアロンの Scala プログラムを使用する](#standalone)

### <a name="sparkshell"></a>Spark シェルを使用して、対話型のクエリを実行する
次の手順を実行して、対話型の Spark シェルから Spark クエリを実行します。このセクションでは、既定で HDInsight クラスターに用意されているサンプル データ ファイル (/example/data/gutenberg/davinci.txt) に対して Spark クエリを実行します。

1. Spark のインストールによって作成したクラスターに対し、Azure ポータルでリモート デスクトップを有効にし、クラスターにリモート接続します。手順については、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>￼」をご覧ください。

2. Remote Desktop Protocol (RDP) セッションで、デスクトップから Hadoop コマンド ライン (デスクトップ ショートカットから) を開き、Spark がインストールされている場所、たとえば **C:\\apps\\dist\\spark-1.2.0** に移動します。


3. 次のコマンドを実行して、Spark シェルを起動します。

		 .\bin\spark-shell --master yarn

	コマンドの実行が完了すると、Scala プロンプトが表示されます。

		 scala>

5. Scala プロンプトで、下記のように Spark クエリを入力します。このクエリは、クラスターに関連付けられている Azure BLOB ストレージの /example/data/gutenberg/ で利用可能な davinci.txt ファイル内の各単語が出現する回数をカウントします。

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. 出力は次のようになります。

	![Output from running Scala interactive shell in an HDInsight cluster](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)
		

7. :q を入力して、Scala プロンプトを終了します。

		:q

### <a name="sparksql"></a>Spark シェルを使用して、Spark SQL のクエリを実行する

Spark SQL では、Spark を使用して構造化照会言語 (SQL)、HiveQL、Scala で表されるリレーショナル クエリを実行することができます。このセクションでは、Spark を使用した Hive テーブルのサンプルにおける Hive クエリの実行について確認します。このセクションで使用する Hive テーブル (**hivesampletable**) は、クラスターをプロビジョニングする際に既定で使用できます。

>[AZURE.NOTE]下記のサンプルは HDInsight 3.2 クラスターのプロビジョニング中にスクリプト アクションを実行するとインストールされる **Spark 1.2.0** に対して作成されました。

1. Spark のインストールによって作成したクラスターに対し、Azure ポータルでリモート デスクトップを有効にし、クラスターにリモート接続します。手順については、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>￼」をご覧ください。

2. RDP セッションで、デスクトップから Hadoop コマンドライン (デスクトップ ショートカットから) を開き、Spark がインストールされている場所、たとえば **C:\\apps\\dist\\spark-1.2.0** に移動します。


3. 次のコマンドを実行して、Spark シェルを起動します。

		 .\bin\spark-shell --master yarn

	コマンドの実行が完了すると、Scala プロンプトが表示されます。

		 scala>

4. Scala プロンプトで Hive のコンテキストを設定します。これは、Spark を使用して Hive クエリを操作するために必要になります。

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	**sc** は Spark シェルを起動するときに設定されている既定の Spark コンテキストになります。

5. Hive コンテキストを使用して Hive クエリを実行し、コンソールに出力します。クエリは、特定のメーカーのデバイス上のデータを取得し、レコードの数 20 までに制限します。

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. 出力次のように表示されます。

	![Output from running Spark SQL on an HDInsight cluster](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. :q を入力して、Scala プロンプトを終了します。

		:q

### <a name="standalone"></a>スタンドアロンの Scala プログラムの使用

このセクションでは、既定で HDInsight クラスターに用意されているサンプル データ ファイル (/example/data/gutenberg/davinci.txt) 内の文字 a と b を含む行の数をカウントする Scala アプリケーションを作成します。Spark のインストールによりカスタマイズされたクラスターで、スタンドアロンの Scala プログラムの作成して使用するには、次の手順を実行する必要があります。

- Scala プログラムを作成する
- Scala プログラムを構築して .jar ファイルを取得する
- クラスター上でジョブを実行する

#### Scala プログラムを作成する
このセクションでは、サンプル データ ファイル内で、文字 a と b を含む行の数をカウントする Scala アプリケーションを作成します。

1. テキスト エディターを開き、次のコードを貼り付けます。


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
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
このセクションでは、<a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (sbt) を使用して Scala プログラムをビルドします。sbt には Java 1.6 またはそれ以降が必要なので、このセクションの作業を進める前に適切なバージョンの Java がインストールされていることを確認してください。

1. http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html から sbt をインストールします。
2. **SimpleScalaApp** という名前のフォルダーを作成し、このフォルダー内に **simple.sbt** という名前のファイルを作成します。これは Scala バージョン、ライブラリの依存関係などに関する情報を含む構成ファイルです。次のデータを simple.sbt ファイルに貼り付け、保存します。


		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



	>[AZURE.NOTE]必ずファイル内に空の行を保持してください。

	
3. **SimpleScalaApp** フォルダーの下にディレクトリ構造 **\\src\\main\\scala** を作成し、前に作成した Scala プログラム (**SimpleApp.scala**) を \\src\\main\\scala フォルダーの下に貼り付けます。
4. コマンド プロンプトを開き、SimpleScalaApp ディレクトリに移動して、次のコマンドを入力します。


		sbt package


	アプリケーションがコンパイルされると、ルート SimpleScalaApp フォルダー内の **\\target\\scala-2.10** ディレクトリの下に **simpleapp_2.10-1.0.jar** ファイルが作成されます。


#### クラスター上でジョブを実行する
このセクションでは、Spark がインストールされているクラスターにリモート接続し、SimpleScalaApp プロジェクトの対象フォルダーをコピーします。次に、**spark-submit** コマンドを使用して、クラスター上のジョブを送信します。

1. Spark がインストールされているクラスターにリモート接続します。SimpleApp.scala プログラムを作成し、ビルドしたコンピューターから **SimpleScalaApp\\target** フォルダーをコピーし、クラスター上の場所に貼り付けます。
2. RDP セッションで、デスクトップから Hadoop コマンドラインを開き、**target** フォルダーを貼り付けた場所に移動します。
3. 次のコマンドを入力して、SimpleApp.scala プログラムを実行します。


		C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. プログラムの実行が完了すると、コンソールに出力が表示されます。


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>Azure PowerShell を使用した HDInsight Hadoop クラスターでの Spark のインストール

このセクションでは、**<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptActionScript Action</a>** コマンドレットで、スクリプト アクションを使用し、クラスターのカスタマイズを行うスクリプトを呼び出します。次に進む前に、Azure PowerShell をインストールして構成したことを確認します。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」をご覧ください。

次の手順に従います。

1. Azure PowerShell ウィンドウを開き、次の変数を宣言します。

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"
	
2. クラスター内のノードや使用する既定のストレージなどの構成値を指定します。

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. **Add-AzureHDInsightScriptAction** コマンドレットを使用して、スクリプト アクションをクラスター構成に追加します。その後、クラスターの作成時に、このスクリプト アクションが実行されます。

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">スクリプト アクションの名前。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カスタマイズ スクリプトが実行されるノードを指定します。有効な値は HeadNode (ヘッド ノードにインストールする場合) または DataNode (すべてのデータ ノードにインストールする場合) です。いずれかまたは両方の値を使用することができます。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">実行されるスクリプトへの URI を指定します。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">パラメーター</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">スクリプトで必要なパラメーター。このトピックで使用するサンプル スクリプトでは、パラメーターは必要ありません。そのため、このパラメーターは上記のスニペットに含まれません。
	</td></tr>
	</table>
	
4. 最後に、Spark がインストールされているカスタマイズされたクラスターのプロビジョニングを開始します。
	
		# Start provisioning a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

入力を求められたら、クラスターの資格情報を入力します。クラスターが作成されるまでに数分かかる場合があります。


## <a name="usingSDK"></a>.NET SDK を使用した HDInsight Hadoop クラスターでの Spark のインストール

HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。このセクションでは、SDK からの Script Action を使用して、Spark がインストールされているクラスターをプロビジョニングする方法について説明します。次の手順を実行する必要があります。

- HDInsight .NET SDK のインストール
- 自己署名証明書の作成
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**

公開されている最新の SDK を [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**自己署名証明書を取得するには**

自己署名証明書を作成し、それをコンピューターにインストールして、さらに、Azure サブスクリプションにアップロードします。手順については、「[Create a self-signed certificate (自己署名証明書の作成)](http://go.microsoft.com/fwlink/?LinkId=511138)」を参照してください。


**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio 2013 を開きます。

2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. **[新しいプロジェクト]** で、次の値を入力するか、選択します。
	
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
	
9. 次のコードをコピーして Main() 関数に貼り付けた後、変数の値を指定します。
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
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
            Version = "3.2"
        };        

10. [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) クラスを使用して、Spark をインストールするカスタム スクリプトを呼び出すために、次のコードを Main() 関数に追加します。

		// Add the script action to install Spark
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), // Location of the script to install Spark.
		  null //Because the script used does not require any parameters
        ));

11. 最後に、クラスターを作成します。

		client.CreateCluster(clusterInfo);

11. アプリケーションに変更を保存し、ソリューションをビルドします。

**アプリケーションを実行するには**

Azure PowerShell コンソールを開き、Visual Studio プロジェクトを保存した場所に移動し、プロジェクト内の \\bin\\debug ディレクトリに移動して、次のコマンドを実行します。

	.\CreateSparkCluster <cluster-name>

クラスター名を指定し、ENTER を押して、Spark がインストールされているクラスターをプロビジョニングします。


## 関連項目##
- 「[HDInsight Hadoop クラスターでの R のインストール][hdinsight-install-r]」に関するページでは、HDInsight Hadoop クラスターで R をインストールして使用するためのクラスター カスタマイズの使用法に関する手順が説明されています。R は、統計計算用のオープン ソースの言語および環境です。R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。また、広範なグラフィカル機能も提供します。
- [HDInsight クラスターでの Giraph のインストール](hdinsight-hadoop-giraph-install.md):クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。
- [HDInsight クラスターでの Solr のインストール](hdinsight-hadoop-solr-install.md):クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Solr をインストールします。Solr は、格納されたデータに対して強力な検索操作を実行することができます。





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: install-configure-powershell.md

<!--HONumber=54-->