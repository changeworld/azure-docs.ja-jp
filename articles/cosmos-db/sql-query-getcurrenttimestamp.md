---
title: Azure Cosmos DB クエリ言語の GetCurrentTimestamp
description: Azure Cosmos DB での SQL システム関数 GetCurrentTimestamp について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351013"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過したミリ秒数を返します。 
  
## <a name="syntax"></a>構文
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>戻り値の型
  
  Unix エポックから現在までに経過したミリ秒数を表す数値、つまり 1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過したミリ秒数を返します。

## <a name="remarks"></a>解説

  GetCurrentTimestamp() は非決定論的関数です。
  
  返される結果は UTC (協定世界時) です。

## <a name="examples"></a>例
  
  次の例は、GetCurrentTimestamp() 組み込み関数を使用して、現在のタイムスタンプを取得する方法を示しています。
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 結果セットは次のようになります。
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
