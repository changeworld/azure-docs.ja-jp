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
ms.topic: article
ms.date: 09/27/2018
ms.author: bwren
ms.openlocfilehash: d2db9d426da58b3783b07210165a55cc6ec27658
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185955"
---
# <a name="standard-properties-in-log-analytics-records"></a>Log Analytics レコードでの標準プロパティ
[Log Analytics](../log-query/log-query-overview.md) のデータは、それぞれにプロパティの固有セットがある特定のデータ型を持つ、レコードのセットとして格納されます。 多くのデータ型には、複数の型にわたって共通の標準プロパティがあります。 この記事では、これらのプロパティについて説明し、プロパティをクエリで使用する方法の例を示します。

これらのプロパティの一部は、まだ実装の途中ですので、いくつかのデータ型では表示され、他のデータ型では表示されない可能性があります。

## <a name="timegenerated"></a>TimeGenerated
**TimeGenerated** プロパティには、レコードが作成された日付と時刻が含まれています。 これは、時間でフィルター処理または集計するために使用する一般的なプロパティを提供します。 Azure Portal でビューまたはダッシュボードの時間範囲を選択した場合は、結果をフィルター処理するために TimeGenerated が使用されます。

### <a name="examples"></a>例

次のクエリは、前の週の各日に作成されたエラー イベントの数を返します。

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>type
**Type** プロパティは、そのレコードの取得元のテーブルの名前を保持します。これは、レコードの種類と考えることもできます。 このプロパティは、複数のテーブルからのレコードを結合するクエリ (`search` オペレーターを使用するクエリなど) で、異なる種類のレコードを区別するために役立ちます。 場所によっては、**Type** の代わりに **$table** を使用できます。

### <a name="examples"></a>例
次のクエリは、過去 1 時間に収集された種類ごとのレコード数を返します。

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
**\_ResourceId** プロパティは、レコードが関連付けられているリソースの一意識別子を保持します。 これにより、ご自分のクエリを特定のリソースからのレコードのみに範囲を絞り込んだり、複数のテーブルにわたって関連するデータを結合したりするために使用する標準プロパティが提供されます。

Azure リソースの場合、**_ResourceId** の値は [Azure リソース ID の URL](../../azure-resource-manager/resource-group-template-functions-resource.md) です。 現在、プロパティは Azure リソースに制限されていますが、オンプレミスのコンピューターなど、Azure 外のリソースに拡張される予定です。

> [!NOTE]
> 一部のデータ型には、Azure リソース ID か、または少なくともその一部 (サブスクリプション ID など) を含むフィールドが既に割り当てられています。 これらのフィールドは下位互換性のために保持されていますが、_ResourceId を使用して相互相関を実行する方が一貫性があるため、この方法をお勧めします。

### <a name="examples"></a>例
次のクエリは、各コンピューターのパフォーマンスとイベント データを結合します。 ID が _101_ でプロセッサ使用率が 50% を超えるすべてのイベントが表示されます。

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

次のクエリは、_AzureActivity_ レコードを _SecurityEvent_ レコードに結合します。 これらのコンピューターにログインしたユーザーを含むアクティビティ操作がすべて表示されます。

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

- [Log Analytics データの格納方法](../log-query/log-query-overview.md)に関するページをご覧ください。
- [Log Analytics でのクエリの記述](../../azure-monitor/log-query/get-started-queries.md)に関するレッスンをご覧ください。
- [Log Analytics クエリでのテーブルの結合](../../azure-monitor/log-query/joins.md)に関するレッスンをご覧ください。
