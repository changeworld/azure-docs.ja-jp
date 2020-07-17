---
title: Azure Cosmos DB クエリ言語の COT
description: Azure Cosmos DB の Cotangent (COT) SQL システム関数で、数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返す方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299489"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返します。  
  
## <a name="syntax"></a>構文
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定された角度の `COT` を計算します。  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 結果セットは次のようになります。  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
