<properties
	pageTitle="プログラムで Hadoop YARN アプリケーション ログにアクセスする| Microsoft Azure"
	description="HDInsight の Hadoop クラスター上のアプリケーション ログにプログラムを使用してアクセスします。"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
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

# プログラムで HDInsight の Hadoop の YARN アプリケーション ログにアクセスする

このトピックでは、Azure HDInsight の Hadoop クラスター上に作成された YARN (Yet Another Resource Negotiator) アプリケーションをプログラムを使って列挙する方法と、リモート デスクトップ プロトコル (RDP) を使用してクラスターに接続せずに、アプリケーション ログにプログラムを使ってアクセスする方法について説明します。具体的には、次の新しいコンポーネントと新しい API が追加されました。

  1. HDInsight クラスターで汎用アプリケーション履歴サーバーが有効化されました。これは、完了したアプリケーションに関する汎用的な情報の格納と取得を処理する YARN タイムライン サーバーのコンポーネントです。
  2. Azure HDInsight .Net SDK の API が使用できるようになりました。これにより、クラスターで実行するアプリケーションをプログラムで列挙し、関連するアプリケーションやコンテナーに限定したログ (テキスト形式) をダウンロードして、アプリケーションに問題が発生した際のデバッグに役立てられます。

> [AZURE.NOTE]このドキュメントの情報は、Windows ベースの HDInsight クラスターに固有のものです。Linux ベースの HDInsight クラスター上の YARN ログへのアクセスの詳細については、「[Access YARN application logs on Linux-based Hadoop on HDInsight (HDInsight の Linux ベースの Hadoop 上の YARN アプリケーション ログにアクセスする)](hdinsight-hadoop-access-yarn-app-logs-linux.md)」を参照してください。

## 前提条件

Azure HDInsight SDK は、このトピックで説明するコードを .NET Framework アプリケーションで使用する必要があります。SDK の最新の公開ビルドは [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) で入手できます。

Visual Studio アプリケーションから HDInsight SDK をインストールするには、**[ツール]** メニューで **[NuGet パッケージ マネージャー]** をクリックし、次に **[パッケージ マネージャー コンソール]** をクリックします。コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

このコマンドにより、HDInsight の .NET ライブラリと .NET ライブラリへの参照が現在の Visual Studio プロジェクトに追加されます。


## <a name="YARNTimelineServer"></a>YARN タイムライン サーバー

<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN タイムライン サーバー</a>は完了したアプリケーションに関する汎用的な情報のほか、2 つの異なるインターフェイスを通じてフレームワーク固有のアプリケーション情報を提供します。具体的には次の処理が行われます。

* HDInsight クラスター上での汎用アプリケーション情報の格納と取得はバージョン 3.1.1.374 以降で有効になります。
* タイムライン サーバーのフレームワーク固有のアプリケーション情報コンポーネントは、現在 HDInsight クラスターで使用できません。


アプリケーションの汎用情報には次のようなデータが含まれています。

* アプリケーション ID (アプリケーションの一意の識別子)
* アプリケーションを開始したユーザー
* アプリケーションを完了するために実行された試みに関する情報
* 特定のアプリケーションの試行で使用されたコンテナー

HDInsight クラスターで、この情報は Azure リソース マネージャーにより、既定の Azure ストレージ アカウントの既定のコンテナーにある履歴ストアに格納されます。完了したアプリケーションに関するこの汎用データは、REST API を介して取得できます。

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

HDInsight .NET SDK に新しい API が追加され、このデータをプログラムで簡単に取得できるようになりました。汎用データは、クラスター ノード上で直接 YARN コマンド ライン インターフェイス (CLI) コマンドを実行しても取得できます (RDP を使用してクラスターに接続した後)。

## <a name="YARNAppsAndLogs"></a>YARN アプリケーションとログ

YARN はアプリケーションのスケジュール設定/監視からリソース管理を切り離すことで、複数のプログラミング モデル (MapReduce はそのうちの 1 つ) をサポートします。これは、グローバルな *リソース マネージャー* (RM)、ワーカー ノードごとの*ノード マネージャー* (NM)、アプリケーションごとの*アプリケーション マスター* (AM) によって実現されます。アプリケーションごとの AM は、アプリケーションを実行するためのリソース (CPU、メモリ、ディスク、ネットワーク) を RM と調整します。RM は NM と連携して、これらのリソースに*コンテナー*としての許可を付与します。AM は、RM によって自身に割り当てられたコンテナーの進行状況を追跡します。アプリケーションはその性質によって、多くのコンテナーを必要とする場合があります。

さらに、クラッシュが発生した場合または AM と RM の間で通信が失われた場合、アプリケーションを完了するために、各アプリケーションで複数の*アプリケーション試行*が行われる場合があります。そのため、アプリケーションの特定の試行にコンテナーが付与されます。コンテナーは YARN アプリケーションによって実行される作業の基本単位のコンテキストを提供します。そのコンテキストの中で行われるすべての作業は、コンテナーが割り当てられた 1 つのワーカー ノードで実行されます。詳細については、「[YARN の概念][YARN-concepts]」に関するページをご覧ください。

アプリケーションのログ (および関連するコンテナーのログ) は、問題のある Hadoop アプリケーションのデバッグに重要です。YARN は、[ログの集計][log-aggregation]機能により、アプリケーションのログを収集、集計、格納するための便利なフレームワークを提供します。ログの集計機能により、アプリケーションのログへのアクセスはより確実になります。この機能は、ワーカー ノード上のすべてのコンテナーでログを集計し、アプリケーションが終了した後でワーカー ノードごとに 1 つの集計されたログ ファイルとして既定のファイル システムに保存します。アプリケーションは数百または数千のコンテナーを使用することがありますが、1 つのワーカー ノードで実行されるすべてのコンテナーのログは常に 1 つのファイルに集計されます。つまりアプリケーションで使用するワーカー ノードごとに 1 つのログ ファイルが生成されます。ログの集計は、HDInsight クラスターでは既定で有効になっており (バージョン 3.0 以上)、集計されたログは、クラスターの既定のコンテナーの次の場所にあります。

	wasb:///app-logs/<user>/logs/<applicationId>

ここで、*user* はアプリケーションを開始したユーザーの名前であり、*applicationId* は YARN リソース マネージャーにより割り当てられたアプリケーションの一意の識別子です。

集計されたログは、コンテナーでインデックスが作成され、[TFile][T-file] に[バイナリ形式][binary-format]で書かれているので、直接読み取ることはできません。YARN は、アプリケーションまたは関連するコンテナーに対して、これらのログをプレーン テキストとしてダンプする CLI ツールを提供します。次の YARN コマンドをクラスター ノード上で直接実行することで (RDP による接続後)、これらのログをプレーン テキストとして表示できます。

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

次のセクションでは、HDInsight クラスターへの接続に RDP を使用せずに、特定のアプリケーションやコンテナーのログにプログラムを使用してアクセスする方法について説明します。

## <a name="enumerate-and-download"></a>プログラムによるアプリケーションの列挙とログのダウンロード

次のコード サンプルを使用するには、HDInsight .Net SDK の最新バージョンをダウンロードして上記の前提条件を満たす必要があります。提示されている手順を参照してください。

次のコードは、新しい API を使用してアプリケーションを列挙し、完了したアプリケーションのログをダウンロードする方法を示しています。

> [AZURE.NOTE]次の API は 3.1.1.374 以降のバージョンで "実行中" の Hadoop クラスターに対してのみ機能します。次のディレクティブを追加します。

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

これらは、次のコードで新しく定義した API を参照します。次のコード スニペットは、サブスクリプションで、"実行中" のクラスターに対して、アプリケーション履歴クライアントを作成します。

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";

	// Create an HDInsight client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);

	HDInsightCertificateCredential creds =
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);

	IHDInsightClient client = HDInsightClient.Connect(creds);

	// Get the cluster on which your applications were run
	// The cluster needs to be in the "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);

	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient =
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


アプリケーション履歴クライアントを使用して、完了したアプリケーションの一覧表示、条件に基づくアプリケーションのフィルター処理、関連するアプリケーション ログのダウンロードができます。次のコード スニペットは、プログラムで実行する方法を示しています。

	// Local download folder location where the logs will be placed
	string downloadLocation = "E:\\YarnApplicationLogs";

	// List completed applications on your cluster that were submitted in the last 24 hours but failed
	// Search for applications based on application name
	string appNamePrefix = "your-app-name-prefix";
	DateTime endTime = DateTime.UtcNow;
	DateTime startTime = endTime.AddHours(-24);
	IEnumerable<ApplicationDetails> applications = appHistoryClient
	                .ListCompletedApplications(startTime, endTime)
	                .Where(app =>
	                    app.GetApplicationFinalStatusAsEnum() == ApplicationFinalStatus.Failed
	                    && app.Name.StartsWith(appNamePrefix));

	// Download logs for failed or killed applications
	// This will generate one log file for each application
	foreach (ApplicationDetails application in applications)
	{
	    appHistoryClient.DownloadApplicationLogs(application, downloadLocation);
	}

上記のコードは、アプリケーション履歴クライアントを使用して対象のアプリケーションの一覧表示または検索を行い、それらのアプリケーションのログをローカル フォルダーにダウンロードします。

または、次のコード スニペットで、アプリケーション ID がわかっているアプリケーションのログをダウンロードします。アプリケーション ID は、リソース マネージャーにより割り当てられる、アプリケーションのグローバルに一意の識別子です。これは、リソース マネージャーの開始時刻とそこに送信されるアプリケーションのカウンターを使用して作成されます。カウンター数は単調に増加します。アプリケーション ID の形式は "application\_<RM-start-time>\_<Counter>" です。アプリケーション ID とジョブ ID は異なることに注意してください。アプリケーション ID はフレームワークに依存しない YARN の概念であるのに対して、ジョブ ID は MapReduce フレームワークに固有の概念です。YARN では、ジョブ ID は、リソース マネージャーに送信された MapReduce アプリケーションのアプリケーション マスターによって処理される、特定の MapReduce ジョブを識別します。

	// Download application logs for an application whose application ID is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

必要に応じて、以下のように、アプリケーションが使用した各コンテナー (または特定のコンテナー) のログをダウンロードすることもできます。

	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);

	// Download logs separately for each container of application(s) of interest
	// This will generate one log file per container
	IEnumerable<ApplicationAttemptDetails> applicationAttempts =
				appHistoryClient.ListApplicationAttempts(someApplication);

	ApplicationAttemptDetails finalAttempt = applicationAttempts
	    		.Single(x => x.ApplicationAttemptId == someApplication.LatestApplicationAttemptId);

	IEnumerable<ApplicationContainerDetails> containers =
				appHistoryClient.ListApplicationContainers(finalAttempt);

	foreach (ApplicationContainerDetails container in containers)
	{
	    appHistoryClient.DownloadApplicationLogs(container, downloadLocation);
	}



[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!---HONumber=Oct15_HO1-->