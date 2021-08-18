---
title: Azure Cosmos DB クエリ言語の CONTAINS
description: Azure Cosmos DB の CONTAINS SQL システム関数で、1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返す方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4ae5f786fe75d96bd0dc1aa0afab7c5a8965c826
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206529"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   検索対象となる文字列式です。  
  
*str_expr2*  
   検索する文字列式です。  

*bool_expr* 大文字と小文字の区別を無視する場合のオプションの値です。 true に設定すると、CONTAINS は大文字と小文字を区別せずに検索を実行します。 指定しない場合、この値は false になります。
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、"abc" に "ab" が含まれ、"abc" に "A" が含まれるかどうかを確認します。  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
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
