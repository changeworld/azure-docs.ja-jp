<properties 
	pageTitle="Web サイトのログ分析のための Hadoop での Hive の使用| Microsoft Azure" 
	description="Web サイトのログを分析するために HDInsight で Hive を使用する方法を説明します。HDInsight テーブルへの入力にログ ファイルを使用し、HiveQL を使用してデータを照会します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="nitinme"/>

# Web サイトのログを分析するための HDInsight での Hive の使用

Web サイトのログを分析するために HDInsight で HiveQL を使用する方法を説明します。Web サイトのログ分析は、類似するアクティビティに基づく対象ユーザーの区分、人口統計によるサイト訪問者の分類、参照されたコンテンツや訪問元の Web サイトの確認などのために使用できます。

このサンプルでは、HDInsight クラスターを使用して Web サイトのログ ファイルを分析することにより、1 日の間に発生した外部 Web サイトからの Web サイトへのアクセス数を調べます。また、発生した Web サイト エラーの概要を生成します。学習内容:

- Web サイトのログ ファイルが含まれている Azure Blob ストレージへの接続
- これらのログを照会するための HIVE テーブルの作成
- データを分析するための HIVE クエリの作成
- 分析したデータを取得するための、Microsoft Excel を使用した HDInsight への接続 (Open Database Connectivity (ODBC) を使用)

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##前提条件

- **HDInsight クラスター**のプロビジョニングを終えている必要があります。手順については、「[HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision]」を参照してください。 
- Microsoft Excel 2013 または Microsoft Excel 2010 がインストールされていること。
- Hive から Excel にデータをインポートするための [Microsoft Hive ODBC ドライバー](http://www.microsoft.com/download/details.aspx?id=40886)があること。


##サンプルを実行するには

1. Azure ポータルから、サンプルの実行に使用するクラスターを選択し、下部にある **[クエリ コンソール]** をクリックします。または、次に示す URL を使用してクエリ コンソールを直接開くこともできます。

	 	https://<clustername>.azurehdinsight.net
	
	入力を要求されたら、クラスターをプロビジョニングするときに使用した管理者名とパスワードを使用して認証します。
  
2. 表示された Web ページで **[概要ギャラリー]** タブをクリックし、**[サンプル]** カテゴリにある **[Web サイト ログ分析]** サンプルを選択します。

3. Web ページに記載されている手順に従って、サンプルを完了します。

##次のステップ
「[Hive を HDInsight と共に使用したセンサー データの分析](hdinsight-hive-analyze-sensor-data.md)」のサンプル手順に従ってください。


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png

<!--HONumber=54--> 