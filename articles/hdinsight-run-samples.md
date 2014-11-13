<properties urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="HDInsight での Hadoop のサンプルの実行 | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="用意されたサンプルを利用して、Azure HDInsight サービスを使い始めます。データ クラスター上で MapReduce プログラムを実行する PowerShell スクリプトを使用します。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight での Hadoop のサンプルの実行" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# HDInsight での Hadoop のサンプルの実行

Azure HDInsight を使用して、Hadoop クラスターで MapReduce ジョブの実行を始めるときに役立つサンプルが用意されています。これらのサンプルは、HDInsight によって管理されるクラスターを作成して、それぞれのクラスターで利用できます。これらのサンプルを実行すると、Hadoop クラスターで Azure PowerShell HDInsight コマンドレットを使用してジョブを実行するのに慣れることができます。

MapReduce プログラムは、Microsoft .NET API for HDInsight を使用してアプリケーションから実行することもできます。ジョブ送信に HDInsight API を使用する方法の詳細については、「[プログラムによる Hadoop ジョブの送信][プログラムによる Hadoop ジョブの送信]」を参照してください。

Hadoop 関連技術の追加情報は、Java ベースの MapReduce プログラミングやストリーミング、PowerShell スクリプトで使用するコマンドレットのドキュメントなど、Web 上に多数存在しています。これらのリソースの詳細については、「[Azure HDInsight 入門][Azure HDInsight 入門]」の最後のセクション「**HDInsight の関連リソース**」を参照してください。

**サンプルの内容**

これらのサンプルを利用すれば、Hadoop ジョブの展開方法をすぐに理解し、サービスで使用される概念とスクリプト作成手順を自在に試すことができます。サンプルは、さまざまなサイズのデータ セットの作成とインポート、ジョブの実行、逐次的なジョブの作成、ジョブの結果の確認など、一般的なタスクの実例です。使用するデータ セットはサイズを変更できるため、データ セットのサイズがジョブのパフォーマンスに及ぼす影響を確認することができます。

**前提条件**:

-   Azure アカウントが必要です。アカウントにサインアップする方法については、[Azure の無料評価版のページ][Azure の無料評価版のページ]を参照してください。

-   HDInsight クラスターのプロビジョニングを終えている必要があります。クラスターを作成するさまざまな方法については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

-   Azure PowerShell をインストールして、アカウントを使用するように構成している必要があります。その手順については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell のインストールおよび構成]」を参照してください。

## サンプル

HDInsight には次のサンプルが付属します。

-   [**Pi 推定サンプル**][**Pi 推定サンプル**]: このチュートリアルでは、HDInsight を使用して MapReduce プログラムを実行する方法を紹介します。MapReduce プログラムは、統計的手法 (準モンテカルロ法) に基づいてパイ (Pi) の値を計算します。
-   [**ワードカウント サンプル**][**ワードカウント サンプル**]: このチュートリアルでは、HDInsight クラスターを使用してテキスト ファイル内の単語出現回数をカウントする MapReduce プログラムを実行する方法を紹介します。
-   [**10 GB GraySort サンプル**][**10 GB GraySort サンプル**]: このチュートリアルでは、HDInsight を使用し、10 GB のファイルに対して汎用 GraySort を実行する方法を紹介します。実行するジョブは 3 つあります。データを生成する Teragen、データをソートする Terasort、データが適切にソートされているか確認する Teravalidate です。
-   [**C# ストリーミング サンプル**][**C# ストリーミング サンプル**]: このチュートリアルでは、Hadoop ストリーミング インターフェイスを使用する MapReduce プログラムを C# で記述する方法を紹介します。

## サンプルの実行方法

サンプルは Azure PowerShell を使用して実行できます。その方法は、上でリンクされた各サンプルのページで説明されています。

## 次のステップ

この記事および各サンプルの記事では、Azure PowerShell を使用して HDInsight クラスターに付属するサンプルを実行する方法を説明しました。HDInsight で Pig、Hive、および MapReduce を使用する方法のチュートリアルについては、次のトピックを参照してください。

-   [Azure HDInsight サービスの概要][Azure HDInsight サービスの概要]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]
-   [HDInsight での Hive の使用][HDInsight での Hive の使用]
-   [プログラムによる Hadoop ジョブの送信][プログラムによる Hadoop ジョブの送信]
-   [Azure HDInsight SDK のドキュメント][Azure HDInsight SDK のドキュメント]
-   [HDInsight のデバッグ: エラー メッセージ][HDInsight のデバッグ: エラー メッセージ]

  [プログラムによる Hadoop ジョブの送信]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Azure HDInsight 入門]: ../hdinsight-hadoop-introduction/
  [Azure の無料評価版のページ]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure PowerShell のインストールおよび構成]: ../install-configure-powershell/
  [**Pi 推定サンプル**]: ../hdinsight-sample-pi-estimator/
  [**ワードカウント サンプル**]: ../hdinsight-sample-wordcount/
  [**10 GB GraySort サンプル**]: ../hdinsight-sample-10gb-graysort/
  [**C# ストリーミング サンプル**]: ../hdinsight-sample-csharp-streaming/
  [Azure HDInsight サービスの概要]: ../hdinsight-get-started/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [Azure HDInsight SDK のドキュメント]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx
  [HDInsight のデバッグ: エラー メッセージ]: ../hdinsight-debug-jobs/
