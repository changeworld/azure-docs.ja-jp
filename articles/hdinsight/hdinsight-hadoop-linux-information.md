<properties
   pageTitle="Linux ベースの HDInsight で Hadoop を使用するためのヒント | Microsoft Azure"
   description="ここには、Azure クラウドで稼動する使い慣れた Linux 環境で、Linux ベースの HDInsight (Hadoop) クラスターを使用するための実装上のヒントを記載します。"
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
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# Linux での HDInsight の使用方法

Linux ベースの Azure HDInsight クラスターは、Azure クラウドで実行される使い慣れた Linux 環境での Hadoop を提供します。使い方は、ほとんどの点で、Linux 環境の他の Hadoop とまったく同じです。ここでは、知っておく必要がある特定の違いについて説明します。

##前提条件

このドキュメントの手順の多くでは次のユーティリティを使用するので、これらがシステムにインストールされている必要があります。

* [cURL](https://curl.haxx.se/) - Web ベースのサービスとの通信に使用します
* [jq](https://stedolan.github.io/jq/) - JSON ドキュメントの解析に使用します
* [Azure CLI](../xplat-cli-install.md) - Azure サービスをリモート管理するために使用します

	[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## ドメイン名

インターネットからクラスターへの接続時に使用する完全修飾ドメイン名 (FQDN) は、**<clustername>.azurehdinsight.net** または (SSH のみ) **<clustername-ssh>.azurehdinsight.net** です。

内部的には、クラスターの各ノードに、クラスターの構成時に割り当てられた名前が与えられます。クラスター名を見つけるには、Ambari Web UI の __[Hosts]__ (ホスト) ページにアクセスするか、次の方法で Ambari REST API からホストの一覧を返します。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

いずれかの対話形式で__PASSWORD__ を管理者アカウントのパスワードに、__CLUSTERNAME__ をクラスターの名前に変えます。これで返される JSON ドキュメントにクラスター内のホストの一覧が含まれます。jq により、クラスター内の各ホストの `host_name` 要素値が取り出されます。

特定のサービスのノード名を見つける必要がある場合、そのコンポーネントについて Ambari に問い合わせることができます。たとえば、HDFS 名のノードのホストを見つけるには、次の方法を利用します。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

これでサービスの説明が記載された JSON ドキュメントが返されます。jq により、ホストの `host_name` 値のみが引き出されます。

## サービスへのリモート アクセス

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

	クラスター管理者のユーザー名とパスワードを使用して認証した後、Ambari にログインします。その場合も、クラスター管理者のユーザー名とパスワードを使用します。

	認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

	> [AZURE.IMPORTANT]クラスター用の Ambari にはインターネットから直接アクセスできますが、一部の機能では、クラスターが使用する内部ドメイン名によってノードにアクセスします。これは内部ドメイン名で、パブリックではないため、インターネット経由で機能にアクセスしようとすると、サーバーが見つからないことを示すエラーが発生します。
	>
	> Ambari Web UI の全機能を使用するには、SSH トンネルを使用して、クラスター ヘッド ノードに対する Web トラフィックがプロキシ経由になるようにします。「[SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」を参照してください。

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]クラスター管理者のユーザー名とパスワードを使用して認証します。
	>
	> 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]クラスター管理者のユーザー名とパスワードを使用して認証します。
	>
	> 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **SSH** - ポート 22 または 23 上の &lt;clustername>-ssh.azurehdinsight.net。ポート 22 はプライマリ ヘッドノードへの接続に、23 はセカンダリ ヘッドノードへの接続に使用されます。ヘッド ノードの詳細については、「[HDInsight における Hadoop クラスターの可用性と信頼性](hdinsight-high-availability-linux.md)」を参照してください。

	> [AZURE.NOTE]クラスター ヘッド ノードにアクセスするには、クライアント コンピューターから SSH を使用する必要があります。接続されたら、ヘッド ノードから SSH を使用して worker ノードにアクセスできます。

## ファイルの場所

Hadoop 関連ファイルは、`/usr/hdp` のクラスター ノードにあります。このディレクトリには、次のサブディレクトリが含まれます。

* __2.2.4.9-1__: このディレクトリは HDInsight が使用する Hortonworks Data Platform のバージョンから名前が付けられるため、クラスター上の番号がここに記載されたものと異なる場合があります。
* __current__: このディレクトリには、__2.2.4.9-1__ ディレクトリ下のディレクトリへのリンクが含まれており、(変わる可能性がある) バージョン番号を、ファイルにアクセスするたびに入力する手間を省くために存在します。

サンプル データ ファイルと JAR ファイルは、Hadoop 分散ファイル システム (HDFS) または Azure BLOB ストレージの '/example' または 'wasbs:///example' にあります。

## HDFS、Azure BLOB ストレージ、ストレージのベスト プラクティス

ほとんどの Hadoop ディストリビューションでは、HDFS はクラスター内のコンピューターのローカル ストレージによって支えられています。これは効率的である一方、コンピューティング リソースが時間または分単位で課金されるクラウド ベースのソリューションでは、コストが高くなります。

HDInsight は Azure BLOB ストレージを既定の保管場所として使用します。これには次の利点があります。

* 低コストの長期ストレージ

* Websites、ファイル アップロード/ダウンロード ユーティリティ、さまざまな言語の SDK、Web ブラウザーなどの外部サービスからアクセスできます。

HDInsight の既定の保管場所であるため、通常は何もしなくても使用できます。たとえば、次のコマンドは、Azure BLOB ストレージに保存されている **/example/data** フォルダー内のファイルの一覧を表示します。

	hdfs dfs -ls /example/data

一部のコマンドでは、BLOB ストレージを使用することを指定する必要があります。その場合、コマンドにプレフィックスとして **wasb://**、または **wasbs://** を付けることができます。

HDInsight では、クラスターに複数の BLOB ストレージ アカウントを関連付けることもできます。既定以外の BLOB ストレージ アカウントのデータにアクセスするには、**wasbs://&lt;container-name>@<account-name>.blob.core.windows.net/** という形式を使用できます。たとえば、次のコマンドは、指定したコンテナーと BLOB ストレージ アカウントについて、**/example/data** ディレクトリの内容を表示します。

	hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### クラスターで使用している BLOB ストレージ

クラスター作成時、既存の Azure ストレージ アカウントとコンテナーを使用するか、新しく作成するかを選択します。後になって、何を選択したかを忘れることもあります。Ambari REST API を使用して、既定のストレージ アカウントとコンテナーを調べることができます。

1. 次のコマンドを使用して、curl を使用する HDFS の構成情報を取得し、[jq](https://stedolan.github.io/jq/) を使用してフィルター処理します。

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] このコードは、この情報が含まれるサーバーに適用される最初の構成を返します (`service_config_version=1`)。クラスターの作成後に変更された値を取得する場合は、構成のバージョンを一覧表示した後で最新の構成を取得することが必要になる場合があります。

    このコードは、次のような値を返します。ここで、__CONTAINER__ は既定のコンテナー、__ACCOUNTNAME__ は Azure ストレージ アカウント名です。

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. ストレージ アカウントのリソース グループを取得し、[Azure CLI](../xplat-cli-install.md) を使用します。次のコマンドで、__ACCOUNTNAME__ を、Ambari から取得したストレージ アカウント名に置き換えます。

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    アカウントのリソース グループ名が返されます。
    
    > [AZURE.NOTE] このコマンドから何も返されない場合は、Azure CLI を Azure リソース マネージャー モードに変更し、コマンドを再度実行することが必要になる場合があります。Azure リソース マネージャー モードに切り替えて、次のコマンドを実行します。
    >
    > `azure config mode arm`
    
2. ストレージ アカウントのキーを取得します。__GROUPNAME__ を、前の手順で取得したリソース グループ名に置き換えてください。__ACCOUNTNAME__ を、ストレージ アカウント名に置き換えてください。

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    アカウントのプライマリ キーが返されます。

次のように Azure ポータルを使用して、ストレージ情報を検索することもできます。

1. [Azure ポータル](https://portal.azure.com/)で HDInsight クラスターを選択します。

2. __[Essentials]__ セクションで、__[すべての設定]__ を選択します。

3. __[設定]__ で __[Azure Storage キー]__ を選択します。

4. __[Azure Storage キー]__ で、表示されているストレージ アカウントのいずれかを選択します。これにより、ストレージ アカウントに関する情報が表示されます。

5. キーのアイコンを選択します。これにより、このストレージ アカウントのキーが表示されます。

### BLOB ストレージにアクセスする方法

クラスターから Hadoop コマンドを使用する以外にも、BLOB にアクセスするさまざまな方法があります。

* [Mac、Linux、Windows 用の Azure CLI](../xplat-cli-install.md): Azure を使用するためのコマンドライン インターフェイス コマンド。インストール後、ストレージの使用方法については `azure storage`、BLOB 特有のコマンドについては `azure blob` をご覧ください。

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): Azure Storage で BLOB を使用するための Python スクリプト。

* さまざまな SDK:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.JS](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [ストレージ REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>クラスターのスケーリング

クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用されるデータ ノードの数を、クラスターを削除して再作成することなく、変更できます。

クラスターで他のジョブまたはプロセスを実行している間にスケーリング操作を実行できます。

別のクラスターの種類は、次のようにスケーリングすることによって影響を受けます。

* __Hadoop__: クラスター内のノードの数をスケール ダウンすると、クラスター内のサービスの一部が再起動します。これにより、スケーリング操作の完了時に、実行中および保留中のジョブが失敗する可能性があります。ただし、操作が完了した時点で、ジョブを再送信できます。

* __HBase__: リージョンのサーバーは、スケーリング操作の完了後、数分以内に自動的に調整されます。リージョンのサーバーを手動で調整するには、次の手順を使用します。

	1. SSH を使用して HDInsight クラスターに接続します。HDInsight での SSH の使用方法の詳細については、次のいずれかのドキュメントを参照してください。

		* [Linux、Unix、Mac OS X から HDInsight で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

		* [Windows から HDInsight で SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

	1. HBase シェルを開始するには、次のコマンドを使用します。

			hbase shell

	2. HBase シェルが読み込まれたら、次のコマンドを使用してリージョンのサーバーを手動で調整します。

			balancer

* __Storm__: スケーリング操作が実行された後に、実行中の Storm トポロジをすべて再調整する必要があります。これにより、トポロジはクラスター内のノードの新しい数に基づいて並列処理の設定を再調整できます。実行中のトポロジを再調整するには、次のオプションのいずれかを使用します。

	* __SSH__: サーバーに接続し、次のコマンドを使用してトポロジを再調整します。

			storm rebalance TOPOLOGYNAME

		また、トポロジによって最初に提供された並列処理のヒントを上書きするパラメーターも指定できます。たとえば、`storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` は、トポロジを再構成して、5 個の worker プロセス、blue-spout コンポーネント用の 3 個の実行プログラム、yellow-bolt コンポーネント用の 10 個の実行プログラムにします。

	* __Storm UI__: 次の手順により、Storm UI を使用してトポロジを再調整します。

		1. Web ブラウザーで https://CLUSTERNAME.azurehdinsight.net/stormui__ に移動します。CLUSTERNAME は実際の Storm クラスターの名前に置き換えます。メッセージが表示されたら、HDInsight クラスター管理者 (admin) の名前と、クラスターの作成時に指定したパスワードを入力します。

		3. 再調整するトポロジを選択し、__[再調整]__ ボタンをクリックします。再調整の操作が実行されるまでの待ち時間を入力します。

HDInsight クラスターのスケーリングに関する具体的な情報については、以下を参照してください。

* [Azure ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-portal-linux.md#scaling)

* [Azure PowerShell を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-command-line.md#scaling)

## Hue (またはその他の Hadoop コンポーネント) のインストール方法

HDInsight は、管理されたサービスです。つまり、問題が検出された場合、Azure によってクラスター内のノードは自動的に破棄されて、再プロビジョニングされる可能性があります。そのため、クラスター ノードにコンポーネントを直接、手動でインストールすることは推奨されません。代わりに、次をインストールする必要があるとき、[HDInsight スクリプト アクション](hdinsight-hadoop-customize-cluster.md)を使用します。

* Spark や Hue など、サービスや Web サイト。
* クラスターの複数のノードで構成変更を必要とするコンポーネント。たとえば、必要な環境変数、ログ ディレクトリの作成、構成ファイルの作成。

スクリプト アクションとは、クラスターのプロビジョニング中に実行される Bash スクリプトで、クラスター上に追加のコンポーネントをインストールし、構成するために使用できます。次のコンポーネントをインストールするスクリプトの例が用意されています。

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

独自のスクリプト アクションを開発する方法の詳細については、「[HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」を参照してください。

###Jar ファイル

一部の Hadoop 技術は自己完結型の jar ファイルで提供されます。このファイルには MapReduce ジョブの一環として、あるいは Pig または Hive 内から使用される関数が含まれています。ファイルはスクリプト アクションでインストールできますが、多くの場合、セットアップを必要とせず、プロビジョニング後にクラスターにアップロードし、直接使用できます。クラスターの再イメージ化の後もコンポーネントが残っていることを確認するには、WASB に jar ファイルを保存します。

たとえば、最新版の [DataFu](http://datafu.incubator.apache.org/) を使用する場合、プロジェクトを含む jar をダウンロードし、それを HDInsight クラスターにアップロードできます。その後、Pig や Hive からそれを使用する方法については、DataFu のドキュメントに従います。

> [AZURE.IMPORTANT]スタンドアロン jar ファイルである一部のコンポーネントは HDInsight で提供され、パスにはありません。特定のコンポーネントを探している場合、次を利用し、クラスターでそれを検索できます。
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> 一致する jar ファイルがあれば、そのパスが返されます。

クラスターがスタンドアロン jar ファイルとしてあるバージョンのコンポーネントを提供するが、別のバージョンを希望する場合、クラスターに新しいバージョンのコンポーネントをアップロードし、ジョブでそれを試すことができます。

> [AZURE.WARNING]HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/ja-JP/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) や [Spark](http://spark.apache.org/) などのプロジェクト サイトがあります。

## 次のステップ

* [Windows ベースの HDInsight から Linux ベースへの移行](hdinsight-migrate-from-windows-to-linux.md)
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0921_2016-->