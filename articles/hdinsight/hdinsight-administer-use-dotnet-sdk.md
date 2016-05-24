<properties
	pageTitle=".NET SDK を使用した HDInsight での Hadoop クラスターの管理 | Microsoft Azure"
	description="HDInsight .NET SDK を使用して、HDInsight で Hadoop クラスターの管理タスクを実行する方法について説明します。"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="jgao"/>

# .NET SDK を使用した HDInsight での Hadoop クラスターの管理

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-portal-management-selector.md)]

[HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx) を使用して、HDInsight クラスターを管理する方法について説明します。


**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。


##Azure HDInsight への接続

次の Nuget パッケージが必要です。

	Install-Package Microsoft.Azure.Common.Authentication -Pre
	Install-Package Microsoft.Azure.Management.ResourceManager -Pre
	Install-Package Microsoft.Azure.Management.HDInsight

次のコード サンプルは、Azure サブスクリプションで HDInsight クラスターを管理する前に Azure に接続する方法を示しています。

	using System;
	using System.Security;
	using Microsoft.Azure;
	using Microsoft.Azure.Common.Authentication;
	using Microsoft.Azure.Common.Authentication.Factories;
	using Microsoft.Azure.Common.Authentication.Models;
	using Microsoft.Azure.Management.HDInsight;
	using Microsoft.Azure.Management.HDInsight.Models;
	using Microsoft.Azure.Management.ResourceManager;

	namespace HDInsightManagement
	{
		class Program
		{
			private static HDInsightManagementClient _hdiManagementClient;
			private static Guid SubscriptionId = new Guid("<Your Azure Subscription ID>");

			static void Main(string[] args)
			{
				var tokenCreds = GetTokenCloudCredentials();
				var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
				
				var svcClientCreds = new TokenCredentials(tokenCreds.Token); 
				var resourceManagementClient = new ResourceManagementClient(svcClientCreds);
				var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");

				_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

				// insert code here

				System.Console.WriteLine("Press ENTER to continue");
				System.Console.ReadLine();
			}

			public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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

			public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
			{
				return new TokenCloudCredentials(subId.ToString(), creds.Token);
			}
		}
	}

このプログラムを実行すると、プロンプトが表示されます。プロンプトを表示しない場合は、「[非対話型認証 .NET HDInsight アプリケーションを作成する](hdinsight-create-non-interactive-authentication-dotnet-applications.md)」をご覧ください。

##クラスターの作成

「[.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)」をご覧ください。

##クラスターの一覧表示

次のコード スニペットは、クラスターと一部のプロパティを一覧表示します。

    var results = _hdiManagementClient.Clusters.List();
    foreach (var name in results.Clusters) {
        Console.WriteLine("Cluster Name: " + name.Name);
        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
        Console.WriteLine("\t Cluster location: " + name.Location);
        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
    }

##クラスターの削除

クラスターを同期または非同期で削除するには、次のコード スニペットを使用します。

    _hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
    _hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
            
##クラスターのスケール
クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用される worker ノードの数を、クラスターを再作成することなく、変更できます。

>[AZURE.NOTE] HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。クラスターのバージョンがわからない場合、[プロパティ] ページを確認できます。「[クラスターの一覧と表示](hdinsight-administer-use-portal-linux.md#list-and-show-clusters)」をご覧ください。

HDInsight でサポートされているクラスターの種類ごとに、データ ノード数を変更した場合の影響:

- Hadoop は、

	保留中または実行中のジョブに影響を与えることなく、実行中の Hadoop クラスター内の worker ノードの数をシームレスに増加できます。処理の進行中に新しいジョブを送信することもできます。スケール設定処理の失敗は正常に処理され、クラスターは常に機能状態になります。

	データ ノードの数を減らして Hadoop クラスターのスケールを小さくした場合、クラスター内の一部のサービスが再起動されます。これにより、スケール設定処理の完了時に、実行中および保留中のすべてのジョブが失敗します。ただし、処理が完了した後にジョブを再送信できます。

- HBase

	実行中の HBase クラスターに対して、ノードの追加または削除をシームレスに実行できます。地域サーバーは、スケール設定処理の完了の数分以内に自動的に分散されます。ただし、クラスターのヘッドノードにログインし、コマンド プロンプト ウィンドウから次のコマンドを実行して、地域サーバーを手動で分散することもできます。

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

- Storm

	実行中の Storm クラスターに対して、データ ノードの追加または削除をシームレスに実行できます。ただし、スケール設定処理が正常に完了した後、トポロジのバランス再調整が必要になります。

	バランス再調整は、次の 2 つの方法で実行できます。

	* Storm Web UI
	* コマンド ライン インターフェイス (CLI) ツール

	詳細については、[Apache Storm に関するドキュメント](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)をご覧ください。

	Storm Web UI は、HDInsight クラスターで使用できます。

	![HDInsight Storm の規模のバランス調整](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	CLI コマンドを使用して Storm トポロジのバランスを再調整する方法を次の例で示します。

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

次のコード スニペットは、同期または非同期でクラスターのサイズを変更する方法を示しています。

    _hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    _hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
	

##アクセス権の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


既定では、これらのサービスへのアクセス許可が付与されます。アクセス許可を取り消す/付与することができます。取り消するには、次を実行します。

	var httpParams = new HttpSettingsParameters
	{
		HttpUserEnabled = false,
		HttpUsername = "admin",
		HttpPassword = "*******",
	};
	_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);

許可するには、次を実行します。

	var httpParams = new HttpSettingsParameters
	{
		HttpUserEnabled = enable,
		HttpUsername = "admin",
		HttpPassword = "*******",
	};
	_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);


>[AZURE.NOTE] アクセス許可を付与するか、取り消すことで、クラスターのユーザー名とパスワードがリセットされます。

これは、ポータルを使用して行うこともできます。[Azure ポータルを使用した HDInsight の管理][hdinsight-admin-portal]に関するページを参照してください。

##HTTP ユーザーの資格情報の更新

[HTTP アクセス許可の付与/取り消し](#grant/revoke-access)と同じ手順です。HTTP アクセスがクラスターで許可されている場合、最初に取り消す必要があります。次に、新しい HTTP ユーザーの資格情報を使用してアクセス許可を付与してください。


##既定のストレージ アカウントの検索

次のコード スニペットは、クラスターの既定のストレージ アカウント名と既定のストレージ アカウント キーを取得する方法を示しています。

	var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
	foreach (var key in results.Configuration.Keys)
	{
	    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
	}


##ジョブの送信

**MapReduce ジョブを送信するには**

[HDInsight での Hadoop MapReduce のサンプルの実行](hdinsight-hadoop-run-samples-linux.md)に関する記事をご覧ください。

**Hive ジョブを送信するには**

[.NET SDK を使用した Hive クエリの実行](hdinsight-hadoop-use-hive-dotnet-sdk.md)に関する記事をご覧ください。

**Pig ジョブを送信するには**

[.NET SDK を使用した Pig ジョブの実行](hdinsight-hadoop-use-pig-dotnet-sdk.md)に関する記事をご覧ください。

**Sqoop ジョブを送信するには**

「[Hadoop .NET SDK と HDInsight の使用](hdinsight-hadoop-use-sqoop-dotnet-sdk.md)」を参照してください。

**Oozie ジョブを送信するには**

「[HDInsight での Oozie と Hadoop を使用したワークフローの定義と実行](hdinsight-use-oozie-linux-mac.md)」を参照してください。

##Azure BLOB ストレージにデータをアップロードする
「[データを HDInsight にアップロードする方法][hdinsight-upload-data]」を参照してください。


## 関連項目
* [HDInsight .NET SDK リファレンス ドキュメント](https://msdn.microsoft.com/library/mt271028.aspx)
* [Azure ポータルを使用した HDInsight の管理][hdinsight-admin-portal]
* [コマンド ライン インターフェイスを使用した HDInsight の管理][hdinsight-admin-cli]
* [HDInsight クラスターの作成][hdinsight-provision]
* [HDInsight へのデータのアップロード][hdinsight-upload-data]
* [Azure HDInsight の概要][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

<!---HONumber=AcomDC_0511_2016-->