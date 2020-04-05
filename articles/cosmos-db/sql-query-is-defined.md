---
title: Azure Cosmos DB クエリ言語の IS_DEFINED
description: Azure Cosmos DB での SQL システム関数 IS_DEFINED について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303853"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 プロパティに値が代入されているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>引数
  
*expr*  
   任意の式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定された JSON ドキュメント内のプロパティの存在を確認します。 1 つ目は、"a" が存在するので true を返しますが、2 つ目は "b" が存在しないので false を返します。  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 結果セットは次のようになります。  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。

## <a name="next-steps"></a>次のステップ

- [型チェック関数 (Azure Cosmos DB)](sql-query-type-checking-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
