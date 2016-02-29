<properties
   pageTitle="HDInsight での Windows ベースの Hadoop クラスターの作成 | Microsoft Azure"
   	description="Azure HDInsight のクラスターを作成する方法について説明します。"
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
   ms.date="02/12/2016"
   ms.author="jgao"/>

# HDInsight での Windows ベースの Hadoop クラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-create-windows-cluster-selector.md)]

HDInsight クラスターの作成を計画する方法について説明します。

###前提条件:

この記事の手順を開始する前に、次の項目を用意する必要があります。

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。


## 基本的な構成オプション

HDInsight クラスターを作成する際の基本的な構成オプションを以下に示します。

- **クラスター名**

	クラスター名は、クラスターの識別に使用します。クラスター名は、次のガイドラインに従う必要があります。

	- このフィールドには、3 ～ 63 文字の文字列を指定する必要があります。
	- このフィールドには、文字、数字、ハイフンのみを含めることができます。

- **サブスクリプション名**

	HDInsight クラスターは、1 つの Azure サブスクリプションに関連付けられます。

- **リソース グループ名**

	Azure リソース マネージャー (ARM) を使用すると、アプリケーション内の複数のリソースを 1 つのグループ (Azure リソース グループと呼ばれます) と見なして作業できます。アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。詳細については、「[Azure リソース マネージャーの概要](resource-group-overview.md)」を参照してください。
	
- **オペレーティング システム**

	HDInsight クラスターは、次の 2 つのオペレーティング システムのいずれかで作成できます。- **Windows 上の HDInsight (Windows Server 2012 R2 Datacenter)**。- **Linux 上の HDInsight (Ubuntu 12.04 LTS for Linux)**。HDInsight には、Azure で Linux クラスターを構成するオプションが用意されています。Linux または Unix に詳しい場合や、Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、既存の Linux ベースの Hadoop ソリューションから移行することで Linux クラスターを構成します。詳細については、「[Get started with Hadoop on Linux in HDInsight (HDInsight の Linux での Hadoop の使用)](hdinsight-hadoop-linux-tutorial-get-started.md)」をご覧ください。

- **クラスターの種類**と**クラスター サイズ (データ ノード)**

	HDInsight を使用すると、さまざまなクラスターの種類を、異なるデータ分析ワークロードにデプロイできるようになります。現在提供されているクラスターの種類は次のとおりです。

	- Hadoop クラスター: クエリおよび分析ワークロード用
	- HBase クラスター: NoSQL ワークロード用
	- Storm クラスター: リアルタイム イベント処理ワークロード用
	- Spark クラスター: メモリ内処理、対話型クエリ、ストリーム、機械学習の各ワークロード用

	![HDInsight クラスター](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE] "Azure HDInsight クラスター" は、"HDInsight の Hadoop クラスター"、または "HDInsight クラスター" とも呼ばれます。また、"Hadoop クラスター" と同じ意味で使用される場合もあります。これらはすべて、Microsoft Azure 環境でホストされている Hadoop クラスターを指します。

	特定のクラスターの種類には、各種ノードのさまざまなロールがあります。これにより、ワークロードの詳細に適した特定のロールでノードのサイズを設定できます。たとえば、Hadoop クラスターで実行する分析がメモリ消費型である場合、大量のメモリを備えた worker ノードを作成できます。

	![HDInsight Hadoop クラスター ロール](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	HDInsight の Hadoop クラスターは 2 つのロールでデプロイされます。

	- ヘッド ノード (2 ノード)
	- データ ノード (1 つ以上のノード)

	![HDInsight Hadoop クラスター ロール](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	HDInsight 用 HBase クラスターは 3 つのロールでデプロイされます。- ヘッド サーバー (2 ノード) - リージョン サーバー (1 ノード以上) - Master/Zookeeper ノード (3 ノード)

	![HDInsight Hadoop クラスター ロール](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	HDInsight 用 Storm クラスターは 3 つのロールでデプロイされます。Nimbus ノード (2 ノード) - Supervisor サーバー (1 ノード以上) - Zookeeper ノード (3 ノード)


	![HDInsight Hadoop クラスター ロール](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	HDInsight 用 Spark クラスターは 3 つのロールでデプロイされます。ヘッド ノード (2 ノード) - worker ノード (1 ノード以上) - Zookeeper ノード (3 ノード) (A1 Zookeeper は無料)

	クラスターの有効期間中、これらのノードの使用量に対して課金されます。課金は、クラスターが作成された時点で開始され、クラスターが削除されると終了します (クラスターを割り当て解除したり、保留にしたりすることはできません)。クラスター サイズはクラスターの価格に影響します。学習目的の場合、データ ノードを 1 つ使用することをお勧めします。HDInsight の価格の詳細については、「[HDInsight 価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」をご覧ください。


	>[AZURE.NOTE] クラスター サイズの制限は、Azure サブスクリプションによって異なります。制限値を上げるには、課金サポートにお問い合わせください。

- **HDInsight のバージョン**

	このクラスターに使用する HDInsight のバージョンを指定するために使用します。詳細については、[HDInsight での Hadoop クラスターのバージョンとコンポーネント](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)に関するページをご覧ください。


- **場所 (リージョン)**

	HDInsight クラスターとその既定のストレージ アカウントは、同じ Azure の場所に配置されている必要があります。
	
	![Azure Azure リージョン](./media/hdinsight-provision-clusters/Azure.regions.png)

	サポートされているリージョンの一覧については、「[HDInsight 価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」の **[リージョン]** ボックスの一覧をクリックしてください。

- **ノード サイズ**

	![HDInsight VM ノードのサイズ](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	ノードの VM サイズを選択します。詳細については、「[クラウド サービスのサイズを構成する](cloud-services-sizes-specs.md)」をご覧ください。クラスターで使用されるコンピューティング リソースのサイズを選択できます。たとえば、大量のメモリを必要とする操作を実行することがわかっている場合は、より多くのメモリを持つコンピューティング リソースを選択できます。

	>[AZURE.NOTE] クラスターによって使用されるノードは Virtual Machines としてカウントされません。これは、ノードで使用される Virtual Machines イメージは、HDInsight サービスの実装の詳細であるためです。ただし、ノードによって使用されるコンピューティング コアは、サブスクリプションで利用できるコンピューティング コアとしてカウントされ、その合計数に含まれます。クラスターによって使用されるコアの数と利用可能なコアの数は、HDInsight クラスターを作成するときに、[ノード価格レベル] ブレードの [概要] セクションで確認できます。

	クラスターの種類によって、ノードの種類、ノードの数およびノードのサイズは異なります。たとえば、Hadoop クラスターの種類には 2 つの_ヘッド ノード_と 4 つの_データ ノード_ (既定) がありますが、Storm クラスターの種類には 2 つの _Nimbus ノード_、3 つの _Zookeeper ノード_と 4 つの_Supervisor ノード_ (既定) があります。

	> [AZURE.IMPORTANT] クラスター作成または作成後の拡大で 32 以上の worker ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。

	Azure ポータルを使用してクラスターを構成する場合、ノード サイズは __[ノード価格レベル]__ ブレードを介して公開され、別のノード サイズに関連付けられているコストも表示されます。

	> [AZURE.IMPORTANT] クラスターが作成されると課金が開始され、クラスターが削除された場合にのみ停止します。価格の詳細については、[HDInsight の価格詳細](https://azure.microsoft.com/pricing/details/hdinsight/)に関する記述を参照してください。


- **HDInsight ユーザー**

	HDInsight クラスターでは、プロビジョニング時に次の 2 つのユーザー アカウントを構成できます。

	- HTTP ユーザー。既定のユーザー名は、Azure ポータルで基本構成を使用する admin です。
	- RDP ユーザー (Windows クラスター): RDP を使用してクラスターに接続する際に使用します。アカウントの作成時に、当日から 90 日以内の有効期限を設定する必要があります。
	- SSH ユーザー (Linux クラスター): SSH を使用してクラスターに接続する際に使用します。「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)」の手順に従ってクラスターを作成した後に、追加の SSH ユーザー アカウントを作成できます。



- **Azure ストレージ アカウント**

	元の HDFS では、クラスターの多数のローカル ディスクを使用します。HDInsight では、データ ストレージとして Azure BLOB ストレージを使用します。Azure BLOB ストレージは、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。Hadoop 分散ファイル システム (HDFS) のインターフェイスを利用して、HDInsight のすべてのコンポーネントが BLOB ストレージの構造化データまたは非構造化データを直接操作できます。BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。

	構成時に、Azure ストレージ アカウントと、その Azure ストレージ アカウントの Azure BLOB ストレージ コンテナーを指定する必要があります。一部の作成プロセスには、事前に作成された Azure ストレージ アカウントと BLOB ストレージ コンテナーが必要です。BLOB ストレージ コンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターがアクセス可能なその他の Azure Storage アカウント (リンクされたストレージ) を指定できます。また、クラスターは、完全なパブリック読み取りアクセスまたは BLOB だけを対象とするパブリック読み取りアクセスで構成された BLOB コンテナーにアクセスすることもできます。アクセスの制限の詳細については、「[Microsoft Azure ストレージ リソースへのアクセスの管理](storage-manage-access-to-resources.md)」をご覧ください。

	![HDInsight ストレージ](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] 次の図に示すように、BLOB ストレージ コンテナーには、一連の BLOB がまとめられています。

	![Azure BLOB ストレージ](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING] 1 つの Blob ストレージ コンテナーを複数のクラスターで共有しないでください。これはサポートされていません。

	従属的な BLOB ストアの使用の詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)」をご覧ください。

- **Hive/Oozie メタストア**

	メタストアには、Hive テーブル、パーティション、スキーマ、列などについての Hive と Oozie メタデータが格納されます。メタストアを使用すると Hive と Oozie メタデータを保持できるため、新しいクラスターを作成するときに、Hive テーブルまたは Oozie ジョブを再作成する必要はありません。既定では、Hive は組み込みの Azure SQL Database を使用してこの情報を格納します。組み込みデータベースは、クラスターが削除されるとメタデータを保持できません。たとえば、Hive メタストアを使用して作成されたクラスターがあるとします。作成済みの Hive テーブルがいくつかあります。クラスターを削除した後に、同じ Hive メタストアを使用してクラスターを再作成すると、元のクラスターで作成していた Hive テーブルがあることがわかります。
    
    > [AZURE.NOTE] HBase のクラスターの種類では、メタストア構成は使用できません。

## HDInsight クラスターのカスタマイズ (ブートストラップ) を使用したクラスターのカスタマイズ

次の構成ファイルを構成することが必要な場合があります。

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

再イメージ化により、クラスターは変更を保持できません。詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)」を参照してください。クラスターの有効期間中に変更を保持するには、作成プロセスで HDInsight クラスターのカスタマイズを使用します。これはクラスターの構成を変更するための推奨方法であり、変更は Azure 再イメージ化再起動イベントを越えて存続します。これらの構成の変更はサービスが起動される前に適用されるため、サービスを再起動する必要はありません。

サンプルの詳細については、「[ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)」を参照してください。

## スクリプト アクションを使用したクラスターのカスタマイズ

追加コンポーネントをインストールするか、作成中にスクリプトを使用してクラスターの構成をカスタマイズできます。このようなスクリプトは、**スクリプト アクション**によって呼び出されます。これは、ポータル、HDInsight Windows PowerShell コマンドレット、または HDInsight .NET SDK で使用できる構成オプションです。詳細については、「[Script Action を使って HDInsight をカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。


## Azure Virtual Network の使用

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![クラウドのみの構成の図](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

	サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。

	![サイト間構成の図](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。

	![ポイント対サイト構成の図](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Virtual Network の具体的な構成要件など、Virtual Network で HDInsight を使用する方法については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。

## クラスターの作成方法

この記事では、Windows ベースの HDInsight クラスターの作成に関する基本的な情報を提供しました。ニーズに最も適した方法を使用してクラスターを作成する方法に関する特定の情報を検索するには、次の表を使用します。

| クラスターの作成時に使用する方法 | Web ブラウザーを使用 | コマンドラインを使用 | REST API を使用 | SDK を使用 | Linux、Mac OS X、または Unix から | Windows から |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure ポータル](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure CLI](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [ARM テンプレート](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0218_2016-->