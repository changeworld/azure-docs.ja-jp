<properties
   	pageTitle="クロスプラットフォーム Azure CLI を使用した HDInsight での Linux ベースの Hadoop、HBase、または Storm クラスターの作成 | Microsoft Azure"
   	description="クロスプラットフォーム Azure CLI、Azure リソース マネージャー テンプレート、および Azure REST API を使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。クラスターの種類 (Hadoop、HBase、または Storm) を指定するか、スクリプトを使用してカスタム コンポーネントをインストールすることができます。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="09/20/2016"
   	ms.author="larryfr"/>

#Azure CLI を使用した HDInsight の Linux ベースのクラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-selector-create-clusters.md)]

Azure CLI は、Azure サービスを管理できる、プラットフォームに依存しないコマンドライン ユーティリティです。Azure リソース管理テンプレートと共に使用して、HDInsight クラスター、関連するストレージ アカウント、その他のサービスを作成できます。

Azure リソース管理テンプレートは、__リソース グループ__とその中のすべてのリソース (HDInsight など) について記述する JSON ドキュメントです。 このテンプレート ベースのアプローチでは、HDInsight で必要なすべてのリソースを 1 つのテンプレートで定義することができます。__デプロイ__の際にグループの全体としての変更を管理して、グループ全体に変更を適用することもできます。

このドキュメントの手順では、Azure CLI とテンプレートを使用して新しい HDInsight クラスターを作成するプロセスを示します。

> [AZURE.IMPORTANT] この文書の手順では、HDInsight クラスターにワーカー ノードの既定数 (4) を使用します。クラスターの作成または拡張にあたって 32 個を超えるワーカー ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。
>
> ノードのサイズと関連コストに関する詳細については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

##前提条件

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- __Azure CLI__。このドキュメントの手順は、Azure CLI Version 0.10.1 でテストされています。

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##Azure サブスクリプションにログイン

「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../xplat-cli-connect.md)」に記載されている手順に従い、__login__ メソッドを使用してサブスクリプションに接続します。

##クラスターの作成

Azure CLI をインストールして構成したら、コマンド プロンプト、シェル、またはターミナル セッションから次の手順を実行します。

1. 次のコマンドを使用して、Azure サブスクリプションに対して認証します。

        azure login

    名前とパスワードを入力するように求められます。複数の Azure サブスクリプションがある場合は、`azure account set <subscriptionname>` を使用して、Azure CLI コマンドが使用するサブスクリプションを設定します。

3. 次のコマンドで Azure リソース マネージャー モードに切り替えます。

        azure config mode arm

4. リソース グループを作成します。このリソース グループに、HDInsight クラスターおよび関連するストレージ アカウントを追加することになります。

        azure group create groupname location
        
    * __groupname__ は、グループの一意の名前に置き換えます。
    * __location__ には、グループの作成先となる地理的領域を指定します。
    
        グループの作成先として有効な場所は、`azure location list` コマンドで一覧表示できます。__Name__ 列に表示されるいずれかの場所を使用してください。

5. ストレージ アカウントを作成します。このストレージ アカウントが、HDInsight クラスターの既定のストレージとして使用されます。

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * __groupname__ には、前の手順で作成したグループの名前を指定します。
     * __location__ には、前の手順で使用した場所を指定します。
     * __storagename__ には、ストレージ アカウントの一意の名前を指定します。
     
     > [AZURE.NOTE] このコマンドのパラメーターの詳細については、「`azure storage account create -h`」と入力してコマンドのヘルプを表示してください。

5. ストレージ アカウントにアクセスするためのキーを取得します。

        azure storage account keys list -g groupname storagename
        
    * __groupname__ には、リソース グループ名を指定します。
    * __storagename__ には、ストレージ アカウントの名前を指定します。
    
    返されたデータから、__key1__ の __key__ 値を保存します。

6. HDInsight クラスターを作成する。

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * __groupname__ には、リソース グループ名を指定します。
    * __location__ には、前の手順で使用した場所を指定します。
    * __storagename__ には、ストレージ アカウントの名前を指定します。
    * __storagekey__ には、前の手順で取得したキーを指定します。
    * `--defaultStorageContainer` パラメーターには、クラスターに使用している名前と同じ名前を指定します。
    * __admin__ と __httppassword__ には、HTTPS でクラスターにアクセスするときに使用する名前とパスワードを指定します。
    * __sshuser__ と __sshuserpassword__ には、SSH でクラスターにアクセスするときに使用するユーザー名とパスワードを指定します。

    クラスターの作成処理は、完了までに数分かかる場合があります。通常は約 15 です。

##次のステップ

Azure CLI を使用して HDInsight クラスターを作成したら、クラスターの使用方法について、以下のトピックを参照してください。

###Hadoop クラスター

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

###HBase クラスター

* [HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux.md)

###Storm クラスター

* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](hdinsight-storm-develop-python-topology.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0921_2016-->