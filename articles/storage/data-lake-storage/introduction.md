---
title: Azure Data Lake Storage Gen2 プレビューの概要
description: Azure Data Lake Storage Gen2 プレビューの概要について説明します
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 51f38cf7ade01b58ad5ce7925af5546d1a4f1a0c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525384"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Azure Data Lake Storage Gen2 プレビューの概要

Azure Data Lake Storage Gen2 プレビューは、[Azure Blob ストレージ](../blobs/storage-blobs-introduction.md)の上位にビルドされる、ビッグ データ分析専用の機能セットです。 ファイル システムとオブジェクト ストレージの両方のパラダイムを使用して、データと連携させることができます。 これにより、Data Lake Storage Gen2 は、唯一のクラウドベースのマルチモーダル ストレージ サービスとなり、すべてのデータから分析値を抽出することができます。

Data Lake Storage Gen2 は、全サイクルの分析データに必要なすべての特性を備えています。 これは、2 つの既存のストレージ サービスの機能に由来します。 ファイル システム セマンティクス、ファイル レベルのセキュリティおよびスケールなど、[Azure Data Lake Storage Gen1](../../data-lake-store/index.md) に由来する機能は、[Azure Blob ストレージ](../blobs/storage-blobs-introduction.md)の低コストの階層型記憶域、高可用性/ディザスター リカバリー機能、および SDK/ツーツ エコシステムと組み合わされています。 Data Lake Storage Gen2 では、すべてのオブジェクト ストレージの品質を備えたまま、分析ワークロード用に最適化されたファイル システム インターフェイスの利点が追加されています。

## <a name="designed-for-enterprise-big-data-analytics"></a>エンタープライズ ビッグ データ分析用に設計されている

Data Lake Storage Gen2 は、Azure 上に Enterprise Data Lake (EDL) をビルドするための基本的なストレージ サービスです。 当初から、何百ものギガビット単位のスループットを維持しつつ、複数のペタバイト単位の情報を利用可能にする目的で設計されています。Data Lake Storage Gen2 は、大量のデータを簡単に管理する方法を提供します。

Data Lake Storage Gen2 の基本的な機能は、パフォーマンスの高いデータ アクセスのために、オブジェクト/ファイルをディレクトリの階層に編成した Blob ストレージ サービスに、[階層構造の名前空間](./namespace.md)を追加することです。 また、階層構造の名前空間では、Data Lake Storage Gen2 を使ってオブジェクト ストアとファイル システムの両方のパラダイムをサポートできます。 たとえば、共通のオブジェクト ストアの名前規則では、名前の中でスラッシュを使用して、階層フォルダー構造を再現します。 この構造は、Data Lake Storage Gen2 を使って、実際のものになります。 ディレクトリの名前変更や削除などの操作は、ディレクトリ名のプレフィックスを共有するすべてのオブジェクトを列挙して処理するのではなく、ディレクトリ上の単一のアトミック メタデータの操作になります。

以前は、パフォーマンス、管理、およびセキュリティの領域では、クラウドベース分析は妥協する必要がありました。 Data Lake Storage Gen2 では、次の方法で各側面に対応しています。

- **パフォーマンス**。分析の前提条件としてデータをコピーまたは変換する必要がないため、最適化されます。 階層構造の名前空間は、ジョブ パフォーマンス全体を向上させるディレクトリ管理操作のパフォーマンスを大幅に向上させます。

- **管理**。ディレクトリおよびサブディレクトリを利用してファイルを編成および操作できるため、簡単になりました。

- **コスト効率**。Data Lake Storage Gen2 が低コストの [Azure Blob ストレージ](../blobs/storage-blobs-introduction.md)の上位にビルドされていることで、実現されました。 さらに、追加の機能により、Azure 上でビッグ データ分析を実行するための総保有コストが低下しました。

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 の主な機能

> [!NOTE]
> Data Lake Storage Gen2 のパブリック プレビューの間は、以下に示された一部の機能の可用性が変更される可能性があります。 プレビュー プログラム中に新しい機能やリージョンがリリースされるため、この情報は公開される予定です。
> Data Lake Storage Gen2 のパブリック プレビューに[サインアップ](https://aka.ms/adlsgen2signup)します。  

- **Hadoop と互換性のあるアクセス**: Data Lake Storage Gen2 では、[Hadoop 分散ファイル システム (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) を利用する場合と同様に、データの管理およびアクセスを可能にします。 新しい [ABFS ドライバー](./abfs-driver.md)は、Data Lake Storage Gen2 に格納されたデータにアクセスするための [Azure HDInsight](../../hdinsight/index.yml) および [Azure Databricks](../../azure-databricks/index.yml) など、すべての Apache Hadoop 環境内で利用できます。

- **マルチプロトコルおよびマルチモーダル データ アクセス**: Data Lake Storage Gen2 は、オブジェクト ストア インターフェイスとファイル システム インターフェイスの両方を同じデータに対して**同時に**提供するので、**マルチモーダル** ストレージ サービスと見なされます。 これは、同じデータにアクセスできる複数のプロトコル エンドポイントを提供することで、実現されます。 

    他の分析ソリューションとは違って、Data Lake Storage Gen2 に格納されているデータは、さまざまな分析ツールを実行する前に、移行や変換を行う必要はありません。 従来の [Blob ストレージ API](../blobs/storage-blobs-introduction.md) 経由でデータにアクセスして (たとえば、[Event Hubs Capture](../../event-hubs/event-hubs-capture-enable-through-portal.md) 経由のデータの取り込み)、HDInsight または Azure Databricks を使用して同時にそのデータを処理できます。 

- **コスト効率**: Data Lake Storage Gen2 は、低コストのストレージ容量とトランザクションを備えています。 ライフサイクル全体を通したデータ遷移として、課金レートは、[Azure Blob ストレージ ライフサイクル](../common/storage-lifecycle-managment-concepts.md)などの組み込みの機能を介して、最小限になるまで継続コストを変更します。

- **Blob ストレージ ツール、フレームワーク、およびアプリの操作**: Data Lake Storage Gen2 では、Blob ストレージ用に今日存在するさまざまなツール、フレームワーク、およびアプリケーションを継続的に利用できます。

- **最適化されたドライバー**: `abfs` ドライバーは、ビッグ データ分析のために[特別に最適化](./abfs-driver.md)されています。 該当する REST API は、`dfs` エンドポイント、`dfs.core.windows.net` を介して表示されます。

## <a name="scalability"></a>スケーラビリティ

Azure Storage では、Data Lake Storage Gen2 または Blob ストレージ インターフェイスのどちらを経由してアクセスするか、設計ごとにスケーラブルになっています。 また、*多数のエクサバイト データ*を格納および提供できます。 このストレージ容量は、秒単位での高レベルの入出力処理 (IOPS).で、ギガビット/秒 (Gbps) で計測されるスループットによって利用可能になります。 単なる永続性ではなく、サービス、アカウント、およびファイル レベルで計測される定数に近いリクエストごとの待機時間で、処理が実行されます。

## <a name="cost-effectiveness"></a>コスト効率

Azure Blob ストレージの上位に Data Lake Storage Gen2 をビルドする多くのメリットの 1 つが、[低コストの](https://azure.microsoft.com/pricing/details/storage)ストレージ容量とトランザクションです。 他のクラウド ストレージ サービスとは違って、Data Lake Storage Gen2 では、分析を実行する前にデータの移行または変換を行う必要ないため、コストが削減されます。

さらに、[階層構造の名前空間](./namespace.md)などの機能は、多数の分析ジョブのパフォーマンス全体を大幅に向上させます。 このパフォーマンスの向上は、同じデータ量を処理するうえで必要とするコンピューティング能力をより低減できることを意味し、結果として、エンド ツー エンドの分析ジョブの総保有コスト (TCO) を削減できます。

## <a name="next-steps"></a>次の手順

以下の記事では、Data Lake Storage Gen2 の主要な概念の一部と、データの分析情報を保管、アクセス、管理、および取得する方法の詳細を示しています。

* [階層構造の名前空間](./namespace.md)
* [ストレージ アカウントの作成](./quickstart-create-account.md)
* [Create an HDInsight cluster with Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md) (Azure Data Lake Storage Gen2 で HDInsight クラスターを作成する)
* [Azure Databricks での Azure Data Lake Storage Gen2 アカウントの使用](./quickstart-create-databricks-account.md) 
