<properties linkid="manage-services-hdinsight-howto-work-with-the-interactive-console" urlDisplayName="対話型コンソール" pageTitle="HDInsight サービスで対話型コンソールを使用する方法 | Azure" metaKeywords="" description="このガイドでは、HDInsight サービスの対話型コンソールを使用して一般的な操作 (ファイルのアップロード、ジョブの実行、データの視覚化など) を行う方法について説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight サービスの対話型 JavaScript コンソールと Hive コンソール" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />




# HDInsight サービスの対話型 JavaScript コンソールと Hive コンソール

Microsoft Azure HDInsight サービスには、JavaScript と Hive 両方の対話型コンソールが備わっています。これらのコンソールは、読み取り (read)、評価 (evaluate)、出力 (print)、繰り返し (loop) を実現するシンプルな対話型環境 (REPL) です。ユーザーは式を入力し、それらを評価し、クエリを実行することによって MapReduce ジョブの結果を即座に表示できます。JavaScript コンソールは Pig Latin ステートメントを実行します。Hive コンソールは Hive クエリ言語 (Hive QL) のステートメントを評価します。どちらのタイプのステートメントも、MapReduce プログラムとしてコンパイルされます。これらのコンソール上で Hadoop ジョブを管理すれば、Hadoop クラスターのヘッド ノードにリモート接続して MapReduce プログラムを直接実行するより、処理が格段に容易になります。

**JavaScript コンソール**: Hadoop エコシステムとの "流れるインターフェイス" を実現するコマンド シェルです。"流れるインターフェイス" では、複数の命令をつなぎ合わせ、一連の流れの中で 1 つの呼び出しのコンテキストを後続の呼び出しに伝える手法が用いられます。JavaScript コンソールの特長は次のとおりです。
		
- Hadoop クラスターとそのリソース、および Hadoop 分散ファイル システム (HDFS) のコマンドにアクセスできる。
- Hadoop クラスターとの間でやり取りされるデータを管理および操作できる。
- Pig Latin と JavaScript のステートメントを評価して一連の MapReduce プログラムを定義し、データ処理ワークフローを作成する "流れるインターフェイス"

**Hive コンソール**: Hadoop を基盤とするデータ ウェアハウス フレームワークであり、データの管理、照会、分析を行います。SQL 言語の一種である HiveQL を使用して、Hadoop クラスターに保存されたデータを照会します。Hive コンソールの特長は次のとおりです。
			
- Hadoop クラスターとそのリソース、および HDFS のコマンドにアクセスできる。		
- Hadoop クラスター上で HiveQL ステートメントを実行できる Hive フレームワーク。	
- HDFS 対応のリレーショナル データベース モデルであり、テーブルに保存されたデータと同じように、分散ファイル システムに保存されたデータを操作できる。		
JavaScript コンソールは Pig Latin (データ フロー言語) を使用し、Hive コンソールは HiveQL (照会言語) を使用します。	

Pig (JavaScript コンソール) はスクリプトに慣れているユーザーに好まれる傾向があります。この場合、連結された ("流れるような") 一連の命令によってデータ処理ワークフローを定義します。データの非構造化が著しい場合にも適しています。	

Hive (Hive コンソール) は、SQL やリレーショナル データベース環境に慣れているユーザーに好まれる傾向があります。Hive ではスキーマとテーブル抽象化が使用されているので、RDBMS での一般的な処理と非常によく似ています。

Pig と Hive は高水準言語であり、Java で記述された MapReduce プログラムにコンパイルされ、HDFS 上で実行されます。綿密な制御や高いパフォーマンスが求められる場合は、MapReduce プログラムを直接記述する必要があります。


##このチュートリアルの内容

* [JavaScript コンソールを使用して MapReduce ジョブを実行する](#runjob)
* [JavaScript コンソールを使用して結果をグラフィカルに表示する](#displayresults)
* [Hive コンソールを使用して結果を Hive テーブルにエクスポートする](#createhivetable)
* [Hive コンソールを使用して Hive テーブルのデータを照会する](#queryhivetable)


##<a name="runjob"></a>JavaScript コンソールを使用して MapReduce ジョブを実行する

このセクションでは、JavaScript コンソールを使用して、HDInsight サービスに用意されている WordCount サンプルを実行します。ここでは、対話型 JavaScript コンソールに備わっている、Pig の "流れる API" を使用します。ここで分析するテキスト ファイルは、『*The Notebooks of Leonardo Da Vinci (レオナルド・ダ・ヴィンチの手記)*』の Project Gutenberg 電子書籍版です。MapReduce ジョブの結果として、"最も頻繁に出現する 10 個の単語" のみが出力されるようにフィルターを指定します。

1. [管理ポータル](https://manage.windowsazure.com)にサインインします。
2. **[HDINSIGHT]** をクリックします。展開した Hadoop クラスターが一覧表示されます。
3. 接続する HDInsight クラスターの名前をクリックします。
4. **[クラスターの管理]** をクリックします。
5. ユーザー名とパスワードを入力して **[ログオン]** をクリックします。
6. HDInsight ポータルで **[サンプル]** をクリックします。

	![HDI.Tiles.Samples][hdi-tiles-samples]

7. **[Hadoop サンプル ギャラリー]** ページで、**[WordCount]** タイルをクリックします。
8. 右上にある **[WordCount.js]** をクリックして、このファイルをローカル ディレクトリに保存します (../downloads フォルダーなど)。

	![HDI.JsConsole.WordCountDownloads][hdi-jsconsole-wordcount]

9. 右上隅にある **[Azure HDInsight]** をクリックして、クラスターのダッシュボード ページに戻ります。
10. **[対話型クラスター]** をクリックして、JavaScript コンソールを表示します。
	
	![HDI.Tiles.InteractiveConsole][hdi-tiles-interactive-console]
11. 右上にある **[JavaScript]** をクリックします。
12. 次のコマンドを実行します。

	fs.put()

13. **[ファイルのアップロード]** ウィンドウで次のパラメーターを入力します。

	- **ソース:** _..\downloads\Wordcount.js
 	- **保存先:** ./WordCount.js/	

	![HDI.JsConsole.UploadJs][hdi-jsconsole-upload]

	WordCount.js ファイルの場所を指定します。完全なローカル パスを指定する必要があります。HDFS の相対アドレスであることを示すため、保存先パスの先頭にドットを 1 つ追加してください。


14. **[アップロード]** をクリックします。

15. 次のコマンドを実行して、ファイルを一覧表示し、その内容を表示します。

		#ls
		#cat WordCount.js

	![HDI.JsConsole.JsCode][hdi-jsconsole-jscode]

	JavaScript の map 関数は、toLowerCase() メソッドを使用してテキストから大文字を削除します。その後、reduce 関数によって単語の出現回数をカウントします。
	
16. 次のコマンドを実行して、WordCount MapReduce ジョブで処理するデータ ファイルを一覧表示します。

 		#ls /example/data/gutenberg
	
17. 次のコマンドを実行して、MapReduce プログラムを実行します。

		pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
	
	admin は現在のログイン ユーザー名に置き換えます。

	ドット演算子を使用して命令が "チェーン" されていること、出力ファイルが *DaVinciTop10Words* と呼ばれていることに注意してください。次のセクションでは、この出力ファイルにアクセスします。

	完了すると、次のように表示されます。

		pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
		2013-04-25 18:54:28,116 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! (View Log)

18. ジョブの進捗を確認するには、右へスクロールして **[ログの表示]** をクリックします。ジョブを完了できなかった場合は、その診断情報もこのログに表示されます。ジョブが完了すると、ログの末尾に次のメッセージが表示されます。

		[main] INFO org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! followed by a link to the log file.
	
19. 次のコマンドを実行して、出力ファイルを一覧表示します。

		#ls

	DavinciTop10Words フォルダーが作成されていることを確認してください。

##<a name="displayresults"></a>JavaScript コンソールを使用して結果をグラフィカルに表示する
前のセクションでは、テキスト ファイルから出現回数が上位 10 個の単語を取得する MapReduce ジョブを実行しました。出力ファイルは、./DaVinciTop10Words です。

1. 次のコマンドを実行して、DaVinciTop10Words ディレクトリの結果を表示します。

		file = fs.read("DaVinciTop10Words")

	結果は次のようになります。

		js> file=fs.read("DaVinciTop10Words")
		the	22966
		of	11228
		and	8428
		in	5737
		to	5296
		a	4791
		is	4261
		it	3073
		that	2903
		which	2544
	
2. 次のコマンドを実行して、ファイルの内容を解析しデータ ファイルに出力します。

		data = parse(file.data, "word, count:long")
		
3. 次のコマンドを実行して、データをプロットします。

		graph.bar(data)
		
	![HDI.JsConsole.BarGraphTop10Words][hdi-jsconsole-bargraph-top10words]


##<a name="createhivetable"></a>Hive コンソールを使用して結果を Hive テーブルにエクスポートする

このセクションでは Hive 対話型コンソールを使用し、MapReduce ジョブの出力から Hive テーブルを作成します。次のセクションでは、このテーブル内のデータを照会する方法を説明します。

**Hive テーブルの作成**
	
1. 右上にある [Hive] をクリックして、Hive コンソールを開きます。
	
2. 次のコマンドを入力します。DaVinciTop10Words フォルダーに保存した WordCount サンプル出力を基にして、2 列から成る _DaVinciWordCountTable_ テーブルが作成されます。
		
		CREATE EXTERNAL TABLE DaVinciWordCountTable		
		(word STRING,
		count INT)	
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'	
		STORED AS TEXTFILE LOCATION '/user/admin/DaVinciTop10Words';	

	admin はログイン ユーザー名に置き換えます。

	対象フォルダーに依存しないようにするため、ここでは外部テーブルとして作成します。また、指定するのは出力ファイルの場所だけであることに注意してください。ファイル名自体は指定する必要がありません。

3. 左下にある **[評価]** をクリックします。		

4. 次のコマンドを入力して、2 列のテーブルが作成されたことを確認します。

		SHOW TABLES;
		DESCRIBE DaVinciWordCountTable;

5. **[評価]** をクリックします。
	
	![HDI.Hive.ShowDescribeTable][hdi-hive-showdescribetable]

##<a name="queryhivetable"></a>Hive コンソールを使用して Hive テーブルのデータを照会する

1. 次のコマンドを実行して、出現回数が最も多い 10 個の単語を照会します。

		SELECT word, count
		FROM DaVinciWordCountTable
		ORDER BY count DESC LIMIT 10

	結果が次のように表示されます。			

		SELECT word, count FROM DaVinciWordCountTable ORDER BY count DESC LIMIT 10
		the 22966
		of 11228
		and 8428
		in 5737
		to 5296
		a 4791
		is 4261
		it 3073
		that 2903
		which 2544

## 次のステップ

ここでは、対話型 JavaScript コンソールから Hadoop ジョブを実行する方法、さらにこのコンソールを使用してジョブの結果を検証する方法について説明しました。また、対話型 Hive コンソールを使用して Hadoop ジョブの結果を確認および処理しました。その際、MapReduce プログラムの出力を含むテーブルを作成し、そのテーブルに対して照会を実行しました。これらのコンソールで使用する Pig Latin ステートメントと Hive QL ステートメントの例を確認しました。最後に、JavaScript コンソールと Hive コンソールの REPL 対話機能を使用すれば、Hadoop クラスターを容易に操作できることを確認しました。詳細については、次の記事を参照してください。

* [HDInsight での Pig の使用][hdinsight-pig]
* [HDInsight での Hive の使用][hdinsight-hive]
* [HDInsight での MapReduce の使用][hdinsight-mapreduce]

[hdinsight-pig]: /ja-jp/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-hive]: /ja-jp/manage/services/hdinsight/using-hive-with-hdinsight/
[hdinsight-mapreduce]: /ja-jp/manage/services/hdinsight/using-mapreduce-with-hdinsight/

[hdi-tiles-samples]: ./media/hdinsight-interactive-console/HDI.TileSamples.PNG
[hdi-jsconsole-wordcount]: ./media/hdinsight-interactive-console/HDI.JsConsole.WordCountDownloads.PNG
[hdi-tiles-interactive-console]: ./media/hdinsight-interactive-console/HDI.TileInteractiveConsole.PNG
[hdi-jsconsole-upload]: ./media/hdinsight-interactive-console/HDI.JsConsole.UploadJs.PNG
[hdi-jsconsole-jscode]: ./media/hdinsight-interactive-console/HDI.JsConsole.JsCode.PNG
[hdi-jsconsole-bargraph-top10words]: ./media/hdinsight-interactive-console/HDI.JsConsole.BarGraphTop10Words.PNG
[hdi-hive-showdescribetable]: ./media/hdinsight-interactive-console/HDI.Hive.ShowDescribeTable.PNG "Hive テーブルの構成")

