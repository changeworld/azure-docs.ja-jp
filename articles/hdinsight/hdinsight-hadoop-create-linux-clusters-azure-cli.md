---
title: コマンド ラインを使用して Hadoop クラスターを作成する - Azure HDInsight
description: クロス プラットフォーム Azure CLI 1.0 を使用して HDInsight クラスターを作成する方法について説明します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: 7185e492a2cc42835ce1fa7043a963c7d5d0afb4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594799"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Azure CLI を使用した HDInsight クラスターの作成

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

このドキュメントの手順では、Azure CLI 1.0 を使用して HDInsight 3.5 クラスターをプロセスを順を追って説明します。

> [!IMPORTANT]
> このトピックでは、Azure CLI 1.0 を使用して HDInsight クラスターを作成する方法について説明します。 このバージョンの CLI は非推奨となっています。また、Azure CLI 2.0 には、HDInsight クラスターの作成機能がまだ追加されていません。
>
> HDInsight クラスターの作成と管理は、Azure PowerShell を使用して行うこともできます。 詳しくは、[Azure PowerShell を使用した HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* **Azure CLI**。 このドキュメントの手順は、Azure CLI バージョン 0.10.14 で最後にテストされました。

    > [!IMPORTANT]
    > Azure CLI 1.0 は非推奨となっています。また、Azure CLI 2.0 には、HDInsight クラスターの作成機能がまだ追加されていません。

## <a name="log-in-to-your-azure-subscription"></a>Azure サブスクリプションにログイン

「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](/cli/azure/authenticate-azure-cli)」に記載されている手順に従い、 **login** メソッドを使用してサブスクリプションに接続します。

## <a name="create-a-cluster"></a>クラスターの作成

PowerShell または Bash などのコマンド ラインから、次の手順を実行してください。

1. 次のコマンドを使用して、Azure サブスクリプションに対して認証します。

        azure login

    名前とパスワードを入力するように求められます。 複数の Azure サブスクリプションがある場合は、 `azure account set <subscriptionname>` を使用して、Azure CLI コマンドが使用するサブスクリプションを設定します。

2. 次のコマンドで Azure リソース マネージャー モードに切り替えます。

        azure config mode arm

3. リソース グループを作成します。 このリソース グループには、HDInsight クラスターおよび関連するストレージ アカウントが含まれます。

        azure group create groupname location

    * `groupname` には、グループの一意の名前を指定します。

    * `location` には、グループの作成先となる地理的領域を指定します。

       グループの作成先として有効な場所は、`azure location list` コマンドで一覧表示できます。`Name` 列に表示されるいずれかの場所を使用してください。

4. ストレージ アカウントを作成します。 このストレージ アカウントは、HDInsight クラスターの既定のストレージとして使用されます。

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * `groupname` には、前の手順で作成したグループの名前を指定します。

    * `location` には、前の手順で使用した場所を指定します。

    * `storagename` には、ストレージ アカウントの一意の名前を指定します。

        > [!NOTE]
        > このコマンドのパラメーターの詳細については、「`azure storage account create -h`」と入力してコマンドのヘルプを表示してください。

5. ストレージ アカウントにアクセスするためのキーを取得します。

        azure storage account keys list -g groupname storagename

    * `groupname` には、リソース グループ名を指定します。
    * `storagename` には、ストレージ アカウントの名前を指定します。

     返されたデータで、`key1` の `key` の値を保存します。

6. HDInsight クラスターを作成します。

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * `groupname` には、リソース グループ名を指定します。

    * `Hadoop` には、作成するクラスターの種類を指定します。 たとえば、`Hadoop`、`HBase`、`Kafka`、`Spark`、`Storm` などです。

     > [!IMPORTANT]
     > HDInsight クラスターにはさまざまな種類があり、それぞれに適したワークロードやテクノロジに対応しています。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。

    * `location` には、前の手順で使用した場所を指定します。

    * `storagename` には、ストレージ アカウント名を指定します。

    * `storagekey` には、前の手順で取得したキーを指定します。

    * `--defaultStorageContainer` パラメーターには、クラスターに使用している名前と同じ名前を指定します。

    * `admin` と `httppassword` には、HTTPS を使用してクラスターにアクセスするときに使用する名前とパスワードを指定します。

    * `sshuser` と `sshuserpassword` には、SSH を使用してクラスターにアクセスするときに使用するユーザー名とパスワードを指定します。

    > [!IMPORTANT]
    > この例では、2 つの worker ノードを持つクラスターが作成されます。 クラスターの作成後にスケーリング操作を実行することによって、ワーカー ノードの数を変更することもできます。 32 個を超えるワーカー ノードの使用を予定している場合は、コアが 8 個以上で RAM が 14 GB 以上のヘッド ノード サイズを選択する必要があります。 ヘッド ノード サイズは、クラスターの作成中に `--headNodeSize` パラメーターを使用して設定できます。
    >
    > ノードのサイズと関連コストに関する詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

    クラスターの作成処理は、完了までに数分かかる場合があります。 通常は約 15 です。

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](hdinsight-administer-use-portal-linux.md#create-clusters)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure CLI を使用して HDInsight クラスターを作成したら、クラスターの使用方法について、以下のトピックを参照してください。

### <a name="hadoop-clusters"></a>Hadoop クラスター

* [HDInsight での Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hadoop/hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase クラスター

* [HDInsight での HBase の使用](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm クラスター

* [HDInsight での Storm の Java トポロジの開発](storm/apache-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](storm/apache-storm-develop-python-topology.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](storm/apache-storm-deploy-monitor-topology-linux.md)
