---
title: 用語 - Azure Synapse Analytics (ワークスペース プレビュー)
description: Azure Synapse Analytics をユーザーに紹介するリファレンス ガイド
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c58ee46a608ccdcbb01a082ee278d9e0f8a07f6e
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030681"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics の用語

[!INCLUDE [preview](includes/note-preview.md)]

このドキュメントでは、Azure Synapse Analytics の基本的な概念を紹介します。

## <a name="basics"></a>基本

**Synapse ワークスペース** は、Azure でクラウドベースのエンタープライズ分析を行うための、セキュリティ保護可能なコラボレーション境界です。 ワークスペースは、特定のリージョンにデプロイされ、関連付けられた ADLS Gen2 アカウントと (一時データを格納するための) ファイル システムを持ちます。 ワークスペースは、リソース グループに属します。

ワークスペースを使用すると、SQL および Apache Spark で分析できます。 SQL および Spark の分析に利用できるリソースは SQL および Spark **プール** にまとめられます。 

## <a name="linked-services"></a>リンクされたサービス

ワークスペースには、任意の数の **リンクされたサービス** を含めることができます。これは、基本的には、ワークスペースが外部リソースに接続するために必要な接続情報を定義する接続文字列です。

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** は、Synapse ワークスペースで T-SQL に基づいて分析するための機能です。 Synapse SQL には、専用とサーバーレスという 2 つの消費モデルがあります。  専用モデルの場合、**専用の SQL プール** を使用します。 ワークスペースには、このようなプールをいくらでも与えることができます。 サーバーレスモデルを使用するには、**サーバーレス SQL プール** を使用します。 ワークスペースごとにこのようなプールが 1 つ与えられます。

* **SQL 要求** - 専用 SQL プールまたはサーバーレス SQL プールを介して実行されるクエリなどの操作。
* **SQL スクリプト** - ファイルに保存された SQL コマンドのセット。 1 つの SQL スクリプトに 1 つまたは複数の SQL ステートメントを含めることができます。 専用 SQL プールまたはサーバーレス SQL プールを介して SQL 要求を実行するために使用できます。

## <a name="apache-spark-for-synapse"></a>Synapse 用の Apache Spark

Spark 分析を使用するには、Synapse ワークスペースで **サーバーレス Apache Spark プール** を作成し、使用します。 Spark プールの使用を開始すると、**Spark セッション** がワークスペースによって作成され、そのセッションに関連付けられているリソースが処理されます。 

Synapse では、次の 2 とおりの方法で Spark を使用できます。
* **Spark Notebooks**。データ サイエンスやエンジニアリングを行う用途で、Scala、PySpark、C#、SparkSQL が使用されます。
* **Spark ジョブ定義**。jar ファイルを使用したバッチ Spark ジョブの実行に使用されます。

バージョンのサポート:
* Spark 2.4
* Python 3.6.1
* Scala 2.11.12
* .NET for Apache Spark 1.0
* Delta Lake 0.3  

## <a name="pipelines"></a>パイプライン

* **データ統合** - さまざまなソース間でデータを取り込み、ワークスペース内またはワークスペース外で実行されるアクティビティを調整する機能を提供します。
* **データ フロー** - ビッグ データの変換を行うために、コーディングを必要としない、完全に視覚的なエクスペリエンスを提供します。 すべての最適化と実行は、サーバーレス方式で処理されます。
* **パイプライン** - 1 つのタスクを連携して実行するアクティビティの論理的なグループ。
* **アクティビティ** - データのコピー、ノートブックや SQL スクリプトの実行など、データに対して実行するアクションを定義します。
* **トリガー** - パイプラインを実行します。 手動または自動で実行できます (スケジュール、タンブリング ウィンドウ、またはイベントベース)
* **統合データセット** - アクティビティで入力および出力として使用されるデータを単にポイントまたは参照する、データの名前付きビュー。 リンクされたサービスに属します。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)

