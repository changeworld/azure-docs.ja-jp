<properties
   pageTitle="Ambari ビューによる HDInsight (Hadoop) での Hive の使用 | Microsoft Azure"
   description="Web ブラウザーから Hive ビューを使用して Hive クエリを送信する方法について説明します。Hive ビューは、Linux ベースの HDInsight クラスターに付属する Ambari Web UI の要素です。"
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
   ms.date="05/20/2016"
   ms.author="larryfr"/>

#HDInsight での Hive View と Hadoop の使用

[AZURE.INCLUDE [Hive セレクター](../../includes/hdinsight-selector-use-hive.md)]

Ambari は、Linux ベースの HDInsight クラスターに付属する管理および監視ユーティリティです。Ambari が提供する機能の 1 つに、Hive クエリを実行するときに使用される Web UI があります。これを __Hive ビュー__と呼びます。これは、HDInsight クラスターに付属する Ambari のビューの一部です。

> [AZURE.NOTE] Ambari には数多くの機能がありますが、このドキュメントではそれらについて説明しません。詳細については、「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

##前提条件

- Linux ベースの HDInsight クラスター。クラスターの作成方法については、[Linux ベースの HDInsight の使用](hdinsight-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

##Hive ビューを開く

Azure ポータルから Ambari ビューを表示するには、HDInsight クラスターを選択し、__[クイック リンク]__ セクションの __[Ambari ビュー]__ を選択します。

![quick links section](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

また、Ambari に直接移動することもできます。そのためには、Web ブラウザーで https://CLUSTERNAME.azurehdinsight.net (ここで、__CLUSTERNAME__ は HDInsight クラスターの名前) に移動し、ページ メニューの (ページの左側の __[Admin]__ リンク ボタンの横にある) 四角形のセットを選択して使用可能なビューの一覧を表示します。__[Hive view]__ を選択します。

![Selecting ambari views](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png)

> [AZURE.NOTE] Ambari にアクセスすると、サイトに対する認証が求められます。クラスターを作成するときに使用した管理者アカウント名 (既定値は `admin`) とパスワードを入力します。

次のようなページが表示されます。

![Image of the hive view page, containing a query editor section](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

##テーブルを表示する

ページの __[Database Explorer]__ セクションの __[Databases]__ タブで、__[default]__ エントリを選択します。これにより、既定のデータベース内にあるテーブルの一覧が表示されます。新しい HDInsight クラスターの場合、存在するテーブルは __hivesampletable__ のみです。

![database explorer with the default database expanded](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

このドキュメントの手順に従って新しいテーブルを追加した際、データベース エクスプローラーの右上隅にある更新アイコンを使用すると、利用可能なテーブルの一覧を更新できます。

##<a name="hivequery"></a>クエリ エディター

クラスターに格納されているデータに対して Hive クエリを実行するには、Hive ビューで次の手順を実行します。

1. ページの __[Query Editor]__ セクションで、次の HiveQL ステートメントをワークシートに貼り付けます。

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	これらのステートメントは次のアクションを実行します。

	- **DROP TABLE** - テーブルが既存の場合にテーブルとデータ ファイルを削除します。
	- **CREATE EXTERNAL TABLE** - Hive に新しく "外部" テーブルを作成します。外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。
	- **ROW FORMAT** - Hive にデータの形式を示します。ここでは、各ログのフィールドは、スペースで区切られています。
	- **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data ディレクトリ) と、データはテキストとして格納されていることを示します。
	- **SELECT** - t4 列の値が [ERROR] であるすべての行の数を指定します。

	>[AZURE.NOTE] 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。外部テーブルを削除しても、データは削除*されません*。テーブル定義のみが削除されます。

2. クエリ エディターの下部にある __[Execute]__ ボタンを使用してクエリを開始します。ボタンの色がオレンジ色に変わり、テキストが __[Stop execution]__ に変わります。クエリ エディターの下に __[Query Process Results]__ セクションが表示され、ジョブに関する情報が表示されます。

    > [AZURE.IMPORTANT] ブラウザーによっては、ログまたは結果の情報が正しく更新されない場合があります。ジョブを実行したときに、ジョブの実行が続いているにもかかわらずログが更新されたり結果が返されたりしない場合は、Mozilla FireFox または Google Chrome を代わりに使用してください。

3. クエリが完了すると、__[Query Process Results]__ セクションに操作の結果が表示されます。__[Stop execution]__ ボタンも緑色の __[Execute]__ ボタンに戻ります。__[Results]__ タブには次の情報が表示されます。

        sev       cnt
        [ERROR]   3

    __[Logs]__ タブを使用すると、ジョブによって作成されたログ情報を表示できます。ログ情報は、クエリに問題が発生した場合のトラブルシューティングに使用できます。

    > [AZURE.TIP] __[Query Process Results]__ セクションの左上には __[Save results]__ ボックスがあります。これを使用して、結果をダウンロードしたり、CSV ファイルとして HDInsight のストレージに保存したりできます。

3. このクエリの最初の 4 行を選択し、__[Execute]__ を選択します。ジョブが完了したときに結果は生成されません。これは、クエリの一部が選択されているときに __[Execute]__ ボタンを使用すると、選択したステートメントのみが実行されるためです。この場合は、テーブルの行を取得する最後のステートメントが選択範囲に含まれていませんでした。その行のみを選択して __[Execute]__ を使用すると、予想どおりの結果が表示されます。

3. __クエリ エディター__の下部にある __[New Worksheet]__ ボタンを使用して新しいワークシートを作成します。新しいワークシートに、次の HiveQL ステートメントを入力します。

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

	これらのステートメントは次のアクションを実行します。

	- **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。**EXTERNAL** キーワードが使用されていないため、これは内部テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。外部テーブルとは異なり、内部テーブルを削除すると、基盤となるデータも削除されます。
	- **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
	- **INSERT OVERWRITE ...SELECT** - [ERROR] を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。

    __[Execute]__ ボタンを使用してこのクエリを実行します。このクエリで返される行はないため、__[Results]__ タブには情報が表示されませんが、状態は __[SUCCEEDED]__ と表示されます。

###Hive の設定

エディターの右側にある __[Settings]__ アイコンを選択します。

![icons](./media/hdinsight-hadoop-use-hive-ambari-view/settings.png)

Settings では、Hive の実行エンジンを Tez (既定値) から MapReduce に変更するなど、さまざまな Hive 設定を変更できます。

###ビジュアルの説明

エディターの右側にある __[Visual Explain]__ アイコンを選択します。

![icons](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplainicon.png)

これは、クエリの __[Visual Explain]__ ビューです。このビューは、複雑なクエリのフローを理解する際に役立ちます。クエリ エディターの __[Explain]__ ボタンを使用して、このビューに対応するテキストを表示できます。

![visual explain image](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

###Tez

エディターの右側にある __[Tez]__ アイコンを選択します。

![icons](./media/hdinsight-hadoop-use-hive-ambari-view/tez.png)

このクエリのために Tez で使用される有向非巡回グラフ (DAG) が表示されます (利用できる場合)。過去に実行したクエリの DAG を表示する場合、または Tez プロセスをデバッグする場合は、[[Tez View]](hdinsight-debug-ambari-tez-view.md) を使用します。

###通知

エディターの右側にある __[Notifications]__ アイコンを選択します。

![icons](./media/hdinsight-hadoop-use-hive-ambari-view/notifications.png)

通知とは、クエリを実行するときに生成されるメッセージです。たとえば、クエリを送信したときや、エラーが発生したときに、通知が届きます。

##保存済みのクエリ

1. クエリ エディターから、新しいワークシートを作成し、次のクエリを入力します。

        SELECT * from errorLogs;

    クエリを実行して、動作することを確認します。結果は次のようになります。

        errorlogs.t1 	errorlogs.t2 	errorlogs.t3 	errorlogs.t4 	errorlogs.t5 	errorlogs.t6 	errorlogs.t7
        2012-02-03 	18:35:34 	SampleClass0 	[ERROR] 	incorrect 	id 	
        2012-02-03 	18:55:54 	SampleClass1 	[ERROR] 	incorrect 	id 	
        2012-02-03 	19:25:27 	SampleClass4 	[ERROR] 	incorrect 	id

2. エディターの下部にある __[Save as]__ ボタンを使用します。このクエリに __Errorlogs__ という名前を付け、__[OK]__ を選択します。ワークシートの名前が __Errorlogs__ に変わります。

3. [Hive View] ページの上部にある __[Saved Queries]__ タブを選択します。__Errorlogs__ が、保存済みのクエリとして表示されます。クエリは、削除されるまでこの一覧に表示されます。名前を選択すると、クエリ エディターにクエリが表示されます。

##クエリの履歴

[Hive View] ページの上部にある __[History]__ ボタンを選択すると、以前に実行したクエリを表示できます。これを使用して、以前に実行したクエリの一部を選択できます。クエリを選択すると、そのクエリがクエリ エディターに表示されます。

##ユーザー定義関数 (UDF)

Hive は**ユーザー定義関数 (UDF)** で拡張することもできます。UDF では、HiveQL で簡単にモデル化されない機能やロジックを実装することができます。

UDF は、クエリの HiveQL ステートメントの一部として追加できるほか、Hive ビューの上部にある [UDF] タブで、__クエリ エディター__で使用できる UDF のセットを宣言して保存できます。

Hive ビューに UDF を追加すると、__[Insert udfs]__ ボタンが__クエリ エディター__の下部に表示されます。このボタンを選択すると、Hive ビューで定義した UDF のドロップダウン リストが表示されます。UDF を選択すると、HiveQL ステートメントがクエリに追加され、UDF が有効になります。

たとえば、次のプロパティを持つ UDF を定義したとします。

* リソース名: myudfs
* リソース パス: wasb:///myudfs.jar
* UDF 名: myawesomeudf
* UDF のクラス名: com.myudfs.Awesome

__[Insert udfs]__ ボタンを使用すると、__myudfs__ という名前のエントリと、そのリソースに対して定義されている UDF ごとにドロップダウン リストが表示されます。ここでは、__myawesomeudf__ が表示されます。このエントリを選択すると、クエリの先頭に次の内容が追加されます。

    add jar wasb:///myudfs.jar;

    create temporary function myawesomeudf as 'com.myudfs.Awesome';

これにより、クエリでこの UDF を使用できます。たとえば、「`SELECT myawesomeudf(name) FROM people;`」のように入力します。

HDInsight において Hive で UDF を使用する方法の詳細については、以下の記事を参照してください。

* [HDInsight における Python と Hive および Pig の使用](hdinsight-python.md)

* [HDInsight にカスタムの Hive UDF を追加する方法](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

##<a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0525_2016-->