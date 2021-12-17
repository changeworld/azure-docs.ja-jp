---
title: 用語 - Azure Synapse Analytics
description: Azure Synapse Analytics をユーザーに紹介するリファレンス ガイド
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/02/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d525d3657a48e01240721e4c868fa481db58dc0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074707"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics の用語

このドキュメントでは、Azure Synapse Analytics の基本的な概念を紹介します。

## <a name="basics"></a>基本

**Synapse ワークスペース** は、Azure でクラウドベースのエンタープライズ分析を行うための、セキュリティ保護可能なコラボレーション境界です。 ワークスペースは、特定のリージョンにデプロイされ、関連付けられた ADLS Gen2 アカウントと (一時データを格納するための) ファイル システムを持ちます。 ワークスペースは、リソース グループに属します。

ワークスペースを使用すると、SQL および Apache Spark で分析できます。 SQL および Spark の分析に利用できるリソースは SQL および Spark **プール** にまとめられます。

## <a name="linked-services"></a>リンクされたサービス

ワークスペースには、任意の数の **リンクされたサービス** を含めることができます。これは、基本的には、ワークスペースが外部リソースに接続するために必要な接続情報を定義する接続文字列です。

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** は、Synapse ワークスペースで T-SQL に基づいて分析するための機能です。 Synapse SQL には、専用とサーバーレスという 2 つの消費モデルがあります。  専用モデルの場合、**専用の SQL プール** を使用します。 ワークスペースには、このようなプールをいくらでも与えることができます。 サーバーレスモデルを使用するには、**サーバーレス SQL プール** を使用します。 ワークスペースごとにこのようなプールが 1 つ与えられます。

Synapse Studio 内で、**SQL スクリプト** を実行することによって、SQL プールを操作できます。

## <a name="apache-spark-for-synapse"></a>Synapse 用の Apache Spark

Spark 分析を使用するには、Synapse ワークスペースで **サーバーレス Apache Spark プール** を作成し、使用します。 Spark プールの使用を開始すると、ワークスペースによって **Spark セッション** が作成され、そのセッションに関連付けられているリソースが処理されます。

Synapse では、次の 2 とおりの方法で Spark を使用できます。

* **Spark Notebooks**。データ サイエンスやエンジニアリングを行う用途で、Scala、PySpark、C#、SparkSQL が使用されます。
* **Spark ジョブ定義**。jar ファイルを使用したバッチ Spark ジョブの実行に使用されます。

## <a name="pipelines"></a>パイプライン

Azure Synapse では、パイプラインを使用してデータ統合を提供します。これにより、サービス間でデータを移動したり、アクティビティを調整したりすることができます。

* **パイプライン** は、1 つのタスクを連携して実行するアクティビティの論理的なグループです。
* **アクティビティ** は、データのコピー、ノートブックや SQL スクリプトの実行など、データに対して実行する、パイプライン内のアクションを定義します。
* **データ フロー** は、内部的に Synapse Spark を使用するデータ変換を実行するための、ノーコードのエクスペリエンスを提供する、特定の種類のアクティビティです。
* **トリガー** - パイプラインを実行します。 手動または自動で実行できます (スケジュール、タンブリング ウィンドウ、またはイベントベース)
* **統合データセット** - アクティビティで入力および出力として使用されるデータを単にポイントまたは参照する、データの名前付きビュー。 リンクされたサービスに属します。

## <a name="data-explorer-preview"></a>Data Explorer (プレビュー)

Azure Synapse Data Explorer は、ログとテレメトリ データから分析情報を引き出すための対話型クエリ エクスペリエンスをお客様に提供します。

* **Data Explorer プール** は、クエリのパフォーマンスを最適化するためのローカル SSD ストレージ (ホット キャッシュ) と、永続化のための複数の BLOB ストレージ (コールド キャッシュ) を備えた 2 つ以上のコンピューティング ノードを含む専用クラスターです。
* **Data Explorer データベース** は、Data Explorer プール上でホストされ、テーブルなどのデータベース オブジェクトのコレクションで構成される論理エンティティです。 プールごとに複数のデータベースを使用できます。
* **テーブル** は、従来のリレーショナル データ モデルを使用して整理されたデータを格納するデータベース オブジェクトです。 データは、Data Explorer の明確に定義されたテーブル スキーマに準拠したレコードに格納されます。スキーマでは、列の順序指定済みリストが定義されています。各列には、名前とスカラー データ型が含まれます。 スカラー データ型は、構造化 (*int*、*real*、*datetime*、または *timespan*)、半構造化 (*dynamic*)、フリー テキスト (*string*) のいずれかになります。 dynamic 型は、単一のスカラー値、配列、またはそのような値の辞書を保持できるという点で、JSON に似ています。
* **外部テーブル** は、Data Explorer データベースの外部にあるストレージまたは SQL データ ソースを参照するテーブルです。 外部テーブルには、テーブルと同じように、明確に定義されたスキーマ (列名とデータ型のペアの順序指定済みリスト) があります。 Data Explorer プールにデータが取り込まれる Data Explorer テーブルとは異なり、外部テーブルは、プールの外部に格納され、管理されているデータに対して動作します。 外部テーブルにはデータは保持されず、データのクエリや、外部データ ストアへのエクスポートに使用されます。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)
* [Synapse Studio を使用して Data Explorer プールを作成する](data-explorer/data-explorer-create-pool-studio.md)
