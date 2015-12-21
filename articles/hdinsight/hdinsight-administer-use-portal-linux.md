<properties
	pageTitle="Azure ポータルを使用した HDInsight での Linux ベースの Hadoop クラスターの管理 | Microsoft Azure"
	description="Azure ポータルを使用して Linux ベースの HDInsight クラスターを作成および管理する方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="larryfr"/>

# Azure ポータルを使用した HDInsight での Hadoop クラスターの管理

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-portal-management-selector.md)]


[Azure ポータル][preview-portal]を使用すると、Azure HDInsight に Linux ベースの Hadoop クラスターをプロビジョニングして管理できます。

> [AZURE.NOTE]このドキュメントの手順は Linux ベースの Hadoop クラスターの使用に固有のものです。Windows ベースのクラスターの使用については、「[Azure ポータルを使用した HDInsight の Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md)」を参照してください。


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## HDInsight を管理するためのその他のツール
Azure ポータル以外にも、HDInsight を管理するツールが用意されています。

- [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md): Azure CLI はクロス プラットフォームのコマンド ライン ツールであり、Azure サービスを管理できます

- [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md): Azure PowerShell には、Azure サービスを管理するための PowerShell コマンドレットが用意されています

## 前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

## HDInsight クラスターのプロビジョニング

以下の手順を使用して、Azure ポータルから HDInsight クラスターをプロビジョニングできます。

1. [Azure ポータル][preview-portal]にサインインします。

2. **[新規]**、__[データ分析]__、__[HDInsight]__ の順にクリックします。

	![Creating a new cluster in the Azure Portal](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. __[クラスター名]__ を入力し、作成する __[クラスターの種類]__ を選択します。クラスターを使用できる場合は、__[クラスター名]__ の横に緑色のチェック マークが表示されます。

	![クラスター名、クラスターの種類、および OS の種類](./media/hdinsight-administer-use-portal-linux/clustername.png)

4. 複数のサブスクリプションがある場合は、__[サブスクリプション]__ エントリを選択し、クラスターで使用する Azure サブスクリプションを選択します。

5. __[リソース グループ]__ では、エントリを選択して既存のリソース グループの一覧を表示し、クラスターを作成するグループを選択できます。または、__[新規作成]__ をクリックし、新しいリソース グループの名前を入力できます。新しいグループ名を使用できる場合は、緑のチェック マークが表示されます。

	> [AZURE.NOTE]このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。

6. __[資格情報]__ を選択し、__[クラスターのログイン ユーザー名]__ に対応する __[クラスターのログイン パスワード]__ を入力します。さらに、SSH ユーザーを認証するために使用される __[SSH ユーザー名]__ と、__[パスワード]__ または __[公開キー]__ のどちらかを入力する必要があります。最後に、__[選択]__ ボタンをクリックして資格情報を設定します。このドキュメントではリモート デスクトップは使用しないため、無効にしておくことができます。

	![[クラスターの資格情報] ブレード](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)
    
    > [AZURE.NOTE]SSH はコマンドラインで HDInsight クラスターにリモート アクセスするために使用されます。ここで使用するユーザー名とパスワードまたは公開キーは、SSH でクラスターに接続するときに使用されます。

    HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

7. __[データ ソース]__ では、エントリを選択することで既存のデータ ソースを選択するか、新しいデータ ソースを作成できます。

	![[データ ソース] ブレード](./media/hdinsight-administer-use-portal-linux/datasource.png)

	現在、HDInsight クラスターのデータ ソースとして Azure ストレージ アカウントを選択できます。次の説明を参照して、__[データ ソース]__ ブレードのエントリを理解してください。

	- __[選択方法]__: サブスクリプションのストレージ アカウントを参照可能にする場合は、__[すべてのサブスクリプションから]__ を設定します。既存のストレージ アカウントの __[ストレージ名]__ と __[アクセス キー]__ を入力する場合は、__[アクセス キー]__ を設定します。

	- __新規作成__: これを使用して、新しいストレージ アカウントを作成します。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。

	- __[既定のコンテナーの選択]__: これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。

	- __[場所]__: ストレージ アカウントが存在する、または作成される地理的なリージョンです。

		> [AZURE.IMPORTANT]既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。クラスターと既定のデータ ソースは、同じリージョンに存在する必要があります。

	- __[選択]__: これを使用してデータ ソースの構成を保存します。

	
8. __[ノード価格レベル]__ を選択して、このクラスターに対して作成されるノードに関する情報を表示します。既定では、worker ノードの数は __4__ に設定されます。


	クラスターの推定コストがブレードの下部に表示されます。

	![[ノード価格レベル] ブレード](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)

	__[選択]__ ボタンを使用して、__[ノード価格レベル]__ 情報がを保存します。

9. __[オプションの構成]__ を選択します。このブレードでは、以下の項目を構成できます。

	* __[HDInsight のバージョン]__: クラスターに使用する HDInsight のバージョンです。HDInsight のバージョンの詳細については、[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md)に関するページを参照してください。

	* __[外部メタストア]__: Oozie と Hive の構成情報を格納するために使用する SQL Database を選択できます。これにより、クラスターを削除して再作成するときに構成を再利用できるので、Hive と Oozie の構成をその都度作成し直す必要がなくなります。

	* __[仮想ネットワーク]__: SQL Database や Azure 仮想マシンなど、その他のリソースと同じ仮想ネットワークに HDInsight クラスターを配置できます。仮想ネットワークにリソースを配置することにより、インターネットからの受信トラフィックを処理するパブリック ゲートウェイをバイパスして、リソースが相互に直接通信できるようになります。Azure の仮想ネットワークが HDInsight にもたらす利点の詳細については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」を参照してください。

		> [AZURE.IMPORTANT]HDInsight 構成から新しいネットワークを作成することはできないため、HDInsight クラスターを作成する前に Azure Virtual Network を作成する必要があります。
		>
		> 現時点 (2015 年 8 月 25 日) では、Azure Virtual Network 上に存在できる Linux ベースの HDInsight クラスターは 1 つだけという制限があります。
        >
        > Linux ベースの HDInsight で v1 (クラシック) Azure Virtual Network を使用することはできません。Azure ポータルでの HDInsight クラスターの作成プロセスで Virtual Network をオプションとして表示したり、Azure CLI または Azure PowerShell からクラスターを作成するときに Virtual Network を使用できるようにしたりするには、Virtual Network が v2 (Azure リソース マネージャー) である必要があります。
        >
        > v1 ネットワーク上にリソースがあり、HDInsight から仮想ネットワークを介してそれらのリソースに直接アクセスできるようにする場合、v2 Virtual Network を v1 Virtual Network に接続する方法については、「[従来の VNet を新しい VNet に接続する](../virtual-network/virtual-networks-arm-asm-s2s.md)」をご覧ください。この接続が確立されたら、v2 Virtual Network で HDInsight クラスターを作成できます。

	* __[スクリプト アクション]__: HDInsight クラスターをプロビジョニング中にカスタマイズする Bash スクリプトを指定できます。スクリプトには、(Hadoop を操作するグラフィカル クライアントである) [Hue をインストールするスクリプト](hdinsight-hadoop-hue-linux.md)などがあります。 スクリプト アクションの詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。

	* __[Azure ストレージ キー]__: 追加のストレージ アカウントを HDInsight サーバーに関連付けることができます。

		> [AZURE.NOTE]HDInsight は、この構成セクションを使用して追加される既定のデータ ストアとして使用される Azure ストレージ アカウント、またはパブリックにアクセスできる Azure ストレージ アカウントのみにアクセスできます。

	![[オプションの構成] ブレード](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

10. __[スタート画面にピン留めする]__ が選択されていることを確認し、__[作成]__ をクリックします。これでクラスターが作成され、Azure ポータルのスタート画面にクラスター用のタイルが追加されます。アイコンはクラスターがプロビジョニング中であることを示し、プロビジョニングが完了すると、[HDInsight] アイコンを表示するように変化します。

	| プロビジョニング中 | プロビジョニング完了 |
	| ------------------ | --------------------- |
	| ![スタート画面のプロビジョニング中インジケーター](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![プロビジョニングされたクラスターのタイル](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE]クラスターが作成されるまで、通常は約 15 分かかります。プロビジョニング プロセスを確認するには、スタート画面のタイルまたはページの左側の __[通知]__ エントリを使用します。

## クラスターの管理

Azure ポータルでクラスターを選択すると、名前、リソース グループ、オペレーティング システム、クラスターのダッシュボードの URL (Linux クラスターの Ambari Web へのアクセスに使用) など、クラスターに関する重要な情報が表示されます。

![クラスターの詳細](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

次の説明を参照して、このブレードの上部、__[要点]__ セクション、__[クイック リンク]__ セクションにあるアイコンを理解してください。

* __[設定]__ と __[すべての設定]__: クラスター用の __[設定]__ ブレードを表示します。このブレードを使用して、クラスターの詳しい構成情報にアクセスできます。

* __[ダッシュボード]__、__[クラスター ダッシュボード]__、__[URL]__: これらはすべてクラスター ダッシュボードにアクセスするために使用します。クラスター ダッシュボードは、Linux ベースのクラスター用の Ambari Web です。

* __[Secure Shell]__: SSH を使用してクラスターにアクセスするために必要な情報です。

* __[クラスターのスケーリング]__: このクラスターの worker ノードの数を変更できます。

* __[削除]__: HDInsight クラスターを削除します。

* __[クイック スタート]__ (![雲と雷のアイコン = クイック スタート](./media/hdinsight-administer-use-portal-linux/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。

* __[ユーザー]__ (![ユーザー アイコン](./media/hdinsight-administer-use-portal-linux/users.png)): このクラスターの_ポータル管理_に対する、Azure サブスクリプションの他のユーザーの権限を設定できます。

	> [AZURE.IMPORTANT]これは、Azure ポータルでのこのクラスターへのアクセスと権限だけに影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。

* __[タグ]__ (![タグ アイコン](./media/hdinsight-administer-use-portal-linux/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。

* __[ドキュメント]__: Azure HDInsight のドキュメントへのリンクです。

> [AZURE.IMPORTANT]HDInsight クラスターによって提供されるサービスを管理するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、「[Ambari を使用した HDInsight クラスターの管理 (プレビュー)](hdinsight-hadoop-manage-ambari.md)」をご覧ください。

### <a name="scaling"></a>スケーリング

ポータルを使用してクラスターを拡張するには、HDInsight クラスターを選択し、__[クラスターのスケーリング]__ を選択します。クラスターに設定する __[worker ノード数]__ を入力し、__[保存]__ をクリックします。

![スケーリング UI の画像](./media/hdinsight-administer-use-portal-linux/scaling.png)

スケーリング操作の詳細については、「[Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md#scaling)」をご覧ください。

## クラスターの監視

HDInsight クラスター ブレードの __[使用状況]__ セクションには、サブスクリプションで HDInsight 用に使用できるコアの数、このクラスターに割り当てられているコアの数、およびこのクラスター内のノードへのコアの割り当て方法に関する情報が表示されます。

![使用状況に関する情報](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT]HDInsight クラスターによって提供されるサービスを監視するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、「[Ambari を使用した HDInsight クラスターの管理 (プレビュー)](hdinsight-hadoop-manage-ambari.md)」をご覧ください。

## 次のステップ
この記事では、Azure ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。詳細については、次の記事を参照してください。

* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)
* [プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_1210_2015-->