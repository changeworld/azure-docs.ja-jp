<properties
   pageTitle="HDInsight での MapReduce と Hadoop の Remote Desktop の使用 | Microsoft Azure"
   description="リモート デスクトップを使用して、HDInsight で Hadoop に接続し、MapReduce ジョブを実行する方法を説明します。"
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
   ms.date="04/22/2016"
   ms.author="larryfr"/>

# リモート デスクトップによる HDInsight での MapReduce と Hadoop の使用

[AZURE.INCLUDE [mapreduce セレクター](../../includes/hdinsight-selector-use-mapreduce.md)]

この記事では、リモート デスクトップを使用して HDInsight クラスター上の Hadoop に接続し、Hive コマンドを使用して MapReduce ジョブを実行する方法について説明します。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Windows ベースの HDInsight (HDInsight で Hadoop を使用) クラスター

* Windows 10、Windows 8、Windows 7 を実行するクライアント コンピューター

##<a id="connect"></a>リモート デスクトップへの接続

「[RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#rdp)」の手順に従って、HDInsight クラスターのリモート デスクトップを有効にしてからデスクトップに接続します。

##<a id="hadoop"></a>Hadoop コマンドの使用

HDInsight クラスターのデスクトップに接続したら、次の手順に従って Hadoop コマンドを使用して MapReduce ジョブを実行します。

1. HDInsight デスクトップから、**Hadoop コマンド ライン**を起動します。これにより、**c:\\apps\\dist\\hadoop-&lt;version number>** ディレクトリに新しいコマンド プロンプトが開きます。

	> [AZURE.NOTE] Hadoop の更新に応じて、バージョン番号が変わります。**HADOOP\_HOME** 環境変数を使用して、パスを探します。たとえば、`cd %HADOOP_HOME%` では、バージョン番号がわからなくても、ディレクトリは Hadoop ディレクトリに変更されます。

2. **Hadoop** コマンドを使用してサンプルの MapReduce ジョブ実行するには、次のコマンドを使用します。

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	これは、現在のディレクトリの **hadoop-mapreduce-examples.jar** ファイルに含まれる **wordcount** クラスを起動します。入力として **wasb://example/data/gutenberg/davinci.txt** ドキュメントを使用し、出力は **wasb:///example/data/WordCountOutput** に格納されます。

	> [AZURE.NOTE] この MapReduce ジョブとサンプル データの詳細については、「<a href="hdinsight-use-mapreduce.md">HDInsight での Hadoop MapReduce の使用</a>」をご覧ください。

2. 処理中に詳細が生成され、ジョブが完了すると、次のような情報が返されます。

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. ジョブが完了したら、次のコマンドを使用して **wasb://example/data/WordCountOutput** に格納されている出力ファイルを一覧表示します。

		hadoop fs -ls wasb:///example/data/WordCountOutput

	ここでは、**\_SUCCESS** と **part-r-00000** の 2 つのファイルが表示されます。**part-r-00000** ファイルには、このジョブの出力が含まれています。

	> [AZURE.NOTE] 一部の MapReduce ジョブでは、複数の **part-r-#####** ファイルに結果が分割される場合があります。このとき、ファイルの順番を特定するには ##### サフィックスを使用します。

4. 出力を表示するには、次のコマンドを使用します。

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	ここでは、**wasb://example/data/gutenberg/davinci.txt** ファイルに含まれる単語の一覧と、各単語が発生した回数が表示されます。ファイルに含まれるデータの例を次に示します。

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>概要

このように、Hadoop を使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ出力を表示できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での MapReduce ジョブに関する全般的な情報:

* [HDInsight Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0427_2016-->