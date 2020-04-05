---
title: Azure Cosmos DB Gremlin の TinkerPop 機能との互換性
description: Graph エンジンの互換性の問題に関するリファレンス ドキュメント
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644734"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin の互換性
Azure Cosmos DB Graph エンジンは [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) のトラバーサル ステップの仕様に厳密に従っていますが、違いがあります。

## <a name="behavior-differences"></a>動作の違い

* Azure Cosmos DB Graph エンジンでは***幅優先***トラバーサルが実行されますが、TinkerPop Gremlin では深さ優先になります。 この動作により、Cosmos DB のように水平方向にスケーラブルなシステムでのパフォーマンスが向上します。 

## <a name="unsupported-features"></a>サポートされていない機能

* ***[Gremlin バイトコード](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** は、プログラミング言語に依存しない、グラフ トラバーサルの仕様です。 Cosmos DB Graph ではまだサポートされていません。 `GremlinClient.SubmitAsync()` を使用して、トラバーサルをテキスト文字列として渡してください。

* ***`property(set, 'xyz', 1)`*** set カーディナリティは現在サポートされていません。 代わりに `property(list, 'xyz', 1)` を使用してください 詳細については、[TinkerPop での頂点プロパティ](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)に関する記事を参照してください。

* ***`atch()`*** を使用すると、宣言型のパターン一致を使用してグラフのクエリを実行できます。 この機能は使用できません。

* 頂点または辺の***プロパティとしてのオブジェクト***はサポートされていません。 プロパティには、プリミティブ型または配列のみを指定できます。

* ***配列プロパティによる並べ替え*** (`order().by(<array property>)`) はサポートされていません。 プリミティブ型での並べ替えのみがサポートされています。

* ***非プリミティブ JSON 型***はサポートされていません。 `string` 型、`number` 型、または `true`/`false` 型を使用してください。 `null` 値はサポートされていません。 

* ***GraphSONv3*** シリアライザーは現在サポートされていません。 接続構成で `GraphSONv2` シリアライザー、リーダー、およびライター クラスを使用してください。

* **ラムダ式と関数**は現在サポートされていません。 これには、`.map{<expression>}`、`.by{<expression>}`、および `.filter{<expression>}` 関数が含まれます。 詳細について、および Gremlin の手順を使用してこれらを書き換える方法については、[ラムダ式に関する注意事項](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)に関する記事を参照してください。

* システムの分散型の性質により、***トランザクション***はサポートされていません。  Gremlin アカウントで "自分の書き込みを読み取る" ように適切な整合性モデルを構成し、オプティミスティック同時実行制御を使用して、競合する書き込みを解決してください。

## <a name="next-steps"></a>次のステップ
* [Cosmos DB ユーザーの声](https://feedback.azure.com/forums/263030-azure-cosmos-db)のページにアクセスして、フィードバックを共有し、お客様にとって重要な機能に取り組めるようにチームをサポートしてください。
