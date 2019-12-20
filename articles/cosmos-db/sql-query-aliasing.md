---
title: Azure Cosmos DB でのエイリアス化
description: Azure Cosmos DB の SQL クエリでエイリアス化を使用して、同じ名前の 2 つのプロパティを区別する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873473"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB でのエイリアス化

クエリ内の値を明示的にエイリアス化できます。 クエリに同じ名前を持つ 2 つのプロパティがある場合、エイリアス化を使ってプロパティのいずれかまたは両方の名前を変更することで、プロジェクションの結果でこれらを区別できます。

## <a name="examples"></a>例

2 つ目の値を `NameInfo` としてプロジェクションしている場合の次の例に示すように、エイリアス化に使用する AS キーワードはオプションです。

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 句](sql-query-select.md)
- [FROM 句](sql-query-from.md)
