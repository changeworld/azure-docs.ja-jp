<properties
	pageTitle="HDInsight での R とは何ですか? HDInsight での R Server の概要 |Microsoft Azure"
	description="HDInsight での R の概要と、R を使用してビッグ データ分析用のアプリケーションを作成する方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/29/2016"
   ms.author="jeffstok"/>


# 概要: HDInsight での R Server

HDInsight Premium では、Azure で HDInsight クラスターを作成する際に R Server をオプションとして使用できるようになりました。この新しい機能により、データ サイエンティスト、統計学者、R プログラマは、HDInsight でのスケーラブルで分散型の分析手法にオンデマンドでアクセスできるようになります。クラスターは手元のプロジェクトやタスクに応じてサイズを調整し、不要になれば破棄できます。Azure HDInsight の一部であるこれらのクラスターは、エンタープライズ レベルの 24 時間 365 日のサポートや稼働時間 99.9% の SLA を実現し、Azure エコシステムの他のコンポーネントとも柔軟に統合します。

>[AZURE.NOTE] R Server は HDInsight Premium でのみ使用できます。現在のところ、HDInsight Premium は Hadoop クラスターと Spark クラスターでのみ使用できます。そのため、R Server は現在、HDInsight の Hadoop クラスターと Spark クラスターのみで使用できます。詳細については、[HDInsight で利用可能なさまざまなサービス レベルと Hadoop コンポーネント](hdinsight-component-versioning.md)に関する記事を参照してください。

HDInsight での R Server は、Azure Blob (WASB) に読み込まれた大規模なデータセットでの R ベースの分析に最新の機能を提供します。R Server はオープン ソースの R を基盤としているため、自社で構築する R ベースのアプリケーションでは 8000 を超えるオープン ソースの R パッケージのほか、R Server に付属する Microsoft のビッグ データ分析パッケージ ScaleR のルーチンを活用できます。Premium クラスターのエッジ ノードは、クラスターへの接続と R スクリプトの実行に便利なランディング ゾーンを提供します。エッジ ノードでは、エッジ ノード サーバーのコア間で、またはクラスターのノード間で ScaleR の Hadoop Map Reduce または Spark コンピューティング コンテキストを使用して、ScaleR の並列化された分散関数を実行できます。分析を通じて得られたモデルや予測はダウンロードしてオンプレミスで使用したり、([Azure Machine Learning Studio](http://studio.azureml.net) (AML) [Web サービス](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)を介するなど) Azure の別の場所で運用化できます。

## HDInsight での R の使用

HDInsight クラスターに R Server を組み込むには、Azure ポータルを使用してクラスターを作成する際に、Premium オプションを使用して Hadoop クラスターまたは Spark クラスターを作成する必要があります。このタイプのクラスターは両方とも、クラスターのデータ ノード上の R Server と、R Server ベースの分析用のランディング ゾーンとしてのエッジ ノードを組み込む同様の構成を使用します。クラスター作成の詳細な手順については、[HDInsight での R Server の使用](hdinsight-hadoop-r-server-get-started.md)に関する記事を参照してください。

## データ ストレージ オプション

HDInsight クラスターの既定のストレージは、HDFS ファイル システムが BLOB コンテナーにマップされている Azure Blob (WASB) 上にあります。これにより、分析中にクラスター ストレージにアップロードされるデータまたは書き込まれるデータがどのようなものであっても永続性が保たれます。[AzCopy](../storage/storage-use-azcopy/) ユーティリティは BLOB との間でデータをコピーするための便利な方法です。

エッジ ノードで使用するストレージ オプションとして [Azure Files](../storage/storage-how-to-use-files-linux.md) を使用することもできます。Azure Files では、Azure Storage アカウントで作成したファイル共有を Linux ファイル システムにマウントできます。HDInsight クラスターの R Server 用のデータ ストレージ オプションの詳細については、[HDInsight クラスターの R Server 用のストレージ オプション](hdinsight-hadoop-r-server-storage.md)に関する記事を参照してください。
  
## クラスターでの R Server へのアクセス

R Server でクラスターを作成したら、SSH/Putty を介してクラスターのエッジ ノードの R コンソールに接続できます。また、エッジ ノードに RStudio Server IDE をインストールすることにした場合は、ブラウザーを介して接続できます。RStudio Server のインストールの詳細については、[HDInsight クラスターへの RStudio Server のインストール](hdinsight-hadoop-r-server-install-r-studio.md)に関する記事を参照してください。

## R スクリプトの開発と実行

ユーザーが作成および実行する R スクリプトでは、ScaleR ライブラリの並列化された分散ルーチンに加え、8000 を超えるオープン ソース パッケージを使用できます。エッジ ノードの R Serverで実行されるスクリプトは R インタープリターを使用して実行されますが、コンピューティング コンテキストが Hadoop Map Reduce (RxHadoopMR) または Spark (RxSpark) に設定された ScaleR 関数を呼び出すステップについては例外です。この場合、関数は参照先データに関連付けられたクラスターのデータ (タスク) ノード間に分散した形で実行されます。各種コンピューティング コンテキスト オプションの詳細については、「[Compute context options for R Server on HDInsight Premium (HDInsight Premium での R Server のコンピューティング コンテキストのオプション)](hdinsight-hadoop-r-server-compute-contexts.md)」を参照してください。

## モデルの運用化

データ モデリングが完成したら、Azure とオンプレミスの両方で、モデルを運用化して新しいデータに対する予測 (スコア付け) を実施できます。次に例をいくつか示します。

### HDInsight でのスコア付け

HDInsight でのスコア付けを行うには、Azure Storage アカウントに読み込んだ新しいデータ ファイルに対して予測を行い、予測を Storage アカウントに保存するためにモデルを呼び出す R 関数を記述できます。このルーチンはクラスターのエッジ ノードでオンデマンドで実行するか、スケジュールされたジョブを使用して実行できます。

### Azure Machine Learning でのスコア付け 

Azure Machine Learning Web サービスを使用してスコア付けを行うには、[オープン ソースの Azure Machine Learning R パッケージ](http://www.inside-r.org/blogs/2015/11/18/enhancements-azureml-package-connect-r-azureml-studio)を使用して [Azure Web サービスとしてモデルを発行](http://www.r-bloggers.com/deploying-a-car-price-model-using-r-and-azureml/)し、Azure Machine Learning の機能を使用して Web サービスのユーザー インターフェイスを作成し、必要に応じてスコア付けの Web サービスを呼び出します。この方法を選択した場合、ScaleR モデル オブジェクトを、Web サービスで使用する同等のオープン ソース モデル オブジェクトに変換する必要があります。この変換は、アンサンブルベースのモデル用の ScaleR 強制型変換関数 (`as.randomForest()` など) を使用して実行できます。
  
### オンプレミスのスコア付け

モデル作成後にオンプレミスのスコア付けを行うには、R でモデルをシリアル化し、オンプレミスにダウンロードし、逆シリアル化してから、新しいデータのスコア付けに使用します。「[HDInsight でのスコア付け](#scoring-in-hdinsight)」で前述した手順で行うか、[DeployR](https://deployr.revolutionanalytics.com/) を使用します。

## クラスターの管理 

### R パッケージのインストールと管理

使用する R パッケージのほとんどは、R スクリプトの主要部分が実行されるエッジ ノードに配置する必要があります。エッジ ノードに追加の R パッケージをインストールするには、通常の R の `install.packages()` メソッドを使用できます。
  
たいていは、ScaleR ライブラリのルーチンを使用してクラスターで実行するだけであれば、追加の R パッケージをデータ ノードにインストールする必要はありません。ただし、データ ノードで **rxExec** または **RxDataStep** 実行の使用をサポートするには、追加のパッケージが必要な場合があります。このような場合は、クラスターの作成後にスクリプト アクションを使用して追加のパッケージを指定する必要があります。詳細については、[R Server を使用した HDInsight クラスターの作成](hdinsight-hadoop-r-server-get-started.md)に関する記事を参照してください。
  
### Hadoop Map Reduce メモリ設定の変更 

クラスターに変更を加えることで、Map Reduce ジョブの実行時に R Server で使用可能なメモリ容量を変更できます。これを行うには、クラスターの Azure ポータル ブレードで利用可能な Ambari UI を使用します。クラスターの Ambari UI にアクセスする手順については、「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

以下のように、**RxHadoopMR** の呼び出しで Hadoop スイッチを使用して、R Server で使用可能なメモリ容量を変更することもできます。
 
	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### クラスターのスケーリング

既存のクラスターは Azure ポータルで拡大または縮小できます。これにより、大規模処理で必要な追加容量を獲得できる一方で、使わないクラスターを元の規模に縮小できます。クラスターのスケーリングの方法については、[HDInsight クラスターの管理](hdinsight-administer-use-portal-linux.md)に関する記事を参照してください。

### システム メンテナンス 

メンテナンスは営業時間外に HDInsight クラスター内の基になる Linux VM で実施され、OS 修正プログラムの適用などが行われます。通常は、毎週月曜日と木曜日の午前 3 時 30 分 (VM のローカル時刻に基づく) に実施されます。更新は、4 分の 1 を超えるクラスターに同時に影響を与えないような方法で実施されます。ヘッド ノードは冗長化されており、すべてのデータ ノードに影響があるとは限らないため、この期間に実行中のジョブでは速度が低下する場合がありますが、最後まで実行されます。ユーザーがインストールしたカスタム ソフトウェアまたはローカル データは、クラスターの再構築を必要とするような重大な障害が発生しない限り、メンテナンス イベントを通じて保持されます。

## HDInsight クラスターでの R Server の IDE オプション

HDInsight Premium クラスターの Linux エッジ ノードは、R ベースの分析のランディング ゾーンです。クラスターに接続した後、Linux コマンド プロンプトで「R」と入力して、R Server へのコンソール インターフェイスを起動できます。別のウィンドウで R スクリプト開発用のテキスト エディターを実行し、必要に応じてスクリプトのセクションを切り取って R コンソールに貼り付けると、コンソール インターフェイスの操作を改善できます。
  
R スクリプト開発のシンプルなテキスト エディターからステップアップするには、デスクトップ向けの R ベースの IDE を使用します。Microsoft が最近発表した [R Tools for Visual Studio](https://www.visualstudio.com/ja-JP/features/rtvs-vs.aspx) (RTVS)、[RStudio](https://www.rstudio.com/products/rstudio-server/) のデスクトップおよびサーバー ツール ファミリー、Walware の Eclipse ベースの [StatET](http://www.walware.de/goto/statet) などがあります。
  
Linux のエッジ ノード自体に IDE をインストールすることもできます。このような場合によく使用されている [RStudio Server](https://www.rstudio.com/products/rstudio-server/) は、リモート クライアント向けにブラウザー ベースの IDE を提供します。HDInsight Premium クラスターのエッジ ノードに RStudio Server をインストールすると、クラスターの R Server を使用した R スクリプトの開発と実行を行う IDE の機能をフルに活用できるようになり、既定の R コンソールを使用した場合よりも生産性が大幅に向上します。RStudio Server の使用については、[HDInsight クラスターへの RStudio Server のインストール](hdinsight-hadoop-r-server-install-r-studio.md)に関する記事を参照してください。

## 価格
 
R Server の HDInsight Premium クラスターに関する料金は、Standard HDInsight クラスターと同じように体系化されており、名前、データ、およびエッジ ノード全体で基になる VM のサイズに基づきますが、Premium 向けのコア時間の割増分が加算されます。パブリック プレビューの価格や 30 日間無料試用版など、HDInsight Premium の価格の詳細については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

## 次のステップ

HDInsight クラスターで R Server を使用する方法の詳細については、以下のリンクを参照してください。

- [HDInsight での R Server の使用に関する記事](hdinsight-hadoop-r-server-get-started.md)

- [HDInsight Premium への RStudio Server の追加に関する記事](hdinsight-hadoop-r-server-install-r-studio.md)

- [Compute context options for R Server on HDInsight Premium (HDInsight Premium での R Server のコンピューティング コンテキストのオプション)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure Storage options for R Server on HDInsight Premium (HDInsight Premium での R Server の Azure Storage オプション)](hdinsight-hadoop-r-server-storage.md)

 

<!---HONumber=AcomDC_0330_2016-->