---
title: Azure Cosmos DB クエリ言語の IS_ARRAY
description: Azure Cosmos DB での SQL システム関数 IS_ARRAY について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5867850db6eb3d6552bc129cca3708ef7747072
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303887"
---
# <a name="is_array-azure-cosmos-db"></a>IS_ARRAY (Azure Cosmos DB)
 指定した式の型が配列であるかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
IS_ARRAY(<expr>)  
```  
  
## <a name="arguments"></a>引数
  
*expr*  
   任意の式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`IS_ARRAY` 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null 値、オブジェクト、配列、未定義の型をチェックします。  
  
```sql
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 結果セットは次のようになります。  
  
```json
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。

## <a name="next-steps"></a>次のステップ

- [型チェック関数 (Azure Cosmos DB)](sql-query-type-checking-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
