---
title: Azure Cosmos DB クエリ言語の ENDSWITH
description: 1 つ目の文字列式が 2 つ目で終了しているかどうかを示すブール値を返す、Azure Cosmos DB の ENDSWITH SQL システム関数について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299450"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   文字列式です。  
  
*str_expr2*  
   *str_expr1* の末尾と比較される文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、"b" と "bc" で終わる "abc" を返します。  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 結果セットは次のようになります。  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
