---
title: Azure Cosmos DB クエリ言語の EndsWith
description: 1 つ目の文字列式が 2 つ目で終了しているかどうかを示すブール値を返す、Azure Cosmos DB の ENDSWITH SQL システム関数について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6e7d67757bd1cdf8b82e15a4c925d2716d414442
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206452"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   文字列式です。  
  
*str_expr2*  
   *str_expr1* の末尾と比較される文字列式です。

*bool_expr* 大文字と小文字の区別を無視する場合のオプションの値です。 true に設定すると、ENDSWITH は大文字と小文字を区別せずに検索を実行します。 指定しない場合、この値は false になります。
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
次の例は、文字列 "abc" が "b" および "bC" で終わるかどうかを確認します。  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 結果セットは次のようになります。  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>解説

この文字列システム関数でインデックスがどのように使用されるかについては[こちら](sql-query-string-functions.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
