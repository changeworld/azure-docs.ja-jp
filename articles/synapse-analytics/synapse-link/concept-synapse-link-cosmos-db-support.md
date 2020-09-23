---
title: Azure Cosmos DB 用の Azure Synapse Link (プレビュー) でサポートされる機能
description: Azure Cosmos DB 用の Azure Synapse Link で現在サポートされているアクションの一覧を把握する
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 336409b8b6f804b224b87d5fb11fded0654b8619
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895522"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Azure Cosmos DB 用の Azure Synapse Link (プレビュー) でサポートされる機能

この記事では、Azure Cosmos DB 用の Azure Synapse Link で現在サポートされている機能について説明します。

## <a name="azure-synapse-support"></a>Azure Synapse サポート

Azure Cosmos DB には、次の 2 種類のコンテナーがあります。
* HTAP コンテナー - Synapse Link が有効になっているコンテナー。 このコンテナーには、トランザクション ストアと分析ストアの両方があります。 
* OLTP コンテナー - Synapse Link が有効になっていないコンテナー。 このコンテナーにはトランザクション ストアのみが含まれており、分析ストアはありません。

> [!IMPORTANT]
> 現在、Azure Synapse Link for Azure Cosmos DB は、マネージド仮想ネットワークが有効になっていない Synapse ワークスペースでサポートされています。 

Synapse Link を有効にしなくても、Azure Cosmos DB コンテナーに接続できます。この場合、トランザクション ストアに対する読み取り/書き込みのみが可能です。 Azure Cosmos DB 用の Synapse Link 内で現時点でサポートされている機能の一覧を次に示します。 

| カテゴリ              | 説明 |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL サーバーレス](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **ランタイム サポート** |Azure Cosmos DB にアクセスするためにサポートされている Azure Synapse ランタイム| ✓ | [お問い合わせ](mailto:cosmosdbsynapselink@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB) |
| **Azure Cosmos DB API サポート** | サポートされている Azure Cosmos DB API の種類 | SQL / MongoDB | SQL / MongoDB |
| **Object**  |Azure Cosmos DB コンテナーを直接指す、作成できるテーブルなどのオブジェクト| データフレーム、ビュー、テーブル | 表示 |
| **読み取り**    | 読み取り可能な Azure Cosmos DB コンテナーのタイプ | OLTP / HTAP | HTAP  |
| **書き込み**   | Azure Synapse ランタイムを使用して Azure Cosmos DB コンテナーにデータを書き込むことができる | はい | いいえ |

* Spark から Azure Cosmos DB コンテナーにデータを書き込む場合、これは、Azure Cosmos DB のトランザクション ストア経由で実行され、Azure Cosmos DB のトランザクション パフォーマンスに影響を及ぼし、要求ユニットを消費します。
* 外部テーブルを介した Synapse SQL プールの統合は現在、サポートされていません。

## <a name="supported-code-generated-actions-for-spark"></a>Spark に対してサポートされているコード生成アクション

| ジェスチャ              | 説明 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **データフレームへの読み込み** |Spark データフレームへのデータの読み込みと読み取りを行います |✓| ✓ |
| **Spark テーブルの作成** |Azure Cosmos DB コンテナーを指すテーブルを作成します|✓| ✓ |
| **コンテナーへのデータフレームの書き込み** |コンテナーにデータを書き込みます|✓| ✓ |
| **コンテナーからのストリーミング データフレームの読み込み** |Azure Cosmos DB の変更フィードを使用してデータをストリーミングします|✓| ✓ |
| **コンテナーへのストリーミング データフレームの書き込み** |Azure Cosmos DB の変更フィードを使用してデータをストリーミングします|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>SQL サーバーレスに対してサポートされているコード生成アクション

| ジェスチャ              | 説明 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **データの探索** |使い慣れた T-SQL 構文と自動スキーマ推論を使用してコンテナーからデータを探索する|X| ✓ |
| **ビューの作成と BI レポートの作成** |SQL ビューを作成して、Synapse SQL サーバーレスで BI 用コンテナーに直接アクセス可能 |X| ✓ |
| **Cosmos DB データおよびさまざまなデータ ソースの結合** | CETAS を使用して、Azure Blob Storage または Azure Data Lake Storage のデータと共に、Cosmos DB コンテナーからデータを読み取るクエリの結果を格納する |X| ✓ |

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB 用の Synapse Link に接続する](../quickstart-connect-synapse-link-cosmos-db.md)方法を確認する
* [Spark を使用して分析ストアにクエリを実行する方法を学習する](how-to-query-analytical-store-spark.md)
