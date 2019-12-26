---
title: Azure Cosmos DB クエリ言語の ATN2
description: Azure Cosmos DB の ATN2 SQL システム関数から y/x のアーク タンジェントの主値 (ラジアンで表される) が返される方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11321ef9d7b81af279b04e0e435b19c645cf3bcf
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871654"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 ラジアン単位で表される y/x のアーク タンジェントの主値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、指定された x と y コンポーネントの ATN2 を計算します。  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 結果セットは次のようになります。  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
