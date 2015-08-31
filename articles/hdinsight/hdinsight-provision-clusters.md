<properties
   pageTitle="HDInsight での Hadoop クラスターのカスタム プロビジョニング | Microsoft Azure"
   	description="Azure プレビュー ポータル、Azure PowerShell、コマンド ライン、.NET SDK を使用して Azure HDInsight クラスターのカスタム プロビジョニングを行う方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/11/2015"
   ms.author="jgao"/>

# Provision Hadoop clusters in HDInsight (HDInsight での Hadoop クラスターのプロビジョニング)Provision Hadoop clusters in HDInsight

HDInsight クラスターのプロビジョニングを計画する方法について説明します。

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-portal-management-selector.md)]

* [HDInsight での Hadoop クラスターのプロビジョニング](hdinsight-provision-clusters-v1.md)

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [HDInsight での Hadoop クラスターのプロビジョニング](hdinsight-provision-clusters-v1.md)

**前提条件:**

この記事の手順を開始する前に、次の項目を用意する必要があります。

- Azure サブスクリプション。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。


## 基本的な構成オプション


- **クラスター名**

	クラスター名は、クラスターの識別に使用します。クラスター名は、次のガイドラインに従う必要があります。

	- このフィールドには、3 ～ 63 文字の文字列を指定する必要があります。
	- このフィールドには、文字、数字、ハイフンのみを含めることができます。

- **サブスクリプション名**

	HDInsight クラスターは、1 つの Azure サブスクリプションに関連付けられます。

- **オペレーティング システム**

	HDInsight クラスターは、次の 2 つのオペレーティング システムのいずれかでプロビジョニングできます。- **HDInsight on Windows (Windows Server 2012 R2 Datacenter)**: - **HDInsight on Linux (Ubuntu 12.04 LTS for Linux) (プレビュー)**: HDInsight には、Azure で Linux クラスターを構成するオプションが用意されています。Linux または Unix に詳しい場合や、Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、既存の Linux ベースの Hadoop ソリューションから移行することで Linux クラスターを構成します。詳細については、「[Get started with Hadoop on Linux in HDInsight (HDInsight の Linux での Hadoop の使用)](hdinsight-hadoop-linux-get-started.md)」をご覧ください。


- **HDInsight のバージョン**

	このクラスターに使用する HDInsight のバージョンを指定するために使用します。詳細については、「[HDInsight のバージョンと Hadoop コンポーネント](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)」をご覧ください。

- **クラスターの種類**と**クラスター サイズ (データ ノード)**

	HDInsight を使用すると、さまざまなクラスターの種類を、異なるデータ分析ワークロードにデプロイできるようになります。現在提供されているクラスターの種類は次のとおりです。

	- Hadoop クラスター: クエリおよび分析ワークロード用
	- HBase クラスター: NoSQL ワークロード用
	- Storm クラスター: リアルタイム イベント処理ワークロード用
	- Spark クラスター (プレビュー): メモリ内処理、対話型クエリ、ストリーミング、機械学習の各ワークロード用

	![HDInsight クラスター](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE]*Azure HDInsight クラスター*は *HDInsight の Hadoop クラスター* または *HDInsight クラスター*とも呼ばれます。また、*Hadoop クラスター*と同じ意味で使用される場合もあります。これらはすべて、Microsoft Azure 環境でホストされている Hadoop クラスターを指します。

	特定のクラスターの種類には、各種ノードのさまざまなロールがあります。これにより、ワークロードの詳細に適した特定のロールでノードのサイズを設定できます。たとえば、Hadoop クラスターで実行する分析がメモリ消費型である場合、大量のメモリを備えたワーカー ノードをプロビジョニングできます。

	![HDInsight Hadoop クラスターのロール](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	HDInsight の Hadoop クラスターは 2 つのロールでデプロイされます。

	- ヘッド ノード (2 ノード)
	- データ ノード (1 つ以上のノード)

	![HDInsight Hadoop クラスターのロール](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	HDInsight 用 HBase クラスターは 3 つのロールでデプロイされます。- ヘッド サーバー (2 ノード) - リージョン サーバー (1 ノード以上) - Master/Zookeeper ノード (3 ノード)

	![HDInsight Hadoop クラスターのロール](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	HDInsight 用 Storm クラスターは 3 つのロールでデプロイされます。Nimbus ノード (2 ノード) - Supervisor サーバー (1 ノード以上) - Zookeeper ノード (3 ノード)


	![HDInsight Hadoop クラスターのロール](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	HDInsight 用 Spark クラスターは 3 つのロールでデプロイされます。ヘッド ノード (2 ノード) - ワーカー ノード (1 ノード以上) - Zookeeper ノード (3 ノード) (A1 Zookeeper は無料)

	クラスターの有効期間中、これらのノードの使用量に対して課金されます。課金は、クラスターが作成された時点で開始され、クラスターが削除されると終了します (クラスターを割り当て解除したり、保留にしたりすることはできません)。クラスター サイズはクラスターの価格に影響します。学習目的の場合、データ ノードを 1 つ使用することをお勧めします。HDInsight の価格の詳細については、「[HDInsight の価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」をご覧ください。


	>[AZURE.NOTE]クラスター サイズの制限は、Azure サブスクリプションによって異なります。制限値を上げるには、課金サポートにお問い合わせください。

- **リージョン/仮想ネットワーク (場所)**

	![Azure リージョン](./media/hdinsight-provision-clusters/Azure.regions.png)

	サポートされているリージョンの一覧については、「[HDInsight の価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」の **[リージョン]** ボックスの一覧をクリックしてください。

- **ノード サイズ**

	![HDInsight VM ノードのサイズ](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	ノードの VM サイズを選択します。詳細については、「[クラウド サービスのサイズ](cloud-services-sizes-specs.md)」をご覧ください。

	VM の選択に基づき、料金が異なる場合があります。HDInsight は、クラスター ノードにすべて Standard レベルの VM を使用します。VM サイズに応じた料金の詳細については、「<a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 料金</a>」をご覧ください。


- **HDInsight ユーザー**

	HDInsight クラスターでは、プロビジョニング時に次の 2 つのユーザー アカウントを構成できます。

	- HTTP ユーザー。既定のユーザー名は、Azure プレビュー ポータルの基本構成を使用する admin です。
	- RDP ユーザー (Windows クラスター): RDP を使用してクラスターに接続する際に使用します。アカウントの作成時に、当日から 90 日以内の有効期限を設定する必要があります。
	- SSH ユーザー (Linux クラスター): SSH を使用してクラスターに接続する際に使用します。「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)」の手順に従ってクラスターを作成した後に、追加の SSH ユーザー アカウントを作成できます。



- **Azure ストレージ アカウント**

	元の HDFS では、クラスターの多数のローカル ディスクを使用します。HDInsight では、データ ストレージとして Azure BLOB ストレージを使用します。Azure BLOB ストレージは、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。Hadoop 分散ファイル システム (HDFS) のインターフェイスを利用して、HDInsight のすべてのコンポーネントが BLOB ストレージの構造化データまたは非構造化データを直接操作できます。BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。

	構成時に、Azure ストレージ アカウントと、その Azure ストレージ アカウントの Azure BLOB ストレージ コンテナーを指定する必要があります。一部のプロビジョニング プロセスには、事前に作成された Azure ストレージ アカウントと BLOB ストレージ コンテナーが必要です。BLOB ストレージ コンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターがアクセス可能なその他の Azure Storage アカウント (リンクされたストレージ) を指定できます。また、クラスターは、完全なパブリック読み取りアクセスまたは BLOB だけを対象とするパブリック読み取りアクセスで構成された BLOB コンテナーにアクセスすることもできます。アクセスの制限の詳細については、「[Microsoft Azure ストレージ リソースへのアクセスの管理](storage-manage-access-to-resources.md)」をご覧ください。

	![HDInsight ストレージ](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]次の図に示すように、BLOB ストレージ コンテナーには、一連の BLOB がまとめられています。

	![Azure BLOB ストレージ](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]1 つの Blob ストレージ コンテナーを複数のクラスターで共有しないでください。これはサポートされていません。

	従属的な BLOB ストアの使用の詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-use-blob-storage.md)」をご覧ください。

- **Hive/Oozie メタストア**

	メタストアには、Hive テーブル、パーティション、スキーマ、列などについての Hive と Oozie メタデータが格納されます。メタストアを使用すると Hive と Oozie メタデータを保持できるため、新しいクラスターをプロビジョニングするときに、Hive テーブルまたは Oozie ジョブを再作成する必要はありません。既定では、Hive は組み込みの Azure SQL Database を使用してこの情報を格納します。組み込みデータベースは、クラスターが削除されるとメタデータを保持できません。たとえば、Hive メタストアを使用してプロビジョニングされたクラスターがあるとします。作成済みの Hive テーブルがいくつかあります。クラスターを削除した後に、同じ Hive メタストアを使用してクラスターを再作成すると、元のクラスターで作成していた Hive テーブルがあることがわかります。

## 高度な構成オプション

>[AZURE.NOTE]現在、このセクションは、Windows ベースの HDInsight クラスターにのみ適用されます。

### HDInsight クラスターのカスタマイズを使用したクラスターのカスタマイズ

構成ファイルを構成することが必要な場合があります。次のような構成ファイルがあります。

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

再イメージ化により、クラスターは変更を保持できません。詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)」を参照してください。クラスターの有効期間中に変更を保持するには、プロビジョニング プロセスで HDInsight クラスターのカスタマイズを使用します。

Hive 構成をカスタマイズする Azure PowerShell スクリプトの例を次に示します。

	# hive-site.xml configuration
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60

	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues

	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

他の構成ファイルのカスタマイズのサンプルを次に示します。

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

詳細については、Azim Uddin のブログ「[Customizing HDInsight Cluster provisioning (HDInsight クラスターのプロビジョニングのカスタマイズ)](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx)」をご覧ください。




### スクリプト アクションを使用したクラスターのカスタマイズ

追加コンポーネントをインストールするか、プロビジョニング中にスクリプトを使用してクラスターの構成をカスタマイズできます。このようなスクリプトは、**スクリプト操作** を使用して実行します。これは Azure プレビュー ポータル、HDInsight Windows PowerShell コマンドレット、HDInsight .NET SDK で使用できる構成オプションです。詳細については、「[Script Action を使って HDInsight をカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。


### Azure Virtual Network の使用

[Azure 仮想ネットワーク](http://azure.microsoft.com/documentation/services/virtual-network/)によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

	サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

仮想ネットワークの機能、利点の詳細については、「[Azure 仮想ネットワークの概要](http://msdn.microsoft.com/library/azure/jj156007.aspx)」を参照してください。

> [AZURE.NOTE]HDInsight クラスターをプロビジョニングする前に、Azure 仮想ネットワークを作成する必要があります。詳細については、「[仮想ネットワークに Hadoop クラスターをプロビジョニングする](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network)」を参照してください。
>
> Azure HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在取り扱っていません。既存の Azure Virtual Network が場所ベースかどうかを確認するには、Azure PowerShell コマンドレットの Get-AzureVNetConfig を使用します。Virtual Network が場所ベースでない場合、次のオプションがあります。
>
> - 既存の Virtual Network 構成をエクスポートし、新しい Virtual Network を作成します。新しい Virtual Network はすべて、既定で場所ベースになります。
> - 場所ベースの Virtual Network に移行します。「[Migrating Existing Services to Regional Scope (リージョン スコープへの既存のサービスの移行)](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)」をご覧ください。
>
> 1 つのクラスターには単一のサブネットを指定することを強くお勧めします。

## プロビジョニング ツール

- Azure プレビュー ポータル
- Azure PowerShell
- HDInsight .NET SDK

### プレビュー ポータルを使用する

各フィールドについては、「基本的な構成オプション」および「高度な構成オプション」をご覧ください。

**HDInsight クラスターを作成するには**

1. [Azure プレビュー ポータル][azure-preview-portal]にサインインします。
2. **[新規]**、**[データ分析]**、**[HDInsight]** の順にクリックします。

    ![Azure プレビュー ポータルでの新しいクラスターの作成](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Azure プレビュー ポータルでの新しいクラスターの作成")

3. 次の値を入力または選択します。
  - **クラスター名**: クラスターの名前を入力します。その名前を使用できる場合は、クラスター名の横に緑色のチェック マークが表示されます。
  - **クラスターの種類**: **[Hadoop]** を選択します。
  - **クラスターのオペレーティング システム**: **[Windows Server 2012 R2 Datacenter]** を選択します。
  - **サブスクリプション**: このクラスターをプロビジョニングするために使用する Azure サブスクリプションを選択します。
  - **リソース グループ**: 既存のリソース グループを選択するか、新しいリソース グループを作成します。このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。
  - **資格情報**: Hadoop ユーザー (HTTP ユーザー) のユーザー名とパスワードを構成します。クラスターのリモート デスクトップを有効にする場合は、リモート デスクトップ ユーザーのユーザー名とパスワード、アカウントの有効期限を構成する必要があります。下部にある **[選択]** をクリックして、変更を保存します。

	![クラスターの資格情報の指定](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "クラスターの資格情報の指定")
  - **データ ソース**: クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを新しく作成するか、既存の Azure ストレージ アカウントを選択します。

	   ![[データ ソース] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "データ ソース構成の指定")

  	- **選択方法**: すべてのサブスクリプションのストレージ アカウントを参照できるようにする場合は、**[すべてのサブスクリプションから]** を設定します。既存のストレージ アカウントの **[ストレージ名]** と **[アクセス キー]** を入力する場合は、**[アクセス キー]** を設定します。
  	- **ストレージ アカウントの選択/新規作成**: クラスターに関連付ける既存のストレージ アカウントを参照して選択する場合は **[ストレージ アカウントの選択]** をクリックします。または、**[新規作成]** をクリックして、新しいストレージ アカウントを作成します。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。
    - **既定のコンテナーの選択**。 これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。
  	- **場所**: ストレージ アカウントが存在するリージョン、またはその中にストレージ アカウントが作成されるリージョン。この場所は、クラスターの場所を決定します。クラスターとその既定のストレージ アカウントは、同じデータセンター内に配置されている必要があります。

  - **ノード価格レベル**: クラスターで必要なワーカー ノードの数を設定します。クラスターの推定コストがブレード内に表示されます。

	![[ノード価格レベル] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "クラスター ノード数の指定")

  - **[オプションの構成]** をクリックし、クラスターのバージョンを選択し、その他のオプションの設定を構成します。その他のオプションには、**仮想ネットワーク**に参加すること、Hive と Oozie 用のデータを保持する**外部メタストア** を設定すること、Script Action を使用してカスタム コンポーネントをインストールするようにクラスターをカスタマイズすること、クラスターで追加のストレージ アカウントを使用することなどがあります。

  	- **[HDInsight のバージョン]**: クラスターで使用するバージョンを選択します。詳細については、「[HDInsight クラスターのバージョン](hdinsight-component-versioning.md)」をご覧ください。
  	- **仮想ネットワーク**: クラスターを仮想ネットワークに配置する場合は、Azure 仮想ネットワークとサブネットを選択します。  

		![[仮想ネットワーク] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "仮想ネットワークの詳細の指定")

    >[AZURE.NOTE]Windows ベースの HDInsight クラスターは、クラシック仮想ネットワークにのみ配置できます。

  	- **[外部メタストア]**: クラスターに関連付けられた Hive と Oozie のメタデータを保存するための SQL データベースを指定します。

		![[カスタム メタストア] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "外部メタストアの指定")

		**[Hive メタデータで既存の SQL DB を使用する]** で、**[はい]** をクリックし、SQL データベースを選択し、データベースのユーザー名/パスワードを指定します。**[Oozie メタデータで既存の SQL DB を使用する]** 場合は、これらの手順を繰り返します。**[オプションの構成]** ブレードに戻るまで **[選択]** をクリックします。

		>[AZURE.NOTE]メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

  	- クラスターを作成するときに、カスタム スクリプトを使用してクラスターをカスタマイズする場合は、**[Script Actions]** をクリックします。スクリプト アクションの詳細については、「[Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。[Script Action] ブレードで、次の画面キャプチャに示すように、詳細を指定します。

		![[Script ction] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "スクリプト アクションの指定")

    - **[Azure ストレージ キー]**: クラスターに関連付ける追加のストレージ アカウントを指定します。 **[Azure ストレージ キー]** ブレードで、**[ストレージ キーの追加]** をクリックし、既存のストレージ アカウントを選択するか新しいアカウントを作成します。

		![[追加ストレージ] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "追加ストレージ アカウントの指定")

4. **[作成]** をクリックします。**[スタート画面にピン留め]**を選択すると、プレビュー ポータルのスタート画面にクラスターのタイルが追加されます。アイコンはクラスターがプロビジョニング中であることを示し、プロビジョニングが完了すると、[HDInsight] アイコンを表示するように変化します。

	| プロビジョニング中 | プロビジョニング完了 |
	| ------------------ | --------------------- |
	| ![スタート画面のプロビジョニング中インジケーター](./media/hdinsight-provision-clusters/provisioning.png) | ![プロビジョニングされたクラスターのタイル](./media/hdinsight-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]クラスターが作成されるまで、通常は約 15 分かかります。プロビジョニング プロセスをチェックするには、スタート画面のタイルまたはページの左側の **[通知]** エントリを使用します。

5. プロビジョニングが完了したら、スタート画面でクラスター用のタイルをクリックして、クラスター ブレードを起動します。クラスター ブレードには、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などのクラスターに関する基本情報が表示されます。

	![クラスター ブレード](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "クラスターのプロパティ")

	次の説明を参照して、このブレードの上部と **[要点]** セクションにあるアイコンを理解してください。

	* **設定**と**すべての設定**: クラスター用の **[設定]** ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。

	* **ダッシュボード**、**クラスター ダッシュボード**、**URL**: これらはいつでもクラスター ダッシュボードにアクセスする方法です。クラスター ダッシュボードは、クラスターに関するジョブを実行するための Web ポータルです。

	* **リモート デスクトップ**: クラスター ノードのリモート デスクトップを有効/無効にすることができます。

	* **クラスターのスケーリング**: このクラスターの worker ノードの数を変更できます。

	* **削除**: HDInsight クラスターを削除します。

	* **クイック スタート** (![雲と雷のアイコン = クイック スタート](./media/hdinsight-provision-clusters/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。

	* **ユーザー** (![ユーザー アイコン](./media/hdinsight-provision-clusters/users.png)): このクラスターの_ポータル管理_に対する権限を、Azure サブスクリプションの他のユーザーに設定できます。

		> [AZURE.IMPORTANT]これは、プレビュー ポータルでのこのクラスターへのアクセスと権限のみに影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。

	* **タグ** (![タグ アイコン](./media/hdinsight-provision-clusters/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスで共通の値を使用できます。




### Azure PowerShell の使用
Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。このセクションでは、Azure PowerShell を使用して HDInsight クラスターをプロビジョニングする方法について説明します。ワークステーションを構成して HDInsight Windows Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../install-configure-powershell.md)」をご覧ください。HDInsight で Azure PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)」をご覧ください。HDInsight Windows PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn858087.aspx)」をご覧ください。


Azure PowerShell を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- Azure リソース グループの作成
- Azure のストレージ アカウントの作成
- Azure BLOB コンテナーの作成
- HDInsight クラスターの作成


		# Use the new Azure Resource Manager mode
		Switch-AzureMode AzureResourceManager

		###########################################
		# Create required items, if none exist
		###########################################

		# Sign in
		Add-AzureAccount

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Register your subscription to use HDInsight
		Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

		###########################################
		# Create an HDInsight Cluster
		###########################################

		# Skip these variables if you just created them
		$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
		$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
		$containerName = "<ContainerName>"              # Provide the container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

		# Set these variables
		$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
		$credentials = Get-Credential

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop


	![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)


### HDInsight .NET SDK の使用
HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。Visual Studio コンソール アプリケーションを作成し、クラスターを作成するためのコードを貼り付けるには、次の手順に従います。

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. **[OK]** をクリックしてプロジェクトを作成します。

5. **[ツール]** メニューの **[Nuget パッケージ マネージャー]** をクリックし、**[ソリューションの NuGet パッケージの管理]** をクリックします。ダイアログ ボックス内の [検索] テキスト ボックスで、「**HDInsight**」を検索します。表示される結果から、以下をインストールします。

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	「Azure 認証」を検索し、表示される結果から **Microsoft.Azure.Common.Authentication** をインストールします。

6. ソリューション エクスプ ローラーで、 **Program.cs** をダブルクリックして開き、次のコードを貼り付け、変数の値を指定します。


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;


		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//should be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster provisioning");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
		        }

		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                Location = NewClusterLocation,
		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. **F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。Azure アカウントの資格情報の入力も求められます。HDInsight クラスターの作成は数分かかる場合があります。


##<a id="nextsteps"></a>次のステップ
この記事では、HDInsight クラスターをプロビジョニングする方法をいくつか説明しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要](hdinsight-get-started.md) - HDInsight クラスターの使用方法について説明しています。
* [HDInsight の Hadoop での Sqoop の使用](hdinsight-use-sqoop.md) - HDInsight と SQL Database または SQL Server 間でデータをコピーする方法について説明しています。
* [Azure PowerShell を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-powershell.md) - HDInsight で Azure PowerShell を使用する方法について説明しています。
* [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md) - プログラムを使用して HDInsight にジョブを送信する方法について説明しています。
* [HDInsight][hdinsight-sdk-documentation] - HDInsight SDK について説明しています。


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com

<!---HONumber=August15_HO8-->