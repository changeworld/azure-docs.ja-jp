---
title: Azure Cosmos DB クエリ言語の SIN
description: Azure Cosmos DB の SQL システム関数 SIN について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303105"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 式で指定されたラジアン単位の角度の三角関数サインを返します。  
  
## <a name="syntax"></a>構文
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定された角度の `SIN` を計算します。  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 結果セットは次のようになります。  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
