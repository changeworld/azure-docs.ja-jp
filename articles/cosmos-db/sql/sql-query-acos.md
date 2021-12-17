---
title: Azure Cosmos DB クエリ言語の ACOS
description: Azure Cosmos DB の ACOS（arccosice）SQL システム関数が、コサインが指定された数値式である角度をラジアンで返す方法について学習する
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: af76b7cb36deb4a58d2666497a957ff14ea90b03
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206486"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 コサインが指定された数値式となる角度をラジアン単位で返します。アークコサインとも呼ばれます。  
  
## <a name="syntax"></a>構文
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、-1 の `ACOS` を返します。  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 結果セットは次のようになります。  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
