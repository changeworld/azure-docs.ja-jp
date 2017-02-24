---
title: "Web ブラウザーを使用して Azure HDInsight (Hadoop) を作成する | Microsoft Docs"
description: "Web ブラウザーと Azure プレビュー ポータルを使用して、Linux ベースの Hadoop、HBase、Storm、または Spark クラスターを HDInsight に作成する方法を説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: eed8a3863cc8f2fa7fbd9fe3306bd1bff0f5e91e


---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Azure ポータルを使用した HDInsight の Linux ベースのクラスターの作成
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure ポータルは、Microsoft Azure クラウドでホストされるサービスやリソースの Web ベースの管理ツールです。 この記事では、ポータルを使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **最新の Web ブラウザー**。 Azure Portal では、HTML5 と Javascript が使用されています。Web ブラウザーのバージョンが低いと正しく機能しない場合があります。

### <a name="access-control-requirements"></a>アクセス制御の要件
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>クラスターの作成
Azure ポータルには、ほとんどのクラスターのプロパティが公開されます。 Azure Resource Manager テンプレートを使用すると、多くの詳細を非表示にできます。 詳細については、「 [Azure Resource Manager テンプレートを使用した HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-create-linux-clusters-arm-templates.md)」を参照してください。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[新規]**、**[データ分析]**、**[HDInsight]** の順にクリックします。
   
    ![Azure Portal で新しいクラスターを作成する](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Azure Portal で新しいクラスターを作成する")

3. **クラスター名**を入力します。この名前はグローバルに一意である必要があります。

4. **[サブスクリプション]** ドロップダウンから、このクラスターに使用する Azure サブスクリプションを選択します。

5. **[クラスターの構成]** をクリックして、次を選択します。
   
   * **[クラスターの種類]**: どれを選択すべきかわからない場合は、**[Hadoop]** を選択します。 これが最も一般的なクラスターの種類です。
     
     > [!IMPORTANT]
     > HDInsight クラスターにはさまざまな種類があり、それぞれ適したワークロードやテクノロジに対応しています。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。 
     > 
     > 
   * **[オペレーティング システム]**: **[Linux]** を選択します。
   * **[バージョン]**: どれを選択すべきかわからない場合は、既定のバージョンを使用します。 詳細については、「 [HDInsight クラスターのバージョン](hdinsight-component-versioning.md)」をご覧ください。
   * **[クラスター レベル]**: Azure HDInsight では、Standard レベルと Premium レベルの&2; つのカテゴリでビッグ データのクラウド サービスを提供します。 詳細については、 [クラスター レベル](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers)に関するページをご覧ください。
     
     ![HDInsight の Premium レベルの構成](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-cluster-type-configuration.png)

6. **[アプリケーション]** をクリックして、HDInsight クラスターで動作するアプリケーションをインストールします。 マイクロソフトや独立系ソフトウェア ベンダー (ISV) によって作成されるほか、ユーザーが独自に作成することもできます。 詳細については、[HDInsight アプリケーションのインストール](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)に関する記事をご覧ください。 

7. **[資格情報]** をクリックし、管理ユーザーのパスワードを入力します。 さらに、SSH ユーザーを認証するために使用される **[SSH ユーザー名]** と、**[パスワード]** または **[公開キー]** のどちらかを入力する必要があります。 公開キーを使用することをお勧めします。 下部にある **[選択]** をクリックして資格情報の構成を保存します。
   
    ![クラスターの資格情報の指定](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "クラスターの資格情報の指定")
   
    HDInsight での SSH の使用方法の詳細については、次の記事を参照してください。
   
   * [Linux、Unix、OS X から HDInsight 上の Hadoop で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [HDInsight の Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)


8. **[データ ソース]** ブレードで、既定のストレージとして Azure Storage (WASB) と Data Lake Store のどちらを使用するか指定します。  

    * **既定のストレージとしての Azure Storage BLOB**

        **[Primary Storage Type (プライマリ ストレージの種類)]** で、**[Azure Storage]** をクリックします。 ストレージ アカウントおよびストレージ コンテナーの詳細を指定し、場所を指定して、**[クラスター AAD ID]** をクリックします。

        ![HDInsight クラスターにサービス プリンシパルを追加する](./media/hdinsight-hadoop-create-linux-cluster-portal/hdi.adl.1.png "HDInsight クラスターにサービス プリンシパルを追加する")

      >[!NOTE]
      > クラスター AAD ID を使用すると、AAD 構成に基づいてクラスターから Azure Data Lake ストアにアクセスできます。 クラスターに関連付けられた追加のストレージとして Data Lake Store アカウントを使用する場合はこのオプションを使用します。
      > 
      >

        **[選択]** をクリックしてデータ ソースの構成を保存します。

    * **既定のストレージとしての Azure Data Lake Store**

        既定のストレージとして Data Lake Store を使用する HDInsight クラスターの作成方法については、「[Azure Portal を使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。

        **[選択]** をクリックしてデータ ソースの構成を保存します。

9. **[ノード価格レベル]** をクリックして、このクラスターのために作成されるノードに関する情報を表示します。 クラスターで必要な worker ノードの数を設定します。 クラスターの推定コストがブレード内に表示されます。
   
    ![[ノード価格レベル] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "クラスター ノード数の指定")
   
   > [!IMPORTANT]
   > クラスター作成または作成後の拡大で 32 以上の worker ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。
   > 
   > ノードのサイズと関連コストに関する詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。
   > 
   > 
   
   **[選択]** をクリックして、ノードの価格構成を保存します。

10. **[詳細な構成]** をクリックし、その他のオプション設定を構成します。その他のオプションには、**仮想ネットワーク**への参加、Hive と Oozie 用のデータを保持する**外部 metastore** の設定、**スクリプト アクション**を使用してカスタム コンポーネントをインストールするようにクラスターをカスタマイズすること、クラスターでの**追加のストレージ アカウント**の使用などがあります。

    * **[Virtual Network]**: クラスターを仮想ネットワークに配置する場合は、Azure Virtual Network とサブネットを選択します。  
      
        ![[仮想ネットワーク] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "仮想ネットワークの詳細の指定")
      
        Virtual Network の具体的な構成要件など、Virtual Network で HDInsight を使用する方法については、「 [Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。

    * **[外部メタストア]** をクリックし、クラスターに関連付けられた Hive と Oozie のメタデータを保存するために使用する SQL データベースを指定します。
      
      > [!NOTE]
      > HBase のクラスターの種類では、メタストア構成は使用できません。
      > 
      > 
      
        ![[カスタム metastore] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "外部 metastore の指定")
      
        **[Hive メタデータで既存の SQL DB を使用する]** で、**[はい]** をクリックし、SQL データベースを選択し、データベースのユーザー名/パスワードを指定します。 **[Oozie メタデータで既存の SQL DB を使用する]**場合は、これらの手順を繰り返します。 **[オプションの構成]** ブレードに戻るまで **[選択]** をクリックします。
      
      > [!NOTE]
      > メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。 Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。 これは、SQL Database インスタンスが実行されているサーバーです。 サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。
      > 
      > 
      
        &nbsp;
      
      > [!IMPORTANT]
      > メタストアを作成するときは、データベース名にダッシュやハイフンを使用しないでください。使用すると、クラスター作成プロセスが失敗することがあります。
      > 
      > 

    * **[スクリプト アクション]** をクリックします。 スクリプト アクションの詳細については、「 [Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。 [Script Action] ブレードで、次の画面キャプチャに示すように、詳細を指定します。
      
        ![[スクリプト アクション] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "スクリプト アクションの指定")

    * **[追加のストレージ アカウント]** をクリックして、クラスターに関連付ける追加のストレージ アカウントを指定します。 **[Azure ストレージ キー]** ブレードで、**[ストレージ キーの追加]** をクリックし、既存のストレージ アカウントを選択するか、新しいアカウントを作成します。
      
        ![[追加ストレージ] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "追加ストレージ アカウントの指定")
      
        クラスターが作成された後、ストレージ アカウントを追加することもできます。  「 [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。
      
        **[新しい HDInsight クラスター]** ブレードに戻るまで **[選択]** をクリックします。
      
        BLOB ストレージ アカウントに加えて、Azure Data Lake ストアをリンクすることもできます。 この構成は、既定のストレージ アカウントと既定のコンテナーを構成したデータ ソースから AAD を構成して実行できます。

11. **[新しい HDInsight クラスター]** ブレードで、**[スタート画面にピン留めする]** が選択されていることを確認し、**[作成]** をクリックします。 これでクラスターが作成され、Azure Portal のスタート画面にクラスター用のタイルが追加されます。 アイコンはクラスターがプロビジョニング中であることを示し、プロビジョニングが完了すると、[HDInsight] アイコンを表示するように変化します。
    
    | プロビジョニング中 | プロビジョニング完了 |
    | --- | --- |
    | ![スタート画面のプロビジョニング中インジケーター](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) |![プロビジョニングされたクラスターのタイル](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |
    
    > [!NOTE]
    > クラスターが作成されるまで、通常は約 15 分かかります。 プロビジョニング プロセスを確認するには、スタート画面のタイルまたはページの左側の **[通知]** エントリを使用します。
    > 
    > 
12. 作成が完了したら、スタート画面でクラスター用のタイルをクリックして、クラスター ブレードを起動します。 クラスター ブレードには、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などのクラスターに関する基本情報が表示されます。
    
    ![クラスター ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "クラスターのプロパティ")
    
    次の説明を参照して、このブレードの上部と **[要点]** セクションにあるアイコンについて理解してください。
    
    * **[設定]** と **[すべての設定]**: クラスター用の **[設定]** ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。
    * **[ダッシュボード]**、**[クラスター ダッシュボード]**、**[URL]**: これらはいつでもクラスター ダッシュボードにアクセスする方法です。クラスター ダッシュボードは、クラスターに関するジョブを実行するための Web ポータルです。
    * **[Secure Shell]**: SSH を使用してクラスターにアクセスするために必要な情報です。
    * **削除**: HDInsight クラスターを削除します。
    * **[クイック スタート]** (![cloud and thunderbolt icon = quickstart](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): HDInsight の操作を開始するために役立つ情報を表示します。
    * **[ユーザー]** (![ユーザー アイコン](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): このクラスターの "*ポータル管理*" に対する権限を、Azure サブスクリプションの他のユーザーに設定できます。
      
      > [!IMPORTANT]
      > これは、Azure ポータルでのこのクラスターへのアクセスと権限 *"だけ"* に影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。
      > 
      > 
    * **[タグ]** (![タグ アイコン](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。 たとえば、 **プロジェクト**という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。

## <a name="customize-clusters"></a>クラスターのカスタマイズ
* 「 [ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)」を参照してください。
* 「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

## <a name="delete-the-cluster"></a>クラスターを削除する
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ
HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックを参照してください。

### <a name="hadoop-clusters"></a>Hadoop クラスター
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase クラスター
* [HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm クラスター
* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](hdinsight-storm-develop-python-topology.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Spark クラスター
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark と BI: HDInsight で BI ツールと Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)




<!--HONumber=Jan17_HO4-->


