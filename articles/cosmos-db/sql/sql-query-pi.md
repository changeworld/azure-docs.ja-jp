---
title: Azure Cosmos DB クエリ言語の PI
description: Azure Cosmos DB での SQL システム関数 PI について学習します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 15cb31f1ed98c9100b7bd04ec5f9e7b5102091b3
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206839"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 PI の定数値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`PI` の値を返します。  
  
```sql
SELECT PI() AS pi 
```  
  
 結果セットは次のようになります。  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](../introduction.md)
