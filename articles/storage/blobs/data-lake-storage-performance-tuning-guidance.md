---
title: パフォーマンス用に Azure Data Lake Storage Gen2 を最適化する | Microsoft Docs
description: パフォーマンス用に Azure Data Lake Storage Gen2 を最適化する方法を理解します。 データの取り込み、データセットの構造などについて説明します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3cb875f14fe3d9b18b5249a9d2cd9e1901000610
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609310"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>パフォーマンス用に Azure Data Lake Storage Gen2 を最適化する

Azure Data Lake Storage Gen2 は、I/O 集中型分析とデータ移動での高スループットをサポートします。 この記事は、スループットと効率的なデータ アクセスの最適化に役立ちます。

> [!NOTE]
> 分析パイプラインの全体的なパフォーマンスは、分析エンジンに固有の要因にも依存します。 ワークロードのパフォーマンスの最適化に関する最適で最新のガイダンスについては、使用する予定の各システムのドキュメントを参照してください。

## <a name="optimize-data-ingestion"></a>データ インジェストを最適化する

ソース システムからデータを取り込むときに、ソース ハードウェア、ソース ネットワーク ハードウェア、またはストレージ アカウントへのネットワーク接続がボトルネックになることがあります。

![ソース システムから Data Lake Storage Gen2 にデータを取り込むときに考慮すべき要素を示す図。](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

### <a name="source-hardware"></a>ソース ハードウェア

オンプレミスのマシンを使用するか、Azure で仮想マシン (VM) を使用しているかにかかわらず、適切なハードウェアを慎重に選択してください。 ディスク ハードウェアの場合は、ソリッド ステート ドライブ (SSD) の使用を検討し、より高速なスピンドルを搭載したディスク ハードウェアを選択してください。 ネットワーク ハードウェアの場合は、できるだけ最速のネットワーク インターフェイス コントローラー (NIC) を使用します。 Azure に関しては、適度に強力なディスクとネットワーク ハードウェアを備えた Azure D14 VM をお勧めします。

### <a name="network-connectivity-to-the-storage-account"></a>ストレージ アカウントへのネットワーク接続

ソース データとストレージ アカウントとの間のネットワーク接続がボトルネックになることがあります。 ソース データがオンプレミスの場合、[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) で専用のリンクを使用することを検討してください。 ソース データが Azure にある場合、データが Data Lake Storage Gen2 対応アカウントと同じ Azure リージョンにあると、パフォーマンスは最適となります。

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>データ インジェスト ツールの最大並列化処理の構成

最高のパフォーマンスを得るには、できるだけ多くの読み取りと書き込みを並列に実行して、使用可能なすべてのスループットを使用します。

![Data Lake Storage Gen2 のパフォーマンス](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

次の表に、一般的なインジェスト ツールの主要な設定の概要を示します。

| ツール               | 設定 |
|--------------------|------------------------------------------------------|
| [DistCp](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)             | -m (マッパー) |
| [Azure Data Factory](../../data-factory/copy-activity-performance.md) | parallelCopies    |
| [Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)          | fs.azure.block.size、-m (マッパー)   |

アカウントは、あらゆる分析シナリオで必要とされるスループットを提供するようにスケーリングできます。 既定では、Data Lake Storage Gen2 対応アカウントは、広範なカテゴリのユース ケースのニーズを満たすのに十分なスループットを既定の構成で提供します。 既定の制限に達した場合、[Azure サポート](https://azure.microsoft.com/support/faq/)に連絡して、さらに高いスループットを提供するようにアカウントを構成することができます。

## <a name="structure-your-data-set"></a>データ セットの構成

データが Data Lake Storage Gen2 対応ストレージ アカウントに保存されると、ファイル サイズ、ファイル数、およびフォルダー構造がパフォーマンスに影響を与えるようになります。  次のセクションでは、これらの領域に関するベスト プラクティスについて説明します。

### <a name="file-size"></a>ファイル サイズ

通常、HDInsight などの分析エンジンには、一覧表示、アクセスの確認、さまざまなメタデータ操作の実行などのタスクを含むファイルごとのオーバーヘッドがあります。 データを多数の小さなファイルとして保存すると、パフォーマンスに悪影響を及ぼすことがあります。 一般に、パフォーマンスを向上させるには、データをより大きなサイズ (256 MB から 100 GB) のファイルにまとめます。 エンジンとアプリケーションの中には、サイズが 100 GB を超えるファイルを効率的に処理できないものもあります。

場合によっては、データ パイプラインで小さなファイルを多数含む生データの制御が制限されることがあります。 一般に、ダウンストリーム アプリケーションで使用できるように小さいファイルを大きなファイルに集約する何らかのプロセスを、お使いのシステムに用意することをお勧めします。 リアルタイムでデータを処理している場合、リアル タイム ストリーミング エンジン ([Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) や [Spark Streaming](https://databricks.com/glossary/what-is-spark-streaming) など) を、メッセージ ブローカー ([イベント ハブ](../../event-hubs/event-hubs-about.md)や [Apache Kafka](https://kafka.apache.org/) など) と一緒に使用して、より大きなファイルとしてデータを保存できます。

小さいファイルを大きなファイルにまとめるときは、ダウンストリーム処理のために [Apache Parquet](https://parquet.apache.org/) などの読み取り最適化形式で保存することを検討してください。 Apache Parquet は、読み取り負荷の高い分析パイプラインに最適化されたオープン ソース ファイル形式です。 Parquet の列指向ストレージ構造を使用すると、関連のないデータをスキップできます。 ストレージから分析エンジンに送信するデータの範囲を絞ることができるので、クエリの方がはるかに効率的です。 また、類似のデータ型 (列の場合) が一緒に格納されるため、Parquet ではデータ ストレージ コストを削減できる効率的なデータ圧縮とエンコード スキームをサポートしています。 [Azure Synapse Analytics](../../synapse-analytics/overview-what-is.md)、[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)、[Azure Data Factory](../../data-factory/introduction.md) などのサービスには、Parquet ファイル形式を利用するネイティブ機能があります。

データ形式の詳細については、[ベスト プラクティスの記事のデータ形式に関するセクション](data-lake-storage-best-practices.md)を参照してください。

### <a name="organizing-time-series-data-in-folders"></a>フォルダー内の時系列データの整理

Hive ワークロードでは、時系列データのパーティションを削除すると、一部のクエリがデータのサブセットのみを読み取るようにできるため、パフォーマンスを向上させることができます。

時系列データを取り込むこれらのパイプラインでは、多くの場合、ファイルにかなり構造化されたファイル名やフォルダー名が付けられます。 以下に、日付によって構成されたデータの一般的な例を示します。

*\DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv*

フォルダーとファイル名の両方に、日時の情報が示されていることに注意してください。

日付と時刻については、以下が一般的なパターンです。

*\DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv*

繰り返しになりますが、フォルダーとファイルの整理については、より大きなファイルサイズに最適化され、各フォルダーに妥当な数のファイルが配置されるような選択を行ってください。

その他のディレクトリ レイアウト構造の推奨事項については、「[ディレクトリの構造](data-lake-storage-best-practices.md#directory-layout-considerations)」を参照してください

### <a name="access-data-efficiently-with-query-acceleration"></a>クエリ アクセラレーションを使用して効率的にデータにアクセスする

クエリ アクセラレーションでは、特定の操作の実行に必要なデータのみを取得することで、アプリケーションと分析フレームワークによって、データ処理を劇的に最適化することができます。 これにより、格納されているデータに対する重要な分析情報を得るために必要な時間と処理能力が削減されます。

クエリ アクセラレーションでは、データをディスクから読み取るときに、アプリケーションが行および列をフィルター処理できるようにするフィルターの "述語" および "列のプロジェクション" を受け入れます。 述語の条件を満たすデータのみが、ネットワーク経由でアプリケーションに転送されます。 これにより、ネットワーク待機時間とコンピューティング コストが削減されます。

詳細については、「[Azure Data Lake Storage のクエリ アクセラレーション](data-lake-storage-query-acceleration.md)」を参照してください

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>HDInsight の Hadoop および Spark ワークロードでの I/O 集中型ジョブの最適化

I/O 集中型ジョブは、大半の時間を I/O 処理に費やします。  読み取りおよび書き込み操作のみを行うコピー ジョブなどが一般的な例です。  その他の例には、大量のデータを読み取ってデータ変換を行った後、保存場所に書き戻すデータ準備ジョブなどがあります。  1 回の操作で 100 MB 程度を読み書きするジョブを持つことができますが、そのサイズのバッファーはパフォーマンスを低下させる可能性があります。 パフォーマンスを最適化するには、I/O 操作のサイズを 4 MB から 16 MB までに維持するようにしてください。

### <a name="io-intensive-jobs-with-hdinsight-clusters"></a>HDInsight クラスターでの I/O 集中型ジョブ

- **HDInsight のバージョン** 最適なパフォーマンスを得るには、HDInsight の最新リリースを使用してください。
- **リージョン** Data Lake Storage Gen2 アカウントは HDInsight クラスターと同じリージョンに配置します。

HDInsight クラスターは、2 つのヘッド ノードと複数のワーカー ノードで構成されます。 各ワーカー ノードは、特定数のコアとメモリを提供します。これは VM の種類によって決まります。  ジョブの実行時には、YARN がリソース ネゴシエーターとなり、コンテナー作成で使用可能なメモリとコアを割り当てます。  各コンテナーは、ジョブを完了するために必要なタスクを実行します。  コンテナーは、タスクを迅速に処理するために並列で実行されます。 そのため、できるだけ多くの並列コンテナーを実行することでパフォーマンスが向上します。

HDInsight クラスター内には 3 つのレイヤーがあります。これらは、コンテナー数を増やし、使用可能なすべてのスループットを使用するようにチューニングできます。

- **物理レイヤー**
- **YARN レイヤー**
- **ワークロード レイヤー**

### <a name="physical-layer"></a>物理レイヤー

**より多くのノードと大きなサイズの VM、またはそのどちらかで、クラスターを実行します。**  次の図に示すように、クラスターを大きくすると、より多くの YARN コンテナーを実行できます。

![クラスターを大きくすると、より多くの YARN コンテナーを実行できることを示す図。](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**より大きなネットワーク帯域幅を備えた VM を使用します。**  ネットワーク帯域幅が Data Lake Storage Gen2 のスループットよりも小さい場合、ネットワーク帯域幅の容量がボトルネックとなる可能性があります。  VM によって、ネットワーク帯域幅のサイズは異なります。  できる限り大きなネットワーク帯域幅を備えた VM タイプを選択してください。

### <a name="yarn-layer"></a>YARN レイヤー

**より小さい YARN コンテナーを使用します。**  各 YARN コンテナーのサイズを小さくして、同じ容量のリソースを備えたコンテナーの数を増やします。

![各 YARN コンテナーのサイズを小さくしてコンテナーの数を増やした場合の結果を示す図。](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

ワークロードによっては、YARN コンテナーが常に必要とする最小サイズが存在します。 選択したコンテナーが小さすぎる場合、ジョブでメモリ不足の問題が発生します。 通常、YARN コンテナーは 1 GB 以上にする必要があります。 3 GB の YARN コンテナーを検討するのが一般的です。 一部のワークロードでは、それよりも大きい YARN コンテナーが必要な場合もあります。

**YARN コンテナーあたりのコア数を増やします。**  各コンテナーで実行される並列タスクの数を増やすには、各コンテナーに割り当てるコアの数を増やします。  これは、コンテナーごとに複数のタスクを実行する Spark のようなアプリケーションで有効です。  各コンテナーで単一スレッドを実行する Hive のようなアプリケーションでは、コンテナーごとのコア数を増やすよりもコンテナー数を増やすことをお勧めします。

### <a name="workload-layer"></a>ワークロード レイヤー

**使用可能なすべてのコンテナーを使用します。**  すべてのリソースが使用されるように、タスク数を使用可能なコンテナー数と同じ数、またはそれ以上に設定します。

![すべてのコンテナーの使用を示す図。](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**タスクでエラーが発生するとコストがかかります。** 各タスクで大量のデータを処理すると、タスクでエラーが発生した場合、再試行に高いコストがかかることになります。  そのため、多数のタスクを作成し、各タスクで少量のデータを処理するようにすることをお勧めします。

上記の一般的なガイドラインに加えて、それぞれの分析システムまたはフレームワークには、パフォーマンスが最適になるように調整できるさまざまなパラメーターがあります。 ワークロードのパフォーマンスの最適化に関する最適で最新のガイダンスについては、使用する予定の各システムのドキュメントを参照してください。

## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage Gen2 の使用に関するベスト プラクティス](data-lake-storage-best-practices.md)
- [Azure Data Lake Storage Gen2 の概要](data-lake-storage-introduction.md)
