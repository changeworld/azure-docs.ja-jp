---
title: Azure Cosmos DB クエリ言語の UPPER
description: Azure Cosmos DB での SQL システム関数 UPPER について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9809a138a5e28eb069d545d39cfda815c915bd78
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728890"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 文字列式の小文字データを大文字に変換して返します。  

UPPER システム関数では、インデックスは使用されません。 大文字と小文字を区別しない比較を頻繁に行う場合、UPPER システム関数は、大量の RU を消費する可能性があります。 この場合は、UPPER システム関数を使用して比較のたびにデータを正規化するのではなく、挿入時に大文字と小文字を正規化することができます。 その後、SELECT * FROM c WHERE UPPER(c.name) = 'BOB' などのクエリは、単に SELECT * FROM c WHERE c.name = 'BOB' になります。

## <a name="syntax"></a>構文
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、クエリ内での `UPPER` の使用方法を示しています  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 結果セットは次のようになります。  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
