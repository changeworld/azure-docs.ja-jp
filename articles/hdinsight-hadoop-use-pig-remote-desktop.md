<properties
   pageTitle="HDInsight での Hadoop Pig の使用 | Azure"
   description="リモート デスクトップを通じて、HDInsight で Pig と Hadoop を使用する方法を説明します。"
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

#Pig コマンド (リモート デスクトップ) を使用した Pig ジョブの実行

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、HDInsight クラスターの Linux ベースの Hadoop で、Pig コマンドを使用して Pig Latin ステートメントを対話的に、またはバッチ ジョブとして実行する方法を順を追って説明します。Pig Latin では、map 関数や reduce 関数ではなく、データ変換を記述することで MapReduce アプリケーションを作成できます。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Windows ベースの HDInsight (HDInsight で Hadoop を使用) クラスター

* Windows 7、8、または 10 クライアント

##<a id="connect"></a>リモート デスクトップへの接続

「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>」の手順に従って、HDInsight クラスターのリモート デスクトップを有効にしてからデスクトップに接続します。

##<a id="pig"></a>Pig コマンドの使用

2. 接続したら、デスクトップのアイコンを使用して **Hadoop コマンド ライン**を起動します。

2. Pig コマンド ラインを起動するには次のコマンドを使用します。

		%pig_home%\bin\pig

	 `grunt>` プロンプトが表示されます。 

3. 次のステートメントを入力します。

		LOGS = LOAD 'wasb:///example/data/sample.log';

	このコマンドは、sample.log ファイルの内容をログに読み込みます。ファイルの内容を表示するには、次のコマンドを使用します。

		DUMP LOGS;

4. 次のコマンドを使用して、正規表現を適用してデータを変換し、各レコードのログ レベルのみを抽出します。

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	変換後のデータを表示するには、**DUMP** を使用します。例では、`DUMP LEVELS;` が使用されます。

5. 次のステートメントを使用して、変換を適用します。各手順の後に `DUMP` を使用して、変換の結果を表示します。

	<table>
	<tr>
	<th>ステートメント</th><th>実行内容</th>
	</tr>
	<tr>
	<td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>ログ レベルに null 値を含む行を削除し、結果を FILTEREDLEVELS に格納します。</td>
	</tr>
	<tr>
	<td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>ログ レベルで列をグループ化し、結果を GROUPEDLEVELS に格納します。</td>
	</tr>
	<tr>
	<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>一意のログ レベル値とそれが出現した回数を含む新しい データ セットを作成します。これは FREQUENCIES に格納されます。</td>
	</tr>
	<tr>
	<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>数が多い順にログ レベルを並べ替えて、RESULT に格納します。</td>
	</tr>
	</table>

6. 変換の結果は `STORE` ステートメントで保存することもできます。たとえば、以下では `RESULT` がクラスターの既定のストレージ コンテナーの **/example/data/pigout** ディレクトリに保存されます。

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE] データは、**part-nnnnn** という名前のファイルの指定したディレクトリに保存されます。ディレクトリが既に存在する場合は、エラーが発生します。

7. エラーを解決するには、次のステートメントを入力します。

		QUIT;

###Pig Latin バッチ ファイル

Pig コマンドを使用して、ファイルに含まれた Pig Latin を実行することもできます。

3. エラーを解決したら、**メモ帳**を開き、**%PIG_HOME%** ディレクトリに **pigbatch.pig** という名前の新しいファイルを作成します。

4. 次の行を **pigbatch.pig** ファイルに入力または貼り付けて、保存します。

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. 次の pig コマンドを使用して、**pigbatch.pig** ファイルを実行します。

		pig %PIG_HOME%\pigbatch.pig

	バッチ ジョブが完了すると、次の出力が表示されます (前の手順の `DUMP RESULT;` での出力と同じ内容です)。

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

##<a id="summary"></a>まとめ

このように、Pig コマンドでは、Pig Latin を使用して MapReduce 操作を対話的に実行できるだけでなく、バッチ ファイルに格納されたステートメントも実行できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)
<!--HONumber=47-->
