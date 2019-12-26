---
title: Azure Cosmos DB クエリ言語の ARRAY_LENGTH
description: Azure Cosmos DB の配列の長さ SQL システム関数から、指定した配列式の要素の数を返す方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9a8bf33befdd842a2979151fef3d54679ee03de1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871773"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 指定された配列式の要素の数を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*arr_expr*  
   配列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`ARRAY_LENGTH` を使用して、配列の長さを取得する方法を示します。  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 結果セットは次のようになります。  
  
```json
[{"len": 3}]  
```  
  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の配列関数](sql-query-array-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
