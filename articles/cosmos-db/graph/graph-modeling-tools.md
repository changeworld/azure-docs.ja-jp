---
title: Azure Cosmos DB グラフ データのサードパーティー製データ モデリング ツール
description: この記事では、Graph データ モデルを設計するためのさまざまなツールについて説明します。
author: mansha
ms.author: mansha
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.reviewer: sngun
ms.openlocfilehash: 224465fe282b086c2158f4d81ee67e5bf7cedc09
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771785"
---
# <a name="third-party-data-modeling-tools-for-azure-cosmos-db-graph-data"></a>Azure Cosmos DB グラフ データのサードパーティー製データ モデリング ツール

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

データ モデルを設計することは重要であり、保守することはさらに重要です。 ここでは、グラフ データ モデルの設計と保守に役立つサードパーティー製ビジュアル設計ツールについて説明します。

> [!IMPORTANT] 
> この記事で説明されているソリューションは情報提供のみを目的としており、所有権は個々のソリューションの所有者にあります。 十分な評価を行ってから、最適なものを選択することをお勧めします。

## <a name="hackolade"></a>Hackolade

Hackolade は、NoSQL データベース向けのデータ モデリングおよびスキーマ設計ツールです。 データ モデリング スタジオを備えており、保存データと移動中のデータのスキーマを管理するのに役立ちます。

### <a name="how-it-works"></a>しくみ
このツールには、頂点、エッジと、それぞれのプロパティのデータ モデリングが用意されています。  いくつかのユース ケースをサポートしています。その一部を次に示します。
-   空白のページから始めて、さまざまなオプションを検討し、Cosmos DB Gremlin モデルをグラフィカルに構築します。  次に、そのモデルを Azure インスタンスにフォワード エンジニアリングし、結果を評価して進化を続けます。  このような優れた機能を 1 行のコードも書かずに実現できます。
-   既存のグラフを Azure 上でリバース エンジニアリングし、その構造を明確に理解することで、効果的にグラフのクエリを実行することができます。  次に、説明、メタデータ、制約条件を使用してデータ モデルを強化し、ドキュメントを作成します。 HTML、Markdown、PDF 形式をサポートしており、企業のデータ ガバナンスや辞書システムにフィードすることができます。
-   データ構造の非正規化により、リレーショナル データベースから NoSQL に移行します。
-   コマンドライン インターフェイスを使用して CI/CD パイプラインと統合します
-   Git を使用したコラボレーションとバージョン管理
-   その他にもたくさんあります。

### <a name="sample"></a>サンプル

図 - 2 のアニメーションは、リバース エンジニアリングのデモンストレーションです。RDBMS からエンティティが抽出され、Hackolade によって外部キーのリレーションシップから関係が検出され、修正されます。

SQL Server としてのソースのサンプル DDL は、[こちら](https://github.com/Azure-Samples/northwind-ddl-sample/blob/main/nw.sql)で入手できます   


:::image type="content" source="./media/graph-modeling-tools/hackolade-screenshot.jpg" alt-text="Graph の図":::
**図 - 1:** Graph の図 (グラフ データ モデルを抽出)

データ モデルの修正後、このツールを使用して gremlin スクリプトを生成できます。これには、最適なインデックスを作成するためのカスタム Cosmos DB インデックス スクリプトを含めることができます。完全なフローについては、図 2 を参照してください。

次の図は、RDBMS と Hackolade からのリバース エンジニアリングの動作を示しています。:::image type="content" source="./media/graph-modeling-tools/cosmos-db-gremlin-hackolade.gif" alt-text="Hackolade の動作":::

**図 - 2:**  Hackolade の動作 (SQL から Gremlin へのデータ モデル変換のデモ)
### <a name="useful-links"></a>便利なリンク 
-   [14 日間の無料試用版をダウンロードする](https://hackolade.com/download.html)
-   [デモのスケジュールを設定する](https://c.x.ai/pdesmarets)
-  [その他のデータ モデルを取得する](https://hackolade.com/samplemodels.html#cosmosdb)。
-  [Hackolade のドキュメント](https://hackolade.com/help/CosmosDBGremlin.html)

## <a name="next-steps"></a>次のステップ
- [データの視覚化](./graph-visualization-partners.md)