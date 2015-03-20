<properties
   pageTitle="HDInsight での Python MapReduce ジョブの開発 | Azure"
   description="Linux ベースの HDInsight クラスターで Python MapReduce ジョブを作成、実行する方法を説明します。"
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

#HDInsight 用 Python ストリーミング プログラムの開発

Hadoop には MapReduce に対するストリーミング API が用意されていて、Java 以外の言語の map 関数と reduce 関数を記述することができます。このドキュメントでは、Python を使用して、MapReduce 操作を実行する方法を説明します。

> [AZURE.NOTE] この記事は、Michael Noll が公開する情報および例に基づいています ([http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/))。

##要件

* HDInsight クラスターでの Linux ベースの Hadoop

* テキスト エディター

* Windows クライアントとして PuTTY および PSCPこれらのユーティリティは次の場所から入手できます。<a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

##文字数

この例では、マッパーとレジューサを使用して、基本的な文字カウントを実装します。マッパーは文をいくつかの単語に分割し、レジューサは単語数や回数を合計して出力を生成します。

次の図は、map および reduce フェーズで実際に何が実行されるかを示しています。

![illustration of map reduce](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##Python について

Python は、多くの他の言語より少ないコード行で概念を表現できる高度な汎用プログラム言語です。迅速なアプリケーションに最適な構造、動的型付けのほか、その洗練された構文といった特徴のため、データ サイエンティストの間で、近年、プロトタイプ言語として一般的になりつつあります。

Python はすべての HDInsight クラスターにインストールされています。

##MapReduce ストリーミング

Hadoop では、ジョブで使用される map および reduce ロジックを含むファイルを指定できます。map および reduce ロジックに固有の要件は次のとおりです。

* **Input** - map および reduce コンポーネントは STDIN から入力データを読み取る必要があります。

* **Output** - map および reduce コンポーネントは STDOUT に出力データを書き込む必要があります。

* **Data format** - 使用および生成されるデータは、タブ文字で区切られたキーと値のペアである必要があります。

Python では、STDIN からの読み取りに **sys** モジュールを、STDOUT への印刷に **print** を使用して、これらの要件を簡単に処理できます。その他、キーと値間にタブ (`\t`) 文字を使用してデータを簡単に書式設定できます。

##マッパーとレジューサの作成

マッパーとレジューサは単なるテキスト ファイルで、この場合 **mapper.py** および **reducer.py** であるため、各ファイルで何が実行されるかは明白です。これらは、好みのエディターを使用して作成できます。

###Mapper.py

**mapper.py** という名前の新しいファイルを作成し、内容を以下のように作成します。

	#!/usr/bin/env python

	# Use the sys module
	import sys

	# 'file' in this case is STDIN
	def read_input(file):
		# Split each line into words
		for line in file:
			yield line.split()

	def main(separator='\t'):
		# Read the data using read_input
		data = read_input(sys.stdin)
		# Process each words returned from read_input
		for words in data:
			# Process each word
			for word in words:
				# Write to STDOUT
				print '%s%s%d' % (word, separator, 1)

	if __name__ == "__main__":
		main()

コードが読み取られ、解読されるまでしばらく待ちます。

###Reducer.py

**reducer.py** という名前の新しいファイルを作成し、内容を以下のように作成します。

	#!/usr/bin/env python
	
	# import modules
	from itertools import groupby
	from operator import itemgetter
	import sys
	
	# 'file' in this case is STDIN
	def read_mapper_output(file, separator='\t'):
		# Go through each line
	    for line in file:
			# Strip out the separator character
	        yield line.rstrip().split(separator, 1)
	
	def main(separator='\t'):
	    # Read the data using read_mapper_output
	    data = read_mapper_output(sys.stdin, separator=separator)
		# Group words and counts into 'group'
		#   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
	    for current_word, group in groupby(data, itemgetter(0)):
	        try:
				# For each word, pull the count(s) for the word
				#   from 'group' and create a total count
	            total_count = sum(int(count) for current_word, count in group)
				# Write to stdout
	            print "%s%s%d" % (current_word, separator, total_count)
	        except ValueError:
	            # Count was not a number, so do nothing
	            pass
	
	if __name__ == "__main__":
	    main()

##ファイルのアップロード

**mapper.py** および **reducer.py** はその実行前に、いずれもクラスターのヘッド ノードにある必要があります。これらをアップロードする最も簡単な方法は **scp** を使用することです (Windows クライアントを使用する場合は **pscp**)。

クライアントで、**mapper.py** および **reducer.py** と同じディレクトリで、次のコマンドを使用します。**username** を SSH ユーザーに置き換え、**clustername** をクラスターの名前に置き換えます。

	scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

これにより、ファイルがローカル システムからヘッド ノードにコピーされます。

> [AZURE.NOTE] SSH アカウントのセキュリティ保護にパスワードを使用している場合は、パスワードの入力が求められます。SSH キーを使用している場合は、`-i` パラメーターと、秘密キーのパスを使用する必要があることがあります。たとえば、`scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:` のように指定します。

##MapReduce の実行

1. SSH を使用したクラスターへの接続

		ssh username@clustername-ssh.azurehdinsight.net

	> [AZURE.NOTE] SSH アカウントのセキュリティ保護にパスワードを使用している場合は、パスワードの入力が求められます。SSH キーを使用している場合は、`-i` パラメーターと、秘密キーのパスを使用する必要があることがあります。たとえば、 `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net` のように指定します。

2. MapReduce ジョブを開始するには次のコマンドを使用します。

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasb:///example/data/davinci.txt -output wasb:///example/wordcountout

	このコマンドには次のようなものがあります。

	* **hadoop-streaming.jar** - MapReduce 操作のストリーミングを実行する際に使用します。Hadoop と指定した外部 MapReduce コードとの橋渡しを務めます。
	
	* **-files** - 指定されたファイルがこの MapReduce ジョブに必要なこと、また、すべてのワーカー ノードにコピーする必要があることを Hadoop に伝えます。

	* **-mapper** - マッパーとして使用するファイルを Hadoop に伝えます。

	* **-reducer** - レジューサとして使用するファイルを Hadoop に伝えます。

	* **-input** - 文字数をカウントする入力ファイル

	* **-output** - 出力の書き込み先のディレクトリ

		> [AZURE.NOTE] このディレクトリはジョブによって作成されます。

ジョブの開始時に一連の **INFO** ステートメントに続いて、**map** および **reduce** 操作がパーセンテージで表示されます。

	15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
	15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
	15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

完了すると、ジョブに関するステータス情報が返されます。

##出力を表示する

ジョブが完了したら、次のコマンドを使用して出力を表示します。

	hadoop fs -text /example/wordcountout/part-00000

これにより、文字の一覧と、その出現回数が表示されます。出力データは次のようになります。

	wrenching       1
	wretched        6
	wriggling       1
	wrinkled,       1
	wrinkles        2
	wrinkling       2

##次のステップ

これで、HDInsight でストリーミング MapRedcue ジョブを使用する方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight での Hive の使用](../hdinsight-use-hive/)
* [HDInsight での Pig の使用](../hdinsight-use-pig/)
* [HDInsight での MapReduce ジョブの使用](../hdinsight-use-mapreduce)
<!--HONumber=47-->
