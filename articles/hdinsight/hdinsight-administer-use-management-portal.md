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
	ms.date="08/11/2015"
	ms.author="jgao"/>

# Azure プレビュー ポータルを使用した HDInsight での Hadoop クラスターの管理

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-portal-management-selector.md)]

[Azure プレビュー ポータル][azure-portal]を使用すると、Azure HDInsight で Hadoop クラスターをプロビジョニングし、Hadoop ユーザーのパスワードを変更して、クラスターで Hadoop コマンド コンソールにアクセスできるようにリモート デスクトップ プロトコル (RDP) を有効にすることができます。

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Azure ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal-v1.md)

> [AZURE.NOTE]このドキュメントの手順は Windows ベースの Hadoop クラスターの使用に固有のものです。Linux ベースのクラスターの使用については、「[Azure プレビュー ポータルを使用した HDInsight の Hadoop クラスターの管理](hdinsight-administer-use-portal-linux.md)」を参照してください。



> [AZURE.NOTE]プレビュー ポータル以外にも、HDInsight を管理するツールが用意されています。
>
> - [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
> - [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- **Azure ストレージ アカウント** - HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。Azure BLOB ストレージと HDInsight クラスターのシームレスな統合の詳細については、[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)に関するページを参照してください。Azure ストレージ アカウントの作成の詳細については、[ストレージ アカウントの作成方法](../storage-create-storage-account.md)に関するページを参照してください。


##HDInsight クラスターのプロビジョニング

プレビュー ポータルを使用したプロビジョニング方法については、「[HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)」を参照してください。

##HDInsight クラスター プロビジョニングのカスタマイズ

HDInsight は、広範囲の Hadoop コンポーネントで動作します。検証およびサポートされているコンポーネントの一覧については、「[Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)」を参照してください。次のいずれかのオプションを使用して、HDInsight をカスタマイズできます。

- Script Action を使用してカスタム スクリプトを実行します。これによってクラスターをカスタマイズして、クラスター構成を変更することや、Giraph や Solr などのカスタム コンポーネントをインストールすることができます。詳細については、[Script Action を使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster.md)に関するページを参照してください。
- クラスターのプロビジョニング中に、HDInsight .NET SDK または Azure PowerShell でクラスター カスタマイズ パラメーターを使用します。そうすることで、クラスターの有効期間中はこれらの構成変更が保持され、Azure プラットフォームが保守のために定期的に実行するクラスター ノードの再イメージ化の影響も受けません。クラスター カスタマイズ パラメーターの使用法の詳細については、「[HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)」を参照してください。
- Mahout や Cascading などの一部のネイティブ Java コンポーネントは、JAR ファイルとしてクラスター上で実行できます。これらの JAR ファイルは、Azure BLOB ストレージに分配し、Hadoop ジョブ送信メカニズムによって HDInsight クラスターに送信できます。詳細については、[プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)に関するページを参照してください。


	>[AZURE.NOTE]HDInsight クラスターへの JAR ファイルのデプロイ、または HDInsight クラスターでの JAR ファイルの呼び出しに関する問題がある場合は、[Microsoft サポート](http://azure.microsoft.com/support/options/)にお問い合わせください。

	> Cascading は HDInsight ではサポートされておらず、Microsoft サポートの対象でもありません。サポートされているコンポーネントの一覧については、「[HDInsight によって提供されるクラスター バージョンの新機能](hdinsight-component-versioning.md)」を参照してください。


リモート デスクトップ接続を使用したクラスターへのカスタム ソフトウェアのインストールはサポートされていません。クラスターの再作成が必要な場合にファイルが失われるため、ヘッド ノードのドライブにはファイルを保存しないでください。Azure BLOB ストレージにファイルを保存することをお勧めします。BLOB ストレージは永続的です。

## クラスター ポータル インターフェイスについての理解する

**クラスターにアクセスするには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[参照]** ブレードを開きます。
3. **[HDInsight クラスター]** をクリックし、**[HDInsight クラスター]** ブレードを開きます。
4. 一覧にあるクラスターの 1 つをクリックし、クラスター ブレードを開きます。

	![HDInsight ポータル](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.blade.png)

5. **[設定]** をクリックし、構成の詳細を確認し、クラスターを構成します。

	![HDInsight ポータルのクラスター設定](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	|設定|説明|
	|-----|-----|
	|プロパティ| クラスターのプロパティを表示します。|
	|Azure ストレージ キー| 既定の Azure ストレージ アカウント情報を表示します。 |
	|クラスター ログイン| HTTP Web サービス アクセスを付与するか、取り消し、クラスター ログイン情報を構成します。 |
	|外部メタストア| Hive/Oozie メタストア情報を表示します。|
	|クラスターの増減| クラスターの worker ノードの数を増減します。|
	|リモート デスクトップ| リモート デスクトップの接続を有効または無効にします。リモート デスクトップ経由でクラスターに接続します。|


##HTTP サービスのアクセス許可の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

既定では、これらのサービスへのアクセス許可が付与されます。Azure ポータルからアクセス許可を取り消す/付与することができます。

>[AZURE.NOTE]アクセス許可を付与するか、取り消すことで、クラスターのユーザー名とパスワードがリセットされます。

**HTTP Web サービスのアクセス許可を付与する/取り消すには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[クラスター ログイン]** をクリックします。
5. **[クラスター ログイン アクセス]** で **[オン]** または **[オフ]** をクリックします。  
6. **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]** に、クラスターの新しいユーザー名とパスワードをそれぞれ入力します。
7. **[保存]** をクリックします。

	![HDInsight による HTTP Web サービス アクセスの付与と削除](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)


この処理は、Azure PowerShell コマンドレットによって行うこともできます。

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

[Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)に関するページを参照してください。

##HDInsight クラスターのユーザー名とパスワードの変更
HDInsight クラスターは、2 つのユーザー アカウントを持つことができます。HDInsight クラスターのユーザー アカウントは、プロビジョニング処理中に作成されます。RDP を使用してクラスターにアクセスするために、RDP ユーザー アカウントを作成することもできます。「[リモート デスクトップを有効にする](#connect-to-hdinsight-clusters-by-using-rdp)」を参照してください。

**HDInsight クラスターのユーザー名とパスワードを変更するには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[クラスター ログイン]** をクリックします。
4. **[クラスター ログイン名]** と **[クラスター ログイン パスワード]** を変更し、**[保存]** をクリックします。

	![HDInsight クラスターの HTTP ユーザーのユーザー名とパスワードの変更](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##クラスターのスケール
クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用される worker ノードの数を、クラスターを再作成することなく、変更できます。

>[AZURE.NOTE]HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。クラスターのバージョンがわからない場合、[プロパティ] ページを確認できます。「[クラスター ポータル インターフェイスについての理解する](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface)」を参照してください。

HDInsight でサポートされているクラスターの種類ごとに、データ ノード数を変更した場合の影響:

- Hadoop

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
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[クラスターの拡大縮小]** をクリックします。
4. **[worker ノードの数]** を入力します。クラスター ノードの数に制限は Azure サブスクリプションによって異なります。制限値を上げるには、課金サポートにお問い合わせください。コスト情報にはノード数の変更が反映されます。


	![HDInsight Hadoop HBase Storm および Spark の規模](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)



##RDP を使用した HDInsight クラスターへの接続

クラスターの作成時に指定したクラスターの資格情報を使用するとクラスター上のサービスにアクセスできますが、リモート デスクトップを介してクラスター自体にアクセスすることはできません。クラスターにプロビジョニングするとき、あるいはクラスターにプロビジョニングした後、リモート デスクトップ アクセスをオンにできます。プロビジョニング時にリモート デスクトップを有効にする方法については、「[HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)」を参照してください。

**リモート デスクトップを有効にするには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[リモート デスクトップ]** をクリックします。
4. **[有効期限]**、**[リモート デスクトップのユーザー名]**、**[リモート デスクトップのパスワード]** に入力し、**[有効化]** をクリックします。

	![HDInsight によるリモート デスクトップ構成の有効化と無効化](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	[有効期限] の既定値は 1 週間です。
> [AZURE.NOTE]HDInsight .NET SDK を使用して、クラスターに対するリモート デスクトップを有効にすることもできます。HDInsight クライアント オブジェクトで、**EnableRdp** メソッドを次の形式で使用します: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**。同様に、クラスターに対するリモート デスクトップを無効にするには、**client.DisableRdp(clustername, location)** を使用します。これらのメソッドの詳細については、[HDInsight .NET SDK のリファレンス](http://go.microsoft.com/fwlink/?LinkId=529017)を参照してください。この方法は、Windows で実行されている HDInsight クラスターにのみ適用できます。

**RDP を使用してクラスターに接続するには**

1. [プレビュー ポータル][azure-portal]にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[リモート デスクトップ]** をクリックします。
4. **[接続]** クリックし、指示に従います。接続が無効の場合は、最初に有効する必要があります。必ずリモート デスクトップ ユーザーのユーザー名とパスワードを使用してください。クラスターのユーザー資格情報は使用できません。

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
* [HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)
* [プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight の概要](../hdinsight-get-started.md)
* [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "クラスター ランディング ページ"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop コマンド ライン"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!---HONumber=August15_HO8-->