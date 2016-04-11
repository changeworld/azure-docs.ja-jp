<properties
   	pageTitle="HDInsight での Hadoop、HBase、Storm、または Spark クラスター (Linux ベース) の作成 | Microsoft Azure"
   	description="ブラウザー、Azure CLI、Azure PowerShell、REST を使用するか、SDK 経由で HDInsight に Linux ベースの Hadoop、HBase、Storm、または Spark クラスターを作成する方法について説明します。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="03/25/2016"
   	ms.author="jgao"/>


# HDInsight での Linux ベースの Hadoop クラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-selector-create-clusters.md)]

Hadoop クラスターは、クラスターでのタスクの分散処理に使用されるいくつかの仮想マシン (ノード) で構成されます。Azure では個々のノードのインストールと構成の実装の詳細を抽象化しているため、提供する必要があるのは一般的な構成情報のみとなります。この記事では、これらの構成設定について説明します。

##クラスターの種類

現在、HDInsight では、それぞれ特定の機能を提供する一連のコンポーネントを備えた 5 種類のクラスターを提供しています。

| クラスターの種類 | 用途 |
| ------------ | ----------------------------- |
| Hadoop | クエリと分析 (バッチ ジョブ) |
| HBase | NoSQL データ ストレージ |
| Storm | リアルタイム イベント処理 |
| Spark (プレビュー) | メモリ内処理、対話型クエリ、マイクロバッチ ストリーム処理 |
| R Server on Spark | R は、さまざまなビッグ データ統計、予測モデリング、および機械学習の機能をサポートしています。 |

各クラスターの種類にはクラスター内のノードの独自の用語と、ノードの種類ごとにノード数および既定の VM サイズがあります。

| 型| ノード (ノードの数)| ダイアグラム|
|-----|------|--------|
|Hadoop は、| ヘッド ノード (2)、データ ノード (1 以上)|![HDInsight Hadoop クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)|
|HBase|ヘッド サーバー (2)、リージョン サーバー (1 以上)、マスター/Zookeeper ノード (3)|![HDInsight HBase クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)|
|Storm|Nimbus ノード (2)、Supervisor サーバー (1 以上)、Zookeeper ノード (3)|![HDInsight Storm クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)|
|Spark|ヘッド ノード (2)、ワーカー ノード (1 以上)、Zookeeper ノード (3) (A1 Zookeeper VM サイズでは無料)|![HDInsight Spark クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)|

* かっこ内は、各ノードの種類のノード数です。

> [AZURE.IMPORTANT] クラスター作成または作成後の拡大で 32 以上のワーカー ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。

[スクリプト アクション](#customize-clusters-using-script-action)を使用して、Hue や R などの他のコンポーネントをこれらの基本的な種類に追加することができます。

## クラスター レベル

Azure HDInsight は、ビッグ データのクラウド サービスを Standard と [Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium) の 2 つのカテゴリで提供します。HDInsight Premium には、R とその他の追加コンポーネントが含まれています。HDInsight Premium は、HDInsight バージョン 3.4 でのみサポートされます。

次の表に、HDInsight クラスターの種類と HDInsight Premium のサポート状況を示します。

| クラスターの種類 | Standard | Premium |
|--------------|---------------|--------------|
| Hadoop | あり | あり |
| Spark | あり | あり |
| HBase | あり | いいえ |
| Storm | あり | いいえ |
| R Server on Spark | いいえ | あり |

HDInsight Premium でサポートされるクラスターの種類が追加されたら、それに応じてこの表も更新されます。次のスクリーンショットは、クラスターの種類を選択するための Azure ポータルの情報を示しています。

![HDInsight Premium の構成](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


## 基本的な構成オプション

HDInsight クラスターを作成する際の基本的な構成オプションを以下に示します。

- **クラスター名**

	クラスター名は、クラスターの識別に使用します。クラスター名はグローバルに一意である必要があり、次の名前付けのガイドラインに従う必要があります。

	- このフィールドには、3 ～ 63 文字の文字列を指定する必要があります。
	- このフィールドには、文字、数字、ハイフンのみを含めることができます。

- **クラスターの種類**

    「[クラスターの種類](#cluster-types)」および「[クラスター レベル](#cluster-tiers)] を参照してください。

- **オペレーティング システム**

	次の 2 つのオペレーティング システムのいずれかで HDInsight クラスターを作成できます。
	- **Linux (Ubuntu 12.04 LTS for Linux) 上の HDInsight**: HDInsight には、Azure で Linux クラスターを構成するオプションが用意されています。Linux または Unix に詳しい場合や、Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、既存の Linux ベースの Hadoop ソリューションから移行することで Linux クラスターを構成します。詳細については、「[Get started with Hadoop on Linux in HDInsight (HDInsight の Linux での Hadoop の使用)](hdinsight-hadoop-linux-tutorial-get-started.md)」をご覧ください。
	- **Windows (Windows Server 2012 R2 Datacenter) 上の HDInsight **:
    
- **HDInsight のバージョン**

	このクラスターに使用する HDInsight のバージョンを指定するために使用します。詳細については、[HDInsight での Hadoop クラスターのバージョンとコンポーネント](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)に関するページをご覧ください。

- **サブスクリプション名**

	各 HDInsight クラスターは、1 つの Azure サブスクリプションに関連付けられます。
    
- **リソース グループ名**

	[Azure Resource Manager (ARM)](resource-group-overview.md) を使用すると、アプリケーション内の複数のリソースを 1 つのグループ (Azure リソース グループと呼ばれます) と見なして作業できます。アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。

- **資格情報**

	HDInsight クラスターでは、クラスターの作成時に次の 2 つのユーザー アカウントを構成できます。

	- HTTP ユーザー。既定のユーザー名は、Azure ポータルで基本構成を使用する admin です。"クラスター ユーザー" と呼ばれることもあります。
	- SSH ユーザー (Linux クラスター): SSH を使用してクラスターに接続する際に使用します。「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)」または「[HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)」の手順に従ってクラスターを作成した後に、追加の SSH ユーザー アカウントを作成できます。

    >[AZURE.NOTE] Windows ベースのクラスターでは、RDP を使用してクラスターに接続するために使用する RDP ユーザーを作成できます。

- **データ ソース**

	元の HDFS では、クラスターの多数のローカル ディスクを使用します。HDInsight では、データ ストレージとして Azure BLOB ストレージを使用します。Azure BLOB ストレージは、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。Hadoop 分散ファイル システム (HDFS) のインターフェイスを利用して、HDInsight のすべてのコンポーネントが BLOB ストレージの構造化データまたは非構造化データを直接操作できます。BLOB ストレージにデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。

	構成時に、Azure ストレージ アカウントと、その Azure ストレージ アカウントの Azure BLOB ストレージ コンテナーを指定する必要があります。一部の作成プロセスには、事前に作成された Azure ストレージ アカウントと BLOB ストレージ コンテナーが必要です。BLOB ストレージ コンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターがアクセス可能なその他の Azure Storage アカウント (リンクされたストレージ) を指定できます。また、クラスターは、完全なパブリック読み取りアクセスまたは BLOB だけを対象とするパブリック読み取りアクセスで構成された BLOB コンテナーにアクセスすることもできます。アクセスの制限の詳細については、「[Microsoft Azure ストレージ リソースへのアクセスの管理](storage-manage-access-to-resources.md)」をご覧ください。

	![HDInsight ストレージ](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] 次の図に示すように、BLOB ストレージ コンテナーには、一連の BLOB がまとめられています。

	![Azure BLOB ストレージ](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

    ビジネス データを格納するために既定の BLOB コンテナーを使用することはお勧めしません。ストレージ コストを削減するため、既定の BLOB コンテナーをそれぞれ使用したら、削除することをお勧めします。既定のコンテナーには、アプリケーション ログとシステム ログが格納されることに注意してください。コンテナーを削除する前に、ログを取り出してください。
    
	>[AZURE.WARNING] 1 つの BLOB ストレージ コンテナーを複数のクラスターで共有することはサポートされていません。

	従属的な BLOB ストアの使用の詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)」をご覧ください。

    Azure BLOB ストレージに加えて、[Azure Data Lake ストア](data-lake-store-overview.md)も HDInsight での HBase クラスターの既定のストレージ アカウントとして、および 4 つすべての HDInsight クラスターの種類のリンクされたストレージとして使用できます。手順については、「[Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。
    
- **場所 (リージョン)**

	HDInsight クラスターとその既定のストレージ アカウントは、同じ Azure の場所に配置されている必要があります。
	
	![Azure Azure リージョン](./media/hdinsight-provision-clusters/Azure.regions.png)

	サポートされているリージョンのリストについては、「[HDInsight の価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」の **[リージョン]** ドロップダウン リストをクリックしてください。

- **ノード価格レベル**

    クラスターの有効期間中、これらのノードの使用量に対して課金されます。課金は、クラスターが作成された時点で開始され、クラスターが削除されると終了します (クラスターを割り当て解除したり、保留にしたりすることはできません)。

	クラスターの種類によって、ノードの種類、ノードの数およびノードのサイズは異なります。たとえば、Hadoop クラスターの種類には 2 つの_ヘッド ノード_と 4 つの_データ ノード_ (既定) がありますが、Storm クラスターの種類には 2 つの _Nimbus ノード_、3 つの _Zookeeper ノード_と 4 つの_Supervisor ノード_ (既定) があります。HDInsight クラスターのコストは、ノード数とノードの仮想マシンのサイズによって決まります。たとえば、大量のメモリを必要とする操作を実行することがわかっている場合は、より多くのメモリを持つコンピューティング リソースを選択できます。学習目的の場合、データ ノードを 1 つ使用することをお勧めします。HDInsight の価格の詳細については、「[HDInsight 価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」をご覧ください。

	>[AZURE.NOTE] クラスター サイズの制限は、Azure サブスクリプションによって異なります。制限値を上げるには、課金サポートにお問い合わせください。
	
    >クラスターによって使用されるノードは Virtual Machines としてカウントされません。これは、ノードで使用される Virtual Machines イメージは、HDInsight サービスの実装の詳細であるためです。ただし、ノードによって使用されるコンピューティング コアは、サブスクリプションで利用できるコンピューティング コアとしてカウントされ、その合計数に含まれます。クラスターによって使用されるコアの数と利用可能なコアの数は、HDInsight クラスターを作成するときに、[ノード価格レベル] ブレードの [概要] セクションで確認できます。

	Azure ポータルを使用してクラスターを構成する場合、ノード サイズは __[ノード価格レベル]__ ブレードを介して公開され、別のノード サイズに関連付けられているコストも表示されます。次のスクリーン ショットに、Linux ベースの Hadoop クラスターの選択肢を示します。

	![HDInsight VM ノードのサイズ](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

    次の表に、HDInsight クラスターでサポートされているサイズと、それらが提供する容量を示します。

    - Standard レベル: A シリーズ

        クラシック デプロイ モデルでは、一部の VM サイズが PowerShell と CLI で若干異なります。

        * Standard\_A3: Large
        * Standard\_A4: ExtraLarge

        <br>

        |サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
        |---|---|---|---|---|---|---|
        |Standard\_A3\\Large|4|7 GB|2|一時ディスク = 285 GB |8|8 x 500|
        |Standard\_A4\\ExtraLarge|8|14 GB|4|一時ディスク = 605 GB |16|16 x 500|
        |Standard\_A6|4|28 GB|2|一時ディスク = 285 GB |8|8 x 500|
        |Standard\_A7|8|56 GB|4|一時ディスク = 605 GB |16|16 x 500|


    - Standard レベル: D シリーズ

        |サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
        |---|---|---|---|---|---|---|
        |Standard\_D3 |4|14 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
        |Standard\_D4 |8|28 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
        |Standard\_D12 |4|28 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
        |Standard\_D13 |8|56 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
        |Standard\_D14 |16|112 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|

    - Standard レベル: Dv2 シリーズ

        |サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
        |---|---|---|---|---|---|---|
        |Standard\_D3\_v2 |4|14 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
        |Standard\_D4\_v2 |8|28 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
        |Standard\_D12\_v2 |4|28 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
        |Standard\_D13\_v2 |8|56 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
        |Standard\_D14\_v2 |16|112 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|    
 
    これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項については、「[仮想マシンのサイズ](../virtual-machines/virtual-machines-size-specs.md)」を参照してください。さまざまなサイズの価格については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight)」を参照してください。
    
	> [AZURE.IMPORTANT] クラスター作成または作成後の拡大で 32 以上の worker ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。クラスターが作成されると課金が開始され、クラスターが削除された場合にのみ停止します。価格の詳細については、[HDInsight の価格詳細](https://azure.microsoft.com/pricing/details/hdinsight/)に関する記述を参照してください。


## 追加のストレージの使用

場合によっては、クラスターへのストレージの追加が必要になることがあります。たとえば、異なる地理的リージョンまたは異なるサービスの複数の Azure Storage アカウントがあるが、それらをすべて HDInsight で分析する必要がある場合などです。

従属的な BLOB ストアの使用の詳細については、「[HDInsight での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)」をご覧ください。セカンダリ Data Lake ストアの使用の詳細については、「[Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。


## Hive/Oozie メタストアの使用

HDInsight クラスターを削除した後に、Hive テーブルを保持する場合は、将来別の HDInsight クラスターにメタストアをアタッチする目的で、カスタム メタストアを使用することを強くお勧めします。

メタストアには、Hive テーブル、パーティション、スキーマ、列などについての Hive と Oozie メタデータが格納されます。メタストアを使用すると Hive と Oozie メタデータを保持できるため、新しいクラスターを作成するときに、Hive テーブルまたは Oozie ジョブを再作成する必要はありません。既定では、Hive は組み込みの Azure SQL Database を使用してこの情報を格納します。組み込みデータベースは、クラスターが削除されるとメタデータを保持できません。たとえば、Hive メタストアを使用して作成されたクラスターがあるとします。作成済みの Hive テーブルがいくつかあります。クラスターを削除した後に、同じ Hive メタストアを使用してクラスターを再作成すると、元のクラスターで作成していた Hive テーブルがあることがわかります。

> [AZURE.NOTE] HBase のクラスターの種類では、メタストア構成は使用できません。

## Azure Virtual Network の使用

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![クラウドのみの構成の図](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

    | サイト間構成 | ポイント対サイト構成 |
    | -------------------------- | --------------------------- |
    | サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。<br />![サイト間構成の図](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。<br />![ポイント対サイト構成の図](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Virtual Network の具体的な構成要件など、Virtual Network で HDInsight を使用する方法の詳細については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。

## HDInsight クラスターのカスタマイズ (ブートストラップ) を使用したクラスターのカスタマイズ

次の構成ファイルを構成することが必要な場合があります。

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

クラスターの有効期間中に変更を保持するには、作成プロセスで HDInsight クラスターのカスタマイズを使用するか、Linux ベースのクラスターで Ambari を安全に使用できます。詳細については、「[ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)」を参照してください。

>[AZURE.NOTE] 再イメージ化により、Windows ベースのクラスターは変更を保持できません。詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)」を参照してください。クラスターの有効期間中に変更を保持するには、作成プロセスで HDInsight クラスターのカスタマイズを使用する必要があります。

## スクリプト アクションを使用したクラスターのカスタマイズ

追加コンポーネントをインストールするか、作成中にスクリプトを使用してクラスターの構成をカスタマイズできます。このようなスクリプトは、**スクリプト アクション**によって呼び出されます。これは、ポータル、HDInsight Windows PowerShell コマンドレット、または HDInsight .NET SDK で使用できる構成オプションです。詳細については、「[Script Action を使って HDInsight をカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。



## クラスターの作成方法

この記事では、Linux ベースの HDInsight クラスターの作成に関する基本的な情報を提供しました。ニーズに最も適した方法を使用してクラスターを作成する方法に関する特定の情報を検索するには、次の表を使用します。

| クラスターを作成するにはこれを使用する... | Web ブラウザーを使用する... | コマンドラインを使用する | REST API を使用する | SDK を使用する | Linux、Mac OS X、または Unix から | Windows から |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure ポータル](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) | ✔ | ✔ | ✔ |✔ | ✔ | ✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [ARM テンプレート](hdinsight-hadoop-create-linux-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0330_2016-->