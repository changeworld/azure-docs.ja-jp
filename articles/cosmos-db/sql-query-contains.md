---
title: Azure Cosmos DB クエリ言語の CONTAINS
description: Azure Cosmos DB の CONTAINS SQL システム関数で、1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返す方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302595"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
 1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   検索対象となる文字列式です。  
  
*str_expr2*  
   検索する文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、"abc" に "ab" が含まれ、"abc" に "d" が含まれるかどうかを確認します。  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 結果セットは次のようになります。  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
