<properties
   pageTitle="HDInsight で Hadoop クラスターを作成する | Microsoft Azure"
   	description="Azure ポータルを使用して Azure HDInsight のクラスターを作成する方法について説明します。"
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
   ms.date="03/08/2016"
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

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **[データ ソース]**: クラスターの既定のファイル システムとして使用する Azure Storage アカウントを新しく作成するか、既存の Azure Storage アカウントを選択します。

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **[ノード価格レベル]**: クラスターで必要なworker ノードの数を設定します。クラスターの推定コストがブレード内に表示されます。
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **[オプションの構成]** をクリックし、クラスターのバージョンを選択して、その他のオプションの設定を構成します。その他のオプションには、**Virtual Network** への参加、Hive と Oozie 用のデータを保持する**外部メタストア**の設定、スクリプト アクションを使用してカスタム コンポーネントをインストールするようにクラスターをカスタマイズすること、クラスターでの追加のストレージ アカウントの使用などがあります。

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			For information on using HDInsight with a Virtual Network, including specific configuration requirements for the Virtual Network, see [Extend HDInsight capbilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


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
	* **[ユーザー]** (![ユーザー アイコン](./media/hdinsight-provision-clusters/users.png)): このクラスターの_ポータル管理_に対する権限を、Azure サブスクリプションの他のユーザーに対して設定できます。
	

		> [AZURE.IMPORTANT] これは、ポータルでのこのクラスターへのアクセスと権限だけに影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。
		
	* **[タグ]** (![タグ アイコン](./media/hdinsight-provision-clusters/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。たとえば、__プロジェクト__という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。

##クラスターのカスタマイズ

- 「[ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)」を参照してください。
- 「[Script Action を使用して Windows ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」を参照してください。

##次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md) - HDInsight クラスターの使用方法について説明しています。
* [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md) - プログラムを使用して HDInsight にジョブを送信する方法について説明しています。
* [Azure ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0309_2016-->