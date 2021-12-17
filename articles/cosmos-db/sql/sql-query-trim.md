---
title: Azure Cosmos DB クエリ言語の TRIM
description: Azure Cosmos DB の SQL システム関数 TRIM について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/14/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ea47e95a3b47f50613c954c95f3f5fd306e9c729
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638826"
---
# <a name="trim-azure-cosmos-db"></a>TRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

先頭と末尾の空白または指定された文字を削除した後の文字列式を返します。  
  
## <a name="syntax"></a>構文
  
```sql
TRIM(<str_expr1>[, <str_expr2>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   文字列式

*str_expr2*  
   str_expr1 からトリムされる省略可能な文字列式。 設定されていない場合、既定値は空白です。

## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、クエリ内での `TRIM` の使用方法を示しています。  
  
```sql
SELECT TRIM("   abc") AS t1, 
TRIM("   abc   ") AS t2, 
TRIM("abc   ") AS t3, 
TRIM("abc") AS t4,
TRIM("abc", "ab") AS t5,
TRIM("abc", "abc") AS t6
```  
  
 結果セットは次のようになります。  
  
```json
[
    {
        "t1": "abc",
        "t2": "abc",
        "t3": "abc",
        "t4": "abc",
        "t5": "c",
        "t6": ""
    }
]
``` 

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
