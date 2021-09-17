---
title: Azure Synapse Analytics とは
description: Azure Synapse Analytics の概要
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 3d0987e5203288da6c6acd8bec6c478f5762b320
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823730"
---
# <a name="what-is-azure-synapse-analytics"></a>Azure Synapse Analytics とは

**Azure Synapse** は、データ ウェアハウスやビッグ データ システム全体にわたって分析情報を取得する時間を早めるエンタープライズ分析サービスです。 Azure Synapse は、エンタープライズ データ ウェアハウスで使用される **SQL** テクノロジ、ビッグ データに使用される **Spark** テクノロジ、データ統合と ETL および ELT のための **パイプライン**、**Power BI**、**CosmosDB**、**AzureML** などの他の Azure サービスとの緊密な統合の長所を組み合わせたものです。

![Azure Synapse Analytics のアーキテクチャの図。](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>業界最高水準の SQL

**Synapse SQL** は、T-SQL の分散クエリ システムです。データ ウェアハウジングやデータ仮想化のシナリオが可能となるほか、T-SQL を拡張してストリーミングや機械学習のシナリオにも対応することができます。

* Synapse SQL では、**サーバーレス** と **専用** の両方のリソース モデルが提供されます。 予測可能なパフォーマンスとコストに対しては、専用 SQL プールを作成して、SQL テーブルに格納されているデータの処理能力を確保します。 計画外または突発性のワークロードに対しては、常に使用可能なサーバーレスの SQL エンドポイントを使用します。
* 組み込みの **ストリーミング** 機能を使用して、クラウド データ ソースから SQL テーブルにデータを取り込みます。
* 定期的にインポート タスクを実行してその完了を待つ必要なく、データをマネージド テーブルに読み込んで最適なクエリ パフォーマンスを得たり、[Azure Data Lake Storage](sql/query-data-storage.md) や [Azure Cosmos DB](sql/query-cosmos-db-analytical-store.md) のデータを直接クエリしたりすることができます。
* **機械学習** モデルを使用し、[T-SQL PREDICT 関数](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)を使用してデータをスコア付けすることにより、AI を SQL と統合します。

## <a name="industry-standard-apache-spark"></a>業界標準の Apache Spark

**Apache Spark for Azure Synapse** は、Apache Spark と密にシームレスに統合されます。Apache Spark は、データ準備、データ エンジニアリング、ETL、および機械学習に使用される最も人気のあるオープン ソースのビッグ データ エンジンです。

* SparkML アルゴリズムを使用した ML モデルと、Linux Foundation Delta Lake の組み込みサポートを備える Apache Spark 2.4 用の AzureML 統合。
* クラスターの管理について心配する必要からユーザーを解放する、簡素化されたリソース モデル。
* Spark の迅速なスタートアップと積極的な自動スケーリング。
* Spark 向け .NET の組み込みサポートにより、C# の専門知識と既存の .NET コードを Spark アプリケーション内で再利用できます。

## <a name="working-with-your-data-lake"></a>データ レイクの操作

Azure Synapse は、SQL と Spark を組み合わせて使用する場合の従来のテクノロジーの障害を取り除きます。 ニーズと専門知識に基づいて、シームレスにいろいろな組み合わせを使用することができます。

* データ レイク内のファイルで定義されたテーブルを、Spark または Hive でシームレスに使用できます。
* SQL と Spark を使用して、データ レイクに保存されている Parquet、CSV、TSV、Delta Lake、JSON ファイルを直接探索して分析できます。
* SQL データベースと Spark データベース間の高速かつスケーラブルなデータ読み込み


## <a name="built-in-data-integration"></a>組み込みのデータ統合

Azure Synapse には Azure Data Factory と同じデータ統合エンジンとエクスペリエンスが含まれているため、Azure Synapse Analytics から離れることなく、多機能で大規模な ETL パイプラインを作成できます。

* 90 以上のデータ ソースからデータを取り込む
* データ フロー アクティビティを使用したコードフリーの ETL
* ノートブック、Spark ジョブ、ストアド プロシージャ、SQL スクリプトなどを調整する

## <a name="unified-experience"></a>統合エクスペリエンス 

**Synapse Studio** は、ソリューションの構築、保守、セキュリティ保護をすべて 1 つのユーザー エクスペリエンスで行う単一の手段を企業に提供します。

* 主要なタスク (取り込み、調査、準備、調整、視覚化) を実行する
* SQL と Spark 全体のリソース、使用状況、ユーザーを監視する
* ロールベースのアクセス制御を使用して分析リソースへのアクセスを単純化する
* SQL または Spark コードを記述し、エンタープライズ CI/CD プロセスと統合する

## <a name="engage-with-the-synapse-community"></a>Synapse コミュニティへの参加

- [Microsoft Q&A](/answers/topics/azure-synapse-analytics.html): 技術的なご質問はこちらへ。
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse):開発に関する質問はこちらへ

## <a name="next-steps"></a>次の手順

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)
