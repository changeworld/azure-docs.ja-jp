<properties
   pageTitle="HDInsight での MapReduce と Hadoop の SSH 接続 の使用 | Microsoft Azure"
   description="SSH を使用して HDInsight で Hadoop を使用して MapReduce ジョブを実行する方法を説明します。"
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
   ms.date="06/06/2016"
   ms.author="larryfr"/>

# SSH による HDInsight での MapReduce と Hadoop の使用

[AZURE.INCLUDE [mapreduce セレクター](../../includes/hdinsight-selector-use-mapreduce.md)]

この記事では、Secure Shell (SSH) を使用して HDInsight クラスターで Hadoop に接続し、Hadoop コマンドを使用して MapReduce ジョブを送信する方法を説明します。

> [AZURE.NOTE] Linux ベースの Hadoop サーバーは使い慣れているが HDInsight は初めてという場合は、「[Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md)」をご覧ください。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Linux ベースの HDInsight (HDInsight で Hadoop を使用) クラスター

* SSH クライアントSSH クライアントを備えた Linux、Unix、Mac オペレーティング システムWindows ユーザーは [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) などのクライアントをダウンロードする必要があります。

##<a id="ssh"></a>SSH を使用した接続

SSH コマンドを使用して、HDInsight クラスターの完全修飾ドメイン名 (FQDN) に接続します。FQDN はクラスターに指定した名前で、その後、**.azurehdinsight.net** が続きます。以下の例では、**myhdinsight** という名前のクラスターに接続します。

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**HDInsight クラスターの作成時に SSH 認証に証明書キーを指定した場合は**、クライアント システムの秘密キーの場所を指定する必要があることがあります。たとえば、

	ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**HDInsight クラスターの作成時に SSH 認証のパスワードを指定した場合は**、パスワードの入力を求められます。

HDInsight での SSH の使用に関する詳細については、「[Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

###PuTTY (Windows クライアント)

Windows ではビルトイン SSH クライアントは提供されません。[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) からダウンロードできる **PuTTY** を使用することをお勧めします。

PuTTY の使用については、「[HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)」をご覧ください。

##<a id="hadoop"></a>Hadoop コマンドの使用

1. HDInsight クラスターに接続されたら、以下に従って **Hadoop** コマンドを使用して MapReduce ジョブを起動します。

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

	これは、**hadoop-mapreduce-examples.jar** ファイルに含まれる **wordcount** クラスを起動します。入力として **wasbs://example/data/gutenberg/davinci.txt**** ドキュメントを使用し、出力は wasbs:///example/data/WordCountOutput** に格納されます。

	> [AZURE.NOTE] この MapReduce ジョブとサンプル データの詳細については、「[HDInsight での Hadoop MapReduce の使用](hdinsight-use-mapreduce.md)」をご覧ください。

2. 処理中に詳細が生成され、ジョブが完了すると、次のような情報が返されます。

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. ジョブが完了したら、次のコマンドを使用して **wasbs://example/data/WordCountOutput** に格納されている出力ファイルを一覧表示します。

		hdfs dfs -ls wasbs:///example/data/WordCountOutput

	ここでは、**\_SUCCESS** と **part-r-00000** の 2 つのファイルが表示されます。**part-r-00000** ファイルには、このジョブの出力が含まれています。

	> [AZURE.NOTE] 一部の MapReduce ジョブでは、複数の **part-r-#####** ファイルに結果が分割される場合があります。このとき、ファイルの順番を特定するには ##### サフィックスを使用します。

4. 出力を表示するには、次のコマンドを使用します。

		hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

	ここでは、**wasbs://example/data/gutenberg/davinci.txt** ファイルに含まれる単語の一覧と、各単語が発生した回数が表示されます。ファイルに含まれるデータの例を次に示します。

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>概要

このように、Hadoop コマンドを使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ出力を表示できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での MapReduce ジョブに関する全般的な情報:

* [HDInsight Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0727_2016-->