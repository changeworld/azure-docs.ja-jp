---
title: Azure Cosmos DB クエリ言語の LTRIM
description: 先頭の空白を削除して文字列式を返す Azure Cosmos DB の LTRIM SQL システム関数について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8ce558be7b1380fbe16ccdd4226744c3e3c5b222
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206357"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 文字列式の先頭の空白を削除して返します。  
  
## <a name="syntax"></a>構文
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、クエリ内での `LTRIM` の使用方法を示しています。  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 結果セットは次のようになります。  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
