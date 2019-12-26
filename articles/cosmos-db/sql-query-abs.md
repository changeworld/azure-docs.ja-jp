---
title: Azure Cosmos DB クエリ言語の ABS
description: Azure Cosmos DB の Absolute (ABS) SQL システム関数が、指定された数値式の正の値を返す方法について説明します
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70648dcf28d760c3a81c0cb426cd9b2d3ce96fff
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871858"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
 指定された数値式の絶対値 (正の値) を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、3 つの異なる数値に対して `ABS` 関数を使用した結果を示しています。  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 結果セットは次のようになります。  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
