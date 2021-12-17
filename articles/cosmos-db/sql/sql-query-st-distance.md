---
title: Azure Cosmos DB クエリ言語の ST_DISTANCE
description: Azure Cosmos DB での SQL システム関数 ST_DISTANCE について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b1cf5bdad567a82bd3936f2b2a187375259407ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625350"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 2 つの GeoJSON Point、Polygon、MultiPolygon、または LineString 式間の距離を返します。 詳細については、[地理空間データと GeoJSON 位置データ](sql-query-geospatial-intro.md)に関する記事を参照してください。
  
## <a name="syntax"></a>構文
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*spatial_expr*  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
  
## <a name="return-types"></a>戻り値の型
  
  距離を含む数値式を返します。 これは、既定の参照システムのメートル単位で表されます。  
  
## <a name="examples"></a>例
  
  次の例では、指定された場所の 30 km 圏内に存在するすべての世帯ドキュメントを `ST_DISTANCE` 組み込み関数で取得する方法を示します。 .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 結果セットは次のようになります。  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>解説

このシステム関数は、集計を使用したクエリを除き、[地理空間インデックス](../index-policy.md#spatial-indexes)の恩恵を受けます。

> [!NOTE]
> GeoJSON 仕様では、Polygon 内のポイントを反時計回りの順序で指定する必要があります。 時計回りに指定された Polygon は、その中の領域を逆にしたものを表します。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の空間関数](sql-query-spatial-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
