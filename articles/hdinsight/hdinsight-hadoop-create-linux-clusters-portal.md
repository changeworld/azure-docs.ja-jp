---
title: Web ブラウザーを使用して Apache Hadoop クラスターを作成する - Azure HDInsight
description: Web ブラウザーと Azure プレビュー ポータルを使用して、HDInsight 用の Linux ベースの Apache Hadoop、Apache HBase、Apache Storm、または Apache Spark クラスターを作成する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: ca8b398a796284e34ce4e0b577c335552ca75a98
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974269"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal は、Microsoft Azure クラウドでホストされるサービスやリソースの Web ベースの管理ツールです。 この記事では、ポータルを使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **最新の Web ブラウザー**。 Azure Portal では、HTML5 と Javascript が使用されています。Web ブラウザーのバージョンが低いと正しく機能しない場合があります。

## <a name="create-clusters"></a>クラスターの作成
Azure Portal には、ほとんどのクラスターのプロパティが公開されます。 Azure Resource Manager テンプレートを使用すると、多くの詳細を非表示にできます。 詳しくは、[Azure Resource Manager テンプレートを使用して HDInsight で Linux ベースの Apache Hadoop クラスターを作成する方法](hdinsight-hadoop-create-linux-clusters-arm-templates.md)に関する記事をご覧ください。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. [Azure Portal](https://portal.azure.com) にログインします。

1. 左側のメニューで、**[+ リソースの作成]** を選択します。

1.  **[Azure Marketplace]** の **[分析]** を選択します。

1.  **[おすすめ]** の **[HDInsight]** を選択します。
   
    ![Azure Portal で新しいクラスターを作成する](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Azure Portal で新しいクラスターを作成する")

1. **[HDInsight]** ページで、**[カスタム (サイズ、設定、アプリ)]** を選択します。

1. **[1 基本]** を選択してから、次の情報を入力します。

    ![Azure Portal で新しいクラスターを作成する](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Azure Portal で新しいクラスターを作成する")

    * **[クラスター名]** を入力します。この名前はグローバルに一意である必要があります。

    * **[サブスクリプション]** ドロップダウンから、このクラスターに使用する Azure サブスクリプションを選択します。

    * **[クラスターの種類]** を選択し、作成するクラスターの種類 (Hadoop、Spark など) を選択します。 **[オペレーティング システム]** は **[Linux]** になります。 次に、クラスターの種類とバージョンを選択します。 どれを選択すべきかわからない場合は、既定のバージョンを使用します。 詳細については、「 [HDInsight クラスターのバージョン](hdinsight-component-versioning.md)」をご覧ください。
     
        > [!IMPORTANT]  
        > HDInsight クラスターには、ワークロード、またはクラスターが調整されるテクノロジに対応するさまざまな型があります。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。
        
    * **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]** に、管理者ユーザーのユーザー名とパスワードを入力します。

    * **[SSH ユーザー名]** を入力し、前に指定した管理者パスワードと SSH パスワードを同じにする場合、**[クラスター ログインと同じパスワードを使用します]** チェック ボックスをオンします。 同じにしない場合、SSH ユーザーを認証するために使用される **[パスワード]** または **[公開キー]** のどちらかを入力します。 公開キーを使用することをお勧めします。 下部にある **[選択]** をクリックして資格情報の構成を保存します。
   
        詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

    * **[リソース グループ]** で、新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。

    * クラスターを作成するデータ センターの **[場所]** を指定します。

    * **[次へ]** を選択して、次のページに進みます。

4. **[2 セキュリティとネットワーク]** で、表示されているドロップダウンを使用してクラスターを仮想ネットワークに接続できます。 クラスターを仮想ネットワークに配置する場合は、Azure Virtual Network とサブネットを選択します。 Virtual Network の具体的な構成要件など、Virtual Network で HDInsight を使用する方法については、「 [Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。 **Enterprise セキュリティ パッケージ**を使用する場合は、「[Azure Active Directory Domain Services を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)」の手順に従うこともできます。

    **[次へ]** を選択して、次のページに進みます。


5. **[3 ストレージ]** で、既定のストレージとして Azure Storage (WASB) と Data Lake Storage のどちらを使用するかを指定します。 詳細については、下の表をご覧ください。

     ![Azure Portal で新しいクラスターを作成する](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Azure Portal で新しいクラスターを作成する")

     | Storage                                      | 説明 |
     |----------------------------------------------|-------------|
     | **既定のストレージとしての Azure Storage BLOB**   | <ul><li>**[プライマリ ストレージの種類]** で、**[Azure Storage]** を選択します。 その後、**[メソッドの選択]** で、Azure サブスクリプションの一部であるストレージ アカウントを指定する場合は **[個人用サブスクリプション]** を選択して、ストレージ アカウントを選択することができます。 それ以外の場合、**[アクセス キー]** をクリックして、Azure サブスクリプションの外部から選択するストレージ アカウントの情報を提供します。</li><li>**[既定のコンテナー]** では、ポータルによって示された既定のコンテナー名を使用するか、独自の名前を指定することができます。</li><li>既定のストレージとして WASB を使用している場合、**[追加のストレージ アカウント]** をクリックして、クラスターに関連付ける追加のストレージ アカウントを指定することができます (オプション)。 **[Azure Storage キー]** で **[ストレージ キーの追加]** をクリックして、自分の Azure サブスクリプションまたはその他のサブスクリプションから (ストレージ アカウント アクセス キーを指定することで) ストレージ アカウントを指定することができます。</li><li>既定のストレージとして WASB を使用している場合、**[Data Lake Storage アクセス]** をクリックし、追加のストレージとして Azure Data Lake Storage を指定できます (オプション)。 詳細については、「[クイック スタート: HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」をご覧ください。</li></ul> |
     | **既定のストレージとしての Azure Data Lake Storage** | **[プライマリ ストレージの種類]** で、**[Azure Data Lake Storage Gen1]** または **[Azure Data Lake Storage Gen2]** を選択します。その後、「[クイック スタート: HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」の手順を参照してください。 |
     | **外部メタストア**                      | 必要に応じて、クラスターに関連付けられた Hive と Oozie のメタデータを保存するための SQL データベースを指定します。 **[Hive 用の SQL データベースを選択する]** で、SQL データベースを選択し、データベースのユーザー名とパスワードを指定します。 以上の手順を Oozie メタデータについても行います。<br><br>メタストアに対して Azure SQL Database を使用する場合、いくつかの考慮事項があります。 <ul><li>メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。 Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。 これは、SQL Database インスタンスが実行されているサーバーです。 サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。</li><li>メタストアを作成するときは、データベース名にダッシュやハイフンを使用しないでください。使用すると、クラスター作成プロセスが失敗することがあります。</li></ul> |

     > [!WARNING]  
     > HDInsight クラスター以外の場所で追加のストレージ アカウントを使用することはできません。

     **[次へ]** を選択して、次のページに進みます。


6. **[4 アプリケーション (オプション)]** で、目的のアプリケーションを選択します。  マイクロソフトや独立系ソフトウェア ベンダー (ISV) によって作成されるほか、ユーザーが独自に作成することもできます。 詳細については、[HDInsight アプリケーションのインストール](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)に関する記事をご覧ください。

    **[次へ]** を選択して、次のページに進みます。


6. **[5 クラスター サイズ]** に、このクラスターのために使用されるノードに関する情報が表示されます。 クラスターで必要な worker ノードの数を設定します。 クラスター実行の推定コストが表示されます。
   
    ![[ノード価格レベル]](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "クラスター ノード数の指定")
   
   > [!IMPORTANT]  
   > クラスター作成または作成後の拡大で 32 以上の worker ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。
   > 
   > ノードのサイズと関連コストに関する詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。
   
    **[次へ]** を選択して、次のページに進みます。

8. **[6 スクリプト操作]** で、カスタム コンポーネントをインストールするようにクラスターをカスタマイズできます。  クラスターを作成するときに、カスタム スクリプトを使用してクラスターをカスタマイズする場合は、このオプションを使用します。 スクリプト アクションの詳細については、「 [Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。

   **[次へ]** を選択して、次のページに進みます。

9. **[7 概要]** で、以前に入力した情報を確認し、**[作成]** を選択します。

     ![[ノード価格レベル]](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "クラスター ノード数の指定")
    
    > [!NOTE]  
    > クラスターが作成されるまで、通常は約 20 分かかります。 **[通知]** を監視して、プロビジョニング プロセスをチェックします。

10. 作成プロセスが完了したら、**[デプロイメントに成功しました]** という通知から **[リソースに移動]** を選択します。 クラスター ウィンドウには、次の情報が表示されます。
    
    ![クラスター インターフェイス](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "クラスターのプロパティ")
    
    次の説明を参照して、上部にあるアイコンについて理解してください。
    
    * **[概要]** タブには、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などのクラスターに関するすべての基本情報が表示されます。
    * **[ダッシュ ボード]** から、クラスターに関連付けられている Ambari ポータルに移動します。
    * **[Secure Shell]**:SSH を使用してクラスターにアクセスするために必要な情報です。
    * **[クラスターのスケール設定]** では、クラスターに関連付けられているワーカー ノードの数を増やすことができます。
      * **[削除]**:HDInsight クラスターを削除します。
    

## <a name="customize-clusters"></a>クラスターのカスタマイズ
* 「 [ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)」を参照してください。
* 「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

## <a name="delete-the-cluster"></a>クラスターを削除する
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。

## <a name="next-steps"></a>次の手順
HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックを参照してください。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop クラスター
* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hadoop/hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase クラスター
* [HDInsight での Apache HBase の使用](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight での Apache HBase の Java アプリケーションの開発](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm クラスター
* [HDInsight での Apache Storm の Java トポロジの開発](storm/apache-storm-develop-java-topology.md)
* [HDInsight の Apache Storm での Python コンポーネントの使用](storm/apache-storm-develop-python-topology.md)
* [Deploy and monitor topologies with Apache Storm on HDInsight (HDInsight の Storm を使用したトポロジのデプロイと監視)](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark クラスター
* [Scala を使用してスタンドアロン アプリケーションを作成する](spark/apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](spark/apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](spark/apache-spark-machine-learning-mllib-ipython.md)

