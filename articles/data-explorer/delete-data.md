---
title: Azure データ エクスプローラーからデータを削除する
description: この記事では、消去、エクステントのドロップ、アイテム保持期間を使用した削除など、Azure Data Explorer の削除シナリオについて説明します。
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501417"
---
# <a name="delete-data-from-azure-data-explorer"></a>Azure データ エクスプローラーからデータを削除する

Azure Data Explorer は、この記事で説明されているさまざまな削除のシナリオをサポートしています。 

## <a name="delete-data-using-the-retention-policy"></a>アイテム保持ポリシーを使用してデータを削除する

Azure Data Explorer は、[アイテム保持ポリシー](/azure/kusto/management/retentionpolicy)に基づいてデータを自動的に削除します。 この方法は、データ削除のための最も効率的で手間のかからない方法です。 データベースまたはテーブルレベルでアイテム保持ポリシーを設定します。

90 日間のリテンション期間が設定されているデータベースまたはテーブルがあるとします。 60日間のデータのみが必要な場合は、次のように古いデータを削除します。

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>エクステントをドロップしてデータを削除する

[エクステント (データシャード)](/azure/kusto/management/extents-overview)とは、データが格納される内部構造です。 各エクステントは、何百万ものレコードを保持できます。 エクステントは、個別に削除することも、[ドロップ エクステントのコマンド](/azure/kusto/management/extents-commands#drop-extents)を使用してグループとして削除することもできます。 

### <a name="examples"></a>例

テーブル内のすべての行を削除することも、特定のエクステントのみを削除することもできます。

* テーブル内のすべての行を削除する：

    ```kusto
    .drop extents from TestTable
    ```

* 特定のエクステントを削除する：

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>消去を使用して個々の行を削除する

[データの消去](/azure/kusto/concepts/data-purge)は、個々の行を削除するために使用できます。 削除には時間がかかり、大量のシステム リソースを必要とします。 そのため、コンプライアンスのシナリオ以外にはお勧めできません。  

