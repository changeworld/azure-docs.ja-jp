<properties 
	pageTitle="HDInsight で Script Action を使用して Hadoop クラスターに Solr をインストールする | Azure" 
	description="HDInsight クラスターをカスタマイズして Solr をインストールする方法を説明します。Solr をインストールするスクリプトを作成するために、Script Action の構成オプションを使用します。" 
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

# HDInsight Hadoop クラスターに Solr をインストールして使用する

**Script Action** のクラスター カスタマイズ機能を使用すると、HDInsight の Hadoop においてあらゆる種類のクラスターに Solr をインストールできます。Script Action により、クラスターの作成時のみ、スクリプトを実行してクラスターをカスタマイズできます。詳細については、[Script Action を使用した HDInsight のカスタマイズ][hdinsight-cluster-customize]に関するページを参照してください。

このトピックでは、Script Action を使用して、Solr をインストールする方法を学習します。Solr は強力な検索プラットフォームで、Hadoop が管理するデータに対してエンタープライズ レベルの検索機能を提供します。HDInsight クラスターに Solr をインストールした後で、Solr を使用したデータの検索方法についても説明します。

> [AZURE.NOTE] このトピックで使用するサンプル スクリプトは、既定の構成で Solr クラスターを作成します。この構成とは異なるコレクションやシャード、スキーマ、レプリカなどで Solr クラスターを構成する場合には、それに応じてスクリプトと Solr バイナリを変更する必要があります。


## この記事の内容

- [Solr とは何か](#whatis)
- [Solr をインストールする方法](#install)
- [HDInsight で Solr を使用する方法](#usesolr)
- [PowerShell を使用して HDInsight Hadoop クラスターに Solr をインストールする](#usingPS)
- [.NET SDK を使用して HDInsight Hadoop クラスターに Solr をインストールする](#usingSDK) 


## <a name="whatis"></a>Solr とは何か

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> は、データに対して強力なフルテキスト検索ができるエンタープライズ検索プラットフォームです。Hadoop が大量のデータの保存と管理を可能にするのに対し、Apache Solr は迅速にデータを取得するための検索機能を提供します。このトピックでは、HDInsight クラスターをカスタマイズして Solr をインストールする方法を説明します。   

## <a name="install"></a>Solr をインストールする方法

HDInsight クラスターに Solr をインストールするためのサンプル スクリプトは、[https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1) の読み取り専用 Azure ストレージ BLOB から入手できます。このセクションでは、Azure の管理ポータルを使用してクラスターをプロビジョニングする際に、サンプル スクリプトを使用する方法について説明します。 


> [AZURE.NOTE] サンプル スクリプトは、HDInsight クラスター version 3.1 でのみ機能します。HDInsight クラスター バージョンの詳細については、[HDInsight クラスター バージョン](http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-component-versioning/)に関するページを参照してください。


1. [カスタム オプションを使用したクラスターのプロビジョニング](http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-provision-clusters/#portal)に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。 
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、Script Action の詳細を指定します。

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>名前</td>
			<td>Script Action の名前を指定します。たとえば、「<b>Install Solr</b>」のような名前にします。</td></tr>
		<tr><td>スクリプト URI</td>
			<td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。たとえば、次のように入力します。 <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
		<tr><td>ノードの種類</td>
			<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
		<tr><td>パラメーター</td>
			<td>スクリプトで必要な場合は、パラメーターを指定します。Solr をインストールするスクリプトにパラメーターは必要ないため、この部分は空白のままにすることもできます。</td></tr>
	</table>	

	複数の Script Action を追加して、複数のコンポーネントをクラスターにインストールできます。スクリプトの追加後、チェックマークをクリックしてクラスターのプロビジョニングを開始します。

Solr を HDInsight にインストールするスクリプトは、PowerShell または HDInsight .NET SDK を介して使用することもできます。これらの手順については、このトピックの後半で説明します。

## <a name="usesolr"></a>HDInsight で Solr を使用する方法

最初に、いくつかのデータ ファイルに対し Solr のインデックスを作成する必要があります。これにより、インデックス付きデータに対して、Solr を使用して検索クエリを実行できます。HDInsight クラスターで Solr を使用するには、次の手順を実行します。

1. **Solr をインストールした HDInsight クラスターに RDP 接続する**。Solr のインストールによって作成したクラスターに対し、Azure 管理ポータルでリモート デスクトップを有効にし、クラスターにリモート接続します。手順については、 <a href="http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>を参照してください。

2. **データ ファイルをアップロードして Solr のインデックスを作成する**。Solr のインデックスを作成する場合、検索が必要なドキュメントをその中に配置します。Solr のインデックスを作成するには、クラスターに RDP 接続してデスクトップに移動します。次に、Hadoop コマンド ラインを開いて **C:\apps\dist\solr-4.7.2\example\exampledocs** に移動します。次のコマンドを実行します。 
	
		java -jar post.jar solr.xml monitor.xml

	You'll see the following output on the console.

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	post.jar ユーティリティは、**solr.xml** および **monitor.xml** という 2 つのサンプル ドキュメントで Solr のインデックスを作成します。post.jar ユーティリティとサンプル ドキュメントは Solr のインストールで利用できるようになります。

3. **Solr ダッシュ ボードを使用して、インデックス付きドキュメント内を検索する**。HDInsight クラスターへの RDP セッションで Internet Explorer を開き、**http://headnodehost:8983/solr/#/** で Solr のダッシュ ボードを起動します。左側のウィンドウの [Core Selector] ボックスから、**[collection1]** を選択し、メニューの中から **[Query]** をクリックします。例として、Solr 内のすべてのドキュメントを選択して返すために、次の値を指定します。
	1. **[q]** ボックスに「**\*:**\*」を入力します。これにより、Solr でインデックス付けされたすべてのドキュメントが返されます。ドキュメント内の特定の文字列を検索する場合には、ここにその文字列を入力することができます。
	2. **[wt]** ボックスでは、出力形式を選択します。既定値は、**json** です。**[Execute Query]** をクリックします。

		![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Run a query on Solr dashboard")
	
	これによる出力は、Solr のインデックス作成のために使用した 2 つのドキュメントを返します。出力結果は、以下のようになります。

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **推奨: Solr によりインデックス付けされたデータを、HDInsight クラスターに関連付けられている Azure ストレージ Blob (WASB) にバックアップする**。インデックス付きデータを Solr のクラスター ノードから WASB に バックアップすることをお勧めします。そのために、次の手順を実行してください。

	1. RDP セッションから Internet Explorer を開き、次の URL をポイントします。

			http://localhost:8983/solr/replication?command=backup

		You should see a response like this

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. リモート セッションで、{{SOLR_HOME}\{Collection}\data に移動します。サンプル スクリプトを使用して作成したクラスターの場合、この部分は **C:\apps\dist\solr-4.7.2\example\solr\collection1\data** となります。この場所には、**snapshot.*timestamp*** のような名前でスナップショット フォルダーが作成されていることがわかります。
	
	3. スナップショット フォルダーを zip 圧縮して WASB にアップロードします。Hadoop コマンド ラインで、次のコマンドを使用して、スナップショット フォルダーの場所に移動します。

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		このコマンドは、クラスターに関連付けられている既定のストレージ アカウント内のコンテナーの下にある /example/data/ にスナップショットをコピーします。

## <a name="usingPS"></a>PowerShell を使用して HDInsight Hadoop クラスターに Solr をインストールする

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
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

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
	
4. 最後に、Solr がインストールされた状態にカスタマイズされたクラスターのプロビジョニングを開始します。  
	
		# START PROVISIONING A CLUSTER WITH SOLR INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

入力を求められたら、クラスターの資格情報を入力します。クラスターが作成されるまでに数分かかる場合があります。


## <a name="usingSDK"></a>.NET SDK を使用して HDInsight Hadoop クラスターに Solr をインストールする

HDInsight .NET SDK は、.NET アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。このセクションでは、SDK から Script Action を使用して、Solr がインストールされた状態のクラスターをプロビジョニングする方法について説明します。次の手順を実行する必要があります。

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
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

10. [ScriptAction](http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) クラスを使用して Solr をインストールするカスタム スクリプトを呼び出すために、次のコードを Main() 関数に追加します。

		// ADD THE SCRIPT ACTION TO INSTALL Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //because the script used does not require any parameters.
        ));

11. 最後に、クラスターを作成します。

		client.CreateCluster(clusterInfo);

11. アプリケーションに変更を保存し、ソリューションをビルドします。 

**アプリケーションを実行するには**

PowerShell コンソールを開き、Visual Studio プロジェクトを保存した場所に移動し、プロジェクト内の \bin\debug ディレクトリに移動して、次のコマンドを実行します。

	.\CreateSolrCluster <cluster-name>

クラスター名を指定し、Enter キーを押して、Solr がインストールされた状態のクラスターをプロビジョニングします。


## 関連項目##
- [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]。クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Spark をインストールします。Spark はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。
- [HDInsight クラスターに R をインストールする][hdinsight-install-r]。クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに R をインストールします。R は統計計算用のオープン ソースの言語および環境で、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を組み合わせた独自のプログラミング言語を提供します。また、広範なグラフィカル機能も提供します。
- [HDInsight クラスターでの Giraph のインストール](../hdinsight-hadoop-giraph-install):クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。




[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
<!--HONumber=42-->
