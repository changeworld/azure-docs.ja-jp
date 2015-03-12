<properties
   pageTitle="HDInsight での Hadoop Pig の使用 | Azure"
   description="SSH を使用して HDInsight で Pig と Hadoop を使用する方法を説明します。"
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
 
# Curl を使用して HDInsight の Hadoop で Pig ジョブを実行

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、Curl を使用して HDInsight クラスター で Pig Latin ジョブを実行する方法を説明します。Pig Latin プログラミング言語では、入力データに適用される変換を記述し、目的の出力を生成することができます。

Curl は、未加工の HTTP 要求を使用して HDInsight とやり取りし、Pig ジョブを実行、監視し、その結果を取得する方法を示すために使用します。これは、HDInsight クラスターで提供される WebHCat REST API (旧称: Templeton) を使用することで機能します。

> [AZURE.NOTE] Linux ベースの Hadoop サーバーに慣れており、HDInsight を初めて使用するユーザーの場合は、「 <a href="../hdinsight-hadoop-linux-information/" target="_blank">What you need to know about Linux-based Hadoop on HDInsight (HDInsight での Linux ベースの Hadoop について知っておくべきこと)</a>」を参照してください。

## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure HDInsight (HDInsight での Hadoop) クラスター (Linux または Windows ベース)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Curl を使用した Pig ジョブの実行

> [AZURE.NOTE] Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名およびパスワードを指定して要求を認証する必要があります。また、サーバーへの要求の送信に使用する URI にクラスター名を含める必要があります。
> 
> このセクションのコマンドでは、**USERNAME** をクラスターを認証するユーザーの名前に、**PASSWORD** をユーザー アカウントのパスワードに置き換える必要があります。**CLUSTERNAME** はクラスターの名前に置き換えます。
> 
> REST API のセキュリティは、 <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">基本認証を使用して保護します</a>。資格情報を安全にサーバーに送信するには、必ず HTTPS を使用して要求を行う必要があります。

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。 

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    次のような応答を受け取ります。

        {"status":"ok","version":"v1"}

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-u** - 要求の認証に使用するユーザー名およびパスワード
    * **-G** - GET 要求であることを示します。

    URL の先頭は **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** で、これはすべての要求で共通です。パス **/status** は、要求がサーバー用の WebHCat (別名: Templeton) の状態を返すことを示します。

2. 以下を使用して、Pig Latin ジョブをクラスターに送信します。

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-d** - `-G` が使用されていないため、要求は既定で POST メソッドになります。`-d` は要求で送信されるデータ値を指定します。

        * **user.name** - コマンドを実行するユーザー
        * **execute** - 実行する Pig Latinステートメント
        * **statusdir** - ジョブのステータスが書き込まれるディレクトリ

    > [AZURE.NOTE] Curl を使用したとき、Pig Latin ステートメントのスペースが `+` に置き換わることに注意してください。

    このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します。

        {"id":"job_1415651640909_0026"}

3. ジョブのステータスを確認するには、次のコマンドを使用します。**JOBID** を前の手順で返された値に置き換えます。たとえば、戻り値が  `{"id":"job_1415651640909_0026"}` の場合、JOBID は `job_1415651640909_0026` になります。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	ジョブが完了している場合、ステータスは "SUCCEEDED" になります。

    > [AZURE.NOTE] この curl 要求では、ジョブに関する情報が記載された JSON ドキュメントが返されます。状態値のみを取得するには jq を使用します。 

## <a id="results"></a>結果の表示

ジョブのステータスが **SUCCEEDED** に変わったら、Azure Blob ストレージからジョブの結果を取得できます。クエリで渡される  `statusdir` パラメーターには出力ファイルの場所が含まれます。この場合は、**wasb:///example/pigcurl** になります。このアドレスではジョブの出力は、HDInsight クラスターが使用する既定のストレージ コンテナーの **example/pigcurl** ディレクトリに保存されます。

これらのファイルを一覧表示およびダウンロードするには以下を使用します: <a href="../xplat-cli/" target="_blank">Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli)</a>。たとえば、**example/pigcurl** 内のファイルを一覧表示するには、次のコマンドを使用します。

	azure storage blob list <container-name> example/pigcurl

ファイルをダウンロードするには、次のコマンドを使用します。

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] `-a` および `-k` パラメーターを使用して BLOB を含むストレージ アカウントの名前を指定するか、**AZURE\_STORAGE\_ACCOUNT** および **AZURE\_STORAGE\_ACCESS\_KEY** 環境変数を設定します。参照: <a href="../hdinsight-upload-data/" target="_blank" for more information.


## <a id="summary"></a>まとめ

このドキュメントを参照して、未加工の HTTP 要求を使用して、HDInsight クラスターで Pig ジョブを実行、監視し、その結果を表示できます。

この記事で使用されている REST インターフェイスの詳細については、「[WebHCat リファレンス](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)」を参照してください。

## <a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報

* [HDInsight での Pig と Hadoop の使用](../hdinsight-use-pig/)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](../hdinsight-use-hive/)

* [HDInsight での MapReduce と Hadoop の使用](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
