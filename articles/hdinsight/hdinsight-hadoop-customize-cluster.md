<properties
	pageTitle="Script Action を使った HDInsight クラスターのカスタマイズ | Microsoft Azure"
	description="Script Action を使って HDInsight クラスターをカスタマイズする方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="nitinme"/>

# Script Action を使用して HDInsight をカスタマイズする (Windows)

HDInsight は、カスタム スクリプトを呼び出す **Script Action** という構成オプションを提供します。Script Action は作成処理中にクラスター上で実行されるカスタマイズを定義します。これらのスクリプトを使用して、クラスター上に追加のソフトウェアをインストールしたり、クラスター上のアプリケーションの構成を変更したりできます。

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-v1.md)

> [AZURE.NOTE]この記事の情報は、Windows ベースの HDInsight クラスターに固有のものです。この記事の Linux ベースのクラスターに固有のバージョンについては、「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする (Linux)](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

HDInsight クラスターは、その他さまざまな方法でカスタマイズできます。たとえば、Azure ストレージ アカウントの追加、Hadoop 構成ファイルの変更 (core-site.xml、hive-site.xml など)、クラスター内の一般的な場所への共有ライブラリの追加 (Hive、Oozie など) といった方法があります。これらのカスタマイズは、Azure PowerShell、Azure HDInsight .NET SDK、または Azure プレビュー ポータルから実行できます。詳細については、「[HDInsight での Hadoop クラスターの作成][hdinsight-provision-cluster]」を参照してください。

## クラスターの作成処理での Script Action

Script Action は、クラスターが作成中にのみ使用されます。次の図は、作成処理中に Script Action が実行された場合を示しています。

![クラスター作成時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトの実行中、クラスターは **ClusterCustomization** 段階に入ります。この段階でスクリプトは、システム管理者アカウントで、ノードで完全な管理者権限を持ち、クラスター内のすべての指定したノードで並列的に実行されます。

> [AZURE.NOTE]**ClusterCustomization** 段階ではクラスター ノードで管理者権限を持っているため、スクリプトを使用して、Hadoop 関連のサービスを含め、サービスの停止や開始などの操作を行うことができます。そのため、スクリプトの一部として、スクリプトの完了前に Ambari サービスや他の Hadoop 関連のサービスが動作していることを確認する必要があります。これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているか確認する必要があります。これらのサービスに影響するクラスター上の構成を変更する場合は、用意されているヘルパー関数を使用する必要があります。ヘルパー関数の詳細については、「[HDInsight 用の Script Action スクリプトの開発][hdinsight-write-script]」を参照してください。

スクリプトの出力とエラー ログは、クラスター用に指定した既定のストレージ アカウントに格納されます。ログは、**u<\\cluster-name-fragment><\\time-stamp>setuplog** という名前のテーブルに格納されます。これらは、クラスター内のすべてのノード (ヘッドノードとワーカー ノード) で実行されるスクリプトから取得される集計ログです。

各クラスターは、指定された順序で呼び出される複数の Script Action を受け取ることができます。スクリプトはヘッド ノード、ワーカー ノード、またはその両方で実行できます。

HDInsight は、HDInsight クラスターで、次のコンポーネントをインストールするためのいくつかのスクリプトを提供します。

名前 | スクリプト
----- | -----
**Spark のインストール** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1[HDInsight クラスターでの Spark のインストールと使用][hdinsight-install-spark]に関するページを参照してください。
**R のインストール** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1[HDInsight クラスターでの R のインストールと使用][hdinsight-install-r]に関するページを参照してください。
**Solr のインストール** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1[HDInsight クラスターでの Solr のインストールと使用](hdinsight-hadoop-solr-install.md)に関するページを参照してください。
**Giraph のインストール** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1[HDInsight クラスターでの Giraph のインストールと使用](hdinsight-hadoop-giraph-install.md)に関するページを参照してください。



## Azure プレビュー ポータルを使用してスクリプトを呼び出す

**Azure プレビュー ポータルから呼び出す**

1. 「[HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md#portal)」の説明に基づき、クラスターの作成を開始します。
2. [オプションの構成] の **[スクリプト アクション]** ブレードで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Script Action を使ってクラスターをカスタマイズする")

	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。</td></tr>
	<tr><td>ヘッド/ワーカー</td>
		<td>カスタマイズ スクリプトが実行されるノード (**[ヘッド]** または **[ワーカー]**) を指定します。</b>
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。</td></tr>
</table>クラスターに複数のコンポーネントをインストールするには、Enter キーを押して複数のスクリプト アクションを追加します。

3. **[選択]** をクリックしてスクリプト アクションの構成を保存し、クラスターの作成を続行します。

## Azure PowerShell を使用してスクリプトを呼び出す

次の PowerShell スクリプトでは、Windows ベースの HDInsight クラスターに Spark をインストールする方法を示します。その他のソフトウェアをインストールするには、スクリプトのスクリプト ファイルを置換する必要があります。

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

4. 最後に、Spark がインストールされているカスタマイズされたクラスターの作成を開始します。

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

入力を求められたら、クラスターの資格情報を入力します。クラスターが作成されるまでに数分かかる場合があります。

## .NET SDK を使用してスクリプトを呼び出す 

次のサンプルでは、Windows ベースの HDInsight クラスターに Spark をインストールする方法を示します。その他のソフトウェアをインストールするには、コードのスクリプト ファイルを置換する必要があります。

**Spark で HDInsight クラスターを作成するには**

1. Visual Studio で、C# コンソール アプリケーションを作成します。
2. NuGet パッケージ マネージャー コンソールから、次のコマンドを実行します。

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. Program.cs ファイルで次の using ステートメントを使用します。

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. クラスのコードを次のコードに置き換えます。

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }


        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }

4. **F5** キーを押してアプリケーションを実行します。


## HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート
Microsoft Azure HDInsight サービスは柔軟性に優れたプラットフォームであり、Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用し、クラウド内でビッグ データ アプリケーションを構築できます。<a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure サポート FAQ Web サイト</a>の**サポート範囲**のセクションでも説明しているように、Microsoft Azure では、オープン ソース テクノロジについて一般的なレベルのサポートを提供しています。HDInsight サービスでは、次に説明するいくつかのコンポーネントについてさらに高いレベルのサポートを受けることができます。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

- **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。たとえば、YARN リソース マネージャー、Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。クラスタ コンポーネントの完全な一覧は、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">HDInsight で提供される Hadoop クラスター バージョンの新機能</a>」から入手できます。
- **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

組み込みコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。

> [AZURE.WARNING]HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。たとえば、[MSDN の HDInsight フォーラム](https://social.msdn.microsoft.com/Forums/azure/ja-jp/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) や [Spark](http://spark.apache.org/) などのプロジェクト サイトがあります。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信 - Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。
2. クラスターのカスタマイズ - クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。
3. サンプル - よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。これらのサンプルはサポートなしで提供されます。

## Script Action のスクリプトを開発する

「[HDInsight 用の Script Action スクリプトの開発][hdinsight-write-script]」を参照してください。


## 関連項目

- 「[HDInsight での Hadoop クラスターの作成][hdinsight-provision-cluster]」では、その他のカスタム オプションを使用して HDInsight クラスターを作成する方法について説明しています。
- [HDInsight 用の Script Action スクリプトの開発][hdinsight-write-script]
- [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]
- [HDInsight クラスターに R をインストールして使用する][hdinsight-install-r]
- [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)
- [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "クラスター作成時の段階"

<!---HONumber=Oct15_HO3-->