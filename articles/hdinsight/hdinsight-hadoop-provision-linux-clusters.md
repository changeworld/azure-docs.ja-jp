<properties
   	pageTitle="HDInsight での Hadoop、HBase、Storm、または Spark クラスター (Linux ベース) の作成 | Microsoft Azure"
   	description="ブラウザー、Azure CLI、Azure PowerShell、REST を使用するか、SDK 経由で HDInsight に Linux ベースの Hadoop、HBase、Storm、または Spark クラスターを作成する方法について説明します。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="01/28/2016"
   	ms.author="nitinme"/>


#HDInsight での Linux ベースの Hadoop クラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-create-linux-cluster-selector.md)]

このドキュメントでは、クラスターで使用できるオプションの構成に加え、Azure で Linux ベースの HDInsight クラスターを作成するさまざまな方法を学習します。HDInsight は、Azure クラウド プラットフォーム上のサービスとして Apache Hadoop、Apache Storm、および Apache HBase を提供します。

> [AZURE.NOTE] このドキュメントでは、クラスターを作成するさまざまな手順を示します。クラスターをすばやく作成する方法を調べる場合は、[Azure HDInsight (Linux) の概要](hdinsight-hadoop-linux-tutorial-get-started.md)に関する記述を参照してください。

## HDInsight クラスターについて

Hadoop クラスターは、クラスターでのタスクの分散処理に使用されるいくつかの仮想マシン (ノード) で構成されます。Azure では個々のノードのインストールと構成の実装の詳細を抽象化しているため、提供する必要があるのは一般的な構成情報のみとなります。

###クラスターの種類

以下の複数の種類の HDInsight が使用可能です。

| クラスターの種類 | 用途 |
| ------------ | ----------------------------- |
| Hadoop | クエリと分析 (バッチ ジョブ) |
| HBase | NoSQL データ ストレージ |
| Storm | リアルタイム イベント処理 |
| Spark (プレビュー) | メモリ内処理、対話型クエリ、マイクロバッチ ストリーム処理 |

構成時に、クラスターに対してこれらの種類のいずれかを選択します。[スクリプト アクション](#scriptaction)を使用して、Hue や R などの他のテクノロジをこれらの基本的な種類に追加することができます。

各クラスターの種類にはクラスター内のノードの独自の用語と、ノードの種類ごとにノード数および既定の VM サイズがあります。

> [AZURE.IMPORTANT] クラスター作成または作成後の拡大で 32 以上の worker ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。

![HDInsight Hadoop クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

HDInsight 用 Hadoop クラスターには次の 2 種類のノードがあります。

- ヘッド ノード (2 ノード)
- データ ノード (1 つ以上のノード)

![HDInsight HBase クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

HDInsight 用 HBase クラスターには、ヘッド サーバー (2 ノード)、リージョン サーバー (1 ノード以上)、Master/Zookeeper ノード (3 ノード) という 3 種類のノードがあります。

![HDInsight Storm クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

HDInsight 用 Storm クラスターには、Nimbus ノード (2 ノード)、Supervisor サーバー (1 ノード以上)、Zookeeper ノード (3 ノード) という 3 種類のノードがあります。


![HDInsight Spark クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

HDInsight 用 Spark クラスターには、ヘッド ノード (2 ノード) - worker ノード (1 ノード以上) - Zookeeper ノード (3 ノード) (A1 Zookeeper VM サイズでは無料) という 3 種類のノードがあります。

###HDInsight 用 Azure Storage

各クラスターの種類には、クラスターに関連付けられている 1 つ以上の Azure Storage アカウントもあります。HDInsight は、クラスターのデータ ストアとして、これらのストレージ アカウントの Azure BLOB を使用します。データをクラスターから分離しておくことで、使用されていないクラスターを削除した場合でもデータを引き続き保持できます。したがって、さらに分析が必要な場合に、新しいクラスターでも同じストレージ アカウントを使用できます。詳細については、[HDInsight での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。

## <a id="configuration"></a>基本的な構成オプション

次のセクションでは、HDInsight クラスターの作成時に使用できる必要な構成オプションについて説明します。

###クラスター名

クラスター名はクラスターの一意の識別子を示し、インターネット経由でクラスターにアクセスするためのドメイン名の一部として使用されます。たとえば、_mycluster_ という名前のクラスターは、_mycluster_. azurehdinsight.net としてインターネット経由で使用できます。

クラスター名は、次のガイドラインに従う必要があります。

- このフィールドには、3 ～ 63 文字の文字列を含める必要があります。
- このフィールドには、文字、数字、ハイフンのみを含めることができます。

###クラスターの種類

クラスターの種類では、クラスターの特殊用途の構成を選択できます。Linux ベースの HDInsight で使用可能な種類を以下に示します。

| クラスターの種類 | 用途 |
| ------------ | ----------------------------- |
| Hadoop | クエリと分析 (バッチ ジョブ) |
| HBase | NoSQL データ ストレージ |
| Storm | リアルタイム イベント処理 |
| Spark (プレビュー) | メモリ内処理、対話型クエリ、マイクロバッチ ストリーム処理 |

[スクリプト アクション](#scriptaction)を使用して、Hue や R などの他のテクノロジをこれらの基本的な種類に追加することができます。

###クラスターのオペレーティング システム

次の 2 つのオペレーティング システムのいずれかで HDInsight クラスターをプロビジョニングすることができます。

- **Windows 上の HDInsight (Windows Server 2012 R2 Datacenter)**: Hadoop クラスターで実行される Windows ベースのサービスとテクノロジを統合する必要がある場合や、既存の Windows ベースの Hadoop ディストリビューションから移行する場合は、このオプションを選択します。

- **Linux 上の HDInsight (Ubuntu 12.04 LTS for Linux)**: Linux または Unix に詳しい場合、既存の Linux ベースの Hadoop ソリューションから移行する場合、または Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、このオプションを選択します。詳細については、「[Get started with Hadoop on Linux in HDInsight (HDInsight の Linux での Hadoop の使用)](hdinsight-hadoop-linux-tutorial-get-started.md)」をご覧ください。

> [AZURE.NOTE] このドキュメントの情報は、Linux ベースの HDInsight クラスターを使用することを前提としています。Windows ベースのクラスターに固有の情報については、[HDInsight での Windows ベースの Hadoop クラスターの作成](hdinsight-provision-clusters.md)に関する記述を参照してください。

###サブスクリプション名

複数の Azure サブスクリプションがある場合は、これを使用して、HDInsight クラスターの作成時に使用するものを選択します。

###リソース グループ

アプリケーションは通常、Web アプリケーション、データベース、データベース サーバー、ストレージ、サード パーティのサービスなどの、複数のコンポーネントで構成されます。Azure リソース マネージャー (ARM) を使用すると、アプリケーション内の複数のリソースを 1 つのグループ (Azure リソース グループと呼ばれます) と見なして作業できます。アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。デプロイメントにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。グループ全体のロールアップ コストを表示すると、組織の課金ついて明確に把握できます。詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」を参照してください。

###資格情報

HDInsight クラスターで使用される認証には以下の 2 種類があります。

* __Admin__ または __HTTPS__ ユーザー: クラスターによって公開される Web または REST サービスにアクセスすると際には、クラスターの管理者アカウントが主に使用されます。クラスターに直接ログインする場合は使用できません。

* __SSH ユーザー名__: [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) クライアントを使用してクラスターにリモート アクセスする際に、SSH ユーザー アカウントが使用されます。これは、クラスター ヘッド ノードへのリモート コマンドライン アクセスを提供するために最もよく使用されます。

管理者アカウントはパスワードで保護されます。管理者アカウントを使用するクラスターへのすべての Web 通信をセキュリティで保護された HTTPS 接続経由で実行する必要があります。

SSH ユーザーは、パスワードまたは証明書を使用して認証できます。証明書の認証は最も安全なオプションですが、パブリックとプライベート証明書のペアの作成とメンテナンスが必要になります。

SSH キーの作成および使用方法を含む、HDInsight での SSH の使用方法の詳細については、次のいずれかの記事を参照してください。

* [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

###データ ソース

HDInsight では、クラスターの基になるストレージとして Azure Blob Storage を使用します。内部的には、クラスター上の Hadoop と他のソフトウェアは、このストレージを Hadoop 分散ファイル システム (HDFS) と互換性のあるシステムと見なします。

新しいクラスターを作成する場合は、新しい Azure Storage アカウントを作成するか、既存のものを使用する必要があります。

> [AZURE.IMPORTANT] クラスターは既定のストレージ アカウントと同じデータ センター内にある必要があるため、ストレージ アカウントに対して選択する地理的な場所によって HDInsight クラスターの場所が決まります。
>
> サポートされているリージョンのリストについては、「[HDInsight の価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」の **[リージョン]** ドロップダウン リストをクリックしてください。

####既定のストレージ コンテナー

HDInsight は、ストレージ アカウントで_既定のストレージ コンテナー_も作成します。これが、HDInsight クラスターの既定のストレージです。

既定では、このコンテナーの名前はクラスター名と同じです。HDInsight で Azure Blog Storage を使用する方法の詳細については、「[HDInsight の Hadoop での HDFS と互換性のある Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)」を参照してください。

>[AZURE.WARNING] 1 つのコンテナーを複数のクラスターで共有しないでください。これはサポートされていません。

###ノード サイズ

クラスターで使用されるコンピューティング リソースのサイズを選択できます。たとえば、大量のメモリを必要とする操作を実行することがわかっている場合は、より多くのメモリを持つコンピューティング リソースを選択できます。

>[AZURE.NOTE] クラスターによって使用されるノードは Virtual Machines としてカウントされません。これは、ノードで使用される Virtual Machines イメージは、HDInsight サービスの実装の詳細であるためです。ただし、ノードによって使用されるコンピューティング コアは、サブスクリプションで利用できるコンピューティング コアとしてカウントされ、その合計数に含まれます。クラスターによって使用されるコアの数と利用可能なコアの数は、HDInsight クラスターを作成するときに、[ノード価格レベル] ブレードの [概要] セクションで確認できます。

クラスターの種類によって、ノードの種類、ノードの数およびノードのサイズは異なります。たとえば、Hadoop クラスターの種類には 2 つの_ヘッド ノード_と 4 つの_データ ノード_ (既定) がありますが、Storm クラスターの種類には 2 つの _Nimbus ノード_、3 つの _Zookeeper ノード_と 4 つの_Supervisor ノード_ (既定) があります。

> [AZURE.IMPORTANT] クラスター作成または作成後の拡大で 32 以上の worker ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。

Azure プレビュー ポータルを使用してクラスターを構成する場合、ノード サイズは __[ノード価格レベル]__ ブレードを介して公開され、別のノード サイズに関連付けられているコストも表示されます。

> [AZURE.IMPORTANT] クラスターが作成されると課金が開始され、クラスターが削除された場合にのみ停止します。価格の詳細については、[HDInsight の価格詳細](https://azure.microsoft.com/pricing/details/hdinsight/)に関する記述を参照してください。

##<a id="optionalconfiguration"></a>オプションの構成

次のセクションでは、オプションの構成オプションと、これらの構成を必要とするシナリオについて説明します。

### HDInsight のバージョン

このクラスターに使用する HDInsight のバージョンを指定するためにこれを使用します。詳細については、[HDInsight での Hadoop クラスターのバージョンとコンポーネント](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)に関するページを参照してください。

### Azure Virtual Network の使用

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![クラウドのみの構成の図](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

    | サイト間構成 | ポイント対サイト構成 |
    | -------------------------- | --------------------------- |
    | サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。<br />![サイト間構成の図](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。<br />![ポイント対サイト構成の図](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Virtual Network の具体的な構成要件など、Virtual Network で HDInsight を使用する方法の詳細については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。

### メタストア

メタストアには、Hive テーブル、パーティション、スキーマ、列に関する情報などの Hive と Oozie メタデータが含まれます。メタストアを使用すると Hive と Oozie メタデータを保持できるため、新しいクラスターを作成するときに、Hive テーブルまたは Oozie ジョブを再作成する必要はありません。

メタストアの構成オプションを使用すると、SQL Database を使用して外部のメタストアを指定することができます。これにより、クラスターを削除するときに、データベースに外付けで格納されているメタデータ情報を保持できます。Azure での SQL データベースの作成方法について詳しくは、「[最初の Azure SQL Database を作成する](../sql-database/sql-database-get-started.md)」をご覧ください。

> [AZURE.NOTE] HBase のクラスターの種類では、メタストア構成は使用できません。

###<a id="scriptaction"></a>スクリプト アクション

追加コンポーネントをインストールしたり、クラスターのプロビジョニング中にスクリプトを使用してクラスター構成をカスタマイズしたりできます。このようなスクリプトは**スクリプト アクション**経由で呼び出されます。詳細については、「[Script Action を使って HDInsight をカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。

> [AZURE.IMPORTANT] クラスターの作成後にコンポーネントを追加することはできません。そのようなコンポーネントはクラスター ノードの再イメージ化後に利用できないためです。スクリプト アクション経由でインストールされたコンポーネントは再イメージ化プロセスで再度インストールされます。

### その他のストレージ

場合によっては、クラスターへのストレージの追加が必要になることがあります。たとえば、異なる地理的リージョンまたは異なるサービスの複数の Azure Storage アカウントがあるが、それらをすべて HDInsight で分析する必要がある場合などです。

従属的な BLOB ストアの使用の詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)」をご覧ください。

##<a id="nextsteps"></a><a id="options"></a>作成方法

この記事では、Linux ベースの HDInsight クラスターの作成に関する基本的な情報を提供しました。ニーズに最も適した方法を使用してクラスターを作成する方法に関する特定の情報を検索するには、次の表を使用します。

| クラスターの作成時に使用する方法 | Web ブラウザーを使用 | コマンドラインを使用 | REST API を使用 | SDK を使用 | Linux、Mac OS X、または Unix から | Windows から |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure プレビュー ポータル](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |


[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage/storage-create-storage-account.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "クラスターの一覧と表示"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "HDInsight での Sqoop の使用"

<!---HONumber=AcomDC_0218_2016-->