<properties 
	pageTitle="HDInsight で R を使用してクラスターをカスタマイズする | Microsoft Azure" 
	description="R をインストールして使用し、Hadoop クラスターをカスタマイズする方法について説明します。" 
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
	ms.date="07/09/2015" 
	ms.author="jgao"/>

# HDInsight Hadoop クラスターに R をインストールして使用する

**Script Action** クラスターのカスタマイズを使用して HDInsight 上の Hadoop で任意の種類のクラスターに R をインストールできます。これにより、R を使用して強力な MapReduce/YARN プログラミング フレームワークをデプロイし、HDInsight に展開される Hadoop クラスター上で大量のデータを処理できます。

Script Action により、クラスターの作成時のみ、スクリプトを実行してクラスターをカスタマイズできます。詳細については、「[Script Action を使用した HDInsight のカスタマイズ][hdinsight-cluster-customize]」に関するページをご覧ください。


## <a name="whatIs"></a>R とは

<a href="http://www.r-project.org/" target="_blank">統計計算用 R プロジェクト</a>は、統計計算用のオープン ソースの言語と環境です。R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。また、広範なグラフィカル機能も提供します。R は、さまざまな分野におけるほぼすべての統計学専門家や科学者に推奨されるプログラミング環境です。

作成時に Script Action を使用してカスタマイズされた HDInsight の Hadoop クラスター上で R スクリプトを実行し、R 環境をインストールできます。R は Azure BLOB ストレージ (WASB) と互換性があるので、そこに格納されているデータは HDInsight 上の R を使用して処理できます。

## <a name="install"></a>R のインストール方法

HDInsight クラスターで R をインストールする[サンプル スクリプト](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)は、Azure Storage の読み取り専用 BLOB から入手できます。このセクションでは、Azure ポータルを使用してクラスターをプロビジョニングする際に、サンプル スクリプトを使用する方法について説明します。

> [AZURE.NOTE]サンプル スクリプトは、HDInsight クラスター バージョン 3.1 で導入されました。HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](../hdinsight-component-versioning/)」をご覧ください。

1. 「[カスタム オプションを使用したクラスターのプロビジョニング](../hdinsight-provision-clusters/#portal)」に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。 
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次のように、スクリプト アクションの詳細を指定します。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Script Action を使ってクラスターをカスタマイズする")
	
	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>「<b>R のインストール</b>」などスクリプト アクションの名前を指定します。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>たとえば、<i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i> のように、クラスターをカスタマイズするために呼び出されるスクリプトへの URI を指定します。</td></tr>
	<tr><td>ノードの種類</td>
		<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。ただし、R をインストールするスクリプトではパラメーターが必要ないため、空白のままにすることができます。</td></tr>
</table>複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。スクリプトを追加した後、チェック マークをオンにして、クラスターのプロビジョニングを開始します。

Azure PowerShell や HDInsight .NET SDK を使用して、HDInsight に R をインストールするためにスクリプトを使用することもできます。これらの手順については、この記事の後半で説明します。

## <a name="useR"></a>HDInsight で R スクリプトを実行する方法
このセクションでは、HDInsight で Hadoop クラスター上の R スクリプトを実行する方法について説明します。

1. **クラスターへのリモート デスクトップ接続の確立**: Azure ポータルから、R をインストールして作成したクラスターに対してリモート デスクトップを有効にし、クラスターに接続します。手順については、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>」をご覧ください。

2. **Rコンソールを開く**: R をインストールすると、ヘッドノードのデスクトップに R コンソールへのリンクが設定されます。それをクリックして、R コンソールを開きます。

3. **R スクリプトの実行**: R スクリプトは、R コンソールに貼り付け、選択して、ENTER を押すことで、直接実行できます。1 ～ 100 の数値を生成し、その結果に 2 を乗算する、単純なスクリプト例を示します。

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

最初の 2 行は、R と共にインストールされる RHadoop ライブラリを呼び出します。最後の行は、結果をコンソールに出力します。出力は次のようになります。

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200

## <a name="usingPS"></a>Azure PowerShell を使用して HDInsight に R をインストールする

このセクションでは、**<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptActionScript Action</a>** コマンドレットで、スクリプト アクションを使用し、クラスターのカスタマイズを行うスクリプトを呼び出します。次に進む前に、Azure PowerShell をインストールして構成したことを確認します。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」をご覧ください。

次の手順に従います。

1. Azure PowerShell コンソールを開き、次の変数を宣言します。

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"
	
2. クラスター内のノードや使用する既定のストレージなどの構成値を指定します。

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. **Add-AzureHDInsightScriptAction** コマンドレットを使用して、R をインストールするサンプル スクリプトを呼び出します。

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1


	たとえば、**Add-AzureHDInsightScriptAction** コマンドレットには次のパラメーターが必要です。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">パラメーター</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定義</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">スクリプト アクション情報が追加される構成オブジェクト。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Script Action の名前</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カスタマイズ スクリプトが実行されるノードを指定します。有効な値は **HeadNode ** (ヘッド ノードにインストールする場合) や **DataNode ** (すべてのデータ ノードにインストールする場合) です。いずれかまたは両方の値を使用することができます。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">パラメーター</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">スクリプトで必要なパラメーター。 
</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">実行されるスクリプトへの URI を指定します。</td></tr>
</table>
	
4. 最後に、R がインストールされるようにカスタマイズされたクラスターをプロビジョニングします。
	
		# PROVISION A CLUSTER WITH R INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

入力を求められたら、クラスターの資格情報を入力します。クラスターが作成されるまでに数分かかる場合があります。


## <a name="usingSDK"></a>.NET SDK を使用した HDInsight での R のインストール

HDInsight .NET SDK は、.NET アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。

以下の手順に従って SDK を使用して HDInsight クラスターをプロビジョニングします。

- [HDInsight .NET SDK のインストール](#installSDK)
- [自己署名証明書を作成する](#createCert)
- [Visual Studio での .NET アプリケーションの作成](#createApp)
- [アプリケーションの実行](#runApp)

以降のセクションでは、これらの手順を実行する方法について説明します。

### <a name="installSDK"></a>HDInsight .NET SDK をインストールするには

公開されている最新の SDK を [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

### <a name="createCert"></a>自己署名証明書を取得するには

自己署名証明書を作成し、それをコンピューターにインストールして、さらに、Azure サブスクリプションにアップロードします。手順については、「[Create a self-signed certificate (自己署名証明書の作成)](http://go.microsoft.com/fwlink/?LinkId=511138)」を参照してください。


### <a name="createApp"></a>Visual Studio で .NET アプリケーションを作成するには

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateRCluster</td></tr>
</table>

4. **[OK]** をクリックしてプロジェクトを作成します。

5. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトから追加します。

7. **ソリューション エクスプローラー**で **[Program.cs]** をダブルクリックして開きます。

8. 次の **using** ステートメントをファイルの先頭に追加します。

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. **Main()** 関数に次のコードを貼り付けた後、変数の値を指定します。
		
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

10. [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) クラスを使用して R をインストールするカスタム スクリプトを呼び出すために、次のコードを **Main()** 関数に追加します。

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1"), null
            ));

11. 最後に、クラスターを作成します。

		client.CreateCluster(clusterInfo);

11. アプリケーションに変更を保存し、ソリューションをビルドします。

### <a name="runApp"></a>アプリケーションを実行するには

Azure PowerShell コンソールを開き、プロジェクトを保存した場所に移動し、プロジェクト内の \\bin\\debug ディレクトリに移動して、次のコマンドを実行します。

	.\CreateRCluster <cluster-name>

クラスター名を指定し、ENTER を押して、R がインストールされているクラスターをプロビジョニングします。

## <a name="seeAlso"></a>関連項目

- 「[HDInsight クラスターでの Spark のインストールと使用][hdinsight-install-spark]」では、HDInsight Hadoop クラスターでの Spark のインストールと使用を行うためのクラスター カスタマイズの使用法に関する手順が説明されています。Spark はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。
- [HDInsight クラスターでの Giraph のインストール](../hdinsight-hadoop-giraph-install): クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。
- [HDInsight クラスターでの Solr のインストール](../hdinsight-hadoop-solr-install):クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Solr をインストールします。Solr は、格納されたデータに対して強力な検索操作を実行することができます。

[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
 

<!---HONumber=August15_HO6-->