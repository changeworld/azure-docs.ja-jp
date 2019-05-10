---
title: Azure Cosmos DB Spark API の概要
description: Azure Cosmos DB Spark API を使用し、運用分析と AI を実行する方法を学習する
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/06/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: de920f40f2968942b7ac66414170b43bd9317cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080020"
---
# <a name="introduction-to-the-azure-cosmos-db-spark-api-preview"></a>Azure Cosmos DB Spark API の概要 (プレビュー) 

Azure Cosmos DB の Spark API を使用すると、Azure Cosmos アカウントに保存されているデータに対して Apache Spark から分析を実行できます。

Azure Cosmos DB の Spark API からは、世界中に分散されている Cosmos データベースで Apache Spark ジョブを直接実行するためのネイティブ サポートが提供されます。 このような機能があることで、開発者、データ エンジニア、データ サイエンティストは **OLTP と OLAP/HTAP** という両方のワークロードを実行する柔軟性、拡張性、パフォーマンスに優れたデータ プラットフォームとして Azure Cosmos DB を使用できます。 

> [!NOTE]
> Azure Cosmos DB Spark API は現在、期間限定でプレビューされています。 プレビューに申し込むには、[プレビューのためのサインアップ](https://aka.ms/cosmos-spark-preview) ページに移動してください。 

Azure Cosmos DB の Spark API の利点:

* 地理的に分散されているユーザーやデータに関する分析情報を最短で得られます。

* ソリューションのアーキテクチャをシンプルにして[総保有コスト](total-cost-ownership.md) (TCO) を下げることができます。 システムに与えられるデータ処理コンポーネントの数は最小限であり、その間で不要なデータ移動が回避されます。

* [セキュリティ](secure-access-to-data.md)、[コンプライアンス](compliance.md)、すべてのデータを管理下に置く監査境界が作成されます。

* 厳格な SLA に支援される "常時オン" あるいは[可用性の高い](high-availability.md)エンドユーザー分析が提供されます。

![Azure Cosmos DB Spark API の視覚化](./media/spark-api-introduction/spark-api-visualization.png)
 
Azure Cosmos DB Spark API を使用することで、AI とディープ ラーニングのモデル、予測分析、おすすめ候補、IoT、カスタマー 360、詐欺検出、テキスト センチメント、クリックストリーム分析などのソリューションを構築し、配置できます。 これらは Azure Cosmos DB データに対して直接動作します。

データベース サービスの外側に出たり、コンピューティング サービスを追加したりする必要なく、Azure Cosmos DB でバッチおよびストリーミング ETL ジョブを設定できます。 ETL ジョブを実行し、ジョブの完了時、規模を戻す必要があるとき、コンピューティング環境の規模をエラスティックに変更できます。

Azure Cosmos DB の Spark API は、Apache Spark ランタイムの組み込み機械学習をサポートしています。 このランタイムには、Spark MLLib、Microsoft Machine Learning for Spark、Azure Machine Learning、Cognitive Services が含まれています。 このような機能により、データ サイエンティスト、データ エンジニア、データ アナリストは、短い時間と低いコストで、Azure Cosmos DB 内に直接、機械学習モデルを構築し、運用できます。


## <a name="key-benefits"></a>主な利点

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>世界中に分散され、待ち時間の短い運用分析と AI

世界中に分散されている Azure Cosmos データベースの Apache Spark を利用することで、世界中の分析情報を短時間で得ることができるようになりました。 Azure Cosmos DB は、3 つの主要な手法により、**世界中に分散された待ち時間の短い運用分析**をエラスティックなスケールで可能にします。

* Azure Cosmos データベースは世界中に分散しているため、データはすべてローカルで、すなわち、データのプロデューサー (ユーザーなど) がいる場所で取り込まれます。 クエリはデータのプロデューサーとコンシューマーの所在地に関係なく、両方にとって最も近いローカル レプリカに対して実行されます。 

* 分析クエリはすべて、データ パーティション内に保管されているインデックス付きデータで直接実行されています。不要なデータ移動は必要ありません。 

* Spark は Azure Cosmos DB と併設されるため、中間的な変換とデータ移動が少なくなります。その結果、パフォーマンスと拡張性が向上します。

### <a name="unified-serverless-experience-for-apache-spark"></a>Apache Spark の統一サーバーレス体験

マルチモデル データベースの Azure Cosmos DB では、OSS API 向けのサポートが拡張されました。**Apache Spark の統一サーバーレス体験**にキー/値、ドキュメント、グラフ、列ファミリー データ モデルが与えられるようになりました。 MongoDB、Cassandra、Gremlin、Etcd、SQL API を利用することで、さまざまなデータ モデルがサポートされます。すべて同じ基礎データ上で動作します。 

Spark API によって、Scala、Python、Java で記述されたアプリケーションがネイティブ サポートされ、SQL 向けに緊密に統合されたいくつかのライブラリが使用されます。 そのようなライブラリには、([Spark SQL](https://spark.apache.org/sql/))、機械学習 (Spark [MLlib](https://spark.apache.org/mllib/))、ストリーム処理 ([Spark Structured Streaming](https://spark.apache.org/streaming/))、グラフ処理 (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)) があります。 これらのツールにより、さまざまなユース ケースのために Spark API を簡単に活用できます。 Spark や Spark クラスターを扱う必要はありません。 同じ基礎データ上で同時に、使い慣れた Apache Spark API と **Jupyter ノートブック**を分析と SQL API に、Cassandra など、OSS NoSQL API をトランザクション処理に使用できます。

### <a name="no-schema-or-index-management"></a>スキーマやインデックスの管理が不要

従来の分析データベースとは異なり、Azure Cosmos DB の場合、データ エンジニアやデータ サイエンティストは面倒なスキーマやインデックス管理を扱う必要がなくなります。 Azure Cosmos DB のデータベース エンジンは、明示的なスキーマやインデックス管理を必要としません。また、Apache Spark のクエリにすばやくサービスを提供するために取り込むすべてのデータに自動的にインデックスを付けることができます。 

### <a name="consistency-choices"></a>整合性の選択肢

Apache Spark ジョブは Azure Cosmos データベースのデータ パーティションで実行されるため、クエリには [5 つの明確に定義された整合性選択肢](consistency-levels.md)が与えられます。 このような整合性モデルには柔軟性があり、厳密な整合性を選択すれば、待ち時間と高可用性で妥協することなく、機械学習アルゴリズムで最も精確な結果を提供できます。 

### <a name="slas"></a>SLA

Apache Spark ジョブには、業界をリードする包括的な [SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99.999) など、Azure Cosmos DB の長所が与えられ、個々の Apache Spark クラスターを管理するというオーバーヘッドがありません。 このような SLA には、スループット、99 パーセンタイルの待ち時間、整合性、高可用性が含まれます。 

### <a name="mixed-workloads"></a>ワークロードの混在

Apache Spark を Azure Cosmos DB に統合することで、分離していたトランザクションと分析がつながります。この分離は、クラウドネイティブのアプリケーションを世界規模で構築するとき、顧客にとって大きな難点の 1 つでした。 

## <a name="next-steps"></a>次の手順

* Azure Cosmos DB の長所については、[こちら](introduction.md)の概要の記事をご覧ください。
* [Azure Cosmos DB の MongoDB 用 API の概要](mongodb-introduction.md)
* [Azure Cosmos DB Cassandra API を使ってみる](cassandra-introduction.md)
* [Azure Cosmos DB Gremlin API を使ってみる](graph-introduction.md)
* [Azure Cosmos DB Table API を使ってみる](table-introduction.md)




