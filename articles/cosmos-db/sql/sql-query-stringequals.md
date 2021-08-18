---
title: Azure Cosmos DB クエリ言語の StringEquals
description: Azure Cosmos DB の StringEquals SQL システム関数で、1 つ目の文字列式が 2 つ目の文字列式と一致するかどうかを示すブール値を返す方法について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a1fbe64a1daa4773d478162c836f2386eea6f11e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206553"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 1 つ目の文字列式と 2 つ目の文字列式が一致するかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   比較する 1 つ目の文字列式。  
  
*str_expr2*  
   比較する 2 つ目の文字列式。  

*bool_expr* 大文字と小文字の区別を無視する場合のオプションの値です。 true に設定すると、StringEquals は大文字と小文字を区別せずに検索を実行します。 指定しない場合、この値は false になります。
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、"abc" と "abc" が一致し、"abc" と "ABC" が一致するかどうかを確認します。  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 結果セットは次のようになります。  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>解説

この文字列システム関数でインデックスがどのように使用されるかについては[こちら](sql-query-string-functions.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
