---
title: Azure Cosmos DB クエリ言語の ST_WITHIN
description: Azure Cosmos DB の SQL システム関数 ST_WITHIN について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ac107c098313c9ff477490e7502cddd31f54eaf7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206467"
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

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の空間関数](sql-query-spatial-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
