---
title: Azure Cosmos DB クエリ言語の DEGREES
description: ラジアン単位で指定された角度に対応する度単位の角度を返す Azure Cosmos DB の DEGREES SQL システム関数について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 48af54f33e76b336cf3facbaa44b1025392b419e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206413"
---
# <a name="degrees-azure-cosmos-db"></a>DEGREES (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 ラジアン単位で指定された角度に対応する度数単位の角度を返します。  
  
## <a name="syntax"></a>構文
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、PI/2 ラジアンの角度で度数を返します。  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 結果セットは次のようになります。  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
