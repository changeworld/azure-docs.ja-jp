---
title: Azure Monitor で Azure アクティビティ ログ イベントを表示する
description: Azure Monitor で Azure アクティビティ ログを表示し、PowerShell、CLI、および REST API を使用して取得します。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 4ea29888d4dcf589e3e5d4dfe594f5f4bff2287e
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559979"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Azure アクティビティ ログ イベントを表示して取得する

[Azure アクティビティ ログ](platform-logs-overview.md)により、Azure で発生したサブスクリプションレベルのイベントの分析が得られます。 この記事では、アクティビティ ログ イベントの表示と取得のさまざまな方法について詳しく説明します。

## <a name="azure-portal"></a>Azure portal
すべてのリソースのアクティビティ ログは、Azure portal の **[モニター]** メニューから表示します。 特定のリソースのアクティビティ ログは、そのリソースのメニューの **[アクティビティ ログ]** オプションから表示します。

![アクティビティ ログを表示する](./media/activity-logs-overview/view-activity-log.png)

次のフィールドでアクティビティ ログをフィルター処理できます。

* **[期間]** :イベントの開始時刻と終了時刻。
* **カテゴリ**:「[アクティビティ ログのカテゴリ](activity-log-view.md#categories-in-the-activity-log)」で説明されているイベント カテゴリ。
* **サブスクリプション**:1 つまたは複数の Azure サブスクリプション名。
* **[リソース グループ]** :選択したサブスクリプション内の 1 つまたは複数のリソース グループ。
* **リソース (名前)** : 特定のリソースの名前。
* **[リソースの種類]** :リソースの種類 (_Microsoft.Compute/virtualmachines_ など)。
* **[操作名]** - Azure Resource Manager 操作の名前 (_Microsoft.SQL/servers/Write_ など)。
* **[重大度]** :イベントの重大度レベル。 使用可能な値は、_Informational_、_Warning_、_Error_、_Critical_ です。
* **イベント開始者**: 操作を実行したユーザー。
* **[検索を開く]** :すべてのイベントのすべてのフィールド全体で、その文字列を検索するテキスト検索ボックスを開きます。

## <a name="categories-in-the-activity-log"></a>アクティビティ ログのカテゴリ
アクティビティ ログの各イベントには、次の表に示す特定のカテゴリがあります。 これらのカテゴリのスキーマの詳細については、「[Azure アクティビティ ログのイベント スキーマ](activity-log-schema.md)」を参照してください。 

| カテゴリ | 説明 |
|:---|:---|
| 管理 | Resource Manager で実行されるすべての作成、更新、削除、アクション操作のレコードが含まれます。 管理イベントの例としては、_仮想マシンの作成_、_ネットワーク セキュリティ グループの削除_ があります。<br><br>Resource Manager を使用してユーザーまたはアプリケーションが実行するすべてのアクションは、特定のリソースの種類に対する操作としてモデル化されています。 操作の種類が  _書き込み_、_削除_、または _アクション_ の場合、その操作の開始のレコードと成功または失敗のレコードは、いずれも管理カテゴリに記録されます。 管理イベントには、サブスクリプション内のロールベースのアクセス制御に対する任意の変更も含まれています。 |
| サービス正常性 | Azure で発生した任意のサービス正常性インシデントのレコードが含まれます。 サービス正常性イベントの例としては、"_SQL Azure in East US is experiencing downtime_" (米国東部の SQL Azure でダウンタイムが発生しています) があります。 <br><br>Service Health のイベントは 6 種類に分かれます。"_要対応_"、"_支援復旧_"、"_インシデント_"、"_メンテナンス_"、"_情報_"、または "_セキュリティ_"。 これらのイベントが作成されるのは、サブスクリプション内にイベントの影響を受けるリソースがある場合のみです。
| リソース正常性 | Azure リソースで発生したすべてのリソース正常性イベントのレコードが含まれます。 リソース正常性イベントの例としては、"_Virtual Machine health status changed to unavailable_" (仮想マシンの正常性状態が使用不可に変更されました) があります。<br><br>リソース正常性イベントは、次の 4 つの正常性状態のいずれかを示す可能性があります。"_使用可能_"、"_使用不可_"、"_デグレード_"、および "_不明_"。 さらに、リソース正常性イベントは、"_Platform Initiated_" (プラットフォーム開始) または "_User Initiated_" (ユーザー開始) のいずれかのカテゴリに分けることができます。 |
| アラート: | Azure アラートのアクティブ化のレコードが含まれます。 アラート イベントの例としては、"_CPU % on myVM has been over 80 for the past 5 minutes_" (myVM の CPU % が過去 5 分間で 80 を超えています) があります。|
| 自動スケール | サブスクリプションで定義したすべての自動スケーリング設定に基づいて、自動スケーリング エンジンの操作に関連するすべてのイベントのレコードが含まれます。 自動スケーリングの例としては、"_Autoscale scale up action failed_" (自動スケーリングのスケールアップ アクションが失敗しました) があります。 |
| 推奨 | Azure Advisor からの推奨イベントが含まれます。 |
| Security | Azure Security Center によって生成されたアラートのレコードが含まれます。 セキュリティ イベントの例としては、"_Suspicious double extension file executed_" (疑わしい二重拡張子ファイルが実行されました) があります。 |
| ポリシー | Azure Policy によって実行されるすべての効果アクション操作のレコードが含まれます。 ポリシー イベントの例としては、"_監査_" と "_拒否_" があります。 Policy によって実行されるすべてのアクションは、リソースに対する操作としてモデル化されます。 |

## <a name="view-change-history"></a>変更履歴を表示する

アクティビティ ログを表示すると、イベントの時刻に発生した変更を確認できます。 この情報は **[変更履歴]** で表示できます。 アクティビティ ログから、詳細を確認したいイベントを選択します。 **[変更履歴 (プレビュー)]** タブを選択し、そのイベントに関連する変更を表示します。

![イベントの変更履歴の一覧](media/activity-logs-overview/change-history-event.png)

イベントに関連する変更がある場合は、変更の一覧が表示され、選択できます。 これにより、 **[変更履歴 (プレビュー)]** ページが開きます。 このページには、リソースに対する変更が表示されます。 次の例に示されているように、VM のサイズが変更されたことだけを示すのではなく、変更前の VM のサイズと変更後のサイズが表示されます。

![相違を示す変更履歴ページ](media/activity-logs-overview/change-history-event-details.png)

変更履歴の詳細については、「[リソースの変更の取得](../../governance/resource-graph/how-to/get-resource-changes.md)」を参照してください。






## <a name="powershell"></a>PowerShell
PowerShell からアクティビティ ログを取得するには、[Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) コマンドレットを使用します。 一般的な例を次に示します。

> [!NOTE]
> `Get-AzLog` は、15 日間の履歴のみを提供します。 **-MaxRecord** パラメーターを使用して、15 日間を超えて最後の N 件のイベントのクエリを実行します。 15 日より前のイベントにアクセスするには、REST API または SDK を使用します。 **StartTime** を指定しない場合、既定値は **EndTime** から 1 時間引いた値になります。 **EndTime**を指定しない場合、既定値は現在の時刻です。 時刻はすべて UTC 形式です。


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
Get-AzLog -MaxRecord 1000
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


## <a name="next-steps"></a>次のステップ

* [プラットフォーム ログの概要を確認する](platform-logs-overview.md)
* [他の送信先にアクティビティ ログを送信するための診断設定を作成する](diagnostic-settings.md)
