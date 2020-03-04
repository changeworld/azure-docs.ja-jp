---
title: Azure Cosmos DB を使用して地理空間データのインデックスを付ける
description: Azure Cosmos DB を使用して空間データのインデックスを付ける
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566476"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Azure Cosmos DB を使用して地理空間データのインデックスを付ける

Azure Cosmos DB のデータベース エンジンは、本当にスキーマに依存しないように設計されており、JSON のファースト クラスのサポートを実現しています。 書き込みに最適化された Azure Cosmos DB のデータベース エンジンは、GeoJSON 標準で表された空間データをネイティブに認識できるようになっています。

簡単に言えば、ジオメトリは、測地座標系から 2D 平面に投影された後、**4 分木**を使用して段階的にセルに分割されます。 これらのセルは、その位置に基づき、**ヒルベルト空間充填曲線**内で一次元にマッピングされ、ポイントの局所性が維持されます。 さらに、位置情報データのインデックスを作成するとき、そのデータに、**テセレーション**と呼ばれるプロセスが適用されます。つまり、特定の位置と交差するすべてのセルが特定され、キーとして Azure Cosmos DB のインデックスに保存されます。 クエリの実行時、ポイントや Polygon などの引数は、同様にテセレーションを経て関連するセル ID 範囲が抽出された後、インデックスからデータを取得するために使用されます。

/* (すべてのパス) の空間インデックスを含むインデックス ポリシーを指定すると、効率的な空間クエリのために、コンテナー内で見つかったすべてのデータにインデックスが付けられます。

> [!NOTE]
> Azure Cosmos DB は、Point、LineString、Polygon、および MultiPolygon のインデックス作成をサポートしています
>
>

## <a name="geography-data-indexing-examples"></a>geography データのインデックス付けの例

次の JSON スニペットは、**geography** データ型に対して空間インデックスが有効なインデックス ポリシーを示しています。 これは、geography データの種類の空間データに対して有効であり、空間クエリのためにドキュメント内で見つかった GeoJSON の Point、Polygon、MultiPolygon、または LineString のインデックスが付けられます。 Azure portal を使用してインデックス作成ポリシーを変更する場合、インデックス作成ポリシーで以下の JSON を指定するとコンテナーの空間インデックスを有効にすることができます。

**geography 空間インデックスを使用したコンテナー インデックス作成ポリシー JSON**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> ドキュメント内の GeoJSON の location 値が無効であったり形式が正しくなかったりした場合、空間クエリ用のインデックスは作成されません。 ST_ISVALID と ST_ISVALIDDETAILED を使用して location 値を検証できます。
>
>
>

Azure CLI、PowerShell、または任意の SDK を使用して、[インデックス作成ポリシーを変更する](how-to-manage-indexing-policy.md)こともできます。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB の地理空間機能の基本的な使い方を学んだら、次の段階に進みましょう。

* [Azure Cosmos DB のクエリ](sql-query-getting-started.md)の詳細を確認する
* [Azure Cosmos DB を使用した空間データのクエリ](sql-query-geospatial-query.md)の詳細を確認する
* [Azure Cosmos DB で地理空間データと GeoJSON 位置データを扱う](sql-query-geospatial-intro.md)方法の詳細を確認する