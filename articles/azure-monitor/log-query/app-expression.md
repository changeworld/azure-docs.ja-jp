---
title: Azure Log Analytics クエリの app() 式 | Microsoft Docs
description: app 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定の Application Insights アプリからのデータを取得するために、Log Analytics クエリで使用します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: e83ba321a98e40f07ff82e68c7961c2a6a49076d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191837"
---
# <a name="app-expression-in-log-analytics-query"></a>Log Analytics クエリでの app() 式

`app` 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定の Application Insights アプリからのデータを取得するために、Log Analytics クエリで使用します。 これは、Log Analytics クエリにアプリケーション データを含めたり、Application Insights のクエリで複数のアプリケーションのデータをクエリしたりする場合に便利です。



## <a name="syntax"></a>構文

`app(`*識別子*`)`


## <a name="arguments"></a>引数

- *識別子*: 次の表のいずれかの形式を使用してアプリを識別します。

| ID | 説明 | 例
|:---|:---|:---|
| リソース名 | 人間が判読できるアプリの名前 (別名 "コンポーネント名") | app("fabrikamapp") |
| 修飾名 | "subscriptionName/resourceGroup/componentName" の形式のアプリのフルネーム | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | アプリの GUID | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure リソース ID | Azure リソースの識別子 |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>メモ

* アプリケーションへの読み取りアクセスが必要です。
* アプリケーションをその名前で識別することで、アクセス可能なすべてのサブスクリプション全体で一意であるとみなされます。 指定した名前を持つ複数のアプリケーションが存在する場合は、あいまいさのためにクエリが失敗します。 この場合、その他の識別子のいずれかを使用する必要があります。
* 関連する[ワークスペース](workspace-expression.md)の式を使用して、Log Analytics ワークスペースにわたってクエリを実行します。

## <a name="examples"></a>例

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>次の手順

- Log Analytics ワークスペースについては、[ワークスペースの式](workspace-expression.md)を参照してください。
- [Log Analytics データ](../../azure-monitor/log-query/log-query-overview.md)の保存方法をご覧ください。