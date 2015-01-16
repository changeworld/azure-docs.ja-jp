<properties urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="HDInsight での Hadoop のサンプルの実行 | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="用意されたサンプルを利用して、Azure HDInsight サービスを使い始めます。データ クラスター上で MapReduce プログラムを実行する PowerShell スクリプトを使用します。" metaCanonical="" services="hdinsight" documentationCenter="" title="Run the Hadoop samples in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />




#HDInsight での Hadoop のサンプルの実行

Azure HDInsight を使用して、Hadoop クラスターで MapReduce ジョブの実行を始めるときに役立つサンプルが用意されています。これらのサンプルは、HDInsight によって管理されるクラスターを作成して、それぞれのクラスターで利用できます。これらのサンプルを実行すると、Hadoop クラスターで Azure PowerShell HDInsight コマンドレットを使用してジョブを実行するのに慣れることができます。

MapReduce プログラムは、Microsoft .NET API for HDInsight を使用してアプリケーションから実行することもできます。ジョブ送信に HDInsight API を使用する方法の詳細については、「[HDInsight での Hadoop ジョブの送信][hdinsight-submit-jobs]」を参照してください。

Hadoop 関連技術の追加情報は、Java ベースの MapReduce プログラミングやストリーミング、PowerShell スクリプトで使用するコマンドレットのドキュメントなど、Web 上に多数存在しています。これらのリソースの詳細については、「**Resources for HDInsight (HDInsight の関連リソース)**」セクション (「[Introduction to Azure HDInsight (Azure HDInsight 入門)][hdinsight-introduction]」内) を参照してください。

**サンプルの内容**

<p>これらのサンプルを利用すれば、Hadoop ジョブの展開方法をすぐに理解し、サービスで使用される概念とスクリプト作成手順を自在に試すことができます。サンプルは、さまざまなサイズのデータ セットの作成とインポート、ジョブの実行、逐次的なジョブの作成、ジョブの結果の確認など、一般的なタスクの実例です。使用するデータ セットはサイズを変更できるため、データ セットのサイズがジョブのパフォーマンスに及ぼす影響を確認することができます。</p>


**前提条件**:	

- Azure アカウントが必要です。アカウントにサインアップする方法については、「[1 か月間の無料評価版](http://azure.microsoft.com/ja-jp/pricing/free-trial/) 」を参照してください。

- HDInsight クラスターのプロビジョニングを終えている必要があります。クラスターを作成するさまざまな方法については、「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング]」を参照してください。(../hdinsight-provision-clusters/)

- Azure PowerShell をインストールして、アカウントを使用するように構成している必要があります。その手順については、「[Azure PowerShell のインストールおよび構成方法][powershell-install-configure]」を参照してください。

## サンプル ##

HDInsight には次のサンプルが付属します。

- [**Pi 推定サンプル**][hdinsight-sample-pi-estimator]: このチュートリアルでは、HDInsight を使用して MapReduce プログラムを実行する方法を紹介します。MapReduce プログラムは、統計的手法 (準モンテカルロ法) に基づいてパイ (Pi) の値を計算します。
- [**ワードカウント サンプル**][hdinsight-sample-wordcount]: このチュートリアルでは、HDInsight クラスターを使用してテキスト ファイル内の単語出現回数をカウントする MapReduce プログラムを実行する方法を紹介します。
- [**10 GB GraySort サンプル**][hdinsight-sample-10gb-graysort]: このチュートリアルでは、HDInsight を使用し、10 GB のファイルに対して汎用 GraySort を実行する方法を紹介します。実行するジョブは 3 つあります。データを生成する Teragen、データをソートする Terasort、データが適切にソートされているか確認する Teravalidate です。
- [**C# ストリーミング サンプル**][hdinsight-sample-csharp-streaming]: このチュートリアルでは、Hadoop ストリーミング インターフェイスを使用する MapReduce プログラムを C# で記述する方法を紹介します。 


## サンプルの実行方法 ##

サンプルは Azure PowerShell を使用して実行できます。その方法は、上でリンクされた各サンプルのページで説明されています。

##次のステップ ##

この記事および各サンプルの記事では、Azure PowerShell を使用して HDInsight クラスターに付属するサンプルを実行する方法を説明しました。HDInsight で Pig、Hive、および MapReduce を使用する方法のチュートリアルについては、次のトピックを参照してください。

* [Azure HDInsight サービスの概要][hdinsight-get-started]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]
* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation]
* [HDInsight での Hadoop のデバッグ:エラー メッセージ][hdinsight-errors]


[hdinsight-errors]: ../hdinsight-debug-jobs/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-introduction]: ../hdinsight-hadoop-introduction/



[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/


<!--HONumber=35.1-->
