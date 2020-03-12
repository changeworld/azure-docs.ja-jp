---
title: Azure Cosmos DB クエリ言語の LENGTH
description: Azure Cosmos DB での SQL システム関数 LENGTH について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303717"
---
# <a name="length-azure-cosmos-db"></a>LENGTH (Azure Cosmos DB)
 指定された文字列式の文字数を返します。  
  
## <a name="syntax"></a>構文
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   評価される文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、文字列の長さを返します。  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 結果セットは次のようになります。  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
