---
title: Azure Cosmos DB クエリ言語の ATAN
description: Azure Cosmos DB の Arctangent (ATAN) SQL システム関数で、タンジェントが指定された数値式となる角度をラジアン単位で返す方法について説明します
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fd398e343a3e617ceb9f49ca8f31ee776a7f073
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873456"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 タンジェントが指定された数値式となる角度をラジアン単位で返します。 アークタンジェントとも呼ばれます。  
  
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
  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
