---
title: Azure Cosmos DB 上でインデックスのパスを操作する
description: Azure Cosmos DB 上のインデックスのパスの概要
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: c22d8d69284c546a4fccc86302672d81ce65b9e8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54032773"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Azure Cosmos DB 上のインデックスのパス

Azure Cosmos DB 上でインデックスのパスを使用することで、特定のパスをインデックス作成に含めるか除外することができます。 クエリのパターンがわかっているシナリオでは、特定のパスを選択すると、書き込みのパフォーマンスが向上し、インデックス ストレージが削減されます。 インデックスのパスは、ルート (`/`) ワイルドカード演算子で始まり、通常は `?` ワイルドカード演算子で終わります。 このパターンは、プレフィックスに複数の可能な値があることを示します。 たとえば、クエリ `SELECT * FROM Families F WHERE F.familyName = "Andersen"` を処理するには、コンテナーのインデックス ポリシーに `/familyName/?` 用のインデックスのパスを含める必要があります。

インデックスのパスは `*`ワイルドカード演算子を使用して、プレフィックスの下のパスの動作を再帰的に指定することもできます。 たとえば、`/payload/*` を使用して、ペイロード プロパティの下に属するものをすべてインデックス作成から除外できます。

## <a name="common-patterns-for-index-paths"></a>インデックスのパスの一般的なパターン

インデックスのパスを指定するための一般的なパターンは次のとおりです。

| **パス** | **説明/ユース ケース** |
| ---------- | ------- |
| /   | コレクションの既定のパス。 再帰的で、ドキュメント ツリー全体に適用されます。|
| /prop/?  | 次のようなクエリを処理するために必要なインデックスのパス (ハッシュまたは範囲の各種類)。<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop  |
| /"prop"/*  | 指定されたラベルの下のすべてのパスのインデックスのパス。 次のようなクエリで使用<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop |
| /props/[]/?  | ["a", "b", "c"] のようなスカラーの配列に対して、反復クエリと JOIN クエリを処理するために必要なインデックスのパス。<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5  |
| /props/[]/subprop/? | [{subprop: "a"}, {subprop: "b"}] のようなオブジェクトの配列に対して、反復クエリと JOIN クエリを処理するために必要なインデックスのパス。<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" |
| /prop/subprop/? | 次のクエリを処理するために必要なインデックスのパス (ハッシュまたは範囲の各種類)。<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5  |

カスタム インデックスのパスを設定するときは、特定のパス `/*` で表される項目全体に対する既定のインデックス作成ルールを指定する必要があります。

## <a name="next-steps"></a>次の手順

以下の記事で、Azure Cosmos DB 上のインデックス作成の詳細を確認します。

- [インデックス作成の概要](index-overview.md)
- [Azure Cosmos DB での自動インデックス作成](indexing-policies.md)
- [Azure Cosmos DB のインデックスの種類](index-types.md)
