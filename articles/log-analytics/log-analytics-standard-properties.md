---
title: Azure Monitor Log Analytics レコードでの標準プロパティ | Microsoft Docs
description: Azure Monitor Log Analytics で複数のデータ型に共通するプロパティについて説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955869"
---
# <a name="standard-properties-in-log-analytics-records"></a>Log Analytics レコードでの標準プロパティ
[Log Analytics](../log-analytics/log-analytics-queries.md) のデータは、それぞれにプロパティの固有セットがある特定のデータ型を持つ、レコードのセットとして格納されます。 多くのデータ型には、複数の型にわたって共通の標準プロパティがあります。 この記事では、これらのプロパティについて説明し、プロパティをクエリで使用する方法の例を示します。

これらのプロパティの一部は、まだ実装の途中ですので、いくつかのデータ型では表示され、他のデータ型では表示されない可能性があります。


## <a name="resourceid"></a>_ResourceId
**_ResourceId** プロパティには、レコードが関連付けられているリソースに対する一意識別子が保持されます。 これにより、ご自分のクエリを特定のリソースからのレコードのみに範囲を絞り込んだり、複数のテーブルにわたって関連するデータを結合したりするために使用する標準プロパティが提供されます。

Azure リソースの場合、**_ResourceId** の値は [Azure リソース ID の URL](../azure-resource-manager/resource-group-template-functions-resource.md) です。 現在、プロパティは Azure リソースに制限されていますが、オンプレミスのコンピューターなど、Azure 外のリソースに拡張される予定です。 

### <a name="examples"></a>例
次の例は、各コンピューターにおけるパフォーマンスとイベント データを結合するクエリを示しています。 ID が _101_ でプロセッサ使用率が 50% を超えるすべてのイベントが表示されます。

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

次の例は、_AzureActivity_ レコードと _SecurityEvent_ レポートを結合するクエリを示しています。 これらのコンピューターにログインしたユーザーを含むアクティビティ操作がすべて表示されます。

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>次の手順

- [Log Analytics データの格納方法](../log-analytics/log-analytics-queries.md)に関するページをご覧ください。
- [Log Analytics でのクエリの記述](../log-analytics/query-language/get-started-queries.md)に関するレッスンをご覧ください。
- [Log Analytics クエリでのテーブルの結合](../log-analytics/query-language/joins.md)に関するレッスンをご覧ください。