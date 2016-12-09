---
title: "クロスプラットフォーム Azure CLI を使用した HDInsight での Linux ベースの Hadoop、HBase、または Storm クラスターの作成 | Microsoft Docs"
description: "クロスプラットフォーム Azure CLI、Azure リソース マネージャー テンプレート、および Azure REST API を使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。 クラスターの種類 (Hadoop、HBase、または Storm) を指定するか、スクリプトを使用してカスタム コンポーネントをインストールすることができます。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f322b95e3ade4318ccd64f5e2222194bd2fb9361


---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>Azure CLI を使用した HDInsight の Linux ベースのクラスターの作成
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure CLI は、Azure サービスを管理できる、プラットフォームに依存しないコマンドライン ユーティリティです。 Azure リソース管理テンプレートと共に使用して、HDInsight クラスター、関連するストレージ アカウント、その他のサービスを作成できます。

Azure リソース管理テンプレートは、**リソース グループ**とその中のすべてのリソース (HDInsight など) について記述する JSON ドキュメントです。このテンプレート ベースのアプローチでは、HDInsight で必要なすべてのリソースを 1 つのテンプレートで定義することができます。 **デプロイ**の際にグループの全体としての変更を管理して、グループ全体に変更を適用することもできます。

このドキュメントの手順では、Azure CLI とテンプレートを使用して新しい HDInsight クラスターを作成するプロセスを示します。

> [!IMPORTANT]
> この文書の手順では、HDInsight クラスターにワーカー ノードの既定数 (4) を使用します。 クラスターの作成または拡張にあたって 32 個を超えるワーカー ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。
> 
> ノードのサイズと関連コストに関する詳細については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。
> 
> 

## <a name="prerequisites"></a>前提条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **Azure CLI**。 このドキュメントの手順は、Azure CLI Version 0.10.1 でテストされています。
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>アクセス制御の要件
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="log-in-to-your-azure-subscription"></a>Azure サブスクリプションにログイン
「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../xplat-cli-connect.md)」に記載されている手順に従い、 **login** メソッドを使用してサブスクリプションに接続します。

## <a name="create-a-cluster"></a>クラスターの作成
Azure CLI をインストールして構成したら、コマンド プロンプト、シェル、またはターミナル セッションから次の手順を実行します。

1. 次のコマンドを使用して、Azure サブスクリプションに対して認証します。
   
        azure login
   
    名前とパスワードを入力するように求められます。 複数の Azure サブスクリプションがある場合は、 `azure account set <subscriptionname>` を使用して、Azure CLI コマンドが使用するサブスクリプションを設定します。
2. 次のコマンドで Azure リソース マネージャー モードに切り替えます。
   
        azure config mode arm
3. リソース グループを作成します。 このリソース グループに、HDInsight クラスターおよび関連するストレージ アカウントを追加することになります。
   
        azure group create groupname location
   
   * **groupname** は、グループの一意の名前に置き換えます。 
   * **location** には、グループの作成先となる地理的領域を指定します。 
     
       グループの作成先として有効な場所は、`azure location list` コマンドで一覧表示できます。**Name** 列に表示されるいずれかの場所を使用してください。
4. ストレージ アカウントを作成します。 このストレージ アカウントが、HDInsight クラスターの既定のストレージとして使用されます。
   
        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
   
   * **groupname** には、前の手順で作成したグループの名前を指定します。
   * **location** には、前の手順で使用した場所を指定します。 
   * **storagename** には、ストレージ アカウントの一意の名前を指定します。
     
     > [!NOTE]
     > このコマンドのパラメーターの詳細については、「`azure storage account create -h`」と入力してコマンドのヘルプを表示してください。
     > 
     > 
5. ストレージ アカウントにアクセスするためのキーを取得します。
   
        azure storage account keys list -g groupname storagename
   
   * **groupname** には、リソース グループ名を指定します。
   * **storagename** には、ストレージ アカウントの名前を指定します。
     
     返されたデータから、**key1** の **key** 値を保存します。
6. HDInsight クラスターを作成します。
   
        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername
   
   * **groupname** には、リソース グループ名を指定します。
   * **Hadoop** には、作成するクラスターの種類を指定します。 たとえば、`Hadoop`、`HBase`、`Storm`、`Spark` などです。
     
     > [!IMPORTANT]
     > HDInsight クラスターにはさまざまな種類があり、それぞれ適したワークロードやテクノロジに対応しています。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。 
     > 
     > 
   * **location** には、前の手順で使用した場所を指定します。
   * **storagename** には、ストレージ アカウントの名前を指定します。
   * **storagekey** には、前の手順で取得したキーを指定します。 
   * `--defaultStorageContainer` パラメーターには、クラスターに使用している名前と同じ名前を指定します。
   * **admin** と **httppassword** には、HTTPS でクラスターにアクセスするときに使用する名前とパスワードを指定します。
   * **sshuser** と **sshuserpassword** には、SSH でクラスターにアクセスするときに使用するユーザー名とパスワードを指定します。
     
     クラスターの作成処理は、完了までに数分かかる場合があります。 通常は約 15 です。

## <a name="next-steps"></a>次のステップ
Azure CLI を使用して HDInsight クラスターを作成したら、クラスターの使用方法について、以下のトピックを参照してください。

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




<!--HONumber=Nov16_HO3-->


