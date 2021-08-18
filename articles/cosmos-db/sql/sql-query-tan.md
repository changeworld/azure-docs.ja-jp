---
title: Azure Cosmos DB クエリ言語の TAN
description: Azure Cosmos DB の SQL システム関数 TAN について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 84731817f5ec2ab73c9a8089554508f39db35c70
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206437"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 式で指定されたラジアン単位の角度のタンジェントを返します。  
  
## <a name="syntax"></a>構文
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、PI()/2 のタンジェントを計算します。  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 結果セットは次のようになります。  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
