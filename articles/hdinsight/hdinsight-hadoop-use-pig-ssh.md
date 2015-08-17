<properties
   pageTitle="HDInsight クラスターでの Hadoop Pig と SSH の使用 | Microsoft Azure"
   description="SSH で Linux ベースの Hadoop クラスターに接続し、Pig コマンドを使用して Pig Latin ステートメントを対話的に実行するか、バッチ ジョブとして実行する方法について説明します。"
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
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Pig コマンド (SSH) を使用して Linux ベースのクラスターで Pig ジョブを実行する

[AZURE.INCLUDE [pig セレクター](../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、Secure Shell (SSH) を使用して Linux ベースの Azure HDInsight クラスターに接続し、Pig コマンドを使用して Pig Latin ステートメントを対話的にまたはバッチ ジョブとして実行する方法を順を追って説明します。

Pig Latin プログラミング言語では、入力データに適用される変換を記述し、目的の出力を生成することができます。

> [AZURE.NOTE]Linux ベースの Hadoop サーバーは使い慣れているが HDInsight は初めてという場合は、「[Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md)」をご覧ください。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Linux ベースの HDInsight (HDInsight で Hadoop を使用) クラスター

* SSH クライアントSSH クライアントを備えた Linux、Unix、および Mac OSWindows ユーザーは [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) などのクライアントをダウンロードする必要があります。

##<a id="ssh"></a>SSH を使用した接続

SSH コマンドを使用して、HDInsight クラスターの完全修飾ドメイン名 (FQDN) に接続します。FQDN はクラスターに指定した名前で、その後、**.azurehdinsight.net** が続きます。以下の例では、**myhdinsight** という名前のクラスターに接続します。

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**HDInsight クラスターの作成時に SSH 認証に証明書キーを指定した場合は**、クライアント システムの秘密キーの場所を指定する必要があることがあります。

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**HDInsight クラスターの作成時に SSH 認証のパスワードを指定した場合は**、パスワードの入力を求められます。

HDInsight での SSH の使用に関する詳細については、「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

###PuTTY (Windows ベースのクライアント)

Windows ではビルトイン SSH クライアントは提供されません。[http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) からダウンロードできる **PuTTY** を使用することをお勧めします。

PuTTY の使用については、「[HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)」をご覧ください。

##<a id="pig"></a>Pig コマンドの使用

2. 接続したら、次のコマンドを使用して Pig コマンド ライン インターフェイス (CLI) を起動します。

        pig

	少し経ってから `grunt>` プロンプトが表示されます。

3. 次のステートメントを入力します。

		LOGS = LOAD 'wasb:///example/data/sample.log';

	このコマンドは、sample.log ファイルの内容をログに読み込みます。ファイルの内容を表示するには、次のコマンドを使用します。

		DUMP LOGS;

4. 次のコマンドを使用して、正規表現を適用してデータを変換し、各レコードのログ レベルのみを抽出します。

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	変換後のデータを表示するには、**DUMP** を使用します。例では `DUMP LEVELS;` が使用されます。

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

	> [AZURE.NOTE]データは、**part-nnnnn** という名前のファイルの指定したディレクトリに保存されます。ディレクトリが既に存在する場合は、エラーが発生します。

7. エラーを解決するには、次のステートメントを入力します。

		QUIT;

###Pig Latin バッチ ファイル

Pig コマンドを使用して、ファイルに含まれた Pig Latin を実行することもできます。

3. エラーを解決したら、次のコマンドを使用して、STDIN を **pigbatch.pig** という名前のファイルにパイプします。このファイルは、SSH セッションでログインしているアカウントのホーム ディレクトリに作成されます。

		cat > ~/pigbatch.pig

4. 次の行を入力または貼り付けて、Ctrl + D キーを押します。

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. 次の Pig コマンドを使用して、**pigbatch.pig** ファイルを実行します。

		pig ~/pigbatch.pig

	バッチ ジョブが完了したら、次の出力が表示されます (前の手順の `DUMP RESULT;` での出力と同じ内容です)。

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

##<a id="summary"></a>概要

このように、Pig コマンドでは、Pig Latin を使用して MapReduce 操作を対話的に実行できるだけでなく、バッチ ファイルに格納されたステートメントも実行できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO6-->