---
title: Azure Cosmos DB クエリ言語の ST_INTERSECTS
description: Azure Cosmos DB の SQL システム関数 ST_INTERSECTS について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ac3ba6ec00dc617d434026a6693c6c4554421140
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647769"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 最初の引数で指定された GeoJSON オブジェクト (Point、Polygon、MultiPolygon、LineString) が 2 つ目の引数の GeoJSON (Point、Polygon、MultiPolygon、LineString) と交差するかどうかを示すブール式を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*spatial_expr*  
   GeoJSON Point、Polygon、または LineString オブジェクトの式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール値を返します。  
  
## <a name="examples"></a>例
  
  次の例では、特定の多角形と交差するすべての領域を見つける方法を示します。  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 結果セットは次のようになります。  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>解説

このシステム関数は、集計を使用したクエリを除き、[地理空間インデックス](../index-policy.md#spatial-indexes)の恩恵を受けます。

> [!NOTE]
> GeoJSON 仕様では、Polygon 内のポイントを反時計回りの順序で指定する必要があります。 時計回りに指定された Polygon は、その中の領域を逆にしたものを表します。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の空間関数](sql-query-spatial-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
