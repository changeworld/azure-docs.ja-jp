<properties
   pageTitle="HDInsight で Hadoop クラスターを作成する | Microsoft Azure"
   	description="Azure ポータルを使用して Azure HDInsight のクラスターを作成する方法について説明します。"
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
   ms.date="09/02/2016"
   ms.author="jgao"/>

# Azure ポータルを使用した HDInsight での Windows ベースの Hadoop クラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-selector-create-clusters.md)]

Azure ポータルを使用した HDInsight で Hadoop クラスターを作成する方法について説明します。Azure リソースのプロビジョニングと管理は、Microsoft [Azure ポータル](../azure-portal-overview.md)を使って 1 か所で行うことができます。Azure ポータルは、HDInsight での Linux ベースまたは Windows ベースの Hadoop クラスターの作成に使用できるツールの 1 つです。その他のクラスター作成のツールと機能については、このページの上部にあるタブ セレクターをクリックするか、「[クラスターの作成方法](hdinsight-provision-clusters.md#cluster-creation-methods)」を参照してください。

##前提条件:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

この記事の手順を開始する前に、次の項目を用意する必要があります。

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

## クラスターの作成


**HDInsight クラスターを作成するには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]**、**[データ分析]**、**[HDInsight]** の順にクリックします。

    ![Azure ポータルでの新しいクラスターの作成](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Azure ポータルでの新しいクラスターの作成")

3. 次の値を入力または選択します。

  * **[クラスター名]**: クラスターの名前を入力します。その名前を使用できる場合は、クラスター名の横に緑色のチェック マークが表示されます。
  * **[クラスターの種類]**: **[Hadoop]** を選択します。その他のオプションには、**[HBase]**、 **[Storm]**、および **[Spark]** が含まれます。
  * **[クラスターのオペレーティング システム]**: **[Windows]** を選択します。Linux ベースのクラスターを作成するには、**[Linux]** を選択します。
  * **[バージョン]**: [HDInsight のバージョン](hdinsight-component-versioning.md)に関するページを参照してください。
  * **[サブスクリプション]**: このクラスターを作成するために使用する Azure サブスクリプションを選択します。
  * **[リソース グループ]**: 既存のリソース グループを選択するか、新しいリソース グループを作成します。このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。
  * **[資格情報]**: Hadoop ユーザー (HTTP ユーザー) のユーザー名とパスワードを構成します。クラスターのリモート デスクトップを有効にする場合は、リモート デスクトップ ユーザーのユーザー名とパスワード、アカウントの有効期限を構成する必要があります。下部にある **[選択]** をクリックして、変更を保存します。

	   	![クラスターの資格情報の指定](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "クラスターの資格情報の指定")

  * **[データ ソース]**: クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを新しく作成するか、既存の Azure ストレージ アカウントを選択します。

   		![[データ ソース] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "データ ソース構成の指定")

  		* **[選択方法]**: すべてのサブスクリプションのストレージ アカウントを参照できるようにする場合は、**[すべてのサブスクリプションから]** を設定します。既存のストレージ アカウントの **[ストレージ名]** と **[アクセス キー]** を入力する場合は、**[アクセス キー]** を設定します。
  		* **ストレージ アカウントの選択/新規作成**: クラスターに関連付ける既存のストレージ アカウントを参照して選択する場合は **[ストレージ アカウントの選択]** をクリックします。新しいストレージ アカウントを作成する場合は **[新規作成]** をクリックします。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。
  		* **[既定のコンテナーの選択]**: これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、コンテナーが特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。
  		* **場所**: ストレージ アカウントが存在するリージョン、またはその中にストレージ アカウントが作成されるリージョン。この場所は、クラスターの場所を決定します。クラスターとその既定のストレージ アカウントは、同じデータセンター内に配置されている必要があります。
  	
  * **ノード価格レベル**: クラスターで必要なワーカー ノードの数を設定します。クラスターの推定コストがブレード内に表示されます。
  

		![[ノード価格レベル] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "クラスター ノード数の指定")


  * **[オプションの構成]** をクリックし、クラスターのバージョンを選択して、その他のオプションの設定を構成します。その他のオプションには、**仮想ネットワーク**への参加、Hive と Oozie 用のデータを保持する**外部メタストア**の設定、スクリプト アクションを使用してカスタム コンポーネントをインストールするようにクラスターをカスタマイズすること、クラスターでの追加のストレージ アカウントの使用などがあります。

  		* **[HDInsight のバージョン]**: クラスターで使用するバージョンを選択します。詳細については、「[HDInsight クラスターのバージョン](hdinsight-component-versioning.md)」を参照してください。
  		* **[Virtual Network]**: クラスターを仮想ネットワークに配置する場合は、Azure Virtual Network とサブネットを選択します。

			![[仮想ネットワーク] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "仮想ネットワークの詳細の指定")

			Virtual Network の具体的な構成要件など、Virtual Network で HDInsight を使用する方法については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。
  

  		
		* **[外部メタストア]**: クラスターに関連付けられた Hive と Oozie のメタデータを保存するための SQL Database を指定します。
 
            > [AZURE.NOTE] HBase のクラスターの種類では、メタストア構成は使用できません。

			![[カスタム メタストア] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "外部メタストアの指定")


			**[Hive メタデータで既存の SQL DB を使用する]** で、**[はい]** をクリックし、SQL データベースを選択し、データベースのユーザー名/パスワードを指定します。**[Oozie メタデータで既存の SQL DB を使用する]** 場合は、これらの手順を繰り返します。**[オプションの構成]** ブレードに戻るまで **[選択]** をクリックします。


			>[AZURE.NOTE] メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL Database インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

            &nbsp;

            > [AZURE.IMPORTANT] メタストアを作成するときは、データベース名にダッシュやハイフンを使用しないでください。使用すると、クラスター作成プロセスが失敗することがあります。
		
  		* クラスターを作成するときに、カスタム スクリプトを使用してクラスターをカスタマイズする場合は、**[スクリプト アクション]** をクリックします。スクリプト アクションの詳細については、「[Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。[Script Action] ブレードで、次の画面キャプチャに示すように、詳細を指定します。
  	

			![[スクリプト アクション] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "スクリプト アクションの指定")


    	* **[Azure ストレージ キー]**: クラスターに関連付ける追加のストレージ アカウントを指定します。**[Azure ストレージ キー]** ブレードで、**[ストレージ キーの追加]** をクリックし、既存のストレージ アカウントを選択するか、新しいアカウントを作成します。
    

			![追加ストレージ] ブレード](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "追加ストレージ アカウントの指定")


4. **[作成]** をクリックします。**[スタート画面にピン留めする]** を選択すると、ポータルのスタート画面にクラスターのタイルが追加されます。アイコンはクラスターが作成中であることを示し、作成が完了すると、[HDInsight] アイコンを表示するように変化します。
	
    クラスターが作成されるまで、通常は約 15 分かかります。プロビジョニング プロセスをチェックするには、スタート画面のタイルまたはページの左側の **[通知]** エントリを使用します。
	

5. 作成が完了したら、スタート画面でクラスター用のタイルをクリックして、クラスター ブレードを起動します。クラスター ブレードには、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などのクラスターに関する基本情報が表示されます。


	![クラスター ブレード](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "クラスターのプロパティ")


	次の説明を参照して、このブレードの上部と **[要点]** セクションにあるアイコンについて理解してください。


	* **[設定]** と **[すべての設定]**: クラスター用の **[設定]** ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。
	* **[ダッシュボード]**、**[クラスター ダッシュボード]**、**[URL]**: これらはいつでもクラスター ダッシュボードにアクセスする方法です。クラスター ダッシュボードは、クラスターに関するジョブを実行するための Web ポータルです。
	* **[リモート デスクトップ]**: クラスター ノードのリモート デスクトップを有効/無効にすることができます。
	* **[クラスターのスケーリング]**: このクラスターの worker ノードの数を変更できます。
	* **[削除]**: HDInsight クラスターを削除します。
	* **[クイック スタート]** (![雲と雷のアイコン = クイック スタート](./media/hdinsight-provision-clusters/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。
	* **ユーザー** (![ユーザー アイコン](./media/hdinsight-provision-clusters/users.png)): このクラスターの_ポータル管理_に対する権限を、Azure サブスクリプションの他のユーザーに設定できます。
	

		> [AZURE.IMPORTANT] これは、ポータルでのこのクラスターへのアクセスと権限だけに影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。
		
	* **[タグ]** (![タグ アイコン](./media/hdinsight-provision-clusters/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。

##クラスターのカスタマイズ

- 「[ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)」を参照してください。
- 「[スクリプト アクションを使用して Windows ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」を参照してください。

##次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md) - HDInsight クラスターの使用方法について説明しています。
* [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md) - プログラムを使用して HDInsight にジョブを送信する方法について説明しています。
* [Azure ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0914_2016-->