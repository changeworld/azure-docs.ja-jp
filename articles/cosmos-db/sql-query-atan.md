---
title: Azure Cosmos DB クエリ言語の ATAN
description: Azure Cosmos DB の Arctangent (ATAN) SQL システム関数で、タンジェントが指定された数値式となる角度をラジアン単位で返す方法について説明します
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302680"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
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
- [Azure Cosmos DB の概要](introduction.md)
