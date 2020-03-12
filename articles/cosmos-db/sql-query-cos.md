---
title: Azure Cosmos DB クエリ言語の COS
description: Azure Cosmos DB の Cosine (COS) SQL システム関数で指定されたラジアン単位の角度の三角関数コサインを返す方法について説明します
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304023"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 式で指定されたラジアン単位の角度の三角関数コサインを返します。  
  
## <a name="syntax"></a>構文
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定された角度の `COS` を計算します。  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 結果セットは次のようになります。  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
