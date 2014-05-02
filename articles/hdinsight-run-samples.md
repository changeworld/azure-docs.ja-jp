<properties linkid="manage-services-hdinsight-howto-run-samples" urlDisplayName="HDInsight のサンプルの実行" pageTitle="HDInsight のサンプルの実行 | Azure" metaKeywords="" description="用意されたサンプルを利用して、Azure HDInsight サービスを使い始めます。データ クラスター上で MapReduce プログラムを実行する PowerShell スクリプトを使用します。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight のサンプルの実行" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />




#HDInsight のサンプルの実行

Azure HDInsight を使い始めるときに役立つサンプルが用意されています。これらのサンプルは、作成する HDInsight クラスターそれぞれで利用できます。これらのサンプルを実行すると、Azure PowerShell HDInsight コマンドレットに慣れることができます。

MapReduce プログラムは、Microsoft .NET API for HDInsight を使用してアプリケーションから実行することもできます。ジョブ送信に HDInsight API を使用する方法の詳細については、「[プログラムによる Hadoop ジョブの送信][submit-jobs-programmatically]」を参照してください。

Hadoop 関連技術の追加情報は、Java ベースの MapReduce プログラミングやストリーミング、PowerShell スクリプトで使用するコマンドレットのドキュメントなど、Web 上に多数存在しています。これらのリソースの詳細については、「[Azure HDInsight 入門][hdinsight-resources]」の最後のセクション「**HDInsight の関連リソース**」を参照してください。

**サンプルの内容**

<p>これらのサンプルを利用すれば、Hadoop ジョブの展開方法をすぐに理解し、サービスで使用される概念とスクリプト作成手順を自在に試すことができます。サンプルは、さまざまなサイズのデータ セットの作成とインポート、ジョブの実行、逐次的なジョブの作成、ジョブの結果の確認など、一般的なタスクの実例です。使用するデータ セットはサイズを変更できるため、データ セットのサイズがジョブのパフォーマンスに及ぼす影響を確認することができます。</p>


**前提条件**:	

- Azure アカウントが必要です。アカウントにサインアップする方法については、[Azure の無料評価版のページ](http://www.windowsazure.com/ja-jp/pricing/free-trial/)を参照してください。

- HDInsight クラスターのプロビジョニングを終えている必要があります。クラスターを作成するさまざまな方法については、「[HDInsight クラスターのプロビジョニング](/ja-jp/manage/services/hdinsight/provision-hdinsight-clusters/)」を参照してください。

- Azure PowerShell をインストールして、アカウントを使用するように構成している必要があります。その手順については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」を参照してください。

## サンプル##

HDInsight には次のサンプルが付属します。

- [**Pi 推定サンプル**][pi-estimator]: このチュートリアルでは、HDInsight を使用して MapReduce プログラムを実行する方法を紹介します。MapReduce プログラムは、統計的手法 (準モンテカルロ法) に基づいてパイ (Pi) の値を計算します。
- [**ワードカウント サンプル**][wordcount]: このチュートリアルでは、HDInsight クラスターを使用してテキスト ファイル内の単語出現回数をカウントする MapReduce プログラムを実行する方法を紹介します。
- [**10 GB GraySort サンプル**][10gb-graysort]: このチュートリアルでは、HDInsight を使用し、10 GB のファイルに対して汎用 GraySort を実行する方法を紹介します。実行するジョブは 3 つあります。データを生成する Teragen、データをソートする Terasort、データが適切にソートされているか確認する Teravalidate です。
- [**C# ストリーミング サンプル**][cs-streaming]: このチュートリアルでは、Hadoop ストリーミング インターフェイスを使用する MapReduce プログラムを C# で記述する方法を紹介します。


## サンプルの実行方法##

サンプルは Azure PowerShell を使用して実行できます。その方法は、上でリンクされた各サンプルのページで説明されています。

##次のステップ##

この記事および各サンプルの記事では、Azure PowerShell を使用して HDInsight クラスターに付属するサンプルを実行する方法を説明しました。HDInsight で Pig、Hive、および MapReduce を使用する方法のチュートリアルについては、次のトピックを参照してください。

* [Azure HDInsight サービスの概要][getting-started]
* [HDInsight での Pig の使用][pig]
* [HDInsight での Hive の使用][hive]
* [プログラムによる Hadoop ジョブの送信][submit-jobs-programmatically]
* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation]
* [HDInsight のデバッグ: エラー メッセージ][hdinsight-debug-error-messages]

[Powershell-install-configure]: /ja-jp/documentation/articles/install-configure-powershell/

[hdinsight-debug-error-messages]: /ja-jp/manage/services/hdinsight/debug-error-messages/
[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx
[getting-started]: /ja-jp/manage/services/hdinsight/get-started-hdinsight/

[hive]: /ja-jp/manage/services/hdinsight/using-hive-with-hdinsight/
[pig]: /ja-jp/manage/services/hdinsight/using-pig-with-hdinsight/
[pi-estimator]: /ja-jp/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/
[10gb-graysort]: /ja-jp/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/
[wordcount]: /ja-jp/manage/services/hdinsight/howto-run-samples/sample-wordcount/
[cs-streaming]: /ja-jp/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/

[submit-jobs-programmatically]: /ja-jp/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-resources]: /ja-jp/manage/services/hdinsight/introduction-hdinsight/


