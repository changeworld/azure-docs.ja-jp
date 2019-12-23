---
title: Azure Cosmos DB クエリ言語の ASIN
description: Azure Cosmos DB の Arcsine (ASIN) SQL システム関数が、サインが指定された数値式である角度をラジアンで返す方法について説明します
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e790eb5ae5eb780637b199a1a65dec2dd02d1bc
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871740"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 サインが指定された数値式となる角度をラジアン単位で返します。 アークサインとも呼ばれます。  
  
## <a name="syntax"></a>構文
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、-1 の `ASIN` を返します。  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 結果セットは次のようになります。  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
