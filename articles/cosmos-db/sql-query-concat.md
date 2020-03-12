---
title: Azure Cosmos DB クエリ言語の CONCAT
description: Azure Cosmos DB の CONCAT SQL システム関数で、2 つ以上の文字列値を連結した結果である文字列を返す方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302612"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 2 つ以上の文字列値を連結した結果である文字列を返します。  
  
## <a name="syntax"></a>構文
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   他の値に連結する文字列式です。 `CONCAT` 関数には、少なくとも 2 つの *str_expr* 引数が必要です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定した値の連結された文字列を返します。  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 結果セットは次のようになります。  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
