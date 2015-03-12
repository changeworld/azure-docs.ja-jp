<properties
   pageTitle="HDInsight での Hadoop Pig の使用 | Azure"
   description="Curl を使用して Pig ジョブを HDInsight にリモートで送信する方法について説明します。"
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

# Curl を使用した HDInsight の Hadoop での Hive クエリの実行

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

このドキュメントでは、Curl を使用して HDInsight クラスターの Hadoop で Hive クエリを実行する方法について説明します。 

Curl は、未加工の HTTP 要求を使用して HDInsight を操作し、Hive クエリを実行、監視し、その結果を取得する方法を指定するために使用します。これは、HDInsight クラスターで提供される WebHCat REST API (旧称: Templeton) を使用することで機能します。

> [AZURE.NOTE] Linux ベースの Hadoop サーバーに慣れており、HDInsight を初めて使用するユーザーの場合は、「 <a href="../hdinsight-hadoop-linux-information/" target="_blank">What you need to know about Hadoop on Linux-based HDInsight (Linux ベースの HDInsight の Hadoop について知っておくべきこと)</a>」を参照してください。

## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* HDInsight クラスター (Linux または Windows ベース) の Hadoop

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Curl を使用した Hive クエリの実行

> [AZURE.NOTE] Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名およびパスワードを指定して要求を認証する必要があります。また、サーバーへの要求の送信に使用する URI にクラスター名を含める必要があります。
> 
> このセクションでのコマンドについては、**USERNAME** をクラスターを認証するユーザーの名前に、**PASSWORD** をユーザー アカウントのパスワードに置き換える必要があります。**CLUSTERNAME** はクラスターの名前に置き換えます。
> 
> REST API のセキュリティは、 <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">基本認証を使用して保護します</a>。資格情報を安全にサーバーに送信するには、必ず HTTPS を使用して要求を行う必要があります。

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    次のような応答を受け取ります。

        {"status":"ok","version":"v1"}

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-u** - 要求の認証に使用するユーザー名およびパスワード
    * **-G** - GET 要求であることを指定します

    URL の先頭は **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** で、これはすべての要求で共通です。パス **/status** は、要求がサーバー用の WebHCat (別名: Templeton) の状態を返すことを示します。次のコマンドを使用して、Hive のバージョンを要求することもできます。

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    次のような応答が返されます。

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. 次のコマンドを使用して、**log4jLogs** という名前の新しいテーブルを作成します。

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-d** - `-G` が使用されていないため、要求は既定で POST メソッドになります。`-d` は要求で送信されるデータ値を指定します。

        * **user.name** - コマンドを実行するユーザー
        
        * **execute** - 実行する HiveQL ステートメント
        
        * **statusdir** - ジョブのステータスが書き込まれるディレクトリ

    これらのステートメントは次のアクションを実行します。

    * **DROP TABLE** - テーブルが既存の場合にテーブルとデータ ファイルを削除します。
    
    * **CREATE EXTERNAL TABLE** - Hive に新しく  'external' テーブルを作成します。外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。

		> [AZURE.NOTE] 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
		>
		> 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

    * **ROW FORMAT** - Hive にデータの形式を示します。ここでは、各ログのフィールドは、スペースで区切られています。
    
    * **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data directory) と、データはテキストとして格納されていることを示します。
    
    * **SELECT** - **t4** 列の値が **[ERROR]** であるすべての行の数を返します。ここでは、この値を含む列が 3 行あるため、**3** という値が返されています。

    > [AZURE.NOTE] Curl を使用したとき、HiveQL ステートメントのスペースが `+` に置き換わることに注意してください。スペースを含む引用符で囲まれた値 (区切り記号など) は `+` に置き換わりません。

    このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します。

        {"id":"job_1415651640909_0026"}

3. ジョブのステータスを確認するには、次のコマンドを使用します。**JOBID** を前の手順で返された値に置き換えます。たとえば、戻り値が  `{"id":"job_1415651640909_0026"}` の場合、JOBID は `job_1415651640909_0026` になります。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	ジョブが完了している場合、ステータスは "SUCCEEDED" になります。

    > [AZURE.NOTE] この curl 要求では、ジョブに関する情報が記載された JSON ドキュメントが返されます。状態値のみを取得するには jq を使用します。 

4. ジョブのステータスが **SUCCEEDED** に変わったら、Azure Blob ストレージからジョブの結果を取得できます。クエリで渡される  `statusdir` パラメーターには出力ファイルの場所を含めます。この場合は、**wasb:///example/curl** になります。このアドレスではジョブの出力は、HDInsight クラスターが使用する既定のストレージ コンテナーの **example/curl** ディレクトリに保存されます。

    これらのファイルを一覧表示およびダウンロードするには以下を使用します:  <a href="../xplat-cli/" target="_blank">Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli)</a>。たとえば、**example/curl** 内のファイルを一覧表示するには、次のコマンドを使用します。

		azure storage blob list <container-name> example/curl

	ファイルをダウンロードするには、次のコマンドを使用します。

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] `-a` および `-k` パラメーターを使用して BLOB を含むストレージ アカウントの名前を指定するか、**AZURE\_STORAGE\_ACCOUNT** および **AZURE\_STORAGE\_ACCESS\_KEY** 環境変数を設定します。参照: <a href="../hdinsight-upload-data/" target="_blank" for more information.

6. 次のステートメントを使用して、**errorLogs** という名前の新しい "内部" テーブルを作成します。

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    これらのステートメントは次のアクションを実行します。

    * **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。 **EXTERNAL** キーワードが使用されていないため、これは "内部" テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。

		> [AZURE.NOTE] **EXTERNAL**テーブルとは異なり、内部デーブルを削除すると、基盤となるデータは削除されます。

    * **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
    * **INSERT OVERWRITE ... SELECT** - **ERROR** を含む **[log4jLogs]** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。
    * **SELECT * ** - 新しい **errorLogs** テーブルからすべての列を選択します。

7. 返されたジョブ ID を使用して、ジョブのステータスを確認します。確認できたら、前述のように xplat-cli を使用して、結果をダウンロードし、表示します。出力には、それぞれに **[ERROR]** が含まれた 3 つの行が返されます。


## <a id="summary"></a>まとめ

このドキュメントを参照して、未加工の HTTP 要求を使用して、HDInsight クラスターで Hive ジョブを実行、監視し、その結果を表示できます。

この記事で使用されている REST インターフェイスの詳細については、「 <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">WebHCat リファレンス</a>」を参照してください。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](../hdinsight-use-hive/)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](../hdinsight-use-pig/)

* [HDInsight での MapReduce と Hadoop の使用](../hdinsight-use-mapreduce/)


[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/ documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=45--> 
