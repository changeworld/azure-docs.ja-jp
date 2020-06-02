---
title: Azure Cosmos DB 用の Azure Synapse Link (プレビュー) でサポートされる機能
description: Azure Cosmos DB 用の Azure Synapse Link で現在サポートされているアクションの一覧を把握する
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 2e0b1ee5584420ab38fda8897ef610794b09c29a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658848"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Azure Cosmos DB 用の Azure Synapse Link (プレビュー) でサポートされる機能

この記事では、Azure Cosmos DB 用の Azure Synapse Link で現在サポートされている機能について説明します。 

## <a name="azure-synapse-support"></a>Azure Synapse サポート

Azure Cosmos DB には、次の 2 種類のコンテナーがあります。
* HTAP コンテナー - Synapse Link が有効になっているコンテナー。 このコンテナーには、トランザクション ストアと分析ストアの両方があります。 
* OLTP コンテナー - トランザクション ストアのみを含むコンテナー。Synapse Link は有効になっていません。 

Synapse Link を有効にしなくても、Azure Cosmos DB コンテナーに接続できます。この場合、トランザクション ストアに対する読み取り/書き込みのみが可能です。 Azure Cosmos DB 用の Synapse Link 内で現在サポートされている機能の一覧を次に示します。 

| カテゴリ              | 説明 |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL サーバーレス](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **ランタイム サポート** |Azure Synapse ランタイムによる読み取りまたは書き込みをサポートします| ✓ | [お問い合わせ](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Azure Cosmos DB API サポート** |Synapse Link としての API サポート| SQL / MongoDB | SQL / MongoDB |
| **Object**  |Azure Cosmos DB コンテナーを直接指す、作成できるテーブルなどのオブジェクト| ビュー、テーブル | 表示 |
| **読み取り**    |Azure Cosmos DB コンテナーからデータを読み取ります| OLTP / HTAP | HTAP  |
| **書き込み**   |ランタイムから Azure Cosmos DB コンテナーにデータを書き込みます| OLTP | 該当なし |

* Spark から Azure Cosmos DB コンテナーにデータを書き込む場合、このプロセスは Azure Cosmos DB のトランザクション ストアを通じて行われ、要求ユニットを使用することによって Azure Cosmos DB のトランザクション パフォーマンスに影響を与えます。
* 外部テーブルを通じた SQL プールの統合は現在サポートされていません。

## <a name="supported-code-generated-actions-for-spark"></a>Spark に対してサポートされているコード生成アクション

| ジェスチャ              | 説明 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **データフレームへの読み込み** |Spark データフレームへのデータの読み込みと読み取りを行います |X| ✓ |
| **Spark テーブルの作成** |Azure Cosmos DB コンテナーを指すテーブルを作成します|X| ✓ |
| **コンテナーへのデータフレームの書き込み** |コンテナーにデータを書き込みます|✓| ✓ |
| **コンテナーからのストリーミング データフレームの読み込み** |Azure Cosmos DB の変更フィードを使用してデータをストリーミングします|✓| ✓ |
| **コンテナーへのストリーミング データフレームの書き込み** |Azure Cosmos DB の変更フィードを使用してデータをストリーミングします|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>SQL サーバーレスに対してサポートされているコード生成アクション

| ジェスチャ              | 説明 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **上位 100 を選択** |コンテナーから上位 100 項目のプレビューを表示します|X| ✓ |
| **ビューの作成** |Synapse SQL を通じて、BI がコンテナーに直接アクセスするビューを作成します|X| ✓ |

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB 用の Synapse Link に接続する](../quickstart-connect-synapse-link-cosmos-db.md)方法を確認する
* [Spark を使用して分析ストアにクエリを実行する方法を学習する](how-to-query-analytical-store-spark.md)