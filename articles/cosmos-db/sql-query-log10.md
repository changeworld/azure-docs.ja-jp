---
title: Azure Cosmos DB クエリ言語の LOG10
description: 指定された数値式の常用対数を返す Azure Cosmos DB の LOG10 SQL システム関数について説明します
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5950a2a40a78716a76e49fcdaeb3129948b6a0e1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871059"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 指定された数値式の底 10 の対数を返します。  
  
## <a name="syntax"></a>構文
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expression*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="remarks"></a>解説
  
  LOG10 関数と POWER 関数は、互いに逆の関係になります。 たとえば、10 ^ log10 (n) = n になります。  
  
## <a name="examples"></a>例
  
  次の例では、変数を宣言し、指定した変数 (100) の LOG10 値を返します。  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 結果セットは次のようになります。  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
