<properties
   pageTitle="HDInsight での Windows ベースの Hadoop クラスターの作成 | Microsoft Azure"
   	description="Azure HDInsight のクラスターを作成する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/08/2016"
   ms.author="jgao"/>

# HDInsight での Windows ベースの Hadoop クラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-selector-create-clusters.md)]

Hadoop クラスターは、クラスターでのタスクの分散処理に使用される複数の仮想マシン (ノード) で構成されます。Azure では個々のノードのインストールと構成の実装の詳細を抽象化しているため、一般的な構成情報を提供する必要があります。この記事では、これらの構成設定について説明します。

>[AZURE.NOTE] このドキュメントの情報は、Windows ベースの Azure HDInsight クラスターに固有のものです。Linux ベースのクラスターについては、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

## クラスターの種類##

現在、HDInsight では、それぞれ特定の機能を提供する一連のコンポーネントを備えた 4 種類のクラスターを提供しています。

| クラスターの種類 | 機能 |
| ------------ | ----------------------------- |
| Hadoop は、 | クエリと分析 (バッチ ジョブ) |
| HBase | NoSQL データ ストレージ |
| Storm | リアルタイム イベント処理 |
| Spark (プレビュー) | メモリ内処理、対話型クエリ、マイクロバッチ ストリーム処理 |

各クラスターの種類には、独自のノード数、クラスター内のノードを表す用語、およびノードの種類ごとに既定の VM サイズがあります。次の表では、各ノードの種類のノード数がかっこ内に示されています。

| 型| ノード (ノードの数)| ダイアグラム|
|-----|------|--------|
|Hadoop は、| ヘッド ノード (2)、データ ノード (1 以上)|![HDInsight Hadoop クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)|
|HBase|ヘッド サーバー (2)、リージョン サーバー (1 以上)、マスター/ZooKeeper ノード (3)|![HDInsight HBase クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)|
|Storm|Nimbus ノード (2)、Supervisor サーバー (1 以上)、ZooKeeper ノード (3)|![HDInsight Storm クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)|
|Spark|ヘッド ノード (2)、ワーカー ノード (1 以上)、ZooKeeper ノード (3) (A1 ZooKeeper VM サイズでは無料)|![HDInsight Spark クラスター ノード](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)|

> [AZURE.IMPORTANT] クラスターの作成時または作成後のスケーリングで 32 個を超える worker ノードを使用することを予定している場合は、コア数が 8 個以上、RAM が 14 GB 以上のヘッド ノード サイズを選択する必要があります。

[スクリプト アクション](#customize-clusters-using-script-action)を使用して、Hue や R などの他のコンポーネントをこれらの基本的な種類に追加することができます。

## 基本的な構成オプション

HDInsight クラスターを作成する際に必要な基本的な構成オプションを以下に示します。

### クラスター名###

クラスター名は、クラスターの識別に使用します。クラスター名はグローバルに一意である必要があり、次の名前付けのガイドラインに従う必要があります。

- このフィールドには、3 ～ 63 文字の文字列を指定する必要があります。
- このフィールドには、文字、数字、ハイフンのみを含めることができます。

### クラスターの種類 ###

「[クラスターの種類](#cluster-types)」を参照してください。

### オペレーティング システム ###

次の 2 つのオペレーティング システムのいずれかで HDInsight クラスターを作成できます。

- Linux での HDInsightHDInsight には、Azure で Linux クラスターを構成するためのオプションが用意されています。Linux または Unix に詳しい場合や、Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、既存の Linux ベースの Hadoop ソリューションから移行することで Linux クラスターを構成します。詳細については、「[Get started with Hadoop on Linux in HDInsight (HDInsight の Linux での Hadoop の使用)](hdinsight-hadoop-linux-tutorial-get-started.md)」をご覧ください。

- Windows 上の HDInsight (Windows Server 2012 R2 Datacenter)。

### HDInsight のバージョン###

HDInsight のバージョンを使用して、このクラスターで使用する HDInsight のバージョンを特定します。詳細については、[HDInsight での Hadoop クラスターのバージョンとコンポーネント](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)に関する記事を参照してください。

### サブスクリプション名###

各 HDInsight クラスターは、1 つの Azure サブスクリプションに関連付けられます。

### リソース グループ名###

[Azure Resource Manager](../resource-group-overview.md) を使用して、アプリケーションのこれらのリソースを、デプロイ、更新、監視、または削除できます。

### 資格情報

HDInsight クラスターでは、クラスターの作成時に次の 3 つのユーザー アカウントを構成できます。

- [Azure Resource Manager](../resource-group-overview.md) を使用すると、アプリケーション内の複数のリソースを、Azure リソース グループと呼ばれる 1 つのグループとして使用できます。アプリケーションのこれらのすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。

- HTTP ユーザー。既定のユーザー名は、Azure ポータルの基本構成における *admin* です。この既定のユーザーは "クラスター ユーザー" と呼ばれることもあります。
- RDP ユーザー (Windows クラスター)。RDP を使用してクラスターに接続します。アカウントの作成時に、アカウント作成当日から 90 日以内の有効期限を設定する必要があります。
- SSH ユーザー (Linux クラスター)。SSH を使用したクラスターへの接続。「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)」の手順に従ってクラスターを作成した後に、追加の SSH ユーザー アカウントを作成できます。

### データ ソース ###

元の Hadoop 分散ファイル システム (HDFS) では、クラスター上の多数のローカル ディスクを使用します。HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。Azure BLOB ストレージは、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。HDFS インターフェイスを使用して、HDInsight のすべてのコンポーネントで BLOB ストレージの構造化データまたは非構造化データを直接操作できます。BLOB ストレージにデータを格納すると、ユーザー データを失わずに、計算に使用されている HDInsight クラスターを安全に削除できます。

構成時に、Azure ストレージ アカウントと、その Azure ストレージ アカウントの Azure BLOB ストレージ コンテナーを指定する必要があります。一部の作成プロセスでは、Azure ストレージ アカウントと BLOB ストレージ コンテナーを事前に作成しておく必要があります。BLOB ストレージ コンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターがアクセス可能なその他の Azure Storage アカウント (リンクされたストレージ) を指定できます。また、クラスターは、完全なパブリック読み取りアクセスまたは BLOB だけを対象とするパブリック読み取りアクセスで構成された BLOB ストレージ コンテナーにアクセスすることもできます。詳細については、[Azure Storage リソースへのアクセスの管理](../storage/storage-manage-access-to-resources.md)に関するページを参照してください。

![HDInsight ストレージ](./media/hdinsight-provision-clusters/HDInsight.storage.png)

>[AZURE.NOTE] 次の図に示すように、BLOB ストレージ コンテナーには、一連の BLOB がまとめられています。

構成時に、Azure ストレージ アカウントと、その Azure ストレージ アカウントの Azure BLOB ストレージ コンテナーを指定する必要があります。一部の作成プロセスでは、Azure ストレージ アカウントと BLOB ストレージ コンテナーを事前に作成しておく必要があります。BLOB ストレージ コンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターがアクセスできるその他の Azure Storage アカウント (リンクされたストレージ) を指定できます。クラスターは、完全なパブリック読み取りアクセスまたは BLOB だけを対象とするパブリック読み取りアクセスで構成された BLOB コンテナーにアクセスすることもできます。詳細については、[Azure Storage リソースへのアクセスの管理](../storage/storage-manage-access-to-resources.md)に関するページを参照してください。


![Azure BLOB ストレージ](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

ビジネス データの格納には、既定の BLOB ストレージ コンテナーを使用しないことをお勧めします。ストレージ コストを削減するために、既定の BLOB ストレージ コンテナーの使用後、コンテナーを毎回削除することをお勧めします。既定のコンテナーには、アプリケーション ログとシステム ログが格納されます。コンテナーを削除する前に、ログを取り出してください。

>[AZURE.WARNING] HDinsight では、1 つの BLOB ストレージ コンテナーの複数のクラスターによる共有はサポートされません。

セカンダリ BLOB ストレージの詳細については、「[HDInsight の Hadoop での HDFS と互換性のある Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)」を参照してください。

Azure Blob ストレージに加え、[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) も HDInsight での HBase クラスターの既定のストレージ アカウントとして、また、4 つのすべての HDInsight クラスターの種類のリンクされたストレージとして使用できます。詳細については、「[Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。

### 場所 (リージョン)###

HDInsight クラスターとその既定のストレージ アカウントは、同じ Azure の場所に配置されている必要があります。

![Azure Azure リージョン](./media/hdinsight-provision-clusters/Azure.regions.png)

サポートされているリージョンのリストについては、「[HDInsight の価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」の **[リージョン]** ドロップダウン リストをクリックしてください。

### ノード価格レベル###

クラスターの有効期間中、これらのノードの使用量に対して課金されます。課金はクラスターが作成されると開始され、クラスターが削除されると停止されます。クラスターを割り当て解除または保留にすることはできません。

クラスターの種類によって、ノードの種類、ノード数、ノード サイズが異なります。たとえば、Hadoop クラスターの種類には 2 つの_ヘッド ノード_と 4 つの_データ ノード_ (既定) がありますが、Storm クラスターの種類には 2 つの _Nimbus ノード_、3 つの _ZooKeeper ノード_と 4 つの_Supervisor ノード_ (既定) があります。HDInsight クラスターのコストは、ノード数とノードの仮想マシンのサイズによって決まります。たとえば、大量のメモリを必要とする操作を実行することがわかっている場合は、より多くのメモリを持つコンピューティング リソースを選択できます。学習目的の場合は、1 つのデータ ノードの操作をお勧めします。HDInsight の価格の詳細については、「[HDInsight 価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」をご覧ください。

>[AZURE.NOTE] クラスター サイズの制限は、Azure サブスクリプションによって異なります。制限値を上げるには、課金サポートにお問い合わせください。

>ノードに使用される仮想マシン イメージは、HDInsight サービスの実装の詳細であるため、クラスターで使用されるノードは仮想マシンとしてカウントされません。ただし、ノードで使用されるコンピューティング コアは、サブスクリプションで利用できるコンピューティング コアとしてカウントされ、その合計数に含まれます。利用可能なコアとクラスターで使用されるコアの数は、HDInsight クラスターの作成時に、[ノード価格レベル] ブレードの概要セクションで確認できます。

Azure ポータルを使用してクラスターを構成するときに、__[ノード価格レベル]__ ブレードでノード サイズを利用できます。また、別のノード サイズに関連するコストを確認することもできます。次のスクリーンショットは、Linux ベースの Hadoop クラスターの選択肢を示しています。

![HDInsight VM ノードのサイズ](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

次の表に、HDInsight クラスターでサポートされているサイズと、それらが提供する容量を示します。

### Standard レベル: A シリーズ###

クラシック デプロイ モデルでは、一部の VM サイズが PowerShell と CLI で若干異なります。

* Standard\_A3: Large
* Standard\_A4: ExtraLarge

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard\_A3\\Large|4|7 GB|2|一時ディスク = 285 GB |8|8 x 500|
|Standard\_A4\\ExtraLarge|8|14 GB|4|一時ディスク = 605 GB |16|16 x 500|
|Standard\_A6|4|28 GB|2|一時ディスク = 285 GB |8|8 x 500|
|Standard\_A7|8|56 GB|4|一時ディスク = 605 GB |16|16 x 500|


### Standard レベル: D シリーズ###

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard\_D3 |4|14 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D4 |8|28 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D12 |4|28 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D13 |8|56 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D14 |16|112 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|


### Standard レベル: Dv2 シリーズ###

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard\_D3\_v2 |4|14 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D4\_v2 |8|28 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D12\_v2 |4|28 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D13\_v2 |8|56 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D14\_v2 |16|112 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|     

これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項については、[仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md)に関する記事をご覧ください。さまざまなサイズの価格については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight)」をご覧ください。

> [AZURE.IMPORTANT] クラスターの作成時または作成後のスケーリングで 32 個を超える worker ノードを使用することを予定している場合は、コア数が 8 個以上、RAM が 14 GB 以上のヘッド ノード サイズを選択する必要があります。

課金はクラスターが作成されると開始され、クラスターが削除されると停止されます。価格の詳細については、[HDInsight の価格詳細](https://azure.microsoft.com/pricing/details/hdinsight/)に関する記述を参照してください。


|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard\_D3\_v2 |4|14 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D4\_v2 |8|28 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D12\_v2 |4|28 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D13\_v2 |8|56 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D14\_v2 |16|112 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|    

これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項については、[仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md)に関する記事をご覧ください。さまざまなサイズの価格については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight)」をご覧ください。

> [AZURE.IMPORTANT] クラスターの作成時または作成後のスケーリングで 32 個を超える worker ノードを使用することを予定している場合は、コア数が 8 個以上、RAM が 14 GB 以上のヘッド ノード サイズを選択する必要があります。

 課金はクラスターが作成されると開始され、クラスターが削除されると停止されます。価格の詳細については、[HDInsight の価格詳細](https://azure.microsoft.com/pricing/details/hdinsight/)に関する記述を参照してください。


## 記憶域の追加

場合によっては、クラスターへのストレージの追加が必要になることがあります。たとえば、異なる地理的リージョンまたは異なるサービスの複数の Azure ストレージ アカウントがあり、それらをすべて HDInsight で分析する場合などです。

セカンダリ BLOB ストレージの詳細については、「[HDInsight の Hadoop での HDFS と互換性のある Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)」を参照してください。セカンダリ Data Lake Store の詳細については、「[Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。


## Hive/Oozie metastore の使用

HDInsight クラスターを削除した後に、Hive テーブルを保持する場合は、将来別の HDInsight クラスターに metastore をアタッチする目的で、カスタム metastore を使用することを強くお勧めします。

> [AZURE.IMPORTANT] HDInsight のメタストアは、下位互換性がありません。たとえば、HDInsight 3.3 クラスターのメタストアを使用して HDInsight 3.2 のクラスターを作成することはできません。

メタストアには、Hive テーブル、パーティション、スキーマ、列などについての Hive と Oozie メタデータが格納されます。metastore は、Hive と Oozie メタデータを保持するのに役立ちます。新しいクラスターを作成するときに、Hive テーブルまたは Oozie ジョブを再作成する必要はありません。既定では、Hive は組み込みの Azure SQL Database を使用してこの情報を格納します。組み込みデータベースは、クラスターが削除されるとメタデータを保持できません。たとえば、Hive metastore で作成したクラスターで Hive テーブルを作成する場合、同じ Hive metastore を使用してクラスターを削除して再作成すると、それらのテーブルを確認できます。

HBase のクラスターの種類では、メタストア構成は使用できません。

> [AZURE.IMPORTANT] カスタム metastore を作成するときは、データベース名にダッシュやハイフンを使用しないでください。使用すると、クラスター作成プロセスが失敗することがあります。

## Azure 仮想ネットワークの使用

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。Virtual Network では次のことが可能です。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![クラウドのみの構成の図](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)。

| サイト間構成 | ポイント対サイト構成 |
| -------------------------- | --------------------------- |
| サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターの複数のリソースを Azure Virtual Network に接続できます。<br />![サイト間構成の図](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure Virtual Network に接続できます。<br />![ポイント対サイト構成の図](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Windows ベースのクラスターでは、クラシック Virtual Network が必要であり、Linux ベースのクラスターでは、Azure Resource Manager Virtual Network が必要です。ネットワークの種類が正しくない場合、クラスターの作成には使用できません。

Virtual Network の具体的な構成要件など、Virtual Network で HDInsight がどのように機能するかについては、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」を参照してください。

## HDInsight クラスターのカスタマイズ (ブートストラップ) を使用したクラスターのカスタマイズ

次の構成ファイルを構成することが必要な場合があります。

- clusterIdentity.xml
- core-site.xml
- gateway.xml
- hbase-env.xml
- hbase-site.xml
- hdfs-site.xml
- hive-env.xml
- hive-site.xml
- mapred-site
- oozie-site.xml
- oozie-env.xml
- storm-site.xml
- tez-site.xml
- webhcat-site.xml
- yarn-site.xml

クラスターの有効期間中に変更を保持するには、作成プロセスで HDInsight クラスターのカスタマイズを使用します。Linux ベースのクラスターで、Ambari を使用することもできます。詳細については、「[ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)」を参照してください。

>[AZURE.NOTE] 再イメージ化により、Windows ベースのクラスターは変更を保持できません。詳細については、「[Role Instance Restarts Due to OS Upgrades (OS のアップグレードに伴うロール インスタンスの再起動)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)」を参照してください。クラスターの有効期間中に変更を保持するには、作成プロセスで HDInsight クラスターのカスタマイズを使用する必要があります。

## スクリプト アクションを使用したクラスターのカスタマイズ

追加コンポーネントをインストールするか、作成中にスクリプトを使用してクラスターの構成をカスタマイズできます。このようなスクリプトは、**スクリプト操作**を使用して実行します。これは Azure ポータル、HDInsight Windows PowerShell コマンドレット、HDInsight .NET SDK で使用できる構成オプションです。詳細については、「[Script Action を使って HDInsight をカスタマイズする](hdinsight-hadoop-customize-cluster.md)」を参照してください。



## クラスターの作成方法

この記事では、Windows ベースの HDInsight クラスターの作成に関する基本的な情報を提供しました。次の表を使用して、ニーズに最も適した方法を使用したクラスターの作成方法に関する特定の情報を見つけてください。

| クラスターの作成方法 | Web ブラウザー | コマンド ライン | REST API | SDK | Linux、Mac OS X、または Unix | Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure ポータル](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure CLI](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Azure リソース マネージャーのテンプレート](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0914_2016-->