---
title: Azure Cosmos DB クエリ言語の PI
description: Azure Cosmos DB での SQL システム関数 PI について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349654"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 PI の定数値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`PI` の値を返します。  
  
```sql
SELECT PI() AS pi 
```  
  
 結果セットは次のようになります。  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
