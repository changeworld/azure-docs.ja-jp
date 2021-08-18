---
title: Azure Cosmos DB クエリ言語の RAND
description: Azure Cosmos DB の SQL システム関数 RAND について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cd903a6f067c116277a7ec3c2d7d01ce8e2dedad
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206493"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 [0,1) からランダムに生成された数値を返します。
 
## <a name="syntax"></a>構文
  
```sql
RAND ()  
```  

## <a name="return-types"></a>戻り値の型

  数値式を返します。

## <a name="remarks"></a>解説

  `RAND` は非決定論的関数です。 `RAND` の繰り返し呼び出しでは、同じ結果が返されません。 このシステム関数では、インデックスは使用されません。


## <a name="examples"></a>例
  
  次の例では、ランダムに生成された数値を返します。
  
```sql
SELECT RAND() AS rand 
```  
  
 結果セットは次のようになります。  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
