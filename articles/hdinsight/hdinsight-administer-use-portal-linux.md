<properties
	pageTitle="Azure ポータルを使用した HDInsight での Hadoop クラスターの管理 | Microsoft Azure"
	description="HDInsight サービスを管理する方法を学習します。HDInsight クラスターを作成し、対話型 JavaScript コンソールを開いて、Hadoop コマンド コンソールを開きます。"
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
	ms.date="08/06/2015"
	ms.author="larryfr"/>

# Azure プレビュー ポータルを使用した HDInsight での Hadoop クラスターの管理

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-portal-management-selector.md)]


[Azure プレビュー ポータル][preview-portal]を使用すると、Azure HDInsight に Linux ベースの Hadoop クラスターをプロビジョニングして管理できます。

> [AZURE.NOTE]このドキュメントの手順は Linux ベースの Hadoop クラスターの使用に固有のものです。Windows ベースのクラスターの使用については、「[Azure プレビュー ポータルを使用した HDInsight の Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md)」を参照してください。


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## HDInsight を管理するためのその他のツール
Azure ポータル以外にも、HDInsight を管理するツールが用意されています。

- [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md): Azure CLI はクロス プラットフォームのコマンド ライン ツールであり、Azure サービスを管理できます

- [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md): Azure PowerShell には、Azure サービスを管理するための PowerShell コマンドレットが用意されています

##前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

##HDInsight クラスターのプロビジョニング

以下の手順を使用して、Azure ポータルから HDInsight クラスターをプロビジョニングできます。

1. [Azure プレビュー ポータル][preview-portal]にサインインします。

2. **[新規]**、__[データ分析]__、__[HDInsight]__ の順にクリックします。

	![Azure プレビュー ポータルでの新しいクラスターの作成](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. __[クラスター名]__ を入力し、作成する __[クラスターの種類]__ を選択します。クラスターを使用できる場合は、__[クラスター名]__ の横に緑色のチェック マークが表示されます。

	![クラスター名、クラスターの種類、および OS の種類](./media/hdinsight-administer-use-portal-linux/clustername.png)
	
	> [AZURE.NOTE]Linux ベースの HDInsight プレビューでは、Hadoop クラスター タイプのみを使用できます。

4. 複数のサブスクリプションがある場合は、__[サブスクリプション]__ エントリを選択し、クラスターで使用する Azure サブスクリプションを選択します。

5. __[リソース グループ]__ では、エントリを選択して既存のリソース グループの一覧を表示し、その中にクラスターを作成するグループを 1 つ選択できします。または、__[新規作成]__ をクリックし、新しいリソース グループの名前を入力できます。新しいグループ名を使用できる場合は、緑のチェック マークが表示されます。

	> [AZURE.NOTE]このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。

6. __[資格情報]__ を選択し、__[クラスターのユーザー名]__ と __[クラスターのログイン パスワード]__ を入力します。SSH ユーザーを認証するために使用される __[SSH ユーザー名]__ と、__[パスワード]__ または __[公開キー]__ のどちらかを入力する必要があります。最後に、__[選択]__ ボタンを使用して資格情報を設定します。このドキュメントではリモート デスクトップは使用しないため、無効にしておくことができます。

	![[クラスターの資格情報] ブレード](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows)

6. __[データ ソース]__ では、エントリを選択することで既存のデータ ソースを選択するか、新しいデータ ソースを作成できます。

	![[データ ソース] ブレード](./media/hdinsight-administer-use-portal-linux/datasource.png)
	
	現在、HDInsight クラスターのデータ ソースとして Azure ストレージ アカウントを選択できます。次の説明を参照して、__[データ ソース]__ ブレードのエントリを理解してください。
	
	- __選択方法__: サブスクリプションのストレージ アカウントを参照できるようにする場合は、__[すべてのサブスクリプションから]__ を設定します。既存のストレージ アカウントの __[ストレージ名]__ と __[アクセス キー]__ を入力する場合は、__[アクセス キー]__ を設定します。
	
	- __[新規作成]__: これを使用して、新しいストレージ アカウントを作成します。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。
	
	- __既定のコンテナーの選択__。 これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。
	
	- __場所__: ストレージ アカウントが存在するリージョン、またはその中にストレージ アカウントが作成されるリージョン。
	
		> [AZURE.IMPORTANT]既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。クラスターと既定のデータ ソースは、同じリージョンに存在する必要があります。
		
	- __[選択]__: これを使用してデータ ソースの構成を保存します。
	
7. __[ノード価格レベル]__ を選択して、このクラスターのために作成されるノードに関する情報を表示します。既定では、worker ノードの数は __4__ に設定されます。

	クラスターの推定コストがブレードの下部に表示されます。

	![[ノード価格レベル] ブレード](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)
	
	__[選択]__ ボタンを使用して、__ノード価格レベル__情報を保存します。

8. __[オプションの構成]__ を選択します。このブレードでは、以下の項目を構成できます。

	* __HDInsight のバージョン__: クラスターに使用する HDInsight のバージョンです。HDInsight のバージョンの詳細については、「[HDInsight で提供されるクラスター バージョンの新機能](hdinsight-component-versioning.md)」を参照してください。
	* __外部メタストア__: Oozie と Hive の構成情報を格納するために使用する SQL Database を選択できます。これにより、クラスターを削除して再作成するときに構成を再利用できるので、Hive と Oozie の構成をその都度作成し直す必要がなくなります。**__Azure ストレージ キー__: 追加のストレージ アカウントを HDInsight サーバーと関連付けることができます。

		> [AZURE.NOTE]HDInsight は、この構成セクションを使用して追加される既定のデータ ストアとして使用される Azure ストレージ アカウント、またはパブリックにアクセスできる Azure ストレージ アカウントのみにアクセスできます。

	![[オプションの構成] ブレード](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

9. __[スタート画面にピン留めする]__ が選択されていることを確認し、__[作成]__ をクリックします。これでクラスターが作成され、Azure ポータルのスタート画面にクラスター用のタイルが追加されます。アイコンはクラスターがプロビジョニング中であることを示し、プロビジョニングが完了すると、[HDInsight] アイコンを表示するように変化します。

	| プロビジョニング中 | プロビジョニング完了 |
	| ------------------ | --------------------- |
	| ![スタート画面のプロビジョニング中インジケーター](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![プロビジョニングされたクラスターのタイル](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE]クラスターが作成されるまで、通常は約 15 分かかります。プロビジョニング プロセスをチェックするには、スタート画面のタイルまたはページの左側の __[通知]__ エントリを使用します。

##クラスターの管理

Azure プレビュー ポータルでクラスターを選択すると、名前、リソース グループ、オペレーティング システム、クラスターのダッシュボードの URL (Linux クラスターの Ambari Web へのアクセスに使用) など、クラスターに関する重要な情報が表示されます。

![クラスターの詳細](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

次の説明を参照して、このブレードの上部、__[要点]__ セクション、__[クイック リンク]__ セクションにあるアイコンを理解してください。

* __設定__と__すべての設定__: クラスター用の __[設定]__ ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。

* __ダッシュボード__、__クラスター ダッシュボード__、__URL__: これらはすべてクラスター ダッシュボードにアクセスする方法です。クラスター ダッシュボードは、Linux ベースのクラスター用の Ambari Web です。

* __Secure Shell__: SSH を使用してクラスターにアクセスするために必要な情報です。

* __クラスターのスケーリング__: このクラスターの worker ノードの数を変更できます。

* __削除__: HDInsight クラスターを削除します。

* __クイック スタート__ (![雲と雷のアイコン = クイック スタート](./media/hdinsight-administer-use-portal-linux/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。

* __ユーザー__ (![ユーザー アイコン](./media/hdinsight-administer-use-portal-linux/users.png)): このクラスターの_ポータル管理_に対する権限を、Azure サブスクリプションの他のユーザーに設定できます。

	> [AZURE.IMPORTANT]これは Azure プレビュー ポータルでのこのクラスターへのアクセスと権限のみに影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。

* __タグ__ (![タグ アイコン](./media/hdinsight-administer-use-portal-linux/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスで共通の値を使用できます。

* __ドキュメント__: Azure HDInsight のドキュメントへのリンクです。

> [AZURE.IMPORTANT]HDInsight クラスターによって提供されるサービスを管理するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、「[Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

##クラスターの監視

HDInsight クラスター ブレードの __[使用状況]__ セクションには、サブスクリプションで HDInsight 用に使用できるコアの数、このクラスターに割り当てられているコアの数、およびこのクラスター内のノードへのコアの割り当て方法に関する情報が表示されます。

![使用状況に関する情報](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT]HDInsight クラスターによって提供されるサービスを監視するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、「[Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

##次のステップ
この記事では、Azure ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。詳細については、次の記事を参照してください。

* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)
* [プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight の概要](../hdinsight-get-started.md)
* [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=September15_HO1-->