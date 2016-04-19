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
	ms.date="03/30/2016"
	ms.author="larryfr"/>

#Azure ポータルを使用した HDInsight での Hadoop クラスターの管理

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-portal-management-selector.md)]


[Azure ポータル][preview-portal]を使用すると、Azure HDInsight に Linux ベースの Hadoop クラスターをプロビジョニングして管理できます。

> [AZURE.NOTE] このドキュメントの手順は Linux ベースの Hadoop クラスターの使用に固有のものです。Windows ベースのクラスターの使用については、「[Azure ポータルを使用した HDInsight の Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md)」を参照してください。


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## HDInsight を管理するためのその他のツール
Azure ポータル以外にも、HDInsight を管理するツールが用意されています。

- [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md): Azure CLI はクロス プラットフォームのコマンド ライン ツールであり、Azure サービスを管理できます

- [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md): Azure PowerShell には、Azure サービスを管理するための PowerShell コマンドレットが用意されています

## 前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

## HDInsight クラスターの作成

HDInsight クラスターの作成に Azure ポータルを使用できます。詳しくは、[Azure ポータルを使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)に関するドキュメントを参照してください。

## クラスターの管理

Azure ポータルでクラスターを選択すると、名前、リソース グループ、オペレーティング システム、クラスターの URL (Linux ベース クラスターの Ambari Web へのアクセスに使用) など、クラスターに関する重要な情報が表示されます。

![クラスターの詳細](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

次の説明を参照して、このブレードの上部、__[要点]__ セクション、__[クイック リンク]__ セクションにあるアイコンを理解してください。

* __設定__と__すべての設定__: クラスター用の __[設定]__ ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。

* __[ダッシュボード]__、__[クラスター ダッシュボード]__、__[URL]__: これらはすべてクラスター ダッシュボードにアクセスするために使用します。クラスターの種類に基づいて、クラスターのダッシュボードの一覧が表示されます。たとえば、Spark クラスター タイプでは、__[ダッシュボード]__ アイコンを選択するとダッシュボードの一覧が表示され、Hadoop クラスターでは Ambari Web UI が表示されます。

* __[Secure Shell]__: SSH を使用してクラスター ヘッド ノードにアクセスするために必要な情報です。

* __[クラスターのスケーリング]__: このクラスターの worker ノードの数を変更できます。

* __[削除]__: HDInsight クラスターを削除します。

* __[クイック スタート]__ (![雲と雷のアイコン = クイック スタート](./media/hdinsight-administer-use-portal-linux/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。

* __[ユーザー]__ (![ユーザー アイコン](./media/hdinsight-administer-use-portal-linux/users.png)): このクラスターの_ポータル管理_に対する、Azure サブスクリプションの他のユーザーの権限を設定できます。

	> [AZURE.IMPORTANT] これは、Azure ポータルでのこのクラスターへのアクセスと権限だけに影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。

* __[タグ]__ (![タグ アイコン](./media/hdinsight-administer-use-portal-linux/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。

* __[ドキュメント]__: Azure HDInsight のドキュメントへのリンクです。

> [AZURE.IMPORTANT] HDInsight クラスターによって提供されるサービスを管理するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、「[Ambari を使用した HDInsight クラスターの管理 (プレビュー)](hdinsight-hadoop-manage-ambari.md)」をご覧ください。

### 設定

__[設定]__ アイコンまたは __[すべての設定]__ リンクを選択すると、[設定] ブレードが表示されます。前に説明した __[Essentials]__ 領域で利用できるいくつかの機能 (スケーリングまたは Secure Shell 情報など) をここでも利用できます。また、[設定] から以下にアクセスすることもできます。

* __監査ログ__: クラスターの正常性の問題を診断する際に役立つログに記録された情報。

* __クラスター ログイン__: HTTPS を使用したクラスターへのアクセスに使用できる__クラスター ログイン ユーザー名__と__リモート アドレス__を表示します。

* __外部メタストア__: クラスターによって使用される外部メタストアに関する情報を表示します。クラスターの構成時にカスタム メタストアを構成しなかった場合、情報は表示されません。

* __スクリプト アクション__: このクラスターで実行されたスクリプト アクションに関する情報を表示します。新しいスクリプト アクションを実行したり、以前に実行されたスクリプト アクションを永続化または削除したりすることもできます。スクリプト アクションの詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。

* __アプリ__: クラスターにインストールされたアプリに関する情報を表示します。また、Azure Marketplace からクラスターに新しいアプリケーションを追加することもできます。

* __Azure ストレージ キー__: クラスターで使用される Azure Storage アカウントに関する情報を表示します。ストレージ アカウントを選択すると、選択したアカウントの [ストレージ アカウント] ブレードが読み込まれます。

* __クラスター AAD ID__: この HDInsight クラスターのサービス プリンシパルに関する情報を表示します。サービス プリンシパルは Azure Data Lake Store にアクセスするために使用します。クラスターの作成中にクラスターを Azure Data Lake Store に関連付けなかった場合、このブレード上のエントリは __[未構成]__ と表示されます。HDInsight での Azure Data Lake Store の使用について詳しくは、「[Data Lake Store で HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。

### <a name="scaling"></a>スケーリング

ポータルを使用してクラスターを拡張するには、HDInsight クラスターを選択し、__[クラスターのスケーリング]__ を選択します。クラスターに設定する __[worker ノード数]__ を入力し、__[保存]__ をクリックします。

![スケーリング UI の画像](./media/hdinsight-administer-use-portal-linux/scaling.png)

スケーリング操作の詳細については、「[Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md#scaling)」をご覧ください。

## クラスターの監視

HDInsight クラスター ブレードの __[使用状況]__ セクションには、サブスクリプションで HDInsight 用に使用できるコアの数、このクラスターに割り当てられているコアの数、およびこのクラスター内のノードへのコアの割り当て方法に関する情報が表示されます。

![使用状況に関する情報](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT] HDInsight クラスターによって提供されるサービスを監視するには、Ambari Web または Ambari REST API を使用する必要があります。Ambari の使用の詳細については、「[Ambari を使用した HDInsight クラスターの管理 (プレビュー)](hdinsight-hadoop-manage-ambari.md)」をご覧ください。

## 次のステップ
この記事では、Azure ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。詳細については、次の記事を参照してください。

* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)
* [プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->