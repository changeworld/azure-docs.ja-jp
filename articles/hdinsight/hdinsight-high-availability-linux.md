<properties
	pageTitle="Linux ベースの HDInsight (Hadoop) の高可用性機能 | Microsoft Azure"
	description="Linux ベースの HDInsight クラスターで、追加のヘッド ノードを使用することで、信頼性と可用性を改善する方法について説明します。また、その結果が Ambari や Hive などの Hadoop サービスに与える影響、SSH を使用して各ヘッド ノードに個別に接続する方法についても説明します。"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="10/19/2015"
	ms.author="larryfr"/>

#HDInsight における Hadoop クラスターの可用性と信頼性

Azure HDInsight によってデプロイされる Linux ベースの Hadoop クラスターでは、第 2 のヘッド ノードが使用されます。これによって、Azure で実行される Hadoop サービスとジョブの可用性と信頼性が向上します。

> [AZURE.NOTE]このドキュメントの手順は、Linux ベースの HDInsight クラスターに固有のものです。Windows ベースのクラスターを使用する場合は、「[HDInsight におけるWindows ベースの Hadoop クラスターの可用性と信頼性](hdinsight-high-availability.md)」で Windows 固有の情報を参照してください。

##ヘッド ノードについて

Hadoop のいくつかの実装には、データ (ワーカー) ノードの障害を管理するサービスをスムーズにホストする 1 つのヘッド ノードがあります。ただし、ヘッド ノードで実行されているマスター サービスが停止すると、クラスターは動作を停止します。

HDInsight クラスターには、セカンダリ ヘッド ノードが用意されています。これにより、プライマリ ノードで障害が発生した場合に、マスター サービスとコンポーネントの実行をセカンダリ ノードで継続できます。

> [AZURE.IMPORTANT]両方のヘッド ノードは、クラスター内で同時にアクティブに実行されます。HDFS や YARN などの一部のサービスは、常にどちらかのヘッド ノードで「アクティブ」になります (他方のヘッド ノードでは「スタンバイ」になります)。HiveServer2 や Hive MetaStore などのサービスは、両方のヘッド ノードで同時にアクティブになります。

ノード ヘッドでマスター サービスを選択し、そのサービスを確実に動作させるために [ZooKeeper](http://zookeeper.apache.org/) ノード (ZK) が使用され、データ (ワーカー) ノードとゲートウェイはどちらのヘッド ノードでマスター サービスがアクティブであるかがわかります。

## ヘッド ノードへのアクセス

一般に、パブリック ゲートウェイ (Ambari Web と REST API) 経由のクラスターに対するすべてのアクセスは、ヘッド ノードが複数あっても影響を受けることはありません。要求はアクティブなヘッド ノードにルーティングされ、適切に処理されます。

SSH を使用してクラスターにアクセスする場合は、ポート 22 (SSH の既定値) 経由で接続すると headnode0 に接続され、ポート 23 経由で接続すると headnode1 に接続されます。

### 内部完全修飾ドメイン名 (FQDN)

HDInsight クラスター内のノードには、クラスターからのみアクセスできる内部 IP アドレスと FQDN があります (ヘッド ノードまたはクラスターで実行されているジョブに対する SSH セッションなど)。 内部 FQDN または IP アドレスを使用してクラスターのサービスにアクセスする場合は、サービスへのアクセス時に Ambari を使用して IP または FQDN を検証する必要があります。

たとえば、Oozie サービスは 1 つのヘッド ノードでのみ実行でき、SSH セッションからの `oozie` コマンドの使用にはサービスの URL が必要です。これは、次のコマンドを使用して Ambari から取得できます。

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

これにより、`oozie` コマンドで使用する内部 URL を含む、次に似た値が返ります。

	"oozie.base.url": "http://headnode0.CLUSTERNAME-ssh.d9.internal.cloudapp.net:11000/oozie"

## サービスの状態を確認する方法

Ambari Web UI または Ambari REST API のいずれかを使用して、ヘッド ノードで実行されるサービスの状態を確認できます。

###Ambari REST API

次のコマンドを使用して、Ambari REST API を通してサービスの状態を確認できます。

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* **PASSWORD** を HTTP ユーザー (admin) アカウントのパスワードに置き換えます。

* **CLUSTERNAME** をクラスターの名前に置き換えます。

* **SERVICENAME** を、その状態を確認するサービスの名前に置き換えます。

たとえば、**mycluster** という名前のクラスターで、**HDFS** サービスの状態を、**password** というパスワードで確認するには、次のコマンドを使用します。

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

応答は次のようになります。

	{
	  "href" : "http://headnode0.mycluster-ssh.j7.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

URL から、サービスが現在 **headnode0** で実行されていることがわかります。

「state」から、サービスが現在実行されている(**STARTED**) ことがわかります。

どのようなサービスがクラスターにインストールされているかわからない場合は、次のコマンドを使用して一覧を取得できます。

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####サービス コンポーネント

サービスの中に、個別に状態を確認できるコンポーネントが含まれている場合があります。たとえば、HDFS には、NameNode コンポーネントが含まれています。コンポーネントに関する情報を表示するコマンドは次のようになります。

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

サービスがどのようなコンポーネントを備えているかわからない場合は、次のコマンドを使用して一覧を取得できます。

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

###Ambari Web UI

Ambari Web UI は、https://CLUSTERNAME.azurehdinsight.net で表示できます。**CLUSTERNAME** をクラスターの名前に置き換えます。メッセージが表示されたら、クラスターの HTTP ユーザーの資格情報を入力します。既定の HTTP ユーザー名は **admin**であり、パスワードはクラスターを作成するときに入力したパスワードです。

Ambari ページにアクセスすると、インストールされているサービスがページの左側に表示されます。

![インストールされているサービス](./media/hdinsight-high-availability-linux/services.png)

状態を示すためにサービスの横に表示されるアイコンがあります。サービスに関連するアラートは、ページの上部にある **[アラート]** リンクを使用して表示できます。各サービスを選択して、その詳細を表示できます。

サービス ページには、各サービスの状態と構成に関する情報が表示されますが、サービスが実行されているヘッド ノードの情報は表示されません。この情報を表示するには、ページの上部にある **[ホスト]** リンクを使用します。これにより、ヘッド ノードを含むクラスター内のホストが表示されます。

![ホストの一覧](./media/hdinsight-high-availability-linux/hosts.png)

どちらかのヘッド ノードのリンクを選択すると、そのノードで実行されているサービスとコンポーネントが表示されます。

![コンポーネントの状態](./media/hdinsight-high-availability-linux/nodeservices.png)

## セカンダリ ヘッド ノードのログ ファイルへのアクセス方法

###SSH

SSH 経由でヘッド ノードに接続している間、ログ ファイルは **/var/log** に保存されます。たとえば、**/var/log/hadoop-yarn/yarn** には、YARN のログが含まれます。

各ヘッド ノードは独自のログ エントリを持つ可能性があるため、両方のログを確認する必要があります。

###Ambari

> [AZURE.NOTE]Ambari を通してログ ファイルにアクセスするには、SSH トンネルが必要です。これは、個々のサービスの Web サイトはインターネット上に公開されていないためです。SSH トンネルの使用の詳細については、「[SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」を参照してください。

Ambari Web UI から、ログを表示するサービス (例: YARN) を選択し、**[クイック リンク]** を使用してログを表示するヘッド ノードを選択します。

![クイック リンクを使用したログの表示](./media/hdinsight-high-availability-linux/viewlogs.png)

## ヘッド ノードのサイズの構成方法 ##

ヘッド ノードのサイズは、クラスターの作成中にのみ選択できます。ヘッド ノードの既定のサイズは **A3** であり、これは 4 コア、7 GB のメモリ、285 GB のローカル ストレージを提供します。「[HDInsight の料金](http://azure.microsoft.com/pricing/details/hdinsight/)」ページで、HDInsight で使用できる、コア、メモリ、ローカル ストレージを含むさまざまな VM サイズの一覧を確認できます。

新しいクラスターを作成するときに、ノードのサイズを指定できます。この後、[Azure プレビュー ポータル][preview-portal]、[Azure PowerShell][azure-powershell]、[Azure CLI][azure-cli] を使用してサイズを指定する方法についての情報を示します。

* **Azure プレビュー ポータル**: 新しいクラスターを作成するときに、ヘッド ノードとデータ (ワーカー) ノードの両方でそのサイズ (価格レベル) を設定できます。

	![ノード サイズの選択画面を示しているクラスター作成ウィザードの画像](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**: `azure hdinsight cluster create` コマンドを使用するときに、`--headNodeSize` パラメーターを使用してヘッド ノードのサイズを設定できます。

* **Azure PowerShell**: `New-AzureHDInsightCluster` コマンドレットを使用するときに、`-HeadNodeVMSize` パラメーターを使用してヘッド ノードのサイズを設定できます。

##次のステップ

このドキュメントでは、Azure HDInsight での Hadoop の高可用性の実現方法を説明しました。このドキュメントに記載された事柄の詳細については、次の記事を参照してください。

- [Ambari REST リファレンス](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Azure CLI のインストールと構成](../xplat-cli-install.md)

- [Azure PowerShell のインストールおよび構成](../powershell-install-configure.md)

- [Ambari を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari.md)

- [Provision Linux-based HDInsight clusters (Linux ベースの HDInsight クラスターのプロビジョニング)](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=Oct15_HO4-->