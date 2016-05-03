<properties
   pageTitle="HDInsight の R Server (プレビュー) のコンピューティング コンテキストのオプション | Azure"
   description="HDInsight の R Server (プレビュー) でユーザーが使用できるさまざまなコンピューティング コンテキスト オプションについて説明します。"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

#HDInsight の R Server (プレビュー) のコンピューティング コンテキストのオプション

HDInsight の R Server (プレビュー) は、[Azure BLOB](../storage/storage-introduction.md "Azure BLOB ストレージ") ストレージ アカウントまたはローカルの Linux ファイル システム上のコンテナーにある HDFS に格納されたデータを使用して R ベースの分析を行うための最新の機能を備えています。R Server はオープン ソースの R を基盤としているため、自社で構築する R ベースのアプリケーションでは 8000 を超えるオープン ソースの R パッケージのほか、[R Server](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR") に付属する Microsoft のビッグ データ分析パッケージ ScaleR のルーチンを活用できます。Premium クラスターのエッジ ノードは、クラスターへの接続と R スクリプトの実行に便利なランディング ゾーンを提供します。エッジ ノードでは、エッジ ノード サーバーのコア間で、またはクラスターのノード間で ScaleR の Hadoop Map Reduce または Spark コンピューティング コンテキストを使用して、ScaleR の並列化された分散関数を実行できます。

## エッジ ノードに対するコンピューティング コンテキスト

一般に、エッジ ノード上の R Server で実行される R スクリプトは、ScaleR 関数を呼び出す場合の手順を除き、そのノードの R インタープリター内で実行されます。ScaleR 呼び出しは、ScaleR コンピューティング コンテキストの設定の方法によって決定されるコンピューティング環境で実行されます。エッジ ノードから R スクリプトを実行する際に可能なコンピューティング コンテキストの値は、local sequential (‘local’)、local parallel (‘localpar’)、Map Reduce、および Spark です。各値を次の表で説明します。

| コンピューティング コンテキスト | 設定方法 | 実行コンテキスト |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local Sequential | rxSetComputeContext(‘local’) | エッジ ノード サーバーでのシーケンシャル (非並列化) 実行 |
| Local Parallel | rxSetComputeContext(‘localpar’) | エッジ ノード サーバーのコアの並列化 |
| Spark | RxSpark() | HDI クラスターのノード間での、Spark を介した分散実行の並列化 |
| Map Reduce | RxHadoopMR() | HDI クラスターのノード間での Map Reduce を介した分散実行の並列化 |


パフォーマンスの向上を目的として実行の並列化を行う場合は、3 つのオプションを利用できます。どれを選択するかは、分析作業の性質、データのサイズ、データの場所によって決まります。

## コンピューティング コンテキストに関する決定

現時点では、どのコンピューティング コンテキストを使用すればよいかを示す方式はありませんが、いくつかの基本原則があります。これは、適切な選択を行うためのポイントをユーザーに示し、また、最適な選択を行う必要がある場合、ベンチマークを実行する前に選択肢を絞り込むのに役立ちます。これらの基本原則を次に示します。

1.	ローカル Linux ファイル システムは HDFS より処理が高速です。
2.	データがローカルで XDF にある場合、繰り返しの分析の処理が速くなります。 
3.	あるいは、小さいデータを対象にするテキスト データ ソースからのストリームは、分析の前に XDF に変換されます。 
4.	分析用のエッジ ノードにデータをコピー/ストリーミングする際に生じるオーバーヘッドは、データが非常に大きい場合、維持できなくなります。 
5.	Spark は、データが非常に大きくなり、データを分散メモリに収容できなくなるまで、Hadoop での分析では Map Reduce よりも高速です。

これらの原則を考慮した上で、コンピューティング コンテキストを選択するための次のような一般的な経験則があります。

### Local Parallel

- 分析するデータが小さく、繰り返し分析が必要ない場合は、データを分析ルーチンに直接ストリーム転送して、'localpar' を使用します。 
- 分析するデータは小さいが、繰り返し分析が必要である場合、または分析するデータが中規模のサイズである場合は、データをローカル ファイル システムにコピーし、XDF にインポートし、'localpar' を介して分析します。 

### Hadoop Spark

- 分析するデータが大きい場合、記憶域に問題がなければ、HDFS の XDF にインポートし、'Spark' を介して分析を行います。 

### Hadoop Map Reduce

- 分析するデータが非常に大きく、Spark パフォーマンスが低下し始めた場合は、’Map Reduce' を介して分析を試みてください。

## rxSetComputeContext のインライン ヘルプ

ScaleR コンピューティング コンテキストの詳細および例については、rxSetComputeContext メソッドなどにおける R のインライン ヘルプを参照するか、

    > ?rxSetComputeContext 

[R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R Server on MSDN") ライブラリで入手可能な「ScaleR Distributed Computing Guide (ScaleR 分散コンピューティング ガイド)」を参照してください。


## 次のステップ

これで、R Server を含む新しい HDInsight クラスターを作成する方法および SSH セッションで R コンソールを使用するための基本的な事項に関する説明は終了しました。R Server on HDInsight を操作する方法については次のリソースを参照してください。

- [Overview of R Server on Hadoop (R Server on Hadoop の概要)](hdinsight-hadoop-r-server-overview.md)
- [Get started with R server on Hadoop (R Server on Hadoop の使用を開始する)](hdinsight-hadoop-r-server-get-started.md)
- [Add RStudio Server to HDInsight premium (HDInsight Premium への RStudio Server の追加)](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure Storage options for R Server on HDInsight Premium (HDInsight Premium での R Server の Azure Storage オプション)](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0420_2016-->