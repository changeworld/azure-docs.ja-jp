<properties 
	pageTitle="HDInsight Tools for Visual Studio の使用開始 | Azure" 
	description="HDInsight Tools for Visual Studio をインストールおよび使用して、HDInsight への接続と Hive クエリを実行する方法について説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="11/12/2014" 
	ms.author="jgao" 
	editor="cgronlun"/>

# HDInsight Hadoop Tools for Visual Studio の使用開始

HDInsight Tools for Visual Studio をインストールおよび使用して、HDInsight への接続と Hive クエリを実行する方法について説明します。HDInsight の使用に関する詳細については、[HDInsight の入門][hdinsight.introduction]、および [HDInsight の概要][hdinsight.get.started] に関するページを参照してください。

+ [インストール] 
+ [Azure サブスクリプションへの接続]
+ [リンクしているリソースへの移動]
+ [Hive クエリの実行]
+ [次のステップ]


##前提条件

- 次の要件を備えたワークステーション

	- Windows 7 または Windows 8。
	- Visual Studio 2012 [Update 4](http://www.microsoft.com/ja-jp/download/details.aspx?id=39305)、または Visual Studio 2013 [Update 3](http://www.microsoft.com/ja-jp/download/details.aspx?id=43721)、または [Visual Studio Express 2013](http://www.microsoft.com/ja-jp/download/details.aspx?id=43722)。

	>[AZURE.NOTE] 現在、ツールは英語版にのみ付属しています。 


## インストール

HDInsight Tools for Visual Studio は、Microsoft Azure SDK に付属しています。[Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=255386) を使用してインストールできます。

![HDinsight Tools for Visual Studio Web Platform installer][1]


## Azure サブスクリプションへの接続
HDInsight Tools for Visual Studio を使用して、HDInsight クラスターへの接続、いくつかの基本的な管理操作の実行、および Hive クエリの実行が可能です。

**Azure サブスクリプションに接続するには**

1.	Visual Studio を実行します。
2.	**[ビュー]** メニューで、**[サーバー エクスプ ローラー]** をクリックして、サーバー エクスプ ローラー ウィンドウを開きます。
3.	**[Azure]** を展開し、**[HDInsight]** クラスターをクリックします。 

	>[AZURE.NOTE]**[HDInsight タスク一覧]** ウィンドウが開きます。ウィンドウが表示されない場合は、**[ビュー]** メニューで **[その他のウィンドウ]** をクリックし、次に **[HDInsight タスク一覧ウィンドウ]** をクリックして開くことができます。  
4.	Azure サブスクリプションの資格情報を入力し、**[サインイン]** をクリックします。この操作は、このワークステーションで、まだ一度も Visual Studio から Azure サブスクリプションに接続していない場合にのみ必要です。
5.	サーバー エクスプローラーに、既存の HDInsight クラスターの一覧が表示されます。クラスターが 1 つもない場合は、管理ポータル、Azure PowerShell、または HDInsight SDK を使用してプロビジョニングできます。詳細については、[HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページを参照してください。

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	HDInsight クラスターを展開します。**Hive データベース**、既定のストレージ アカウント、およびリンクされているストレージ アカウントが表示されます。さらに、エンティティを展開できます。 

Azure サブスクリプションに接続した後で、次を実行できます。

**Visual Studio から管理ポータルに接続するには**

- サーバー エクスプ ローラーで、**[Azure]**、 **[HDInsight クラスター]** の順に展開し、HDInsight クラスターを右クリックして、**[Azure ポータルでクラスターを管理する]** をクリックします。

**Visual Studio から質問をしたりフィードバックを提供したりするには**

- **[ツール]** メニューで、**[HDInsight]**、**[MSDN フォーラム]** の順にクリックして質問するか、または**フィードバックを提供**します。

## リンクしているリソースへの移動 

サーバー エクスプ ローラーで、既定のストレージ アカウント、およびすべてのリンクされたストレージ アカウントを確認できます。既定のストレージ アカウントを展開し、そのストレージ アカウントのコンテナーを表示できます。既定のストレージ アカウントと既定のコンテナーの両方がマークされます。コンテナーのいずれかの右クリックしてコンテナーを表示することもできます。

![HDInsight Tools for visual studio server explorer cluster list][2]

## Hive クエリの実行
[Apache Hive][apache.hive] は Hadoop 上に構築されるデータ ウェアハウス基盤であり、データ、サマリー、クエリ、および分析を提供します。HDInsight Tools for Visual Studio は Visual Studio からの Hive クエリの実行をサポートします。Hive の詳細については、[HDInsight での Hive の使用][hdinsight.hive] に関するページを参照してください。

###hivesampletable Hive テーブルの表示
すべての HDInsight クラスターには、*hivesampletable* という Hive テーブルのサンプルが付属します。このテーブルを使用して Hive テーブルの一覧表示、テーブル スキーマの表示、および Hive テーブル内の行の一覧表示を行う方法を説明します。



**Hive テーブルを一覧表示し、Hive テーブル スキーマを表示するには**

1.	**サーバー エクスプローラー**から **[Azure]**、**[HDInsight クラスター]**、クラスター、**[Hive データベース]**、**[既定]**、**[hivesampletable]** の順に展開し、テーブル スキーマを表示します。
4.	**[hivesampletable]** を右クリックし、**[上位 100 行を表示]** をクリックして、行を一覧表示します。これは、Hive ODBC ドライバーを使用して、次の Hive クエリを実行することを同等です。

		SELECT * FROM hivesampletable LIMIT 100

	行カウントをカスタマイズできます。 
 
	![HDinsight Hive Visual Studio schema query][6]

###Hive テーブルの作成

GUI を使用して Hive テーブルを作成するか、または Hive クエリを使用できます。Hive クエリの使用については、[Hive クエリの実行](#run.queries)を参照してください。

**Hive テーブルを作成するには**

1. **サーバー エクスプローラー**から、**[Azure]**、 **[HDInsight クラスター]**、HDInsight クラスター、**[Hive データベース]** の順に展開し、**[既定]** を右クリックして、**[テーブルの作成]** をクリックします。
2. テーブルを構成します。
3. **[テーブルの作成]** をクリックして、新しい Hive テーブルを作成するためのジョブを送信します。

	![hdinsight visual studio tools create hive table][7]

###<a name="run.queries"></a>Hive クエリの実行
Hive クエリを作成および実行するには次の 2 つの方法があります。

- アドホック クエリを作成する
- Hive アプリケーションを作成する

**アドホック クエリを作成および実行するには**

1. **サービス エクスプローラー**から、**[Azure]**、**[HDInsight クラスター]** の順に展開します。
2. クエリを実行するクラスターを右クリックして、**[Hive クエリの作成]** をクリックします。 
3. Hive クエリを入力します。
4. **[送信]** または **[(高度な) 送信]** をクリックします。高度な送信の場合は、スクリプトの**ジョブ名**、**引数**、**追加の構成**、および**ステータス ディレクトリ**を構成します。

	![hdinsight hadoop hive query][9]

	ジョブを送信した後で、 **[Hive ジョブの概要]** ウィンドウを確認できます。

	![hdinsight hadoop hive query][8]
5. **[更新]** ボタンを使用して、ジョブのステータスが**完了**に変更されるまで、ステータスを更新します。
6. 下部にあるリンクをクリックして、**ジョブ クエリ**、**ジョブ出力**、および**ジョブ ログ**を表示します。



**Hive ソリューションを作成および実行するには**

1. **[ファイル]** メニューの **[新規作成]** をポイントし、**[プロジェクト]** をクリックします。
2. 左側のウィンドウから **[HDInsight]** を選択し、中央のウィンドウで **[Hive アプリケーション]** を選択して、プロパティを入力し、**[OK]** をクリックしします。
3. **ソリューション エクスプローラー**で、**Script.hql** をダブルクリックして開きます。

 
###Hive ジョブの表示
すべての Hive ジョブついて、ジョブ クエリ、ジョブ出力、およびジョブ ログを表示できます。

**Hive ジョブの表示**

1. **サーバー エクスプローラー**から、**[Azure]**、**[HDInsight]** の順に展開します。 
2. HDInsight クラスターを右クリックし、**[Hive ジョブの表示]** をクリックします。クラスター上で実行した Hive ジョブの一覧が表示されます。 
3. ジョブの一覧でジョブをクリックして選択し、**[Hive ジョブの概要]** ウィンドウを使用して、**ジョブ クエリ**、**ジョブ出力**または**ジョブ ログ**を開きます。

## 次のステップ
この記事では、Visual Studio から HDInsight クラスターに接続して、Hive クエリを実行する方法を説明しました。詳細については、次を参照してください。

- [HDInsight での Hadoop Hive の使用][hdinsight.hive]
- [HDInsight で Hadoop を使用する][hdinsight.get.started]
- [HDInsight での Hadoop ジョブの送信][hdinsight.submit.jobs]
- [HDInsight での Hadoop を使用した Twitter データの分析][hdinsight.analyze.twitter.data]


<!--Anchors-->
[インストール]: #installation
[Azure サブスクリプションへの接続]: #connect-to-your-azure-subscription
[リンクしているリソースへの移動]: #navigate-the-linked-resources
[Hive クエリの実行]: #run-hive-queries
[次のステップ]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/


[apache.hive]: http://hive.apache.org<!--HONumber=42-->
