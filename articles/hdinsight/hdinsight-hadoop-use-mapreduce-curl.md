<properties
   pageTitle="HDInsight での MapReduce と Hadoop の Curl の使用 | Microsoft Azure"
   description="Curl を使用して HDInsight の Hadoop で MapReduce ジョブをリモートで実行する方法を説明します。"
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
   ms.date="03/03/2015"
   ms.author="larryfr"/>

# Curl を使用して HDInsight の Hadoop で MapReduce ジョブを実行

[AZURE.INCLUDE [mapreduce セレクター](../../includes/hdinsight-selector-use-mapreduce.md)]

このドキュメントでは、Curl を使用して HDInsight クラスターの Hadoop で MapReduce ジョブを実行する方法を説明します。

Curl は、未加工の HTTP 要求を使用して HDInsight とやり取りし、MapReduce ジョブを実行する方法を示すために使用します。これは、HDInsight クラスターで提供される WebHCat REST API (旧称: Templeton) を使用することで機能します。

> [AZURE.NOTE]Linux ベースの Hadoop サーバーは使い慣れているが、HDInsight は初めてという場合は、「[Linux ベースの HDInsight の Hadoop について知っておくべきこと](hdinsight-hadoop-linux-information.md)」をご覧ください。

## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* HDInsight クラスター (Linux または Windows ベース) の Hadoop

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Curl を使用した MapReduce ジョブの実行

> [AZURE.NOTE]Curl、または WebHCat を使用したその他の REST 通信が使用できる場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。また、サーバーへの要求の送信に使用する URI にクラスター名を含める必要があります。
>
> このセクションのコマンドでは、**USERNAME** をクラスターを認証するユーザーの名前に、**PASSWORD** をユーザー アカウントのパスワードに置き換えてください。**CLUSTERNAME** はクラスターの名前に置き換えます。
>
> REST API のセキュリティは、<a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">基本アクセス認証</a>の使用によって保護されています。資格情報を安全にサーバーに送信するには、必ず HTTPS を使用して要求を行う必要があります。

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    次のような応答を受け取ります。

        {"status":"ok","version":"v1"}

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-u**: 要求の認証に使用するユーザー名とパスワード
    * **-G**: GET 要求であることを示します。

    URI の先頭は **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** で、これはすべての要求で共通です。

2. MapReduce ジョブを送信するには、次のコマンドを使用します。

		curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasb:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasb:///example/data/gutenberg/davinci.txt -d arg=wasb:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    URI の末尾 (/mapreduce/jar) により、この要求では jar ファイルのクラスから MapReduce ジョブが起動されることが WebHCat に通知されます。このコマンドで使用されるパラメーターの意味は次のとおりです。

	* **-d**: `-G` が使用されていないため、要求は既定で POST メソッドになります。 `-d` は要求で送信されるデータ値を指定します。

        * **user.name**: コマンドを実行するユーザー
        * **jar**: 実行されるクラスを含む jar ファイルの場所
        * **class**: MapReduce ロジックを含むクラス
        * **arg**: MapReduce ジョブに渡される引数で、この場合は、入力テキスト ファイルと出力に使用するディレクトリになります。

    このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します。

        {"id":"job_1415651640909_0026"}

3. ジョブのステータスを確認するには、次のコマンドを使用します。**JOBID** を前の手順で返された値に置き換えます。たとえば、戻り値が `{"id":"job_1415651640909_0026"}` の場合、JOBID は `job_1415651640909_0026` になります。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	ジョブが完了している場合、ステータスは "SUCCEEDED" になります。

    > [AZURE.NOTE]この Curl 要求では、ジョブに関する情報が記載された JSON ドキュメントが返されます。状態値のみを取得するには jq を使用します。

4. ジョブのステータスが **SUCCEEDED** に変わったら、Azure BLOB ストレージからジョブの結果を取得できます。クエリで渡される `statusdir` パラメーターには出力ファイルの場所が含まれます。この場合は、**wasb:///example/curl** になります。このアドレスではジョブの出力は、HDInsight クラスターが使用する既定のストレージ コンテナーの **example/curl** ディレクトリに保存されます。

これらのファイルは [Mac、Linux、Windows 用の Azure CLI](../xplat-cli.md) を使用して一覧表示し、ダウンロードできます。たとえば、**example/curl** 内のファイルを一覧表示するには、次のコマンドを使用します。

	azure storage blob list <container-name> example/curl

ファイルをダウンロードするには、次のコマンドを使用します。

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]`-a` や `-k` パラメーターを使用して BLOB を含むストレージ アカウントの名前を指定するか、環境変数 **AZURE_STORAGE_ACCOUNT** と **AZURE_STORAGE_ACCESS_KEY** を設定する必要があります。詳細については、「[データを HDInsight へアップロードする方法](hdinsight-upload-data.md)」をご覧ください。

## <a id="summary"></a>概要

このドキュメントを参照して、未加工の HTTP 要求を使用して、HDInsight クラスターで Hive ジョブを実行、監視し、その結果を表示できます。

この記事で使用されている REST インターフェイスの詳細については、「[WebHCat リファレンス](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)」に関するページをご覧ください。

## <a id="nextsteps"></a>次のステップ

HDInsight での MapReduce ジョブに関する全般的な情報:

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

<!--HONumber=54--> 