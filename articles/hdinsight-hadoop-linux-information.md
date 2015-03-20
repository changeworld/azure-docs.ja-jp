<properties
   pageTitle="Linux ベースの HDInsight の Hadoop について知っておくべきこと | Azure"
   description="Linux ベースの HDInsight クラスターは、Azure クラウドで実行される使い慣れた Linux 環境での Hadoop を提供します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Linux での HDInsight の使用 (プレビュー)

Linux ベースの HDInsight クラスターは、Azure クラウドで実行される使い慣れた Linux 環境での Hadoop を提供します。使い方は、ほとんどの点で、Linux 環境の他の Hadoop とまったく同じです。ここでは、知っておく必要がある特定の違いについて説明します。

##ドメイン名

クラスターへの接続時に使用する完全修飾ドメイン名 (FQDN) は、**&lt;clustername>.azurehdinsight.net** または (SSH のみ) **&lt;clustername>.aurehdinsight.net** です。

##サービスへのリモート アクセスが可能

* **Ambari (Web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE] クラスター管理者のユーザー名とパスワードを使用して認証した後、Ambari にログインします。その際も、クラスター管理者のユーザー名とパスワードを使用します。
	> 
	> 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

	クラスター用の Ambari にはインターネットから直接アクセスできますが、一部の機能では、クラスターが使用する内部ドメイン名によってノードにアクセスします。これは内部ドメイン名で、パブリックではないため、インターネット経由で機能にアクセスしようとすると、サーバーが見つからないことを示すエラーが発生します。

	この問題を解決するには、SSH トンネルを使用してプロキシ Web トラフィックをクラスター ヘッド ノードに送信します。ローカル コンピューターでポートからクラスターへのトラフィックに対して SSH トンネルを作成するには、次の記事を参照してください。

	* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用する</a> - `ssh` コマンドを使用して SSH トンネルを作成する手順

	* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する</a> - Putty を使用して SSH トンネルを作成する手順

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE] クラスター管理者のユーザー名とパスワードを使用して認証します。
	> 
	> 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE] クラスター管理者のユーザー名とパスワードを使用して認証します。
	> 
	> 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net on port 22

	> [AZURE.NOTE] クラスター ヘッド ノードにアクセスするには、クライアント コンピューターから SSH を使用する必要があります。接続されたら、ヘッド ノードから SSH を使用して worker ノードにアクセスできます。

##ファイルの場所

Hadoop 関連ファイルは、 `/usr/hdp/current` のクラスター ノードにあります。

サンプルのデータと jar は、'/example' または 'wasb:///example' の HDFS (WASB) にあります。

##HDFS、WASB、およびストレージのベスト プラクティス

ほとんどの Hadoop ディストリビューションでは、Hadoop 分散ファイル システム (HDFS) はクラスター内のコンピューターのローカル ストレージによって支えられています。これは効率的である一方、コンピューティング リソースが時間単位で課金されるクラウド ベースのソリューションでは、コストが高くなります。

HDInsight は Azure BLOB ストレージを既定の保管場所として使用します。これには次の利点があります。

* 低コストの長期ストレージ

* Web サイト、ファイル アップロード/ダウンロード ユーティリティ、さまざまな言語の SDK、Web ブラウザーなどの外部サービスからアクセスできます。

HDInsight の既定の保管場所であるため、通常は何もしなくても使用できます。たとえば、次のコマンドは、Azure BLOB ストレージに保存されている **/example/data** フォルダー内のファイルの一覧を表示します。

	hadoop fs -ls /example/data

一部のコマンドでは、BLOB ストレージを使用することを指定する必要があります。その場合、コマンドにプレフィックスとして **WASB://** を付けることができます。

HDInsight では、クラスターに複数の BLOB ストレージ アカウントを関連付けることもできます。既定以外の BLOB ストレージ アカウントのデータにアクセスするには、**WASB://&lt;container-name>@&lt;account-name>.blob.core.windows.net/** という形式を使用できます。たとえば、次のコマンドは、指定したコンテナーとストレージ アカウントについて、**/example/data** ディレクトリの内容を表示します。

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

###クラスターで使用している BLOB ストレージ

クラスター作成時、既存のストレージ アカウントとコンテナーを使用するか、新しく作成するかを選択します。後になって、何を選択したかを忘れることもあります。次の方法を使用してストレージ アカウントとコンテナーを確認できます。

**Azure 管理ポータル**

1. <a href="https://manage.windowsazure.com/" target="_blank">Azure の管理ポータル</a>で、HDInsight クラスターを選択します。

2. ページの上部にある **[ダッシュボード]** をクリックします。

3. ストレージ アカウントとコンテナーの一覧が、ページの**リンク済みリソース**のセクションに表示されます。

	![linked resources](./media/hdinsight-hadoop-linux-information/storageportal.png)

**Azure クロスプラットフォーム コマンド ライン インターフェイス**

*近日対応予定*

###BLOB ストレージにアクセスする方法

クラスターから Hadoop コマンドを使用する以外にも、BLOB にアクセスするさまざまな方法があります。

* <a href="http://azure.microsoft.com/documentation/articles/xplat-cli/" target="_blank">Azure クロスプラットフォーム コマンド ライン インターフェイス</a>に関するページ - インストールしたら、ストレージの使用方法のヘルプについては  `azure storage` に関するトピック、BLOB 固有のコマンドのヘルプについては  `azure blob` に関するトピックを参照してください。

* さまざまな SDK:

	* <a href="https://github.com/Azure/azure-sdk-for-java" target="_blank">Java</a>

	* <a href="https://github.com/Azure/azure-sdk-for-node" target="_blank">Node.js</a>

	* <a href="https://github.com/Azure/azure-sdk-for-php" target="_blank">PHP</a>

	* <a href="https://github.com/Azure/azure-sdk-for-python" target="_blank">Python</a>

	* <a href="https://github.com/Azure/azure-sdk-for-ruby" target="_blank">Ruby</a>

	* <a href="https://github.com/Azure/azure-sdk-for-net" target="_blank">.NET</a>

* <a href="https://msdn.microsoft.com/library/azure/dd135733.aspx" target="_blank">ストレージ REST API</a>


##次のステップ

* [HDInsight での Hive の使用](../hdinsight-use-hive/)
* [HDInsight での Pig の使用](../hdinsight-use-pig/)
* [HDInsight での MapReduce ジョブの使用](../hdinsight-use-mapreduce)


<!--HONumber=47-->
