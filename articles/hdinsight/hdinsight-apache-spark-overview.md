<properties 
	pageTitle="HDInsight での Apache Spark の概要 |Azure" 
	description="HDInsight での Apache Spark の概要およびアプリケーションで HDInsight の Spark を使用するシナリオです。" 
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
	ms.date="07/10/2015" 
	ms.author="nitinme"/>

# 概要: Azure HDInsight での Apache Spark 
 
<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。Spark 処理エンジンは、速度、使いやすさ、高度な分析用に作成されています。Spark のメモリ内の計算能力により、機械学習とグラフ計算における反復的なアルゴリズムに対して、Spark は適切な選択肢となります。また、Spark は Azure BLOB ストレージ (WASB) と互換性があるので、Azure に格納されている既存データを Spark で簡単に処理できます。

HDInsight で Spark クラスターをプロビジョニングするときは、インストールおよび構成されている Spark で Azure コンピューティング リソースをプロビジョニングします。HDInsight で Spark クラスターをプロビジョニングするのにかかる時間はわずか約 10 分です。処理対象のデータは、Azure BLOB ストレージに格納されます。「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」をご覧ください。

![Azure HDInsight での Apache Spark](./media/hdinsight-apache-spark-overview/SparkArchitecture.png "Azure HDInsight での Apache Spark")


**Azure HDInsight での Apache Spark の概要** 「[クイック スタート: HDInsight での Spark クラスターのプロビジョニングと Jupyter および Zeppelin を使用したサンプル アプリケーションの実行](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)」をご覧ください。



Azure HDInsight での Apache Spark について説明している概要ビデオをご覧ください。

> [AZURE.VIDEO announcing-apache-spark-on-azure-hdinsight]

## Azure HDInsight で Spark を使用する理由 

Azure HDInsight は、完全に管理された Spark サービスを提供します。HDInsight で Spark を使用する利点は次のとおりです。

| 機能 | 説明 |
|-------------------------------------|-------------------|
| プロビジョニングのしやすさ | Azure 管理ポータル、Azure PowerShell、または HDInsight .NET SDK を使用すると、HDInsight に新しい Spark クラスターを数分でプロビジョニングできます。「[HDInsight での Spark クラスターのプロビジョニング](hdinsight-apache-spark-provision-clusters.md)」をご覧ください。 |
| 使いやすさ | HDInsight クラスターの Spark には、Zeppelin および Jupyter の Notebook が事前に構成されています。対話型のデータ処理とビジュアル化にこれらを使用できます。これらの Notebook をクラスター ダッシュボードから起動し、Spark クラスターに対して直接使用できます。|
| REST API | HDInsight の Spark に含まれる Spark ジョブ サーバーは REST API サーバーであり、ユーザーはジョブをリモートで送信して実行を監視できます。 |
| 同時クエリ | HDInsight の Spark は同時クエリをサポートします。これにより、1 人のユーザーからの複数のクエリまたは複数のユーザーおよびアプリケーションからの複数のクエリが、同じクラスター リソースを共有できます。 |
| SSD へのキャッシュ | データのキャッシュ先を、メモリまたはクラスター ノードに取り付けられている SSD から選択できます。メモリへのキャッシュは、クエリのパフォーマンスは最高ですが、コストがかかります。SSD へのキャッシュは、メモリ内のデータセット全体を収めるのに必要なサイズのクラスターを作成する必要なしにクエリのパフォーマンスを向上できる優れたオプションです。|
| Azure サービスとの統合 | HDInsight の Spark には、Azure Event Hubs へのコネクタが付属しています。Spark の一部として既に使用できる [Kafka](http://kafka.apache.org/) に加えて、Event Hubs を使用してストリーミング アプリケーションを作成できます。 |
| BI ツールとの統合 | HDInsight の Spark には、データ分析用の [Power BI](http://www.powerbi.com/) や [Tableau](http://www.tableau.com/products/desktop) などの人気のある BI ツールへのコネクタが要されています。|
| 読み込み済みの Anaconda ライブラリ | HDInsight の Spark クラスターには、Anaconda ライブラリが事前にインストールされています。[Anaconda](http://docs.continuum.io/anaconda/) は、機械学習、データ分析、視覚化などのための 200 個近いライブラリを提供します。|
| 拡張性 | 作成中にクラスター内のノード数を指定できますが、ワークロードに一致するようにクラスターを拡大、縮小できます。すべての HDInsight クラスターでは、クラスター内のノード数を変更できます。また、すべてのデータは Azure BLOB ストレージに格納されるため、Spark クラスターはデータの損失なしで削除できます。 |
| 常時サポート | HDInsight の Spark では、エンタープライズ レベルの 24 時間無休体制のサポートと、アップタイム 99.9% の SLA が提供されます。|



## HDInsight での Spark の使用例

HDInsight の Apache Spark では、次のような主要なシナリオが可能です。

### 対話型のデータ分析と BI

[チュートリアルを見る](hdinsight-apache-spark-use-bi-tools.md)

HDInsight の Apache Spark では、データは Azure BLOB に格納されます。ビジネス エキスパートや重要な意思決定者は、そのデータを分析してレポートを作成し、分析されたデータから Microsoft Power BI を使用して対話型レポートを作成できます。アナリストは Azure Storage の非構造化または半構造化されたデータから開始し、Notebook を使用してデータ用のスキーマを定義し、Microsoft Power BI を使用してデータ モデルを作成できます。また、HDInsight の Spark は Tableau、Qlikview、SAP Lumira などのサードパーティ製 BI ツールをサポートするので、データ アナリスト、ビジネス エキスパート、重要な意思決定者にとって理想的なプラットフォームです。

### 反復的な機械学習

[チュートリアルを見る](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

Apache Spark には、Spark を基にして作成された機械学習ライブラリである [MLlib](http://spark.apache.org/mllib/) が付属しています。さらに、HDInsight の Spark には、機械学習用のさまざまなパッケージを含む Python ディストリビューションである Anaconda も含まれています。これと IPython Notebook の組み込みサポートを組み合わせることで、機械学習アプリケーションを作成するための最高の環境が提供されます。

### ストリーミングおよびリアルタイムのデータ分析

[チュートリアルを見る](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

リアルタイムのデータ分析は、到着したデータの処理によるデータ洞察の時間短縮から、真のストリーミング ソリューションの構築まで、広範囲のシナリオに使用されます。HDInsight の Spark では、リアルタイム分析ソリューションを構築するためのリッチなサポートが提供されます。Spark には既に Kafka、Flume、Twitter、ZeroMQ、TCP ソケットなどの多数のソースからデータを取り込むためのコネクタがありますが、HDInsight の Spark では Azure Event Hubs からデータを取り込むためのファーストクラスのサポートが追加されます。Event Hubs は、Azure で最も広く使用されているキュー サービスです。すぐに使用できる Event Hubs のサポートにより、HDInsight の Spark はリアルタイム分析パイプラインを構築するための理想的なプラットフォームです。

##<a name="next-steps"></a>Spark クラスターに含まれるコンポーネント

HDInsight の Spark には、クラスターで使用できる次のコンポーネントが既定で含まれています。

- [Spark 1.3.1](https://spark.apache.org/docs/1.3.1/)。Spark Core、Spark SQL、Spark ストリーミング API、GraphX、MLlib が付属しています。
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Spark ジョブ サーバー](https://github.com/spark-jobserver/spark-jobserver)
- [Zeppelin Notebook](https://zeppelin.incubator.apache.org)
- [IPython Notebook](https://jupyter.org)

HDInsight の Spark では、Microsoft Power BI や Tableau などの BI ツールから HDInsight の Spark クラスターに接続するための [ODBC ドライバー](http://go.microsoft.com/fwlink/?LinkId=616229)も提供されます。

##<a name="see-also"></a>関連項目

* [クイックスタート: HDInsight と Zeppelin Notebook で Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [HDInsight での Apache Spark クラスターのプロビジョニング](hdinsight-apache-spark-provision-clusters.md)
* [HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Machine Learning アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [Azure HDInsight の Apache Spark クラスターへのジョブのリモート送信](hdinsight-apache-spark-job-server.md)


[hdinsight-storage]: ../hdinsight-use-blob-storage/

<!---HONumber=July15_HO3-->