<properties
   	pageTitle="ポータルを使用して Linux ベースの Hadoop、HBase、Storm クラスターを HDInsight に作成する | Microsoft Azure"
   	description="Web ブラウザーと Azure プレビュー ポータルを使用して、Linux ベースの Hadoop、HBase、Storm クラスターを HDInsight に作成する方法を説明します。"
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
   	ms.date="10/14/2015"
   	ms.author="nitinme"/>


#Azure プレビュー ポータルを使用して HDInsight に Linux ベースのクラスターを作成する

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure プレビュー ポータルは、Microsoft Azure クラウドでホストされるサービスやリソースの Web ベースの管理ツールです。このドキュメントでは、プレビュー ポータルと Web ブラウザーを使用して、Linux ベースの HDInsight クラスターを新たに作成する際に役立つ情報を紹介しています。

##前提条件

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- __最新の Web ブラウザー__。Azure プレビュー ポータルには、HTML5 と Javascript が使用されています。Web ブラウザーのバージョンが低いと正しく機能しない場合があります。

##クラスターの作成

1. [Azure プレビュー ポータル](https://portal.azure.com)にサインインします。

2. **[新規]**、**[データ分析]**、**[HDInsight]** の順にクリックします。

    ![Azure プレビュー ポータルでの新しいクラスターの作成](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Azure プレビュー ポータルでの新しいクラスターの作成")

3. **[クラスター名]** を入力し、**[クラスターの種類]** で **[Hadoop]** を選択し、**[クラスターのオペレーティング システム]** ボックスの一覧から **[Ubuntu]** を選択します。クラスターを使用できる場合は、クラスター名の横に緑色のチェック マークが表示されます。

    > [AZURE.NOTE]HBase または Storm のクラスターをプロビジョニングするには、**[クラスターの種類]** ボックスの一覧で該当する値を選択します。

    ![クラスターの名前と種類の入力](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.2.png "クラスターの名前と種類の入力")

4. 複数のサブスクリプションがある場合は、**[サブスクリプション]** エントリをクリックし、クラスターで使用する Azure サブスクリプションを選択します。

5. **[リソース グループ]** をクリックして既存のリソース グループの一覧を表示し、その中にクラスターを作成するグループを選択します。または、**[新規作成]** をクリックし、新しいリソース グループの名前を入力します。新しいグループ名を使用できる場合は、緑のチェック マークが表示されます。

	> [AZURE.NOTE]このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。

6. **[資格情報]** をクリックし、管理ユーザーのパスワードを入力します。さらに、SSH ユーザーを認証するために使用される **[SSH ユーザー名]** と、**[パスワード]** または **[公開キー]** のどちらかを入力する必要があります。公開キーを使用することをお勧めします。下部にある **[選択]** をクリックして資格情報の構成を保存します。

	![クラスターの資格情報の指定](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "クラスターの資格情報の指定")

	HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)


7. **[データ ソース]** をクリックし、クラスターの既存のデータ ソースを選択するか、新しいデータ ソースを作成します。

	![[データ ソース] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "データ ソース構成の指定")

	現在、HDInsight クラスターのデータ ソースとして Azure ストレージ アカウントを選択できます。次の説明を参照して、**[データ ソース]** ブレードのエントリを理解してください。

	- **選択方法**: すべてのサブスクリプションのストレージ アカウントを参照できるようにする場合は、**[すべてのサブスクリプションから]** を設定します。既存のストレージ アカウントの **[ストレージ名]** と **[アクセス キー]** を入力する場合は、**[アクセス キー]** を設定します。

	- **ストレージ アカウントの選択/新規作成**: クラスターに関連付ける既存のストレージ アカウントを参照して選択する場合は **[ストレージ アカウントの選択]** をクリックします。新しいストレージ アカウントを作成する場合は **[新規作成]** をクリックします。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。

	- **既定のコンテナーの選択**。 これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、コンテナーが特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。

	- **場所**: ストレージ アカウントが存在するリージョン、またはその中にストレージ アカウントが作成されるリージョン。

		> [AZURE.IMPORTANT]既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。クラスターと既定のデータ ソースは、同じリージョンに存在する必要があります。

	**[選択]** をクリックしてデータ ソースの構成を保存します。

8. **[ノード価格レベル]** をクリックして、このクラスターのために作成されるノードに関する情報を表示します。クラスターで必要なワーカー ノードの数を設定します。クラスターの推定コストがブレード内に表示されます。

	![[ノード価格レベル] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "クラスター ノード数の指定")

	**[選択]** をクリックして、ノードの価格構成を保存します。

9. **[オプションの構成]** をクリックし、クラスターのバージョンを選択し、その他のオプションの設定を構成します。その他のオプションには、**Virtual Network** に参加すること、Hive と Oozie 用のデータを保持する**外部メタストア** を設定すること、Script Action を使用してカスタム コンポーネントをインストールするようにクラスターをカスタマイズすること、クラスターで追加のストレージ アカウントを使用することなどがあります。

	* **[HDInsight のバージョン]** ボックスの一覧から、クラスターで使用するバージョンを選択します。詳細については、「[HDInsight クラスターのバージョン](hdinsight-component-versioning.md)」をご覧ください。

	* **[Virtual Network]**: クラスターを仮想ネットワークに配置する場合は、Azure Virtual Network とサブネットを選択します。

		![[仮想ネットワーク] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "仮想ネットワークの詳細の指定")

    	>[AZURE.NOTE]Windows ベースの HDInsight クラスターは、クラシック仮想ネットワークにのみ配置できます。

	* **[外部メタストア]** をクリックし、クラスターに関連付けられた Hive と Oozie のメタデータを保存するために使用する SQL データベースを指定します。

		![[カスタム メタストア] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "外部メタストアの指定")

		**[Hive メタデータで既存の SQL DB を使用する]** で、**[はい]** をクリックし、SQL データベースを選択し、データベースのユーザー名/パスワードを指定します。**[Oozie メタデータで既存の SQL DB を使用する]** 場合は、これらの手順を繰り返します。**[オプションの構成]** ブレードに戻るまで **[選択]** をクリックします。

		>[AZURE.NOTE]メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL Database インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

	* クラスターを作成するときに、カスタム スクリプトを使用してクラスターをカスタマイズする場合は、**[スクリプト アクション]** をクリックします。スクリプト アクションの詳細については、「[Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。[Script Action] ブレードで、次の画面キャプチャに示すように、詳細を指定します。

		![[Script ction] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "スクリプト アクションの指定")

	* **[Azure ストレージ キー]** をクリックして、クラスターに関連付ける追加のストレージ アカウントを指定します。**[Azure ストレージ キー]** ブレードで、**[ストレージ キーの追加]** をクリックし、既存のストレージ アカウントを選択するか新しいアカウントを作成します。

		![[追加ストレージ] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "追加ストレージ アカウントの指定")

		**[新しい HDInsight クラスター]** ブレードに戻るまで **[選択]** をクリックします。

10. **[新しい HDInsight クラスター]** ブレードで、**[スタート画面にピン留めする]** が選択されていることを確認し、**[作成]** をクリックします。これでクラスターが作成され、Azure ポータルのスタート画面にクラスター用のタイルが追加されます。アイコンはクラスターがプロビジョニング中であることを示し、プロビジョニングが完了すると、[HDInsight] アイコンを表示するように変化します。

	| プロビジョニング中 | プロビジョニング完了 |
	| ------------------ | --------------------- |
	| ![スタート画面のプロビジョニング中インジケーター](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![プロビジョニングされたクラスターのタイル](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE]クラスターが作成されるまで、通常は約 15 分かかります。プロビジョニング プロセスをチェックするには、スタート画面のタイルまたはページの左側の **[通知]** エントリを使用します。

11. プロビジョニングが完了したら、スタート画面でクラスター用のタイルをクリックして、クラスター ブレードを起動します。クラスター ブレードには、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などのクラスターに関する基本情報が表示されます。

	![クラスター ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "クラスターのプロパティ")

	このブレードの上部と **[要点]** セクションにあるアイコンについては、次の説明を参照してください。

	* **設定**と**すべての設定**: クラスター用の **[設定]** ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。

	* **[ダッシュボード]**、**[クラスター ダッシュボード]**、**[URL]**: これらはすべて、クラスター ダッシュボードにアクセスする方法です。クラスター ダッシュボードは、クラスターに関するジョブを実行するための Web ポータルです。

	* **[Secure Shell]**: SSH を使用してクラスターにアクセスするために必要な情報です。

	* **削除**: HDInsight クラスターを削除します。

	* **クイック スタート** (![雲と雷のアイコン = クイック スタート](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。

	* **ユーザー** (![ユーザー アイコン](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): このクラスターの_ポータル管理_に対する権限を、Azure サブスクリプションの他のユーザーに設定できます。

		> [AZURE.IMPORTANT]これは、Azure プレビュー ポータルでのこのクラスターへのアクセスと権限_のみ_に影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。

	* **タグ** (![タグ アイコン](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスで共通の値を使用できます。

##次のステップ

HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックをご覧ください。

###Hadoop クラスター

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

###HBase クラスター

* [HDInsight での Hbase の使用](hdinsight-hbase-tutorial-get-stared-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux)

###Storm クラスター

* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](hdinsight-storm-develop-python.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Oct15_HO3-->