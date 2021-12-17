---
title: Azure Cosmos DB クエリ言語の ST_WITHIN
description: Azure Cosmos DB の SQL システム関数 ST_WITHIN について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7f3aea14b4da97624abb8515c560281afbdefe18
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625261"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 最初の引数で指定された GeoJSON オブジェクト (Point、Polygon、MultiPolygon、LineString) が 2 つ目の引数の GeoJSON (Point、Polygon、MultiPolygon、LineString) 内にあるかどうかを示すブール式を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*spatial_expr*  
   GeoJSON Point、Polygon、または LineString オブジェクトの式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール値を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`ST_WITHIN` を使用して多角形内のすべての世帯ドキュメントを検索する方法を示します。  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 結果セットは次のようになります。  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>解説

このシステム関数は、集計を使用したクエリを除き、[地理空間インデックス](../index-policy.md#spatial-indexes)の恩恵を受けます。

> [!NOTE]
> GeoJSON 仕様では、Polygon 内のポイントを反時計回りの順序で指定する必要があります。 時計回りに指定された Polygon は、その中の領域を逆にしたものを表します。


## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の空間関数](sql-query-spatial-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
