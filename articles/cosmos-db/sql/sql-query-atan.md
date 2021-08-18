---
title: Azure Cosmos DB クエリ言語の ATAN
description: Azure Cosmos DB の Arctangent (ATAN) SQL システム関数で、タンジェントが指定された数値式となる角度をラジアン単位で返す方法について説明します
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d956a99b3ec615a7cc1d97e2efedade08681e47a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206391"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 タンジェントが指定された数値式となる角度をラジアン単位で返します。 これはアークタンジェント (逆正接) とも呼ばれます。  
  
## <a name="syntax"></a>構文
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定した値の `ATAN` を返します。  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 結果セットは次のようになります。  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
