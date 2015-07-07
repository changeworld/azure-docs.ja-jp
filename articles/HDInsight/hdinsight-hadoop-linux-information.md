<properties
   pageTitle="Linux ベースの HDInsight で Hadoop を使用するためのヒント | Microsoft Azure"
   description="ここには、Azure クラウドで稼動する使い慣れた Linux 環境で、Linux ベースの HDInsight (Hadoop) クラスターを使用するための実装上のヒントを記載します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/27/2015"
   ms.author="larryfr"/>

# Linux での HDInsight の使用方法 (プレビュー)

Linux ベースの Azure HDInsight クラスターは、Azure クラウドで実行される使い慣れた Linux 環境での Hadoop を提供します。使い方は、ほとんどの点で、Linux 環境の他の Hadoop とまったく同じです。ここでは、知っておく必要がある特定の違いについて説明します。

## ドメイン名

クラスターへの接続時に使用する完全修飾ドメイン名 (FQDN) は、**<clustername>.azurehdinsight.net** または (SSH のみ) **<clustername-ssh>.azurehdinsight.net** です。

## サービスへのリモート アクセス

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE]クラスター管理者のユーザー名とパスワードを使用して認証した後、Ambari にログインします。その際も、クラスター管理者のユーザー名とパスワードを使用します。
	>
	> 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

	クラスター用の Ambari にはインターネットから直接アクセスできますが、一部の機能では、クラスターが使用する内部ドメイン名によってノードにアクセスします。これは内部ドメイン名で、パブリックではないため、インターネット経由で機能にアクセスしようとすると、サーバーが見つからないことを示すエラーが発生します。

	この問題を解決するには、SSH トンネルを使用してプロキシ Web トラフィックをクラスター ヘッド ノードに送信します。ローカル コンピューターでポートからクラスターへのトラフィックに対して SSH トンネルを作成するには、次の記事の **SSH トンネリング** セクションをご覧ください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md) - `ssh` コマンドを使用して SSH トンネルを作成する手順

	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows) - PuTTY を使用して SSH トンネルを作成する手順

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]クラスター管理者のユーザー名とパスワードを使用して認証します。
	>
	> 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]クラスター管理者のユーザー名とパスワードを使用して認証します。
	>
	> 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **SSH** - <clustername>-ssh.azurehdinsight.net on port 22

	> [AZURE.NOTE]クラスター ヘッド ノードにアクセスするには、クライアント コンピューターから SSH を使用する必要があります。接続されたら、ヘッド ノードから SSH を使用してワーカー ノードにアクセスできます。

## ファイルの場所

Hadoop 関連ファイルは、`/usr/hdp/current` のクラスター ノードにあります。

サンプル データ ファイルと JAR ファイルは、Hadoop 分散ファイル システム (HDFS) または Azure BLOB ストレージの '/example' または 'wasb:///example' にあります。

## HDFS、Azure BLOB ストレージ、ストレージのベスト プラクティス

ほとんどの Hadoop ディストリビューションでは、HDFS はクラスター内のコンピューターのローカル ストレージによって支えられています。これは効率的である一方、コンピューティング リソースが時間単位で課金されるクラウド ベースのソリューションでは、コストが高くなります。

HDInsight は Azure BLOB ストレージを既定の保管場所として使用します。これには次の利点があります。

* 低コストの長期ストレージ

* Web サイト、ファイル アップロード/ダウンロード ユーティリティ、さまざまな言語の SDK、Web ブラウザーなどの外部サービスからアクセスできます。

HDInsight の既定の保管場所であるため、通常は何もしなくても使用できます。たとえば、次のコマンドは、Azure BLOB ストレージに保存されている **/example/data** フォルダー内のファイルの一覧を表示します。

	hadoop fs -ls /example/data

一部のコマンドでは、BLOB ストレージを使用することを指定する必要があります。その場合、コマンドにプレフィックスとして **WASB://** を付けることができます。

HDInsight では、クラスターに複数の BLOB ストレージ アカウントを関連付けることもできます。既定以外の BLOB ストレージ アカウントのデータにアクセスするには、**WASB://&lt;container-name>@<account-name>.blob.core.windows.net/** という形式を使用できます。たとえば、次のコマンドは、指定したコンテナーと BLOB ストレージ アカウントについて、**/example/data** ディレクトリの内容を表示します。

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### クラスターで使用している BLOB ストレージ

クラスター作成時、既存の Azure ストレージ アカウントとコンテナーを使用するか、新しく作成するかを選択します。後になって、何を選択したかを忘れることもあります。次の方法を使用してストレージ アカウントとコンテナーを確認できます。

**Ambari API の使用**

1. 次のコマンドを使用して、HDFS の構成情報を取得します。

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. 返された JSON データで、`fs.defaultFS` エントリを見つけます。これには、既定のコンテナーとストレージ アカウント名が次のような形式で含まれています。

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

	> [AZURE.TIP][jq](http://stedolan.github.io/jq/) をインストールしている場合は、次のコマンドを使用すると `fs.defaultFS` エントリのみが返されます。
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`
	
3. ストレージ アカウントの認証に使用するキー、または、クラスターに関連付けられているセカンダリ ストレージ アカウントを見つけるために、次のコマンドを使用します。

		curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"
		
4. 返された JSON データで、`fs.azure.account.key` で始まるエントリを見つけます。エントリ名の残りの部分は、ストレージ アカウント名です。たとえば、「`fs.azure.account.key.mystorage.blob.core.windows.net`」のように入力します。このエントリに格納されている値が、ストレージ アカウントの認証に使用するキーです。

	> [AZURE.TIP][jq](http://stedolan.github.io/jq/) をインストールした場合は、次のコマンドを使用してキーおよび値の一覧を取得できます。
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`


**Azure ポータル**

1. [Azure ポータル](https://manage.windowsazure.com/)で HDInsight クラスターを選択します。

2. ページの上部にある **[ダッシュボード]** をクリックします。

3. ストレージ アカウントとコンテナーの一覧が、ページの **[リンク済みリソース]** セクションに表示されます。

	![リンク済みリソース](./media/hdinsight-hadoop-linux-information/storageportal.png)

### BLOB ストレージにアクセスする方法

クラスターから Hadoop コマンドを使用する以外にも、BLOB にアクセスするさまざまな方法があります。

* [Mac、Linux、Windows 用の Azure CLI](../xplat-cli.md): Azure を使用するためのコマンドライン インターフェイス コマンド。インストール後、ストレージの使用方法については `azure storage`、BLOB 特有のコマンドについては `azure blob` をご覧ください。

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): Azure ストレージで BLOB を使用するための Python スクリプト。

* さまざまな SDK:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.JS](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [ストレージ REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## 次のステップ

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)
 

<!---HONumber=62-->