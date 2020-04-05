---
title: Azure Cosmos DB クエリ言語の SUBSTRING
description: Azure Cosmos DB の SQL システム関数 SUBSTRING について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303700"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 指定された文字のゼロベースの位置で始まる文字列式の一部を返し、指定された長さまたは文字列の末尾まで続きます。  
  
## <a name="syntax"></a>構文
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字列式です。
  
*num_expr1*  
   開始文字を示す数値式です。 値 0 は *str_expr* の最初の文字です。
  
*num_expr2*  
   返される *str_expr* の最大文字数を示す数値式です。 値が 0 以下の場合は、空の文字列になります。

## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、1 から始まる 1 文字の長さの "abc" の部分文字列を返します。  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 結果セットは次のようになります。  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>解説

開始位置が [ の場合、このシステム関数は、](index-policy.md#includeexclude-strategy)範囲インデックス`0`の恩恵を受けます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
