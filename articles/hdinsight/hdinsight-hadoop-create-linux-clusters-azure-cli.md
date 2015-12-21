<properties
   	pageTitle="クロスプラットフォーム Azure CLI を使用した HDInsight での Linux ベースの Hadoop、HBase、または Storm クラスターの作成 | Microsoft Azure"
   	description="クロスプラットフォーム Azure CLI、Azure リソース マネージャー テンプレート、および Azure REST API を使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。クラスターの種類 (Hadoop、HBase、または Storm) を指定するか、スクリプトを使用してカスタム コンポーネントをインストールすることができます。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="11/17/2015"
   	ms.author="larryfr"/>

#Azure CLI を使用した HDInsight の Linux ベースのクラスターの作成

[AZURE.INCLUDE [セレクター](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure CLI は、Azure サービスを管理できる、プラットフォームに依存しないコマンドライン ユーティリティです。Azure リソース管理テンプレートと共に使用して、HDInsight クラスター、関連するストレージ アカウント、その他のサービスを作成できます。

Azure リソース管理テンプレートは、__リソース グループ__とグループ内のすべてのリソース (HDInsight など) について記述する JSON ドキュメントです。 このテンプレート基準の手法では、1 つのテンプレートで HDInsight に必要なすべてのリソースを定義でき、グループに変更を適用する__デプロイメント__を介して、グループに対する変更をまとめて管理できます。

このドキュメントの手順では、Azure CLI とテンプレートを使用して新しい HDInsight クラスターを作成するプロセスを示します。

> [AZURE.IMPORTANT]この文書の手順では、HDInsight クラスターにワーカー ノードの既定数 (4) を使用します。クラスター作成または作成後の拡大で 32 以上のワーカー ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。
>
> ノードのサイズと関連コストに関する詳細については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

##前提条件

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- __Azure CLI__。CLI のインストール方法については、「[Azure CLI のインストール](../xplat-cli-install.md)」をご覧ください。

##Azure サブスクリプションへのログイン

「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../xplat-cli-connect.md)」に記載されている手順に従い、__login__ メソッドを使用してサブスクリプションに接続します。

##クラスターの作成

Azure CLI をインストールして構成したら、コマンド プロンプト、シェル、またはターミナル セッションから次の手順を実行します。

1. 次のコマンドを使用して、Azure サブスクリプションに対して認証します。

        azure login

    名前とパスワードを入力するように求められます。複数の Azure サブスクリプションがある場合は、`azure account set <subscriptionname>` を使用して、Azure CLI コマンドが使用するサブスクリプションを設定できます。

3. 次のコマンドで Azure リソース マネージャー モードに切り替えます。

        azure config mode arm

4. HDInsight クラスターのテンプレートを作成します。基本的なテンプレートの例を次に示します。

    * [Linux ベースのクラスター、SSH パブリック キーを使用](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-publickey)
    * [Linux ベースのクラスター、SSH アカウントのパスワードを使用](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password)

    これらのテンプレートのいずれでも、HDInsight が使用する既定の Azure ストレージ アカウントが作成されます。

    必要なファイルは、__azuredeploy.json__ と __azuredeploy.parameters.json__ です。続行する前に、これらのファイルをローカルにコピーします。

5. __azuredeploy.parameters.json__ ファイルをエディターで開き、`parameters` セクションの項目に値を指定します。

    * __location__: リソースが作成されるデータ センター。許可される場所の一覧については、__azuredeploy.json__ ファイルの `location` セクションを参照してください。
    * __clusterName__: HDInsight クラスターの名前。この名前は一意にする必要があります。一意にしなければデプロイメントは失敗します。
    * __clusterStorageAccountName__: HDInsight に対して作成される Azure Storage のアカウント名。この名前は一意にする必要があります。一意にしなければデプロイメントは失敗します。
    * __clusterLoginPassword__: クラスターの管理者ユーザーのパスワード。これは Web サイトやクラスターの REST サービスのアクセスに使用されるので、安全なパスワードにする必要があります。
    * __sshUserName__: このクラスターに作成する最初の SSH ユーザーの名前。SSH はこのアカウントでクラスターにリモート アクセスするために使用されます。
    * __sshPublicKey__: SSH 公開キーを必要とするテンプレートを使用している場合、この行に公開キーを追加する必要があります。パブリック キーを生成し、それを使用する方法については、次の記事を参照してください。

        * [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: SSH パスワードを必要とするテンプレートを使用している場合、この行にパスワードを追加する必要があります。

    完了後、ファイルを保存し、終了します。

5. 次を利用して空のリソース グループを作成します。__RESOURCEGROUPNAME__ をこのグループに使用する名前に置き換えます。__LOCATION__ を HDInsight クラスターを作成するデータ センターに置き換えます。

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]場所の名前にスペースが含まれる場合は、名前を引用符で囲みます。たとえば、"South Central US" のようになります。

6. 次のコマンドを利用し、このリソース グループの最初のデプロイメントを作成します。__PATHTOTEMPLATE__ を __azuredeploy.json__ テンプレート ファイルのパスに置き換えます。__PATHTOPARAMETERSFILE__ を __azuredeploy.parameters.json__ ファイルのパスに置き換えます。__RESOURCEGROUPNAME__ を前の手順で作成したグループの名前に置き換えます。

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    デプロイメントが承認されると、"`group deployment create command ok`" のようなメッセージが表示されます。

7. デプロイメントの完了には約 15 分かかることがあります。次のコマンドでデプロイメントに関する情報を表示できます。__RESOURCEGROUPNAME__ を前の手順で使用したリソース グループの名前に置き換えます。

        azure group log show -l RESOURCEGROUPNAME
    
    デプロイメントが完了すると、__Status__ フィールドに __Succeeded__.という値が表示されます。デプロイメント中にエラーが発生した場合、次のコマンドでエラーに関する詳細を取得できます。

        azure group log show -l -v RESOURCEGROUPNAME

##次のステップ

HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックをご覧ください。

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

<!---HONumber=AcomDC_1210_2015-->