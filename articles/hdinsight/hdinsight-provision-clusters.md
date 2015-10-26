<properties
   pageTitle="HDInsight で Hadoop クラスターを作成する | Microsoft Azure"
   	description="Azure プレビュー ポータル、Azure PowerShell、コマンド ライン、.NET SDK を使用して Azure HDInsight のクラスターを作成する方法について説明します。"
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
   ms.date="09/29/2015"
   ms.author="jgao"/>

# HDInsight で Hadoop クラスターを作成する

HDInsight クラスターの作成を計画する方法について説明します。

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-portal-management-selector.md)]

* [HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters-v1.md)

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters-v1.md)

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

- **リソース グループ名**

	アプリケーションは通常、Web アプリケーション、データベース、データベース サーバー、ストレージ、サード パーティのサービスなどの、複数のコンポーネントで構成されます。Azure リソース マネージャー (ARM) を使用すると、アプリケーション内の複数のリソースを 1 つのグループ (Azure リソース グループと呼ばれます) と見なして作業できます。アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。デプロイメントにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。グループ全体のロールアップ コストを表示すると、組織の課金ついて明確に把握できます。詳細については、「[Azure リソース マネージャーの概要](resource-group-overview.md)」を参照してください。	
- **オペレーティング システム**

	HDInsight クラスターは、次の 2 つのオペレーティング システムのいずれかで作成できます。- **Windows 上の HDInsight (Windows Server 2012 R2 Datacenter)**。- **Linux 上の HDInsight (Ubuntu 12.04 LTS for Linux) (プレビュー)**。HDInsight には、Azure で Linux クラスターを構成するオプションが用意されています。Linux または Unix に詳しい場合や、Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、既存の Linux ベースの Hadoop ソリューションから移行することで Linux クラスターを構成します。詳細については、「[Get started with Hadoop on Linux in HDInsight (HDInsight の Linux での Hadoop の使用)](hdinsight-hadoop-linux-get-started.md)」をご覧ください。


- **HDInsight のバージョン**

	このクラスターに使用する HDInsight のバージョンを指定するために使用します。詳細については、[HDInsight での Hadoop クラスターのバージョンとコンポーネント](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)に関するページを参照してください。

- **クラスターの種類**と**クラスター サイズ (データ ノード)**

	HDInsight を使用すると、さまざまなクラスターの種類を、異なるデータ分析ワークロードにデプロイできるようになります。現在提供されているクラスターの種類は次のとおりです。

	- Hadoop クラスター: クエリおよび分析ワークロード用
	- HBase クラスター: NoSQL ワークロード用
	- Storm クラスター: リアルタイム イベント処理ワークロード用
	- Spark クラスター (プレビュー): メモリ内処理、対話型クエリ、ストリーミング、機械学習の各ワークロード用

	![HDInsight clusters](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE]"Azure HDInsight クラスター" は、"HDInsight の Hadoop クラスター" または "HDInsight クラスター" とも呼ばれます。また、"Hadoop クラスター" と同じ意味で使用される場合もあります。これらはすべて、Microsoft Azure 環境でホストされている Hadoop クラスターを指します。

	特定のクラスターの種類には、各種ノードのさまざまなロールがあります。これにより、ワークロードの詳細に適した特定のロールでノードのサイズを設定できます。たとえば、Hadoop クラスターで実行する分析がメモリ消費型である場合、大量のメモリを備えたワーカー ノードを作成できます。

	![HDInsight Hadoop cluster roles](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	HDInsight の Hadoop クラスターは 2 つのロールでデプロイされます。

	- ヘッド ノード (2 ノード)
	- データ ノード (1 つ以上のノード)

	![HDInsight Hadoop cluster roles](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	HDInsight 用 HBase クラスターは 3 つのロールでデプロイされます。- ヘッド サーバー (2 ノード) - リージョン サーバー (1 ノード以上) - Master/Zookeeper ノード (3 ノード)

	![HDInsight Hadoop cluster roles](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	HDInsight 用 Storm クラスターは 3 つのロールでデプロイされます。Nimbus ノード (2 ノード) - Supervisor サーバー (1 ノード以上) - Zookeeper ノード (3 ノード)


	![HDInsight Hadoop cluster roles](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	HDInsight 用 Spark クラスターは 3 つのロールでデプロイされます。ヘッド ノード (2 ノード) - worker ノード (1 ノード以上) - Zookeeper ノード (3 ノード) (A1 Zookeeper は無料)

	クラスターの有効期間中、これらのノードの使用量に対して課金されます。課金は、クラスターが作成された時点で開始され、クラスターが削除されると終了します (クラスターを割り当て解除したり、保留にしたりすることはできません)。クラスター サイズはクラスターの価格に影響します。学習目的の場合、データ ノードを 1 つ使用することをお勧めします。HDInsight の価格の詳細については、「[HDInsight 価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」をご覧ください。


	>[AZURE.NOTE]クラスター サイズの制限は、Azure サブスクリプションによって異なります。制限値を上げるには、課金サポートにお問い合わせください。

- **リージョン/仮想ネットワーク (場所)**

	![Azure regions](./media/hdinsight-provision-clusters/Azure.regions.png)

	サポートされているリージョンの一覧については、「[HDInsight の価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」の **[リージョン]** ボックスの一覧をクリックしてください。

- **ノード サイズ**

	![hdinsight vm node sizes](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	ノードの VM サイズを選択します。詳細については、「[Cloud Services のサイズ](cloud-services-sizes-specs.md)」をご覧ください。

	VM の選択に基づき、価格が異なる場合があります。HDInsight は、クラスター ノードにすべて Standard レベルの VM を使用します。VM サイズに応じた価格の詳細については、「<a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight の価格</a>」をご覧ください。


- **HDInsight ユーザー**

	HDInsight クラスターでは、プロビジョニング時に次の 2 つのユーザー アカウントを構成できます。

	- HTTP ユーザー。既定のユーザー名は、Azure プレビュー ポータルの基本構成を使用する admin です。
	- RDP ユーザー (Windows クラスター): RDP を使用してクラスターに接続する際に使用します。アカウントの作成時に、当日から 90 日以内の有効期限を設定する必要があります。
	- SSH ユーザー (Linux クラスター): SSH を使用してクラスターに接続する際に使用します。「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)」の手順に従ってクラスターを作成した後に、追加の SSH ユーザー アカウントを作成できます。



- **Azure ストレージ アカウント**

	元の HDFS では、クラスターの多数のローカル ディスクを使用します。HDInsight では、データ ストレージとして Azure BLOB ストレージを使用します。Azure BLOB ストレージは、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。Hadoop 分散ファイル システム (HDFS) のインターフェイスを利用して、HDInsight のすべてのコンポーネントが BLOB ストレージの構造化データまたは非構造化データを直接操作できます。BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。

	構成時に、Azure ストレージ アカウントと、その Azure ストレージ アカウントの Azure BLOB ストレージ コンテナーを指定する必要があります。一部の作成プロセスには、事前に作成された Azure ストレージ アカウントと BLOB ストレージ コンテナーが必要です。BLOB ストレージ コンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターがアクセス可能なその他の Azure Storage アカウント (リンクされたストレージ) を指定できます。また、クラスターは、完全なパブリック読み取りアクセスまたは BLOB だけを対象とするパブリック読み取りアクセスで構成された BLOB コンテナーにアクセスすることもできます。アクセスの制限の詳細については、「[Azure ストレージ リソースへのアクセスの管理](storage-manage-access-to-resources.md)」をご覧ください。

	![HDInsight storage](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]次の図に示すように、BLOB ストレージ コンテナーには、一連の BLOB がまとめられています。

	![Azure blob storage](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]1 つの Blob ストレージ コンテナーを複数のクラスターで共有しないでください。これはサポートされていません。

	従属的な BLOB ストアの使用の詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-use-blob-storage.md)」をご覧ください。

- **Hive/Oozie メタストア**

	メタストアには、Hive テーブル、パーティション、スキーマ、列などについての Hive と Oozie メタデータが格納されます。メタストアを使用すると Hive と Oozie メタデータを保持できるため、新しいクラスターを作成するときに、Hive テーブルまたは Oozie ジョブを再作成する必要はありません。既定では、Hive は組み込みの Azure SQL Database を使用してこの情報を格納します。組み込みデータベースは、クラスターが削除されるとメタデータを保持できません。たとえば、Hive メタストアを使用して作成されたクラスターがあるとします。作成済みの Hive テーブルがいくつかあります。クラスターを削除した後に、同じ Hive メタストアを使用してクラスターを再作成すると、元のクラスターで作成していた Hive テーブルがあることがわかります。

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

再イメージ化により、クラスターは変更を保持できません。詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)」を参照してください。クラスターの有効期間中に変更を保持するには、作成プロセスで HDInsight クラスターのカスタマイズを使用します。

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

詳細については、[HDInsight クラスターの作成のカスタマイズ](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx)に関する Azim Uddin のブログをご覧ください。




### スクリプト アクションを使用したクラスターのカスタマイズ

追加コンポーネントをインストールするか、作成中にスクリプトを使用してクラスターの構成をカスタマイズできます。このようなスクリプトは、**スクリプト アクション**を使用して実行します。これはプレビュー ポータル、HDInsight Windows PowerShell コマンドレット、HDInsight .NET SDK で使用できる構成オプションです。詳細については、「[Script Action を使って HDInsight をカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。


### Azure Virtual Network の使用

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![クラウドのみの構成の図](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

	サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。

	![サイト間構成の図](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。

	![ポイント対サイト構成の図](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Virtual Network の機能、利点の詳細については、「[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)」を参照してください。

> [AZURE.NOTE]HDInsight クラスターをプロビジョニングする前に、Azure 仮想ネットワークを作成する必要があります。詳細については、[仮想ネットワークへの Hadoop クラスターの作成](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network)に関するページをご覧ください。
>
> Azure HDInsight は場所ベースの Virtual Networks のみをサポートし、アフィニティ グループ ベースの Virtual Networks は現在取り扱っていません。既存の Azure Virtual Network が場所ベースかどうかを確認するには、Azure PowerShell コマンドレットの Get-AzureVNetConfig を使用します。Virtual Network が場所ベースでない場合、次のオプションがあります。
>
> - 既存の Virtual Network 構成をエクスポートし、新しい Virtual Network を作成します。新しい Virtual Network はすべて、既定で場所ベースになります。
> - 場所ベースの Virtual Network に移行します。[リージョン スコープへの既存のサービスの移行](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)に関するページをご参照ください。
>
> 1 つのクラスターには単一のサブネットを指定することを強くお勧めします。

## プレビュー ポータルを使用した作成

各フィールドについては、「基本的な構成オプション」および「高度な構成オプション」をご覧ください。

**HDInsight クラスターを作成するには**

1. [Azure プレビュー ポータル][azure-preview-portal]にサインインします。
2. **[新規]**、**[データ分析]**、**[HDInsight]** の順にクリックします。

    ![Azure プレビュー ポータルでの新しいクラスターの作成](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Azure プレビュー ポータルでの新しいクラスターの作成")

3. 次の値を入力または選択します。

  * **[クラスター名]**: クラスターの名前を入力します。その名前を使用できる場合は、クラスター名の横に緑色のチェック マークが表示されます。
  * **[クラスターの種類]**: **[Hadoop]** を選択します。
  * **[クラスターのオペレーティング システム]**: **[Windows Server 2012 R2 Datacenter]** を選択します。
  * **[サブスクリプション]**: このクラスターを作成するために使用する Azure サブスクリプションを選択します。
  * **[リソース グループ]**: 既存のリソース グループを選択するか、新しいリソース グループを作成します。このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。
  * **[資格情報]**: Hadoop ユーザー (HTTP ユーザー) のユーザー名とパスワードを構成します。クラスターのリモート デスクトップを有効にする場合は、リモート デスクトップ ユーザーのユーザー名とパスワード、アカウントの有効期限を構成する必要があります。下部にある **[選択]** をクリックして、変更を保存します。

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **[データ ソース]**: クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを新しく作成するか、既存の Azure ストレージ アカウントを選択します。

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **選択方法**: すべてのサブスクリプションのストレージ アカウントを参照できるようにする場合は、**[すべてのサブスクリプションから]** を設定します。 既存のストレージ アカウントの **[ストレージ名]** と **[アクセス キー]** を入力する場合は、**[アクセス キー]** を設定します。
  		* **ストレージ アカウントの選択/新規作成**: クラスターに関連付ける既存のストレージ アカウントを参照して選択する場合は **[ストレージ アカウントの選択]** をクリックします。 または、**[新規作成]** をクリックして、新しいストレージ アカウントを作成します。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。
  		* **[既定のコンテナーの選択]**: これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。 任意の名前を入力できますが、特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。
  		* **場所**: ストレージ アカウントが存在するリージョン、またはその中にストレージ アカウントが作成されるリージョン。 この場所は、クラスターの場所を決定します。  クラスターとその既定のストレージ アカウントは、同じデータセンター内に配置されている必要があります。
  	
  * **[ノード価格レベル]**: クラスターで必要なworker ノードの数を設定します。クラスターの推定コストがブレード内に表示されます。
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **[オプションの構成]** をクリックし、クラスターのバージョンを選択して、その他のオプションの設定を構成します。その他のオプションには、**Virtual Network** への参加、Hive と Oozie 用のデータを保持する**外部メタストア**の設定、スクリプト アクションを使用してカスタム コンポーネントをインストールするようにクラスターをカスタマイズすること、クラスターでの追加のストレージ アカウントの使用などがあります。

  		* **[HDInsight のバージョン]**: クラスターで使用するバージョンを選択します。 詳細については、[HDInsight クラスターのバージョン](hdinsight-component-versioning.md)をご覧ください。
  		* **Virtual Network**: クラスターを仮想ネットワークに配置する場合は、Azure Virtual Network とサブネットを選択します。  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] Windows ベースの HDInsight クラスターは、クラシック仮想ネットワークにのみ配置できます。
  

  		
		* **[外部メタストア]**: クラスターに関連付けられた Hive と Oozie のメタデータを保存するための SQL Database を指定します。
 

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			**[Hive メタデータで既存の SQL DB を使用する]** で、**[はい]** をクリックし、SQL データベースを選択し、データベースのユーザー名/パスワードを指定します。 **[Oozie メタデータで既存の SQL DB を使用する]** 場合は、これらの手順を繰り返します。 **[オプションの構成]** ブレードに戻るまで **[選択]** をクリックします。


			>[AZURE.NOTE] メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。 Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。 Tこれは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。
		
  		* クラスターを作成するときに、カスタム スクリプトを使用してクラスターをカスタマイズする場合は、**[スクリプト アクション]** をクリックします。 スクリプト アクションの詳細については、[Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md) をご覧ください。 [Script Action] ブレードで、次の画面キャプチャに示すように、詳細を指定します。
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **[Azure ストレージ キー]**: クラスターに関連付ける追加のストレージ アカウントを指定します。 **[Azure ストレージ キー]** ブレードで、**[ストレージ キーの追加]** をクリックし、既存のストレージ アカウントを選択するか新しいアカウントを作成します。
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. **[作成]** をクリックします。**[スタート画面にピン留めする]** を選択すると、プレビュー ポータルのスタート画面にクラスターのタイルが追加されます。アイコンはクラスターが作成中であることを示し、作成が完了すると、[HDInsight] アイコンを表示するように変化します。


	| 作成中 | 作成完了 |
	| ------------------ | --------------------- |
	| ![スタート画面のプロビジョニング中インジケーター](./media/hdinsight-provision-clusters/provisioning.png) | ![プロビジョニングされたクラスターのタイル](./media/hdinsight-provision-clusters/provisioned.png) |


	
	> [AZURE.NOTE]クラスターが作成されるまで、通常は約 15 分かかります。プロビジョニング プロセスをチェックするには、スタート画面のタイルまたはページの左側の **[通知]** エントリを使用します。
	

5. 作成が完了したら、スタート画面でクラスター用のタイルをクリックして、クラスター ブレードを起動します。クラスター ブレードには、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などのクラスターに関する基本情報が表示されます。


	![クラスター ブレード](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "クラスターのプロパティ")


	次の説明を参照して、このブレードの上部と **[要点]** セクションにあるアイコンについて理解してください。


	* **[設定]** と **[すべての設定]**: クラスター用の **[設定]** ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。
	* **[ダッシュボード]**、**[クラスター ダッシュボード]**、**[URL]**: これらはいつでもクラスター ダッシュボードにアクセスする方法です。クラスター ダッシュボードは、クラスターに関するジョブを実行するための Web ポータルです。
	* **[リモート デスクトップ]**: クラスター ノードのリモート デスクトップを有効/無効にすることができます。
	* **[クラスターのスケーリング]**: このクラスターの worker ノードの数を変更できます。
	* **[削除]**: HDInsight クラスターを削除します。
	* **[クイック スタート]** (![雲と雷のアイコン = クイック スタート](./media/hdinsight-provision-clusters/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。
	* **[ユーザー]** (![ユーザー アイコン](./media/hdinsight-provision-clusters/users.png)): このクラスターの_ポータル管理_に対する権限を、Azure サブスクリプションの他のユーザーに対して設定できます。
	

		> [AZURE.IMPORTANT]これは、プレビュー ポータルでのこのクラスターへのアクセスと権限のみに影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。
		
	* **[タグ]** (![タグ アイコン](./media/hdinsight-provision-clusters/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスで共通の値を使用できます。


## Azure リソース マネージャーのテンプレートを使用した作成

Azure リソース マネージャー (ARM) のテンプレートを使用すると、クラスターのデプロイと再デプロイが容易になります。次の手順では、北ヨーロッパのデータ センターにある Linux オペレーティング システム上に、4 つの worker ノードを持つ Hadoop クラスターを作成します。

**ARM のテンプレートを使用してクラスターをデプロイするには**

1. 付録 A の JSON ファイルをご使用のワークステーションに保存します。
2. 必要な場合は、パラメーターを設定します。
3. 次の PowerShell スクリプトを使用して、テンプレートを実行します。

		$resourceGroupName = "<ResourceGroupName>"
		$Location = "<ResourceGroupLocation>"
		
		$armDeploymentName = "<ARMDeploymentName>"
		$clusterName = "<ClusterName>"
		$clusterStorageAccountName = "<DefaultStorageAccountName>"
		
		# Connect to Azure
		Switch-AzureMode -Name AzureResourceManager
		Add-AzureAccount
		
		# Create a resource group
		New-AzureResourceGroup -Name $resourceGroupName -Location $Location
		
		# Create cluster and the dependent storage accounge
		$parameters = @{clusterName="$clusterName";clusterStorageAccountName="$clusterStorageAccountName"}
		
		New-AzureResourceGroupDeployment `
		    -Name $armDeploymentName `
		    -ResourceGroupName $resourceGroupName `
		    -TemplateFile E:\Tutorials\HDIARMTemplates\ARMTemplate-create-hadoop-cluster-with-storage.json `
		    -TemplateParameterObject $parameters
		
		# List cluster
		Get-AzureHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName

他の方法を使用した ARM テンプレートのデプロイについては、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。


## Azure PowerShell を使用した作成
Azure PowerShell は、Azure のワークロードのデプロイメントと管理を制御し自動化するために使用できる強力なスクリプティング環境です。このセクションでは、Azure PowerShell を使用して HDInsight クラスターをプロビジョニングする方法について説明します。ワークステーションを構成して HDInsight Windows Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../install-configure-powershell.md)」をご覧ください。HDInsight で Azure PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)」をご覧ください。HDInsight Windows PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn858087.aspx)」をご覧ください。


Azure PowerShell を使用して HDInsight クラスターを作成するには、以下の手順が必要です。

- Azure リソース グループの作成
- Azure のストレージ アカウントの作成
- Azure BLOB コンテナーの作成
- HDInsight クラスターの作成


		# Sign in
		Add-AzureAccount

		###########################################
		# Create required items, if none exist
		###########################################

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
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
		New-AzureHDInsightCluster -ResourceGroupName $resourceGroupName `
									-ClusterName $clusterName `
									-Location $location `
									-ClusterSizeInNodes $clusterNodes `
									-ClusterType Hadoop `
									-OSType Windows `
									-HttpCredential $credentials `
									-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
									-DefaultStorageAccountKey $storageAccountKey `
									-DefaultStorageContainer $containerName  

	![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

## HDInsight .NET SDK を使用した作成
HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。Visual Studio コンソール アプリケーションを作成し、クラスターを作成するためのコードを貼り付けるには、次の手順に従います。

**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio で、新しい C# コンソール アプリケーションを作成します。
2. NuGet パッケージ管理コンソールで次の Nuget コマンドを実行します。

		Install-Package Microsoft.Azure.Management.HDInsight -Pre

6. ソリューション エクスプローラーで、**Program.cs** をダブルクリックして開き、次のコードを貼り付け、変数の値を指定します。


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
		        private const string NewClusterLocation = "<location>";		//must be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster creation");

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


## オンプレミスの SQL Server Integration Services を利用した HDInsight クラスターの作成

SQL Server Integration Services (SSIS) を利用して HDInsight クラスターを作成または削除することもできます。Azure Feature Pack for SSIS には、HDInsight クラスターと連動する次のコンポーネントがあります。


- [Azure HDInsight クラスターの作成タスク][ssisclustercreate]
- [Azure HDInsight クラスターの削除タスク][ssisclusterdelete]
- [Azure サブスクリプション接続マネージャー][connectionmanager]

Azure Feature Pack for SSIS の詳細については、[ここ][ssispack]を参照してください。


##<a id="nextsteps"></a> 次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要](hdinsight-get-started.md) - HDInsight クラスターの使用方法について説明しています。
* [HDInsight の Hadoop での Sqoop の使用](hdinsight-use-sqoop.md) - HDInsight と SQL Database または SQL Server 間でデータをコピーする方法について説明しています。
* [Azure PowerShell を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-powershell.md) - HDInsight で Azure PowerShell を使用する方法について説明しています。
* [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md) - プログラムを使用して HDInsight にジョブを送信する方法について説明しています。
* [HDInsight][hdinsight-sdk-documentation] - HDInsight SDK について説明しています。



##付録 A - ARM テンプレート

次に示す Azure リソース マネージャーのテンプレートは、Azure ストレージ アカウントに依存する Hadoop クラスターを作成します。

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "defaultValue": "North Europe",
	      "allowedValues": [
	        "North Europe"
	      ],
	      "metadata": {
	        "description": "The location where all azure resources will be deployed."
	      }
	    },
	    "clusterName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the HDInsight cluster to create."
	      }
	    },
	    "clusterLoginUserName": {
	      "type": "string",
	      "defaultValue": "admin",
	      "metadata": {
	        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
	      }
	    },
	    "clusterLoginPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the cluster login."
	      }
	    },
	    "sshUserName": {
	      "type": "string",
	      "defaultValue": "hdiuser",
	      "metadata": {
	        "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
	      }
	    },
	    "sshPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the ssh user."
	      }
	    },
	    "clusterStorageAccountName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the storage account to be created and be used as the cluster's storage."
	      }
	    },
	    "clusterStorageType": {
	      "type": "string",
	      "defaultValue": "Standard_LRS",
	      "allowedValues": [
	        "Standard_LRS",
	        "Standard_GRS",
	        "Standard_ZRS"
	      ]
	    },
	    "clusterWorkerNodeCount": {
	      "type": "int",
	      "defaultValue": 4,
	      "metadata": {
	        "description": "The number of nodes in the HDInsight cluster."
	      }
	    }
	  },
	  "variables": {},
	  "resources": [
	    {
	      "name": "[parameters('clusterStorageAccountName')]",
	      "type": "Microsoft.Storage/storageAccounts",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-05-01-preview",
	      "dependsOn": [],
	      "tags": {},
	      "properties": {
	        "accountType": "[parameters('clusterStorageType')]"
	      }
	    },
	    {
	      "name": "[parameters('clusterName')]",
	      "type": "Microsoft.HDInsight/clusters",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-03-01-preview",
	      "dependsOn": [
	        "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
	      ],
	      "tags": {},
	      "properties": {
	        "clusterVersion": "3.2",
	        "osType": "Linux",
	        "clusterDefinition": {
	          "kind": "hadoop",
	          "configurations": {
	            "gateway": {
	              "restAuthCredential.isEnabled": true,
	              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
	              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
	            }
	          }
	        },
	        "storageProfile": {
	          "storageaccounts": [
	            {
	              "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
	              "isDefault": true,
	              "container": "[parameters('clusterName')]",
	              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
	            }
	          ]
	        },
	        "computeProfile": {
	          "roles": [
	            {
	              "name": "headnode",
	              "targetInstanceCount": "1",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            },
	            {
	              "name": "workernode",
	              "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            }
	          ]
	        }
	      }
	    }
	  ],
	  "outputs": {
	    "cluster": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
	    }
	  }
	}


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/ja-jp/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/ja-jp/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/ja-jp/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/ja-jp/library/mt146778(v=sql.120).aspx

<!---HONumber=Oct15_HO3-->