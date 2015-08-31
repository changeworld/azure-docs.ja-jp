<properties
	pageTitle="HDInsight での Hadoop のサンプルの実行 | Microsoft Azure"
	description="用意されたサンプルを利用して、Azure HDInsight サービスを使い始めます。データ クラスター上で MapReduce プログラムを実行する PowerShell スクリプトを使用します。"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>




#HDInsight での Hadoop のサンプルの実行

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Azure HDInsight を使用して、Hadoop クラスターで MapReduce ジョブの実行を始めるときに役立つサンプルが用意されています。これらのサンプルは、HDInsight によって管理されるクラスターを作成して、それぞれのクラスターで利用できます。これらのサンプルを実行すると、Hadoop クラスターで Azure PowerShell コマンドレットを使用してジョブを実行するのに慣れることができます。

MapReduce プログラムは、Microsoft .NET API for HDInsight を使用してアプリケーションからプログラムにより実行することもできます。ジョブ送信に HDInsight API を使用する方法の詳細については、「[HDInsight での Hadoop ジョブの送信][hdinsight-submit-jobs]」をご覧ください。

Hadoop 関連技術の追加情報は、Java ベースの MapReduce プログラミングやストリーミング、Windows PowerShell スクリプトで使用するコマンドレットのドキュメントなど、Web 上に多数存在しています。これらのリソースの詳細については、「**Azure HDInsight 入門**」の最後のセクション「[HDInsight の関連リソース][hdinsight-introduction]」をご覧ください。

**サンプルの内容**

<p>これらのサンプルを利用すれば、Hadoop ジョブのデプロイ方法をすぐに理解し、サービスで使用される概念とスクリプト作成手順を自在に試すことができます。サンプルは、さまざまなサイズのデータ セットの作成とインポート、ジョブの実行、逐次的なジョブの作成、ジョブの結果の確認など、一般的なタスクの実例です。使用するデータ セットはサイズを変更できるため、データ セットのサイズがジョブのパフォーマンスに及ぼす影響を確認できます。</p>


**前提条件**:

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- **HDInsight クラスター**。クラスターを作成するさまざまな方法については、「[HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)」を参照してください。
- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。



## サンプル ##

HDInsight には次のサンプルが付属します。

- [**Pi 推定 Hadoop サンプル**][hdinsight-sample-pi-estimator]\: このチュートリアルでは、HDInsight を使用して MapReduce プログラムを実行する方法を紹介します。MapReduce プログラムは、統計的手法 (準モンテカルロ法) に基づいてパイ (Pi) の値を計算します。
- [**HDInsight の Hadoop クラスターでの MapReduce ワード カウント サンプルの実行**][hdinsight-sample-wordcount]\: このチュートリアルでは、HDInsight クラスターを使用してテキスト ファイル内の単語出現回数をカウントする MapReduce プログラムを実行する方法を紹介します。
- [**10 GB GraySort Hadoop サンプル**][hdinsight-sample-10gb-graysort]\: このチュートリアルでは、HDInsight を使用し、10 GB のファイルに対して汎用 GraySort を実行する方法を紹介します。実行するジョブは 3 つあります。データを生成する Teragen、データをソートする Terasort、データが適切にソートされているか確認する Teravalidate です。
- [**Hadoop での C# ストリーミング ワードカウント MapReduce サンプル**][hdinsight-sample-csharp-streaming]\: このチュートリアルでは、Hadoop ストリーミング インターフェイスを使用する MapReduce プログラムを C# で記述する方法を紹介します。


## サンプルの実行方法 ##

サンプルは Azure PowerShell を使用して実行できます。その方法は、上でリンクされた各サンプルのページで説明されています。

##次のステップ ##

この記事および各サンプルの記事では、Azure PowerShell を使用して HDInsight クラスターに付属するサンプルを実行する方法を説明しました。HDInsight で Pig、Hive、MapReduce を使用する方法のチュートリアルについては、次のトピックをご覧ください。

* [HDInsight で Hive と Hadoop を使用し、モバイル ハンドセットの使用状況を分析する][hdinsight-get-started]
* [HDInsight での Pig と Hadoop の使用][hdinsight-use-pig]
* [HDInsight での Hive と Hadoop の使用][hdinsight-use-hive]
* [HDInsight での Hadoop ジョブの送信][hdinsight-submit-jobs]
* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation]
* [HDInsight での Hadoop のデバッグ: エラー メッセージ][hdinsight-errors]


[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=August15_HO8-->