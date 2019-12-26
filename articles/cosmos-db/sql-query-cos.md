---
title: Azure Cosmos DB クエリ言語の COS
description: Azure Cosmos DB の Cosine (COS) SQL システム関数で指定されたラジアン単位の角度の三角関数コサインを返す方法について説明します
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873405"
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

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
