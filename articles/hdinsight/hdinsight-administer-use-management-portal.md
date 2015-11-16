<properties
	pageTitle="Azure プレビュー ポータルを使用した HDInsight での Hadoop クラスターの管理 | Microsoft Azure"
	description="HDInsight サービスを管理する方法を学習します。HDInsight クラスターを作成し、対話型 JavaScript コンソールを開いて、Hadoop コマンド コンソールを開きます。"
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
	ms.date="11/04/2015"
	ms.author="jgao"/>

# Azure プレビュー ポータルを使用した HDInsight での Hadoop クラスターの管理

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-portal-management-selector.md)]

[Azure プレビュー ポータル][azure-portal]を使用すると、Azure HDInsight で Hadoop クラスターを作成し、Hadoop ユーザーのパスワードを変更して、クラスターで Hadoop コマンド コンソールにアクセスできるようにリモート デスクトップ プロトコル (RDP) を有効にすることができます。

この記事の情報は、Windows ベースの HDInsight クラスターにのみ適用されます。Linux ベースのクラスターの管理については、上部のタブ セレクターをクリックしてください。

他のツールを使用して HDInsight で Hadoop クラスターを作成する方法については、タブ セレクターをクリックしてください。

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Azure ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal-v1.md)

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- **Azure ストレージ アカウント** - HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。Azure BLOB ストレージと HDInsight クラスターのシームレスな統合の詳細については、[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)に関するページを参照してください。Azure ストレージ アカウントの作成の詳細については、[ストレージ アカウントの作成方法](../storage-create-storage-account.md)に関するページを参照してください。

##プレビュー ポータルを開く

1. [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. ポータルを開くと、次の操作を行うことができます。

	- 左側のメニューの **[新規]** をクリックして新しいクラスターを作成する。
	
		![new HDInsight cluster buttom](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
	- 左側のメニューの **[HDInsight]** をクリックする。
	
		![Azure portal HDInsight cluster buttom](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

	左側のメニューに **[HDInsight]** が表示されていない場合は、**[参照]** をクリックする。

	![Azure portal Browse cluster buttom](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

##クラスターの作成

プレビュー ポータルを使用した作成方法については、[HDInsight クラスターの作成](hdinsight-provision-clusters.md#create-using-the-preview-portal)に関するセクションを参照してください。

HDInsight は、広範囲の Hadoop コンポーネントで動作します。検証およびサポートされているコンポーネントの一覧については、「[Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)」を参照してください。次のいずれかのオプションを使用して、HDInsight をカスタマイズできます。

- Script Action を使用してカスタム スクリプトを実行します。これによってクラスターをカスタマイズして、クラスター構成を変更することや、Giraph や Solr などのカスタム コンポーネントをインストールすることができます。詳細については、[Script Action を使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster.md)に関するページを参照してください。
- クラスターの作成中に、HDInsight .NET SDK または Azure PowerShell でクラスター カスタマイズ パラメーターを使用します。そうすることで、クラスターの有効期間中はこれらの構成変更が保持され、Azure プラットフォームが保守のために定期的に実行するクラスター ノードの再イメージ化の影響も受けません。クラスター カスタマイズ パラメーターの使用方法の詳細については、[HDInsight クラスターの作成](hdinsight-provision-clusters.md)に関するページを参照してください。
- Mahout や Cascading などの一部のネイティブ Java コンポーネントは、JAR ファイルとしてクラスター上で実行できます。これらの JAR ファイルは、Azure BLOB ストレージに分配し、Hadoop ジョブ送信メカニズムによって HDInsight クラスターに送信できます。詳細については、[プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)に関するページを参照してください。

	>[AZURE.NOTE]HDInsight クラスターへの JAR ファイルのデプロイ、または HDInsight クラスターでの JAR ファイルの呼び出しに関する問題がある場合は、[Microsoft サポート](http://azure.microsoft.com/support/options/)にお問い合わせください。

	> Cascading は HDInsight ではサポートされておらず、Microsoft サポートの対象でもありません。サポートされているコンポーネントの一覧については、「[HDInsight によって提供されるクラスター バージョンの新機能](hdinsight-component-versioning.md)」を参照してください。

リモート デスクトップ接続を使用したクラスターへのカスタム ソフトウェアのインストールはサポートされていません。クラスターの再作成が必要な場合にファイルが失われるため、ヘッド ノードのドライブにはファイルを保存しないでください。Azure BLOB ストレージにファイルを保存することをお勧めします。BLOB ストレージは永続的です。

##クラスターの一覧と表示

1. [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. 左側のメニューの **[HDInsight]** をクリックします。
	
	![Azure portal HDInsight cluster buttom](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)
		
	左側のメニューに **[HDInsight]** が表示されていない場合は、**[参照]** をクリックします。

	![Azure portal Browse cluster buttom](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

	クラスターが存在する場合はクラスターの一覧が表示されます。
	
	![Azure portal cluster list](./media/hdinsight-administer-use-management-portal/hdinsight-list-clusters.png)

3. **[フィルター項目]** と "サブスクリプション" を使用して一覧を絞り込みます。
4. 一覧にあるクラスターをダブルクリックして詳細を表示します。

	**メニューと基本情報**:

	![Azure portal hdinsight cluster essentials](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)
	
	- メニューをカスタマイズするには、メニュー上の任意の場所を右クリックし、**[カスタマイズ]** をクリックします。
	- **[設定]** と **[すべての設定]**: クラスター用の **[設定]** ブレードを表示します。このブレードを使用して、クラスターの詳しい構成情報にアクセスできます。
	- **[ダッシュボード]**、**[クラスター ダッシュボード]**、**[URL]: これらはすべてクラスター ダッシュボードにアクセスするために使用します。クラスター ダッシュボードは、Linux ベースのクラスター用の Ambari Web です。
- **[リモート デスクトップ]**: RDP を使用してクラスターに接続します。
	- **[クラスターのスケーリング]**: このクラスターの worker ノードの数を変更できます。
	- **[削除]**: クラスターを削除します。
	- **[クイック スタート]** (![雲と雷のアイコン = クイック スタート](./media/hdinsight-administer-use-portal-linux/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。
	- **[ユーザー]** (![ユーザー アイコン](./media/hdinsight-administer-use-portal-linux/users.png)): このクラスターの_ポータル管理_に対する、Azure サブスクリプションの他のユーザーの権限を設定できます。
	
		> [AZURE.IMPORTANT]これは、Azure プレビュー ポータルでのこのクラスターへのアクセスと権限_だけ_に影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。
	- **[タグ]** (![タグ アイコン](./media/hdinsight-administer-use-portal-linux/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。
	- **[ドキュメント]**: Azure HDInsight のドキュメントへのリンクです。
	
	> [AZURE.IMPORTANT]HDInsight クラスターによって提供されるサービスを管理するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、[Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)に関するページを参照してください。

	**[使用状況]**:
	
	![Azure portal hdinsight cluster usage](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
	
5. **[設定]** をクリックします。

	![Azure portal hdinsight cluster usage](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	- **[プロパティ]**: クラスターのプロパティを表示します。
	- **[クラスター AAD ID]**: 
	- **[Azure ストレージ キー]**: 既定のストレージ アカウントとそのキーを表示します。ストレージ アカウントは、クラスターの作成処理中に構成します。
	- **[クラスター ログイン]**: クラスターの HTTP ユーザー名とパスワードを変更します。
	- **[外部メタストア]**: Hive メタストアと Oozie メタストアを表示します。メタストアを構成できるのは、クラスターの作成処理中のみです。
	- **[クラスターのスケーリング]**: クラスターの worker ノードの数を増減します。
	- **[リモート デスクトップ]**: リモート デスクトップ (RDP) アクセスを有効および無効にし、RDP ユーザー名を構成します。RDP ユーザー名は HTTP ユーザー名とは別にする必要があります。
	- **[指名パートナー]**:

6. **[プロパティ]** をクリックします。

	次のプロパティが表示されます。
	
	- **[ホスト名]**: クラスター名。
	- **[クラスター URL]**。
	- **[状態]**: Aborted、Accepted、ClusterStorageProvisioned、AzureVMConfiguration、HDInsightConfiguration、Operational、Running、Error、Deleting、Deleted、Timedout、DeleteQueued、DeleteTimedout、DeleteError、PatchQueued、CertRolloverQueued、ResizeQueued、ClusterCustomization が表示されます。
	- **[リージョン]**: Azure の場所。サポートされている Azure の場所の一覧については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」の **[リージョン]** ボックスの一覧を参照してください。
	- **[データの作成日]**。
	- **[オペレーティング システム]**: **Windows** または **Linux**。
	- **[タイプ]**: Hadoop、HBase、Storm、Spark。 
	- **[バージョン]**。[HDInsight のバージョン](hdinsight-component-versioning.md)に関するページを参照してください。
	- **[サブスクリプション]**: サブスクリプション名。
	- **[サブスクリプション ID]**。
	- **[プライマリ データ ソース]**:既定の Hadoop ファイル システムとして使用される Azure BLOB ストレージ アカウント。
	- **[worker ノードの価格レベル]**。
	- **[ヘッド ノードの価格レベル]**。

##クラスターの削除

クラスターを削除しても、既定のストレージ アカウントまたはリンクされたストレージ アカウントは削除されません。同じストレージ アカウントと同じメタストアを使用してクラスターを再作成することができます。

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左側のメニューの **[すべて参照]** をクリックし、**[HDInsight クラスター]**、クラスター名の順にクリックします。
3. 上部のメニューの **[削除]** をクリックし、指示に従います。

「[クラスターの一時停止またはシャットダウン](#pauseshut-down-clusters)」も参照してください。

##クラスターのスケール
クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用される worker ノードの数を、クラスターを再作成することなく、変更できます。

>[AZURE.NOTE]HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。クラスターのバージョンがわからない場合、[プロパティ] ページを確認できます。[クラスター ポータル インターフェイス](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface)に関するセクションを参照してください。

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

	![HDInsight Storm の規模のバランス調整](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	CLI コマンドを使用して Storm トポロジのバランスを再調整する方法を次の例で示します。

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**クラスターの規模を設定するには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左側のメニューの **[すべて参照]** をクリックし、**[HDInsight クラスター]**、クラスター名の順にクリックします。
3. 上部のメニューの **[設定]** をクリックし、**[クラスターのスケーリング]** をクリックします。
4. **[worker ノードの数]** に値を入力します。クラスター ノードの数に制限は Azure サブスクリプションによって異なります。制限値を上げるには、課金サポートにお問い合わせください。コスト情報にはノード数の変更が反映されます。

	![HDInsight Hadoop HBase Storm および Spark の規模](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

##クラスターの一時停止またはシャットダウン

Hadoop ジョブの大半は、たまにしか実行されないバッチ ジョブです。ほとんどの Hadoop クラスターでは、クラスターが処理に使用されていない期間がかなりあります。HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。また、HDInsight クラスターは、使用していない場合でも課金されます。クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

このプロセスをプログラムで実行する方法は数多くあります。

- Azure Data Factory を使用する。オンデマンドおよび自己定義型の HDInsight のリンクされたサービスについては、「[Azure HDInsight のリンクされたサービス](../data-factory-compute-linked-services.md/#azure-hdinsight-linked-service)」と「[Azure Data Factory を使用した変換と分析](../data-factory-data-transformation-activities.md)」を参照してください。
- Azure PowerShell を使用する。[フライト遅延データの分析](hdinsight-analyze-flight-delay-data.md)に関するページを参照してください。
- Azure CLI を使用する。[Azure CLI を使用した HDInsight クラスターの管理](hdinsight-administer-use-command-line.md)に関するページを参照してください。
- HDInsight .NET SDK を使用する。[Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)に関するページを参照してください。

価格情報については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。プレビュー ポータルからクラスターを削除する方法については、「[クラスターの削除](#delete-clusters)」を参照してください。

##クラスターのユーザー名の変更

HDInsight クラスターは、2 つのユーザー アカウントを持つことができます。HDInsight クラスターのユーザー アカウントは、作成処理中に作成されます。RDP を使用してクラスターにアクセスするために、RDP ユーザー アカウントを作成することもできます。「[リモート デスクトップを有効にする](#connect-to-hdinsight-clusters-by-using-rdp)」を参照してください。

**HDInsight クラスターのユーザー名とパスワードを変更するには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左側のメニューの **[すべて参照]** をクリックし、**[HDInsight クラスター]**、クラスター名の順にクリックします。
3. 上部のメニューの **[設定]** をクリックし、**[クラスター ログイン]** をクリックします。
4. **[クラスター ログイン]** が有効になっている場合は、ユーザー名とパスワードを変更する前に、**[無効化]** をクリックしてから **[有効化]** をクリックする必要があります。
4. **[クラスター ログイン名]** または **[クラスター ログイン パスワード]** を変更し、**[保存]** をクリックします。

	![HDInsight クラスターの HTTP ユーザーのユーザー名とパスワードの変更](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##アクセス権の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

既定では、これらのサービスへのアクセス許可が付与されます。アクセス権の取り消しまたは付与は、Azure プレビュー ポータルから実行できます。

>[AZURE.NOTE]アクセス許可を付与するか、取り消すことで、クラスターのユーザー名とパスワードがリセットされます。

**HTTP Web サービスのアクセス許可を付与する/取り消すには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左側のメニューの **[すべて参照]** をクリックし、**[HDInsight クラスター]**、クラスター名の順にクリックします。
3. 上部のメニューの **[設定]** をクリックし、**[クラスター ログイン]** をクリックします。
4. **[クラスター ログイン]** が有効になっている場合は、ユーザー名とパスワードを変更する前に、**[無効化]** をクリックしてから **[有効化]** をクリックする必要があります。
6. **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]** に、クラスターの新しいユーザー名とパスワードをそれぞれ入力します。
7. **[保存]** をクリックします。

	![HDInsight による HTTP Web サービス アクセスの付与と削除](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)




##既定のストレージ アカウントの検索

各 HDInsight クラスターには、既定のストレージ アカウントが設定されています。クラスターの既定のストレージ アカウントとそのキーは、**[設定]** の **[プロパティ]** にある **[Azure ストレージ キー]** に表示されます。「[クラスターの一覧と表示](list-and-show-clusters)」を参照してください。

	
##リソース グループの検索 

ARM モードでは、各 HDInsight クラスターは Azure リソース グループと共に作成されます。クラスターが属している Azure リソース グループは、次の場所に表示されます。

- クラスター一覧の**リソース グループ**列。
- クラスターの **[基本情報]** タイル。  

「[クラスターの一覧と表示](list-and-show-clusters)」を参照してください。
   
##HDInsight クエリ コンソールを開く

HDInsight クエリ コンソールには、次の機能が用意されています。

- **[作業の開始に役立つギャラリー]**: ギャラリーの使用方法については、「[HDInsight 作業の開始に役立つギャラリーを使用して Hadoop について説明する](hdinsight-learn-hadoop-use-sample-gallery.md)」を参照してください。
- **[Hive エディター]**: Hive ジョブを送信するための GUI Web インターフェイス。「[クエリ コンソールを使用して Hive クエリを実行](hdinsight-hadoop-use-hive-query-console.md)」を参照してください。

	![hdinsight portal hive editor](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)

- **[ジョブ履歴]**: Hadoop ジョブを監視します。

	![hdinsight portal job history](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

	**[クエリ名]** をクリックすると、ジョブのプロパティ、**ジョブ クエリ**、**ジョブの出力など、ジョブの詳細が表示されます。また、クエリと出力の両方をワークステーションにダウンロードすることもできます。

- **[ファイル ブラウザー]**: 既定のストレージ アカウント、およびリンクされたストレージ アカウントを参照します。

	![hdinsight portal file browser browse](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

	スクリーンショットでは、種類に **<Account>** とあり、項目が Azure ストレージ アカウントであることを示しています。アカウント名をクリックすると、ファイルを参照できます。
	
- **[Hadoop UI]**。

	![hdinsight portal Hadoop UI](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)
	
	**[Hadoop UI]* では、ファイルを参照したり、ログを確認したりできます。

- **[Yarn UI]**。

	![hdinsight portal YARN UI](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

##Hive クエリの実行

プレビュー ポータルから Hive ジョブを実行するには、HDInsight クエリ コンソールで **[Hive エディター]** をクリックします。「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」を参照してください。

##ジョブの監視

プレビュー ポータルからジョブを監視するには、HDInsight クエリ コンソールで **[ジョブ履歴]** をクリックします。「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」を参照してください。

##ファイルの参照

既定のストレージ アカウントおよびリンクされたストレージ アカウントに格納されているファイルを参照するには、HDInsight クエリ コンソールで **[ファイル ブラウザー]** をクリックします。「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」を参照してください。

また、HDInsight コンソールの **[Hadoop UI]** から **[ファイル システムの参照]** ユーティリティを使用することもできます。「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」を参照してください。



##クラスターの使用状況の監視

HDInsight クラスター ブレードの __[使用状況]__ セクションには、サブスクリプションで HDInsight 用に使用できるコアの数、このクラスターに割り当てられているコアの数、およびこのクラスター内のノードへのコアの割り当て方法に関する情報が表示されます。「[クラスターの一覧と表示](#list-and-show-clusters)」を参照してください。

> [AZURE.IMPORTANT]HDInsight クラスターによって提供されるサービスを監視するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、[Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)に関するページを参照してください。


##Hadoop UI を開く

クラスターの監視、ファイル システムの参照、ログの確認を行うには、HDInsight クエリ コンソールで **[Hadoop UI]** をクリックします。「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」を参照してください。

##Yarn UI を開く

Yarn のユーザー インターフェイスを使用するには、HDInsight クエリ コンソールで **[Yarn UI]** をクリックします。「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」を参照してください。

##RDP を使用したクラスターへの接続

クラスターの作成時に指定したクラスターの資格情報を使用するとクラスター上のサービスにアクセスできますが、リモート デスクトップを介してクラスター自体にアクセスすることはできません。クラスターにプロビジョニングするとき、あるいはクラスターにプロビジョニングした後、リモート デスクトップ アクセスをオンにできます。作成時にリモート デスクトップを有効にする方法については、[HDInsight クラスターの作成](hdinsight-provision-clusters.md)に関するページを参照してください。

**リモート デスクトップを有効にするには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左側のメニューの **[すべて参照]** をクリックし、**[HDInsight クラスター]**、クラスター名の順にクリックします。
3. 上部のメニューの **[設定]** をクリックし、**[リモート デスクトップ]** をクリックします。
4. **[有効期限]**、**[リモート デスクトップのユーザー名]**、**[リモート デスクトップのパスワード]** に入力し、**[有効化]** をクリックします。

	![HDInsight によるリモート デスクトップ構成の有効化と無効化](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	[有効期限] の既定値は 1 週間です。
> [AZURE.NOTE]HDInsight .NET SDK を使用して、クラスターに対するリモート デスクトップを有効にすることもできます。HDInsight クライアント オブジェクトで、**EnableRdp** メソッドを次の形式で使用します: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**。同様に、クラスターに対するリモート デスクトップを無効にするには、**client.DisableRdp(clustername, location)** を使用します。これらのメソッドの詳細については、[HDInsight .NET SDK のリファレンス](http://go.microsoft.com/fwlink/?LinkId=529017)を参照してください。この方法は、Windows で実行されている HDInsight クラスターにのみ適用できます。

**RDP を使用してクラスターに接続するには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左側のメニューの **[すべて参照]** をクリックし、**[HDInsight クラスター]**、クラスター名の順にクリックします。
3. 上部のメニューの **[設定]** をクリックし、**[リモート デスクトップ]** をクリックします。
4. **[接続]** をクリックし、指示に従います。接続が無効の場合は、最初に有効する必要があります。必ずリモート デスクトップ ユーザーのユーザー名とパスワードを使用してください。クラスターのユーザー資格情報は使用できません。


##Hadoop コマンド ラインを開く

リモート デスクトップを使用してクラスターに接続して Hadoop コマンド ラインを使用するには、まず、先のセクションで説明したように、クラスターに対するリモート デスクトップ アクセスを有効にする必要があります。

**Hadoop コマンド ラインを開くには**

1. リモート デスクトップを使用してクラスターに接続します。
8. デスクトップで、**[Hadoop コマンド ライン]** をダブルクリックします。

	![HDI.HadoopCommandLine][image-hadoopcommandline]

	Hadoop コマンドの詳細については、[Hadoop コマンド リファレンス](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html)を参照してください。

先のスクリーンショットで、フォルダー名には Hadoop のバージョン番号が埋め込まれています。バージョン番号は、クラスターにインストールされている Hadoop コンポーネントのバージョンに基づいて変更できます。Hadoop 環境変数を使用して、これらのフォルダーを参照できます。次に例を示します。

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%
	
##次のステップ
この記事では、プレビュー ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。詳細については、次の記事を参照してください。

* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターの作成](hdinsight-provision-clusters.md)
* [プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight の概要](../hdinsight-get-started.md)
* [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop コマンド ライン"

<!---HONumber=Nov15_HO2-->