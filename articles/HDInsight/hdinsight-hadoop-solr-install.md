<properties 
	pageTitle="Script Action を使用して Hadoop クラスターに Solr をインストールする | Microsoft Azure" 
	description="Solr を使用して HDInsight クラスターをカスタマイズする方法について説明します。Solr をインストールするスクリプトを使用するために、Script Action の構成オプションを使用します。" 
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
	ms.date="03/03/2015" 
	ms.author="nitinme"/>

# HDInsight Hadoop クラスターに Solr をインストールして使用する

**Script Action** クラスター カスタマイズを使用して、Azure HDInsight の Hadoop の任意の種類のクラスターに Solr をインストールできます。Script Action では、クラスターの作成時のみ、スクリプトを実行してクラスターをカスタマイズできます。詳細については、「[Script Action を使用した HDInsight のカスタマイズ][hdinsight-cluster-customize]」に関するページをご覧ください。

このトピックでは、Script Action を使用して、Solr をインストールする方法を学習します。Solr は強力な検索プラットフォームで、Hadoop が管理するデータに対してエンタープライズ レベルの検索機能を提供します。HDInsight クラスターに Solr をインストールした後で、Solr を使用したデータの検索方法についても説明します。

> [AZURE.NOTE]このトピックで使用するサンプル スクリプトは、既定の構成で Solr クラスターを作成します。この構成とは異なるコレクションやシャード、スキーマ、レプリカなどで Solr クラスターを構成する場合には、それに応じてスクリプトと Solr バイナリを変更する必要があります。


## <a name="whatis"></a>Solr とは何か

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> は、データに対して強力なフルテキスト検索ができるエンタープライズ検索プラットフォームです。Hadoop が大量のデータの保存と管理を可能にするのに対し、Apache Solr は迅速にデータを取得するための検索機能を提供します。このトピックでは、HDInsight クラスターをカスタマイズして Solr をインストールする方法を説明します。

## <a name="install"></a>Solr をインストールする方法

HDInsight クラスターに Solr をインストールするためのサンプル スクリプトは、[https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1) の読み取り専用 Azure ストレージ BLOB から入手できます。このセクションでは、Azure の管理ポータルを使用してクラスターをプロビジョニングする際に、サンプル スクリプトを使用する方法について説明します。


> [AZURE.NOTE]サンプル スクリプトは、HDInsight クラスター version 3.1 でのみ機能します。HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](hdinsight-component-versioning.md)」をご覧ください。


1. 「[カスタム オプションを使用したクラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)」に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。 
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Script Action を使ってクラスターをカスタマイズする")
	
	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。たとえば、「<b>Install Solr</b>」のような名前にします。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターをカスタマイズするために呼び出すスクリプトの Uniform Resource Identifier (URI) を指定します。例: <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
	<tr><td>ノードの種類</td>
		<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。Solr をインストールするスクリプトにパラメーターは必要ないため、この部分は空白のままにすることもできます。</td></tr>
</table>複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。スクリプトの追加後、チェックマークをクリックしてクラスターのプロビジョニングを開始します。

Azure PowerShell や HDInsight .NET SDK を使用して、HDInsight に Solr をインストールするためにスクリプトを使用することもできます。これらの手順については、このトピックの後半で説明します。

## <a name="usesolr"></a>HDInsight で Solr を使用する方法

最初に、いくつかのデータ ファイルに対し Solr のインデックスを作成する必要があります。これにより、インデックス付きデータに対して、Solr を使用して検索クエリを実行できます。HDInsight クラスターで Solr を使用するには、次の手順を実行します。

1. **リモート デスクトップ プロトコル (RDP) を使用してインストールされている Solr で HDInsight クラスターにリモート接続します**。Solr のインストールによって作成したクラスターに対し、Azure ポータルでリモート デスクトップを有効にし、クラスターにリモート接続します。手順については、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>」をご覧ください。

2. **データ ファイルをアップロードして Solr のインデックスを作成します**。Solr のインデックスを作成する場合、検索が必要なドキュメントをその中に配置します。Solr のインデックスを作成するには、クラスターに RDP をリモート接続してデスクトップに移動します。次に、Hadoop コマンド ラインを開いて **C:\apps\dist\solr-4.7.2\example\exampledocs** に移動します。次のコマンドを実行します。
	
		java -jar post.jar solr.xml monitor.xml

	コンソールで、次の出力が表示します。

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	post.jar ユーティリティは、**solr.xml** と **monitor.xml** という 2 つのサンプル ドキュメントで Solr のインデックスを作成します。post.jar ユーティリティとサンプル ドキュメントは Solr のインストールで利用できるようになります。

3. **Solr ダッシュボードを使用して、インデックス付きドキュメント内を検索します**。HDInsight クラスターへの RDP セッションで Internet Explorer を開き、**http://headnodehost:8983/solr/#/** で Solr のダッシュボードを起動します。左側のウィンドウの **[Core Selector]** ボックスから、**[collection1]** を選択し、メニューの中から **[Query]** をクリックします。例として、Solr 内のすべてのドキュメントを選択して返すために、次の値を指定します。
	1. **[q]** ボックスに「***:***」を入力します。これにより、Solr でインデックス付けされたすべてのドキュメントが返されます。ドキュメント内の特定の文字列を検索する場合には、ここにその文字列を入力することができます。
	2. **[wt]** ボックスでは、出力形式を選択します。既定値は、**json** です。**[Execute Query]** をクリックします。

		![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Solr ダッシュボードでクエリを実行する")
	
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
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
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
   

4. **インデックス付きデータを、Solr から HDInsight クラスターに関連付けられている Azure BLOB ストレージにバックアップすることをお勧めします**。インデックス付きデータを Solr のクラスター ノードから Azure BLOB ストレージ にバックアップすることをお勧めします。そのために、次の手順を実行してください。

	1. RDP セッションから Internet Explorer を開き、次の URL をポイントします。

			http://localhost:8983/solr/replication?command=backup

		次のように、応答が表示されます。

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. リモート セッションで、{SOLR_HOME}{Collection}\data に移動します。サンプル スクリプトを通じて作成したクラスターの場合、この部分は **C:\apps\dist\solr-4.7.2\example\solr\collection1\data** となります。この場所には、**snapshot.*timestamp*** のような名前でスナップショット フォルダーが作成されていることがわかります。
	
	3. スナップショット フォルダーを zip 圧縮して Azure BLOB ストレージにアップロードします。Hadoop コマンド ラインで、次のコマンドを使用して、スナップショット フォルダーの場所に移動します。

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		このコマンドは、クラスターに関連付けられている既定のストレージ アカウント内のコンテナーの下にある /example/data/ にスナップショットをコピーします。

## <a name="usingPS"></a>Azure PowerShell を使用した HDInsight Hadoop クラスターでの Solr のインストール

このセクションでは、**<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptActionScript Action</a>** コマンドレットで、スクリプト アクションを使用し、クラスターのカスタマイズを行うスクリプトを呼び出します。次に進む前に、Azure PowerShell をインストールして構成したことを確認します。コンピューターを構成して HDInsight Windows Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」をご覧ください。

次の手順に従います。

1. Azure PowerShell ウィンドウを開き、次の変数を宣言します。

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. クラスター内のノードや使用する既定のストレージなどの構成値を指定します。

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. **Add-AzureHDInsightScriptAction** コマンドレットを使用して、スクリプト アクションをクラスター構成に追加します。その後、クラスターの作成時に、このスクリプト アクションが実行されます。

		# Add the script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

	**Add-AzureHDInsightScriptAction** コマンドレットは、次のパラメーターを受け取ります。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">パラメーター</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定義</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">スクリプト アクション情報が追加される構成オブジェクト。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">スクリプト アクションの名前。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カスタマイズ スクリプトが実行されるノード。有効な値は HeadNode (ヘッド ノードにインストールする場合) または DataNode (すべてのデータ ノードにインストールする場合) です。いずれかまたは両方の値を使用することができます。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">実行されるスクリプトへの URI。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">パラメーター</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">スクリプトで必要なパラメーター。このトピックで使用するサンプル スクリプトでは、パラメーターは必要ありません。そのため、このパラメーターは上記のスニペットに含まれません。
</td></tr>
</table>
	
4. 最後に、Solr がインストールされた状態にカスタマイズされたクラスターのプロビジョニングを開始します。
	
		# Start provisioning a cluster with Solr installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

入力を求められたら、クラスターの資格情報を入力します。クラスターが作成されるまでに数分かかる場合があります。


## <a name="usingSDK"></a>.NET SDK を使用して HDInsight Hadoop クラスターに Solr をインストールする

HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。このセクションでは、SDK から Script Action を使用して、Solr がインストールされた状態のクラスターをプロビジョニングする方法について説明します。次の手順を実行する必要があります。

- HDInsight .NET SDK のインストール
- 自己署名証明書の作成
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**

公開されている SDK の最新のビルドを [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

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
            Version = "3.1"
        };        

10. [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) クラスを使用して Solr をインストールするカスタム スクリプトを呼び出すために、次のコードを Main() 関数に追加します。

		// Add the script action to install Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //Because the script used does not require any parameters
        ));

11. 最後に、クラスターを作成します。

		client.CreateCluster(clusterInfo);

11. アプリケーションに変更を保存し、ソリューションをビルドします。

**アプリケーションを実行するには**

Windows PowerShell や Azure PowerShell コンソールを開き、Visual Studio プロジェクトを保存した場所に移動し、プロジェクト内の \bin\debug ディレクトリに移動して、次のコマンドを実行します。

	.\CreateSolrCluster <cluster-name>

クラスター名を指定し、Enter キーを押して、Solr がインストールされた状態のクラスターをプロビジョニングします。


## 関連項目##
- [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]。クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Spark をインストールします。Spark はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。
- [HDInsight クラスターに R をインストールする][hdinsight-install-r]。クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに R をインストールします。R は、統計計算用のオープン ソースの言語および環境です。R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。また、広範なグラフィカル機能も提供します。
- [HDInsight クラスターでの Giraph のインストール](hdinsight-hadoop-giraph-install.md): クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。



[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=62-->