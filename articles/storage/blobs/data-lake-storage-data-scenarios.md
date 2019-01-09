---
title: Azure Data Lake Storage Gen2 に関するデータ シナリオ | Microsoft Docs
description: Data Lake Storage Gen2 (旧称 Azure Data Lake Store) でのデータの取り込み、処理、ダウンロード、視覚化に使用するさまざまなシナリオとツールについて説明します
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.openlocfilehash: 978f86141d72cc7be43f24909f9780ab9570605d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976582"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Data Lake Storage Gen2 を使用してビッグ データの要件に対応する

ビッグ データの処理には主に 4 つの段階があります。

* データ ストアへの大量のデータの取り込み (リアルタイムまたは一括)
* データの処理
* データのダウンロード
* データの視覚化

この記事では、Azure Data Lake Storage Gen2 に関してこれらの段階について説明し、お客様のビッグ データのニーズを満たすために使用できるオプションとツールを確認します。

## <a name="ingest-data-into-data-lake-storage-gen2"></a>Data Lake Storage Gen2 にデータを取り込む
このセクションでは、さまざまなデータ ソースと、そのデータを Data Lake Storage Gen2 アカウントに取り込む各種方法について説明します。

![Data Lake Storage Gen2 にデータを取り込む](./media/data-lake-storage-data-scenarios/ingest-data.png "Data Lake Storage Gen2 にデータを取り込む")

### <a name="ad-hoc-data"></a>アドホック データ
これは、ビッグ データ アプリケーションのプロトタイプ作成に使用される小規模なデータ セットを表します。 アドホック データの取り込み方法は、データ ソースに応じてさまざまです。

| [データ ソース] | 取り込みに使用するツール |
| --- | --- |
| ローカル コンピューター |<ul> <li>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)</ul> |
| Azure Storage BLOB |<ul> <li>[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)</li> <li>[AzCopy ツール](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight クラスター上で実行されている DistCp](data-lake-storage-use-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>ストリーミングされたデータ
これは、アプリケーション、デバイス、センサーなどのさまざまなソースによって生成されるデータを表します。Data Lake Storage Gen2 へのこのデータの取り込みには各種ツールを使用できます。 これらのツールでは通常、イベントごとにリアルタイムでデータを取り込み、処理します。その後、さらにデータを処理できるように、Data Lake Storage Gen2 にイベントを一括で書き込みます。

使用できるツールは次のとおりです。

* [Azure HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md)。Storm クラスターから Data Lake Storage Gen2 に直接データを書き込むことができます。

### <a name="relational-data"></a>リレーショナル データ
リレーショナル データベースのデータもソースとして扱うことができます。 一定の期間を経て、リレーショナル データベースには大量のデータが収集されます。ビッグ データのパイプラインを介して処理すると、これらのデータから重要な知見が得られます。 このようなデータを Data Lake Storage Gen2 に移動する場合は、次のツールを使用できます。

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web サーバー ログ データ (カスタム アプリケーションを使用したアップロード)
Web サーバー ログ データの分析は、ビッグ データ アプリケーションの一般的な使用例で、大量のログ ファイルを Data Lake Storage Gen2 にアップロードすることを必要とするため、この種類のデータセットが特に呼び出されます。 このようなデータをアップロードするための独自のスクリプトまたはアプリケーションを記述するには、次のツールのいずれかを使用できます。

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

Web サーバー ログ データをアップロードする場合、または他の種類のデータ (ソーシャル センチメント データなど) をアップロードする場合には、独自のカスタム スクリプトやカスタム アプリケーションを記述することをお勧めします。 これにより、データをアップロードするコンポーネントをより大規模なビッグ データ アプリケーションの一部として含める柔軟性が得られるためです。 このコードは、スクリプトまたは単純なコマンド ライン ユーティリティの形をとる場合もあれば、ビッグ データの処理をビジネス アプリケーションまたはビジネス ソリューションに統合するために使用される場合もあります。

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターに関連付けられたデータ
ほとんどの種類の HDInsight クラスター (Hadoop、HBase、Storm) では、データ ストレージ リポジトリとして Data Lake Storage Gen2 がサポートされています。 HDInsight クラスターは Azure Storage Blob (WASB) のデータにアクセスします。 パフォーマンスを向上させるために、クラスターに関連付けられた Data Lake Storage Gen2 アカウントに WASB のデータをコピーできます。 データのコピーには次のツールを使用できます。

* [Apache DistCp](data-lake-storage-use-distcp.md)
* [AzCopy ツール](../common/storage-use-azcopy-v10.md)
* [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>オンプレミスまたは IaaS Hadoop クラスターに格納されているデータ
大量のデータが既存の Hadoop クラスターの HDFS を使用するコンピューターにローカルに格納されている場合があります。 Hadoop クラスターは、オンプレミスのデプロイ内にある場合も、Azure の IaaS クラスター内にある場合もあります。 このようなデータを 1 回限りまたは定期的に Azure Data Lake Storage Gen2 にコピーする要件が存在します。 これを実現するために、さまざまな方法を使用できます。 以下の表に、それぞれの方法とそのトレードオフを示します。

| アプローチ | 詳細 | 長所 | 考慮事項 |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) を使用して Hadoop クラスターから Azure Data Lake Storage Gen2 にデータを直接コピーする。 |[ADF ではデータ ソースとして HDFS をサポートしている。](../../data-factory/connector-hdfs.md) |ADF では、HDFS が最初からサポートされており、ファースト クラスのエンド ツー エンドの管理と監視が提供される。 |Data Management Gateway をオンプレミスまたは IaaS クラスターにデプロイする必要がある。 |
| Distcp を使用して、Hadoop から Azure Storage にデータをコピーする。 次に、適切なメカニズムを使用してデータを Azure Storage から Data Lake Storage Gen2 にコピーする。 |次のいずれかを使用してデータを Azure Storage から Data Lake Storage Gen2 にコピーできます。 <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy ツール](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight クラスター上で実行されている Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |オープン ソースのツールを使用できる。 |複数のテクノロジを含む複数ステップのプロセス。 |

### <a name="really-large-datasets"></a>非常に大規模なデータセット
数 TB に及ぶデータセットをアップロードする場合、上記の方法では速度が遅く、コストがかかることがあります。 このような場合は、次のオプションを使用できます。

* **Azure ExpressRoute の使用**。 Azure ExpressRoute を使用すると、Azure データ センターとお客様のオンプレミスのインフラストラクチャとの間でプライベート接続を作成できます。 これにより、大量のデータを転送するための信頼性の高いオプションが提供されます。 詳細については、 [Azure ExpressRoute のドキュメント](../../expressroute/expressroute-introduction.md)をご覧ください。

## <a name="process-data-stored-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2 に格納されているデータを処理する
Data Lake Storage Gen2 でデータが利用できるようになったら、サポートされているビッグ データ アプリケーションを使用して、そのデータの分析を実行できます。 現在、Data Lake Storage Gen2 に格納されたデータに対してデータ分析ジョブを実行する場合は、Azure HDInsight と Azure Databricks を使用できます。

![Data Lake Storage Gen2 のデータを分析する](./media/data-lake-storage-data-scenarios/analyze-data.png "Data Lake Storage Gen2 のデータを分析する")


## <a name="download-data-from-data-lake-storage-gen2"></a>Data Lake Storage Gen2 からデータをダウンロードする
次のようなシナリオでは、Azure Data Lake Storage Gen2 からデータをダウンロードしたり、移動したりすることもできます。

* 既存のデータ処理パイプラインとのインターフェイスとなる他のリポジトリにデータを移動する。 たとえば、Data Lake Storage Gen2 から Azure SQL Database またはオンプレミスの SQL Server にデータを移動できます。
* アプリケーション プロトタイプの作成中に IDE 環境で処理するために、ローカル コンピューターにデータをダウンロードする。

![Data Lake Storage Gen2 からデータを出力する](./media/data-lake-storage-data-scenarios/egress-data.png "Data Lake Storage Gen2 からデータを出力する")

このような場合、次のオプションのいずれかを使用できます。

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-storage-use-distcp.md)

## <a name="visualize-data-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2 のデータを視覚化する
複数のサービスを組み合わせて使用することで、Data Lake Storage Gen2 に格納されたデータを視覚的に表現することができます。

![Data Lake Storage Gen2 のデータを視覚化する](./media/data-lake-storage-data-scenarios/visualize-data.png "Data Lake Storage Gen2 のデータを視覚化する")

* まず、[Azure Data Factory を使って、Data Lake Storage Gen2 から Azure SQL Data Warehouse にデータを移動](../../data-factory/copy-activity-overview.md)することができます。
* その後、 [Power BI を Azure SQL Data Warehouse と統合](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) して、データを視覚的に表現することができます。
