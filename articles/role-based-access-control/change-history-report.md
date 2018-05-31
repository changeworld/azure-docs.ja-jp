---
title: Azure での RBAC 変更のアクティビティ ログの表示 | Microsoft Docs
description: 過去 90 日間のロールベースのアクセス制御の変更のアクティビティ ログを表示します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e48ea2293c186bbc337f9d70464df374d64b5e61
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203905"
---
# <a name="view-activity-logs-for-role-based-access-control-changes"></a>ロールベースのアクセス制御の変更のアクティビティ ログを表示する

サブスクリプション内のロール定義またはロール割り当てに変更が加えられたときは常に、変更が管理カテゴリの [Azure アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)に記録されます。 アクティビティ ログを確認すると、過去 90 日間のロールベースのアクセス制御 (RBAC) のすべての変更を確認できます。

## <a name="operations-that-are-logged"></a>ログに記録される操作

アクティビティ ログに記録される RBAC 関連の操作を次に示します。

- カスタムのロール定義の作成または更新
- カスタムのロール定義の削除
- ロール割り当ての作成
- ロール割り当ての削除

## <a name="azure-portal"></a>Azure ポータル

作業を開始する最も簡単な方法は、Azure Portal でアクティビティ ログを表示することです。 次のスクリーンショットは、**管理**カテゴリとロール定義およびロール割り当て操作を表示するようにフィルター処理されたアクティビティ ログの例を示しています。 また、ログを CSV ファイルとしてダウンロードするためのリンクも含まれています。

![Portal を使用したアクティビティ ログ - スクリーンショット](./media/change-history-report/activity-log-portal.png)

詳細については、[アクティビティ ログのイベントの表示](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)に関するページを参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してアクティビティ ログを表示するには、[Get AzureRmLog](/powershell/module/azurerm.insights/get-azurermlog) コマンドを使用します。

このコマンドでは、過去 7 日間のサブスクリプションのロール割り当てのすべての変更が一覧表示されます。

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

このコマンドでは、過去 7 日間のリソース グループのロール定義のすべての変更が一覧表示されます。

```azurepowershell
Get-AzureRmLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

このコマンドでは、過去 7 日間のサブスクリプションのロール割り当てとロール定義のすべての変更が一覧表示され、結果が一覧で表示されます。

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast"}}

```

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してアクティビティ ログを表示するには、[az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) コマンドを使用します。

このコマンドでは、開始時刻以降のリソース グループのアクティビティ ログが一覧表示されます。

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

このコマンドでは、開始時刻以降の承認リソース プロバイダーのアクティビティ ログが一覧表示されます。

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Azure Log Analytics](../log-analytics/log-analytics-overview.md) は、Azure のすべてのリソースのロールベースのアクセス制御の変更の収集および分析に使用できる、もう 1 つのツールです。 Log Analytics には、次のような利点があります。

- 複雑なクエリとロジックを記述する
- アラート、Power BI、およびその他のツールと統合する
- より長い保有期間でデータを保存する
- セキュリティ、仮想マシン、およびカスタムなど他のログを相互参照する

作業を開始する基本的な手順を次に示します。

1. [Log Analytics ワークスペースを作成します](../log-analytics/log-analytics-quick-create-workspace.md)。

1. ワークスペースの [Activity Log Analytics ソリューションを構成します](../log-analytics/log-analytics-activity.md#configuration)。

1. [アクティビティ ログを表示します](../log-analytics/log-analytics-activity.md#using-the-solution)。 Activity Log Analytics の [概要] ページに簡単に移動するには、**[Log Analytics]** オプションをクリックします。

   ![Portal の Log Analytics オプション](./media/change-history-report/azure-log-analytics-option.png)

1. 必要に応じて、[ログ検索](../log-analytics/log-analytics-log-search.md)ページまたは[高度な分析ポータル](https://docs.loganalytics.io/docs/Learn)を使用してログのクエリを実行し、ログを確認します。 これら 2 つのオプションの詳細については、[ログ検索ページまたは高度な分析ポータル](../log-analytics/log-analytics-log-search-portals.md)に関するページをご覧ください。

ターゲットのリソース プロバイダー別に編成された新しいロール割り当てを返すクエリを、次に示します。

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

グラフに表示されるロール割り当ての変更を返すクエリを、次に示します。

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![高度な分析ポータルを使用したアクティビティ ログ - スクリーンショット](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>次の手順
* [アクティビティ ログでのイベントの表示](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Azure アクティビティ ログでサブスクリプション アクティビティを監視する](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
