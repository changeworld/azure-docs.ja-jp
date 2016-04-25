<properties
	pageTitle="Azure ポータルを使用した HDInsight での Linux ベースの Hadoop クラスターの管理 | Microsoft Azure"
	description="Azure ポータルを使用して Linux ベースの HDInsight クラスターを作成および管理する方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="jgao"/>

#Azure ポータルを使用した HDInsight での Hadoop クラスターの管理

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-portal-management-selector.md)]

[Azure ポータル][azure-portal]を使用すると、Azure HDInsight で Linux ベースのクラスターを管理できます。他のツールを使用して HDInsight で Hadoop クラスターを作成する方法については、タブ セレクターを使用してください。

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

##ポータルを開く

1. [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. ポータルを開くと、次の操作を行うことができます。

	- 左側のメニューの **[新規]** をクリックして新しいクラスターを作成する。
	
		![新しい [HDInsight クラスター] ボタン](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
	- 左側のメニューの **[HDInsight クラスター]** をクリックして既存のクラスターを一覧表示する。
	
		![Azure ポータル [HDInsight クラスター] ボタン](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

        左側のメニューに **[HDInsight]** が表示されていない場合は、**[参照]** をクリックする。

        ![Azure ポータル [参照] ボタン](./media/hdinsight-administer-use-portal-linux/azure-portal-browse-button.png)

##クラスターの作成

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight は、広範囲の Hadoop コンポーネントで動作します。検証およびサポートされているコンポーネントの一覧については、「[Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)」を参照してください。一般的なクラスターの作成に関する詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

##クラスターの一覧と表示

1. [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックして既存のクラスターを一覧表示します。
3. クラスター名をクリックします。クラスターの一覧が長い場合は、ページの上部でフィルターを使用できます。
4. 一覧にあるクラスターをダブルクリックして詳細を表示します。

	**メニューと基本情報**:

	![Azure ポータル HDInsight クラスター 基本情報](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
	
	- **設定**と**すべての設定**: クラスター用の **[設定]** ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。
	- **[ダッシュボード]**、**[クラスター ダッシュボード]**、**[URL]: これらはすべてクラスター ダッシュボードにアクセスするために使用します。クラスター ダッシュボードは、Linux ベースのクラスター用の Ambari Web です。
- **[SSH (Secure Shell)]**: Secure Shell (SSH) 接続を使用してクラスターに接続する方法を表示します。
	- **[クラスターのスケーリング]**: このクラスターの worker ノードの数を変更できます。
	- **[削除]**: クラスターを削除します。
	- **[クイック スタート]** (![雲と雷のアイコン = クイック スタート](./media/hdinsight-administer-use-portal-linux/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。
	- **[ユーザー]** (![ユーザー アイコン](./media/hdinsight-administer-use-portal-linux/users.png)): このクラスターの_ポータル管理_に対する、Azure サブスクリプションの他のユーザーの権限を設定できます。
	
		> [AZURE.IMPORTANT] これは、Azure ポータルでのこのクラスターへのアクセスと権限_だけ_に影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。
	- **[タグ]** (![タグ アイコン](./media/hdinsight-administer-use-portal-linux/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。
	- **[Ambari Views]**: Ambari Web へのリンク。
	
	> [AZURE.IMPORTANT] HDInsight クラスターによって提供されるサービスを管理するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、「[Ambari を使用した HDInsight クラスターの管理 (プレビュー)](hdinsight-hadoop-manage-ambari.md)」をご覧ください。

	**[使用状況]**:
	
	![Azure ポータル HDInsight クラスター 使用状況](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-cluster-usage.png)
	
5. **[設定]** をクリックします。

	![Azure ポータル HDInsight クラスター 使用状況](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.cluster.settings.png)

	- **[監査ログ]**:
    - **[クイック スタート]**: HDInsight の操作を開始するために役立つ情報を表示します。
	- **[クラスター ログイン]**: クラスターの HTTP ユーザー名とパスワードを変更します。
	- **[クラスターのスケーリング]**: クラスターの worker ノードの数を増減します。
    - **[SSH (Secure Shell)]**: Secure Shell (SSH) 接続を使用してクラスターに接続する方法を表示します。
    - **[HDInsight パートナー]**: 現在の HDInsight パートナーを追加または削除します。
	- **[外部メタストア]**: Hive メタストアと Oozie メタストアを表示します。メタストアを構成できるのは、クラスターの作成処理中のみです。
    - **[スクリプト アクション]**: クラスター上の Bash スクリプトを実行します。
    - **[プロパティ]**: クラスターのプロパティを表示します。
	- **[Azure ストレージ キー]**: 既定のストレージ アカウントとそのキーを表示します。ストレージ アカウントは、クラスターの作成処理中に構成します。
	- **[クラスター AAD ID]**: 
	- **[ユーザー]**: このクラスターの_ポータル管理_に対する、Azure サブスクリプションの他のユーザーの権限を設定できます。
	- **[タグ]**: タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。
    
    > [AZURE.NOTE] これは使用可能な設定の汎用リストです。すべてのクラスターの種類でこれらの設定がすべて表示されるわけではありません。

6. **[プロパティ]** をクリックします。

	プロパティは次のとおりです。
	
	- **[ホスト名]**: クラスター名。
	- **[クラスター URL]**。
	- **[状態]**: Aborted、Accepted、ClusterStorageProvisioned、AzureVMConfiguration、HDInsightConfiguration、Operational、Running、Error、Deleting、Deleted、Timedout、DeleteQueued、DeleteTimedout、DeleteError、PatchQueued、CertRolloverQueued、ResizeQueued、ClusterCustomization が表示されます。
	- **[リージョン]**: Azure の場所。サポートされている Azure の場所の一覧については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」の **[リージョン]** ボックスの一覧をご覧ください。
	- **[データの作成日]**。
	- **[オペレーティング システム]**: **Windows** または **Linux**。
	- **[タイプ]**: Hadoop、HBase、Storm、Spark。 
	- **[バージョン]**。[HDInsight のバージョン](hdinsight-component-versioning.md)に関するページをご覧ください。
	- **[サブスクリプション]**: サブスクリプション名。
	- **[サブスクリプション ID]**。
    - **[既定のデータ ソース]**: クラスターの既定のファイル システムです。
	- **[worker ノードの価格レベル]**。
	- **[ヘッド ノードの価格レベル]**。

##クラスターの削除

クラスターを削除しても、既定のストレージ アカウントまたはリンクされたストレージ アカウントは削除されません。同じストレージ アカウントと同じメタストアを使用してクラスターを再作成することができます。クラスターを再作成するときに、新しい既定の BLOB コンテナーを使用することをお勧めします。

1. [ポータル][azure-portal]にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上部のメニューの **[削除]** をクリックし、指示に従います。

「[クラスターの一時停止またはシャットダウン](#pauseshut-down-clusters)」もご覧ください。

##クラスターのスケール
クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用される worker ノードの数を、クラスターを再作成することなく、変更できます。

>[AZURE.NOTE] HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。クラスターのバージョンがわからない場合、[プロパティ] ページを確認できます。「[クラスターの一覧と表示](hdinsight-adminster-use-management-portal/#list-and-show-clusters)」をご覧ください。

HDInsight でサポートされているクラスターの種類ごとに、データ ノード数を変更した場合の影響:

- Hadoop は、

	保留中または実行中のジョブに影響を与えることなく、実行中の Hadoop クラスター内の worker ノードの数をシームレスに増加できます。処理の進行中に新しいジョブを送信することもできます。スケール設定処理の失敗は正常に処理され、クラスターは常に機能状態になります。

	データ ノードの数を減らして Hadoop クラスターのスケールを小さくした場合、クラスター内の一部のサービスが再起動されます。これにより、スケール設定処理の完了時に、実行中および保留中のすべてのジョブが失敗します。ただし、処理が完了した後にジョブを再送信できます。

- HBase

	実行中の HBase クラスターに対して、ノードの追加または削除をシームレスに実行できます。地域サーバーは、スケール設定処理の完了の数分以内に自動的に分散されます。ただし、クラスターのヘッドノードにログインし、コマンド プロンプト ウィンドウから次のコマンドを実行して、地域サーバーを手動で分散することもできます。

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	HBase シェルの使用方法の詳細については、以下を参照してください。
- Storm

	実行中の Storm クラスターに対して、データ ノードの追加または削除をシームレスに実行できます。ただし、スケール設定処理が正常に完了した後、トポロジのバランス再調整が必要になります。

	バランス再調整は、次の 2 つの方法で実行できます。

	* Storm Web UI
	* コマンド ライン インターフェイス (CLI) ツール

	詳細については、[Apache Storm に関するドキュメント](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)をご覧ください。

	Storm Web UI は、HDInsight クラスターで使用できます。

	![HDInsight Storm の規模のバランス調整](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)

	CLI コマンドを使用して Storm トポロジのバランスを再調整する方法を次の例で示します。

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**クラスターの規模を設定するには**

1. [ポータル][azure-portal]にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[クラスターの拡大縮小]** をクリックします。
4. **[worker ノードの数]** を入力します。クラスター ノードの数に制限は Azure サブスクリプションによって異なります。制限値を上げるには、課金サポートにお問い合わせください。コスト情報にはノード数の変更が反映されます。

	![HDInsight Hadoop HBase Storm および Spark の規模](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

##クラスターの一時停止またはシャットダウン

Hadoop ジョブの大半は、たまにしか実行されないバッチ ジョブです。ほとんどの Hadoop クラスターでは、クラスターが処理に使用されていない期間がかなりあります。HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。また、HDInsight クラスターは、使用していない場合でも課金されます。クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

このプロセスをプログラムで実行する方法は数多くあります。

- Azure Data Factory を使用する。オンデマンドの HDInsight がリンクされたサービスを作成する詳細については、「[Azure Data Factory を使用した HDInsight でのオンデマンドの Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-create-linux-clusters-adf.md)」を参照してください。
- Azure PowerShell を使用する。「[HDInsight での Hive を使用したフライト遅延データの分析](hdinsight-analyze-flight-delay-data.md)」をご覧ください。
- Azure CLI を使用する。「[Azure CLI を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-command-line.md)」をご覧ください。
- HDInsight .NET SDK を使用する。「[HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)」をご覧ください。

価格情報については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」をご覧ください。ポータルからクラスターを削除する方法については、「[クラスターの削除](#delete-clusters)」をご覧ください。

##クラスターのユーザー名の変更

HDInsight クラスターは、2 つのユーザー アカウントを持つことができます。HDInsight クラスターのユーザー アカウント (HTTP ユーザー アカウントとも呼ばれます) および SSH ユーザー アカウントは作成プロセス中に作成されます。Ambari Web UI を使用してクラスターのユーザー アカウントのユーザー名とパスワードを変更することができます。

**HDInsight クラスターのユーザー パスワードを変更するには**

1. HDInsight クラスター ユーザーの資格情報を使用して Ambari Web UI にサインインします。既定のユーザー名は **admin** です。URL は **https://<HDInsight Cluster Name>azurehdinsight.net** です。
2. 上部のメニューの **[Admin]** をクリックしてから [Manage Ambari (Ambari の管理)] をクリックします。 
3. 左側のメニューで **[ユーザー]** をクリックします。
4. **[Admin]** をクリックします。
5. **[パスワードの変更]** をクリックします。

##アクセス権の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

既定では、これらのサービスへのアクセス許可が付与されます。[Azure CLI](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) と [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access) を使用してアクセス許可を取り消すかまたは付与することができます。

##サブスクリプション ID の検索

**Azure サブスクリプション ID を検索するには**

1. [ポータル][azure-portal]にサインインします。
2. 左側のメニューで **[すべて参照]** をクリックしてから、**[サブスクリプション]** をクリックします。各サブスクリプションには、名前と ID があります。

各クラスターは、Azure サブスクリプションに関連付けられています。サブスクリプション ID はクラスターの **[基本情報]** タイルに表示されます。「[クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。

##リソース グループの検索 

ARM モードでは、各 HDInsight クラスターは Azure リソース グループと共に作成されます。クラスターが属している Azure リソース グループは、次の場所に表示されます。

- クラスター一覧の**リソース グループ**列。
- クラスターの **[基本情報]** タイル。  

「[クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。


##既定のストレージ アカウントの検索

各 HDInsight クラスターには、既定のストレージ アカウントが設定されています。クラスターの既定のストレージ アカウントとそのキーは、**[設定]** の **[プロパティ]** にある **[Azure Storage キー]** に表示されます。「[クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。


##Hive クエリの実行

Azure ポータルから直接 Hive ジョブを実行することはできませんが、Ambari Web UI の Hive ビューを使用することができます。

**Ambari Hive ビューを使用して Hive クエリを実行するには**

1. HDInsight クラスター ユーザーの資格情報を使用して Ambari Web UI にサインインします。既定のユーザー名は **admin** です。URL は **https://<HDInsight Cluster Name>azurehdinsight.net** です。
2. 次のスクリーンショットのように Hive ビューを開きます。  

	![HDInsight Hive ビュー](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)
3. ページの上部にある **[クエリ]** をクリックします。
4. **[クエリ エディター]** で Hive クエリを入力してから、**[実行]** をクリックします。

##ジョブの監視

「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md#monitoring)」をご覧ください。

##Browse files

Azure ポータルを使用して、既定のコンテナーのコンテンツを参照できます。

1. [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックして既存のクラスターを一覧表示します。
3. クラスター名をクリックします。クラスターの一覧が長い場合は、ページの上部でフィルターを使用できます。
4. **[設定]** をクリックします。
5. **[設定]** ブレードで **[Azure Storage キー]** をクリックします。
6. 既定のストレージ アカウント名をクリックします。
7. **[BLOB]** タイルをクリックします。
8. 既定のコンテナー名をクリックします。


##クラスターの使用状況の監視

HDInsight クラスター ブレードの __[使用状況]__ セクションには、サブスクリプションで HDInsight 用に使用できるコアの数、このクラスターに割り当てられているコアの数、およびこのクラスター内のノードへのコアの割り当て方法に関する情報が表示されます。「[クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。

> [AZURE.IMPORTANT] HDInsight クラスターによって提供されるサービスを監視するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、「[Ambari を使用した HDInsight クラスターの管理 (プレビュー)](hdinsight-hadoop-manage-ambari.md)」をご覧ください。

##クラスターへの接続

「[SSH による HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-ssh.md#ssh)」をご覧ください。
	
##次のステップ
この記事では、ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。詳細については、次の記事を参照してください。

* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターの作成](hdinsight-provision-clusters.md)
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での Sqoop の使用](hdinsight-use-sqoop.md)
* [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop コマンド ライン"

<!---HONumber=AcomDC_0413_2016-->