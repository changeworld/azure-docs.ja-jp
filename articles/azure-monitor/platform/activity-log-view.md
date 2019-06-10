---
title: Azure Monitor で Azure アクティビティ ログ イベントを表示する
description: Azure Monitor で Azure アクティビティ ログを表示し、PowerShell、CLI、および REST API を使用して取得します。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245594"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Azure アクティビティ ログ イベントを表示して取得する

[Azure アクティビティ ログ](activity-logs-overview.md)により、Azure で発生したサブスクリプションレベルのイベントの分析が得られます。 この記事では、アクティビティ ログ イベントの表示と取得のさまざまな方法について詳しく説明します。

## <a name="azure-portal"></a>Azure ポータル
すべてのリソースのアクティビティ ログは、Azure portal の **[モニター]** メニューから表示します。 特定のリソースのアクティビティ ログは、そのリソースのメニューの **[アクティビティ ログ]** オプションから表示します。

![アクティビティ ログを表示する](./media/activity-logs-overview/view-activity-log.png)

次のフィールドでアクティビティ ログをフィルター処理できます。

* **[期間]** :イベントの開始時刻と終了時刻。
* **[カテゴリ]** :「[アクティビティ ログのカテゴリ](activity-logs-overview.md#categories-in-the-activity-log)」で説明されているイベント カテゴリ。
* **サブスクリプション**:1 つまたは複数の Azure サブスクリプション名。
* **[リソース グループ]** :選択したサブスクリプション内の 1 つまたは複数のリソース グループ。
* **リソース (名前)** : 特定のリソースの名前。
* **[リソースの種類]** :リソースの種類 (_Microsoft.Compute/virtualmachines_ など)。
* **[操作名]** - Azure Resource Manager 操作の名前 (_Microsoft.SQL/servers/Write_ など)。
* **[重大度]** :イベントの重大度レベル。 使用可能な値は、_Informational_、_Warning_、_Error_、_Critical_ です。
* **[イベント開始者]** : 操作を実行したユーザー。
* **[検索を開く]** :すべてのイベントのすべてのフィールド全体で、その文字列を検索するテキスト検索ボックスを開きます。

### <a name="view-change-history"></a>変更履歴を表示する

アクティビティ ログを表示すると、イベントの時刻に発生した変更を確認できます。 この情報は **[変更履歴]** で表示できます。 アクティビティ ログから、詳細を確認したいイベントを選択します。 **[変更履歴 (プレビュー)]** タブを選択し、そのイベントに関連する変更を表示します。

![イベントの変更履歴の一覧](media/activity-logs-overview/change-history-event.png)

イベントに関連する変更がある場合は、変更の一覧が表示され、選択できます。 これにより、 **[変更履歴 (プレビュー)]** ページが開きます。 このページには、リソースに対する変更が表示されます。 次の例に示されているように、VM のサイズが変更されたことだけを示すのではなく、変更前の VM のサイズと変更後のサイズが表示されます。

![相違を示す変更履歴ページ](media/activity-logs-overview/change-history-event-details.png)

変更履歴の詳細については、「[リソースの変更の取得](../../governance/resource-graph/how-to/get-resource-changes.md)」を参照してください。


## <a name="log-analytics-workspace"></a>Log Analytics ワークスペース
**[アクティビティ ログ]** ページの上部にある **[ログ]** をクリックして、サブスクリプションのための [Activity Log Analytics 監視ソリューション](activity-log-collect.md)を開きます。 これにより、アクティビティ ログの分析を表示し、**AzureActivity** テーブルで[ログ クエリ](../log-query/log-query-overview.md)を実行できます。 アクティビティ ログが Log Analytics ワークスペースに接続されていない場合は、この構成を実行するように求められます。



## <a name="powershell"></a>PowerShell
PowerShell からアクティビティ ログを取得するには、[Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) コマンドレットを使用します。 一般的な例を次に示します。

> [!NOTE]
> `Get-AzLog` は、15 日間の履歴のみを提供します。 **-MaxEvents** パラメーターを使用して、15 日間を超えて最後の N 件のイベントをクエリします。 15 日より前のイベントにアクセスするには、REST API または SDK を使用します。 **StartTime** を指定しない場合、既定値は **EndTime** から 1 時間引いた値になります。 **EndTime**を指定しない場合、既定値は現在の時刻です。 時刻はすべて UTC 形式です。


特定の日時以降に作成されたログ エントリを取得します。

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

日時の範囲間のログ エントリを取得します。

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

特定のリソース グループのログ エントリを取得します。

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

日時の範囲間の特定のリソース プロバイダーのログ エントリを取得します。

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

特定の呼び出し元のログ エントリを取得します。

```powershell
Get-AzLog -Caller 'myname@company.com'
```

過去 1,000 件のイベントを取得します。

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
CLI からアクティビティ ログ エントリを取得するには、[az monitor activity-log](cli-samples.md#view-activity-log-for-a-subscription) を使用します。 一般的な例を次に示します。


使用可能なすべてのオプションを表示します。

```azurecli
az monitor activity-log list -h
```

特定のリソース グループのログ エントリを取得します。

```azurecli
az monitor activity-log list --resource-group <group name>
```

特定の呼び出し元のログ エントリを取得します。

```azurecli
az monitor activity-log list --caller myname@company.com
```

日付の範囲内の特定のリソースの種類で、呼び出し元別にログを取得します。

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
REST クライアントからアクティビティ ログを取得するには、[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) を使用します。 一般的な例を次に示します。

フィルターを使用してアクティビティ ログを取得します。

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

フィルターと選択を使用してアクティビティ ログを取得します。

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

選択を使用してアクティビティ ログを取得します。

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

フィルターや選択を使用せずにアクティビティ ログを取得します。

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>次の手順

* [アクティビティ ログの概要を確認する](activity-logs-overview.md)
* [アクティビティ ログをストレージにアーカイブするか、または Event Hubs にストリーミングする](activity-log-export.md)
* [Azure アクティビティ ログを Event Hubs にストリーミングする](activity-logs-stream-event-hubs.md)
* [Azure アクティビティ ログをストレージにアーカイブする](archive-activity-log.md)

