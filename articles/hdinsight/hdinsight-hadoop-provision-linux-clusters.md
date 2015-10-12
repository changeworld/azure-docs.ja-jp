<properties
   	pageTitle="HDInsight での Hadoop、HBase、Storm クラスター (Linux ベース) のプロビジョニング | Microsoft Azure"
   	description="管理ポータル、コマンド ライン、.NET SDK を使用して、HDInsight 向けに Linux で Hadoop クラスターをプロビジョニングする方法を説明します。"
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
   	ms.date="09/21/2015"
   	ms.author="nitinme"/>


#「Provision Hadoop Linux clusters in HDInsight using custom options (カスタム オプションを使用した HDInsight での Hadoop Linux クラスターのプロビジョニング」

この記事では、Azure ポータル、Azure PowerShell、Azure CLI、または HDInsight .NET SDK を使用して、Azure HDInsight での Hadoop Linux クラスターのプロビジョニングをカスタマイズするさまざまな方法について説明します。

## HDInsight クラスターについて

Hadoop またはビッグ データを説明するときに、クラスターについて必ず触れるのはなぜでしょうか。 その理由は、Hadoop がクラスターのさまざまなノードにまたがる大規模データの分散処理を可能にするためです。クラスターは、1 つのマスター (ヘッドノードまたは名前ノードともいいます) と任意の数のワーカー ノード (データ ノードともいいます) から成るマスター / ワーカー アーキテクチャを構成しています。詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop に関する Web ページ</a>」をご覧ください。

![HDInsight Cluster][img-hdi-cluster]


HDInsight クラスターでは、Hadoop の実装の詳細を抽象化しているため、クラスターの別のノードとの通信方法を心配する必要はありません。HDInsight クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。詳細については、「[HDInsight での Hadoop 入門](hdinsight-hadoop-introduction.md)」を参照してください。変更されるデータは、Azure Blob ストレージに格納されます。詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」を参照してください。


この記事では、クラスターをプロビジョニングするさまざまな手順を示します。クラスターをすばやくプロビジョニングする方法を調べる場合は、「[Azure HDInsight (Linux) の概要](../hdinsight-hadoop-linux-get-started.md)」をご覧ください。

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- **Secure Shell (SSH) キー**。パスワードの代わりに SSH とキーを使用して Linux クラスターにリモートでアクセスする場合に必要です。キーはより安全性が高いことから、キーを使用する方法をお勧めします。SSH キーを生成する手順については、次の記事をご覧ください。
	-  Linux コンピューターの場合 - [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
	-  Windows コンピューターの場合 - [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a id="configuration"></a>構成オプション

### その他のストレージ

構成中に、Azure BLOB ストレージ アカウントと既定のコンテナーを指定する必要があります。このコンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターに関連付ける追加の BLOB も指定することができます。


従属的な BLOB ストアの使用の詳細については、「[HDInsight の Hadoop での BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」をご覧ください。


### メタストア

メタストアには、Hive テーブル、パーティション、スキーマ、列などについての情報が格納されます。この情報は、データが Hadoop 分散ファイル システム (HDFS)、または HDInsight の Azure Blob ストレージのどこに格納されているかを見つけるために、Hive によって使用されます。既定では、Hive は組み込みデータベースを使用してこの情報を格納します。

HDInsight クラスターをプロビジョニングするとき、Hive のメタストアを格納する SQL データベースを指定します。これにより、クラスターを削除するとき、SQL データベースに外付けで格納されているメタデータ情報を保持できます。Azure での SQL データベースの作成方法について詳しくは、「[最初の Azure SQL Database データベースを作成する](sql-database-get-started.md)」をご覧ください。

### スクリプト アクションを使用したクラスターのカスタマイズ

追加コンポーネントをインストールするか、プロビジョニング中にスクリプトを使用してクラスターの構成をカスタマイズできます。このようなスクリプトは**スクリプト アクション**経由で呼び出されます。詳細については、「[Script Action を使って HDInsight をカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。

> [AZURE.IMPORTANT]クラスターのプロビジョニング後にコンポーネントを追加することはできません。そのようなコンポーネントはクラスター ノードの再イメージ化後に利用できないためです。スクリプト アクション経由でインストールされたコンポーネントは再イメージ化プロセスで再度インストールされます。

### Azure Virtual Network の使用

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![diagram of cloud-only configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

	サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of site-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png)

	ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of point-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png)

Virtual Network の機能、利点の詳細については、「[Azure Virtual Network の概要](http://msdn.microsoft.com/library/azure/jj156007.aspx)」を参照してください。

> [AZURE.NOTE]クラスターをプロビジョニングする前に、Azure 仮想ネットワークを作成する必要があります。詳細については、「[Virtual Network の作成方法](virtual-networks-create-vnet.md)」をご覧ください
>
> Azure HDInsight は場所ベースの Virtual Networks のみをサポートし、アフィニティ グループ ベースの Virtual Networks は現在取り扱っていません。既存の Azure Virtual Network が場所ベースかどうかを確認するには、Azure PowerShell コマンドレットの Get-AzureVNetConfig を使用します。Virtual Network が場所ベースでない場合、次のオプションがあります。
>
> - 既存の Virtual Network 構成をエクスポートし、新しい Virtual Network を作成します。既定では、新しい Virtual Networks はすべて場所ベースになります。
> - 場所ベースの Virtual Network に移行します。[リージョン スコープへの既存のサービスの移行](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)に関するページをご参照ください。
>
> 1 つのクラスターには単一のサブネットを指定することを強くお勧めします。
>
> 現時点 (2015 年 8 月 25 日) において、Azure Virtual Network では 1 つの Linux ベースのクラスターのみをプロビジョニングできます。
>
> Linux ベースの HDInsight で v1 (クラシック) Azure Virtual Network を使用することはできません。Azure プレビュー ポータルでの HDInsight クラスターの作成プロセスで Virtual Network をオプションとして表示したり、Azure CLI または Azure PowerShell からクラスターを作成するときに Virtual Network を使用できるようにしたりするには、Virtual Network が v2 (Azure リソース マネージャー) である必要があります。
>
> v1 ネットワーク上にリソースがあり、HDInsight から仮想ネットワークを介してそれらのリソースに直接アクセスできるようにする場合、v2 Virtual Network を v1 Virtual Network に接続する方法については、「[従来の VNet を新しい VNet に接続する](../virtual-network/virtual-networks-arm-asm-s2s.md)」をご覧ください。この接続が確立されると、v2 Virtual Network で HDInsight クラスターを作成できます。

## <a id="options"></a>HDInsight Linux クラスターのプロビジョニング オプション

HDInsight Hadoop Linux クラスターは、Linux コンピューターと Windows ベースのコンピューターからプロビジョニングできます。次の表は、さまざまな OS から使用できるプロビジョニング オプションと、その各手順を記載したリンクを示しています。

この OS を実行するコンピューターからの Linux クラスターのプロビジョニング | Azure ポータルの使用 | Azure CLI の使用 | Azure PowerShell の使用 | .NET SDK の使用
-----------------| --------| ------------------------| -------------------| ---------- | ---------
Linux| [ここ](#portal)をクリック | [ここ](#cli)をクリック | 適用不可 | 適用不可
Mac OS X | [ここ](#portal)をクリック | [ここ](#cli)をクリック | 適用不可 | 適用不可
Windows | [ここ](#portal)をクリック | [ここ](#cli)をクリック | [ここ](#powershell)をクリック | [ここ](#sdk)をクリック

### <a id="portal"></a>Azure ポータルの使用

HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには、同じデータ センターにある Azure ストレージ アカウントが必要です。詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」をご覧ください。Azure ストレージ アカウントの作成の詳細については、「[ストレージ アカウントの作成方法](../storage-create-storage-account.md)」をご覧ください。


> [AZURE.NOTE]現在、HDInsight Linux クラスターをホストできるリージョンは、**東南アジア**、**北ヨーロッパ**、**米国東部**、**米国中南部**のみです。

**HDInsight クラスターを作成するには**

1. [Azure プレビュー ポータル](https://portal.azure.com)にサインインします。
2. **[新規]**、**[データ分析]**、**[HDInsight]** の順にクリックします。

    ![Azure プレビュー ポータルでの新しいクラスターの作成](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "Azure プレビュー ポータルでの新しいクラスターの作成")

3. **[クラスター名]** を入力し、**[クラスターの種類]** で **[Hadoop]** を選択し、**[クラスターのオペレーティング システム]** ボックスの一覧から **[Ubuntu]** を選択します。クラスターを使用できる場合は、クラスター名の横に緑色のチェック マークが表示されます。


	> [AZURE.NOTE]HBase または Storm のクラスターをプロビジョニングするには、**[クラスターの種類]** ボックスの一覧で該当する値を選択します。


	![クラスターの名前と種類の入力](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "クラスターの名前と種類の入力")

4. 複数のサブスクリプションがある場合は、**[サブスクリプション]** エントリをクリックし、クラスターで使用する Azure サブスクリプションを選択します。

5. **[リソース グループ]** をクリックして既存のリソース グループの一覧を表示し、クラスターの作成先にするグループを選択します。または、**[新規作成]** をクリックし、新しいリソース グループの名前を入力できます。新しいグループ名を使用できる場合は、緑のチェック マークが表示されます。

	> [AZURE.NOTE]このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。

6. **[資格情報]** をクリックし、管理ユーザーのパスワードを入力します。さらに、SSH ユーザーの認証に使用される **[SSH ユーザー名]** と、**[パスワード]** または **[公開キー]** のどちらかを入力する必要があります。公開キーを使用することをお勧めします。下部にある **[選択]** をクリックして、資格情報の構成を保存します。

	![クラスターの資格情報の指定](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "クラスターの資格情報の指定")

	HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)


7. **[データ ソース]** をクリックし、クラスターの既存のデータ ソースを選択するか、新しいデータ ソースを作成します。

	![[データ ソース] ブレード](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "データ ソース構成の指定")

	現在、HDInsight クラスターのデータ ソースとして Azure ストレージ アカウントを選択できます。次の説明を参照して、**[データ ソース]** ブレードのエントリについて理解してください。

	- **[選択方法]**: すべてのサブスクリプションのストレージ アカウントを参照できるようにする場合は、**[すべてのサブスクリプションから]** に設定します。既存のストレージ アカウントの **[ストレージ名]** と **[アクセス キー]** を入力する場合は、**[アクセス キー]** に設定します。

	- **[ストレージ アカウントの選択]/[新規作成]**: クラスターに関連付ける既存のストレージ アカウントを参照して選択する場合は、**[ストレージ アカウントの選択]** をクリックします。または、**[新規作成]** をクリックして、新しいストレージ アカウントを作成します。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。

	- **[既定のコンテナーの選択]**: これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、コンテナーが特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。

	- **[場所]**: ストレージ アカウントが存在するリージョン、またはストレージ アカウントの作成先のリージョン。

		> [AZURE.IMPORTANT]既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。クラスターと既定のデータ ソースは、同じリージョンに存在する必要があります。

	**[選択]** をクリックしてデータ ソースの構成を保存します。

8. **[ノード価格レベル]** をクリックして、このクラスターのために作成されるノードに関する情報を表示します。クラスターで必要なワーカー ノードの数を設定します。クラスターの推定コストがブレード内に表示されます。

	![[ノード価格レベル] ブレード](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "クラスター ノード数の指定")

	**[選択]** をクリックして、ノードの価格構成を保存します。

9. **[オプションの構成]** をクリックし、クラスターのバージョンを選択して、その他のオプションの設定を構成します。その他のオプションには、**Virtual Network** への参加、Hive と Oozie 用のデータを保持する**外部メタストア** の設定、スクリプト アクションを使用してカスタム コンポーネントをインストールするようにクラスターをカスタマイズすること、クラスターでの追加のストレージ アカウントの使用などがあります。

	* **[HDInsight のバージョン]** ボックスの一覧から、クラスターで使用するバージョンを選択します。詳細については、「[HDInsight クラスターのバージョン](hdinsight-component-versioning.md)」をご覧ください。


	* **[仮想ネットワーク]**: クラスターを仮想ネットワークに配置する場合は、Azure Virtual Network とサブネットを選択します。

		![[仮想ネットワーク] ブレード](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.6.png "仮想ネットワークの詳細の指定")

    	>[AZURE.NOTE]Windows ベースの HDInsight クラスターは、クラシック仮想ネットワークにのみ配置できます。


	* **[外部メタストア]** をクリックし、クラスターに関連付けられた Hive と Oozie のメタデータを保存するために使用する SQL データベースを指定します。

		![[カスタム メタストア] ブレード](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "外部メタストアの指定")

		**[Hive メタデータで既存の SQL DB を使用する]** で **[はい]** をクリックし、SQL Database を選択して、データベースのユーザー名/パスワードを指定します。**[Oozie メタデータで既存の SQL DB を使用する]** に該当する場合は、上記の手順を繰り返します。**[オプションの構成]** ブレードに戻るまで **[選択]** をクリックします。

		>[AZURE.NOTE]メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL Database インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。


	* クラスターを作成するときに、カスタム スクリプトを使用してクラスターをカスタマイズする場合は、**[スクリプト アクション]** をクリックします。スクリプト アクションの詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。[Script Action] ブレードで、次の画面キャプチャに示すように、詳細を指定します。

		![[Script ction] ブレード](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.8.png "スクリプト アクションの指定")


	* **[Azure ストレージ キー]** をクリックして、クラスターに関連付ける追加のストレージ アカウントを指定します。**[Azure ストレージ キー]** ブレードで、**[ストレージ キーの追加]** をクリックし、既存のストレージ アカウントを選択するか、新しいアカウントを作成します。

		![[追加ストレージ] ブレード](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "追加ストレージ アカウントの指定")

		**[新しい HDInsight クラスター]** ブレードに戻るまで **[選択]** をクリックします。

10. **[新しい HDInsight クラスター]** ブレードで、**[スタート画面にピン留めする]** が選択されていることを確認し、**[作成]** をクリックします。これでクラスターが作成され、Azure ポータルのスタート画面にクラスター用のタイルが追加されます。アイコンはクラスターがプロビジョニング中であることを示し、プロビジョニングが完了すると、[HDInsight] アイコンを表示するように変化します。

	| プロビジョニング中 | プロビジョニング完了 |
	| ------------------ | --------------------- |
	| ![スタート画面のプロビジョニング中インジケーター](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![プロビジョニングされたクラスターのタイル](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]クラスターが作成されるまで、通常は約 15 分かかります。プロビジョニング プロセスをチェックするには、スタート画面のタイル、またはページの左側の **[通知]** エントリを使用します。

11. プロビジョニングが完了したら、スタート画面でクラスター用のタイルをクリックして、クラスター ブレードを起動します。クラスター ブレードには、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などのクラスターに関する基本情報が表示されます。

	![クラスター ブレード](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "クラスターのプロパティ")

	次の説明を参照して、このブレードの上部と **[要点]** セクションにあるアイコンについて理解してください。

	* **[設定]** と **[すべての設定]**: クラスター用の **[設定]** ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。

	* **[ダッシュボード]**、**[クラスター ダッシュボード]**、**[URL]**: これらはすべて、クラスター ダッシュボードにアクセスする方法です。クラスター ダッシュボードは、クラスターに関するジョブを実行するための Web ポータルです。

	* **[Secure Shell]**: SSH を使用してクラスターにアクセスするために必要な情報です。

	* **[削除]**: HDInsight クラスターを削除します。

	* **クイック スタート** (![雲と雷のアイコン = クイック スタート](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。

	* **ユーザー** (![ユーザー アイコン](./media/hdinsight-hadoop-provision-linux-clusters/users.png)): このクラスターの_ポータル管理_に関する権限を、Azure サブスクリプションの他のユーザーに対して設定できます。

		> [AZURE.IMPORTANT]これは、Azure プレビュー ポータルでのこのクラスターへのアクセスと権限_のみ_に影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。

	* **タグ** (![タグ アイコン](./media/hdinsight-hadoop-provision-linux-clusters/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスで共通の値を使用できます。

### <a id="cli"></a>Azure CLI の使用

Azure CLI は、Azure サービスを管理できる、プラットフォームに依存しないコマンドライン ユーティリティです。Azure リソース管理テンプレートと共に使用し、HDInsight クラスター、関連ストレージ アカウント、その他のサービスをプロビジョニングできます。

Azure リソース管理テンプレートは、__リソース グループ__とその中のすべてのリソース (HDInsight など) について記述する JSON ドキュメントです。 このテンプレート基準の手法では、1 つのテンプレートで HDInsight に必要なすべてのリソースを定義でき、グループに変更を適用する__デプロイ__を介して、グループに対する変更をまとめて管理できます。

次の手順では、Azure CLI とテンプレートを利用して新しい HDInsight クラスターを段階的に作成できます。

1. まだインストールされていない場合、[Azure CLI のインストールと構成](../xplat-cli-install.md)に関するドキュメントの手順に従ってください。

2. コマンドライン、ターミナル、シェルから、次のコマンドを利用して Azure サブスクリプションを認証します。

        azure login

    名前とパスワードを入力するように求められます。複数の Azure サブスクリプションがある場合、`azure account set <subscriptionname>` を利用し、Azure CLI コマンドが使用するサブスクリプションを設定できます。

3. 次のコマンドで Azure リソース マネージャー モードに切り替えます。

        azure config mode arm

4. HDInsight クラスターのテンプレートを作成します。基本的なテンプレートの例を次に示します。

    * [Linux ベースのクラスター、SSH パブリック キーを使用](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpublickey)
    * [Linux ベースのクラスター、SSH アカウントのパスワードを使用](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpassword)

    これらのテンプレートのいずれでも、HDInsight が使用する既定の Azure ストレージ アカウントが作成されます。

    必要なファイルは __azuredeploy.json__ と __azuredeploy.parameters.json__ です。

5. __azuredeploy.parameters.json__ ファイルをエディターで開き、`parameters` セクションの項目に値を指定します。

    * __location__: リソースが作成されるデータ センター。許可される場所の一覧については、__azuredeploy.json__ ファイルの `location` セクションを参照してください。
    * __clusterName__: HDInsight クラスターの名前。この名前は一意にする必要があります。一意にしなければデプロイメントは失敗します。
    * __clusterStorageAccountName__: HDInsight に対して作成される Azure Storage のアカウント名。この名前は一意にする必要があります。一意にしなければデプロイメントは失敗します。
    * __clusterLoginPassword__: クラスターの管理者ユーザーのパスワード。これは Web サイトやクラスターの REST サービスのアクセスに使用されるので、安全なパスワードにする必要があります。
    * __sshUserName__: このクラスターに作成する最初の SSH ユーザーの名前。SSH はこのアカウントでクラスターにリモート アクセスするために使用されます。この名前は一意にする必要があります。クラスターで既に使用されているアカウントの名前にすることはできません。無効な名前には root、storm、hbase などがあります。
    * __sshPublicKey__: SSH 公開キーを必要とするテンプレートを使用している場合、この行に公開キーを追加する必要があります。パブリック キーを生成し、それを使用する方法については、次の記事を参照してください。

        * [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: SSH パスワードを必要とするテンプレートを使用している場合、この行にパスワードを追加する必要があります。

    完了後、ファイルを保存し、終了します。

5. 次を利用して空のリソース グループを作成します。__RESOURCEGROUPNAME__ を、このグループに使用する名前で置き換えます。__LOCATION__ を、グループを作成するデータ センターで置き換えます。

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]場所の名前にスペースが含まれる場合、それを二重引用符で囲みます。たとえば、"South Central US" のようになります。

6. 次のコマンドを利用し、このリソース グループの最初のデプロイメントを作成します。__PATHTOTEMPLATE__ を __azuredeploy.json__ テンプレート ファイルのパスで置き換えます。__PATHTOPARAMETERSFILE__ を __azuredeploy.parameters.json__ ファイルのパスで置き換えます。__RESOURCEGROUPNAME__ を前の手順で作成したグループの名前で置き換えます。

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    デプロイメントが承認されると、「`group deployment create command ok`」のようなメッセージが表示されます。

7. デプロイメントの完了には約 15 分かかることがあります。次のコマンドでデプロイメントに関する情報を表示できます。__RESOURCEGROUPNAME__ を前の手順で使用したリソース グループの名前で置き換えます。

        azure group log show -l RESOURCEGROUPNAME

    デプロイメント中にエラーが発生した場合、次のコマンドでエラーに関する詳細を取得できます。

        azure group log show -l -v RESOURCEGROUPNAME

### <a id="powershell"></a>Azure PowerShell の使用

Azure PowerShell は、Azure のワークロードのデプロイと管理を制御し自動化するために使用できる強力なスクリプティング環境です。このセクションでは、Azure PowerShell を使用して HDInsight クラスターをプロビジョニングする方法について説明します。ワークステーションを構成して HDInsight Windows Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../install-configure-powershell.md)」をご覧ください。HDInsight で Azure PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)」をご覧ください。HDInsight Windows PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn858087.aspx)」をご覧ください。


Azure PowerShell を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- Azure リソース グループの作成
- Azure のストレージ アカウントの作成
- Azure BLOB コンテナーの作成
- HDInsight クラスターの作成

Linux クラスターをプロビジョニングするために設定する必要がある最も重要な 2 つのパラメーターは、OS の種類と SSH ユーザーの詳細を指定するパラメーターです。

- **-OSType** パラメーターを **Linux** として指定していることを確認します。
- クラスターのリモート セッションで SSH を使用するには、SSH ユーザー パスワードまたは SSH 公開キーのいずれかを指定します。SSH ユーザー パスワードと SSH 公開キーを両方とも指定すると、キーが無視されます。リモート セッションで SSH キーを使用する場合は、入力を求められたら、空の SSH パスワードを指定する必要があります。HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)


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
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]**$clusterCredentials** に指定する値は、クラスターの Hadoop ユーザー アカウントの作成に使用されます。このアカウントを使用してクラスターに接続します。**$sshCredentials** に指定する値は、クラスターの SSH ユーザーの作成に使用されます。このアカウントを使用してクラスターでリモート SSH セッションを開始し、ジョブを実行します。Azure ポータルから [簡易作成] オプションを使用してクラスターをプロビジョニングする場合、既定の Hadoop ユーザー名は "admin"、既定の SSH ユーザー名は "hdiuser" になります。

クラスターのプロビジョニングが完了するまでに数分かかる場合があります。

![HDI.CLI.Provision][image-hdi-ps-provision]



###<a id="sdk"></a>HDInsight .NET SDK を使用する場合
HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。Visual Studio コンソール アプリケーションを作成し、クラスターを作成するためのコードを貼り付けるには、次の手順に従います。

**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio 2013 または 2015 を開きます。
2. 次の設定を使用して、新しい Visual Studio プロジェクトを作成します。

	|プロパティ|値|
	|--------|-----|
	|テンプレート|テンプレート/Visual C#/Windows/コンソール アプリケーション|
	|名前|CreateHDICluster|

5. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。
6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	これらのコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

6. ソリューション エクスプ ローラーで、**Program.cs** をダブルクリックして開き、次のコードを貼り付け、変数の値を指定します。

		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const string NewClusterVersion = "3.2";
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;

		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

		        private const string NewClusterSshUserName = "sshuser";
		        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
					Comment: ""rsa-key-20150731""
					AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
					gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
					yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
					WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
					pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
					zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
					---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. クラス メンバーの値を置き換えます。

7. **F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。Azure アカウントの資格情報の入力も求められます。HDInsight クラスターの作成は数分かかる場合があります。



##<a id="nextsteps"></a>次のステップ
この記事では、HDInsight Hadoop クラスターを Linux でプロビジョニングする方法をいくつか説明しました。詳細については、次の記事を参照してください。

- [Linux での HDInsight の使用](hdinsight-hadoop-linux-information.md): Linux で HDInsight クラスターを操作するとはどういったことかを把握できます。
- [Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md) - Ambari Web または Ambari REST API を使用して、HDInsight クラスターで Linux ベースの Hadoop を監視し、管理する方法を説明します。
- [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md): さまざまな方法によるクラスターでの MapReduce ジョブの実行について説明します。
- [HDInsight での Hive の使用](hdinsight-use-hive.md): クラスターで Hive クエリを実行するその他の方法を説明します。
- [HDInsight での Pig の使用](hdinsight-use-pig.md): さまざまな方法によるクラスターでの Pig ジョブの実行について説明します。
- [HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md): HDInsight で Azure BLOB ストレージを使用して HDInsight クラスター用にデータを格納する方法を説明します。
- [HDInsight へのデータのアップロード](hdinsight-upload-data.md): HDInsight クラスター用に Azure BLOB ストレージに格納されたデータの操作方法を説明します。


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

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

<!---HONumber=Oct15_HO1-->