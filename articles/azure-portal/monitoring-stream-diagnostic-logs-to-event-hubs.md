<properties
	pageTitle="Event Hubs への Azure 診断ログのストリーミング | Microsoft Azure"
	description="Azure 診断ログを Event Hubs にストリーミングする方法について説明します。"
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="johnkem"/>

# Event Hubs への Azure 診断ログのストリーミング

**[Azure 診断ログ](monitoring-overview-of-diagnostic-logs.md)**は、ポータルに組み込まれた [Event Hubs にエクスポート] オプションを使用するか、Azure PowerShell コマンドレットまたは Azure CLI を使用して診断設定で Service Bus 規則 ID を有効にすることによって、任意のアプリケーションにほぼリアルタイムでストリーミングできます。

## 診断ログと Event Hubs で実行できること
診断ログでストリーミング機能を使用する場合、次のような方法があります。

- **サード パーティのログおよびテレメトリ システムにログをストリーミングする** - 将来的に、Event Hubs のストリーミングは、診断ログをサード パーティの SIEM やログ分析ソリューションにパイプ処理するためのメカニズムになります。

- **"ホットパス" データを PowerBI にストリーミングしてサービスの正常性を表示する** - Event Hubs、Stream Analytics、PowerBI を使用して、診断データを Azure サービスに関するほぼリアルタイムの洞察に簡単に変換できます。Event Hubs をセットアップし、Stream Analytics でデータを処理して、PowerBI を出力として使用する方法の概要については、[こちらの記事](../stream-analytics/stream-analytics-power-bi-dashboard.md)をご覧ください。診断ログを設定する際のヒントを次に示します。
	- オプションをポータルで有効にするか、PowerShell を使用して有効にすると、診断ログの特定のカテゴリの Event Hubs が自動的に作成されます。そのため、名前が "insights-" で始まる Service Bus 名前空間の Event Hubs を選択できます。
	- PowerBI テーブルのすべてのログ データの解析に使用できるサンプルの Stream Analytics クエリを次に示します。

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **カスタムのテレメトリおよびログ プラットフォームを構築する** - カスタム構築されたテレメトリ プラットフォームが既にある場合や構築を検討している場合は、Event Hubs の非常にスケーラブルな発行/サブスクライブの特性により、診断ログを柔軟に取り込むことができます。グローバル規模のテレメトリ プラットフォームで Event Hubs を使用する方法については、[Dan Rosanova によるガイド](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)をご覧ください。

## 診断ログのストリーミングの有効化
診断ログのストリーミングは、プログラム、ポータル、または [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx) を使用して有効することができます。どの方法でも、Service Bus 名前空間を選択すると、有効にしたログ カテゴリごとに Event Hubs がその名前空間に作成されます。診断**ログ カテゴリ**とは、リソースで収集できるログの種類です。Azure ポータルの [診断] ブレードで、特定のリソースで収集するログ カテゴリを選択できます。

![ポータルのログ カテゴリ](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] コンピューティング リソース (VM や Service Fabric など) の診断ログを有効にし、ストリーミングするには、[別の手順が必要](../event-hubs/event-hubs-streaming-azure-diags-data.md)となります。

### PowerShell コマンドレットの使用
[Azure PowerShell コマンドレット](insights-powershell-samples.md)を使用してストリーミングを有効にするには、次のパラメーターを指定して `Set-AzureRmDiagnosticSetting` コマンドレットを使用します。

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

Service Bus 規則 ID は、`{service bus resource ID}/authorizationrules/{key name}` の形式の文字列です。たとえば、`/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey` のようになります。


### Azure CLI の使用
[Azure CLI](insights-cli-samples.md) を使用してストリーミングを有効にするには、次のように `insights diagnostic set` コマンドを使用します。

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Service Bus 規則 ID には、PowerShell コマンドレットのセクションで説明したものと同じ形式を使用します。

###Azure ポータルの使用
Azure ポータルを使用してストリーミングを有効にするには、リソースの診断設定に移動し、[Event Hubs にエクスポート] を選択します。

![ポータルの [Event Hubs にエクスポート]](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

これを構成するには、既存の Service Bus 名前空間を選択します。診断ログを初めてストリーミングする場合は、選択した名前空間に Event Hubs が作成されます。そのログ カテゴリを選択した名前空間にストリーミングするリソースが既に存在する場合は、その名前空間に Event Hubs がストリーミングされます。また、ストリーミング メカニズムで使用するアクセス許可をポリシーで定義します。現在、Event Hubs にストリーミングするには、管理、読み取り、送信の各アクセス許可が必要です。Service Bus 名前空間の共有アクセス ポリシーは、クラシック ポータルの Service Bus 名前空間の [構成] タブで作成または変更できます。これらの診断設定のいずれかを更新するには、クライアントに Service Bus の承認規則に対する ListKey アクセス許可が必要です。

##Event Hubs からログ データを使用する方法
Event Hubs からのサンプル出力データを次に示します。

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| 要素名 | Description |
|--------------|--------------------------------------------------------|
|records | このペイロード内のすべてのログ イベントの配列。 |
|time | イベントが発生した時刻。 |
|カテゴリ | このイベントのログ カテゴリ。 |
|resourceId | このイベントを生成したリソースのリソース ID。 |
|operationName | 操作の名前。 |
|level | 省略可能。ログ イベントのレベル。 |
|プロパティ | イベントのプロパティ。 |


Event Hubs へのストリーミングをサポートするすべてのリソース プロバイダーの一覧については、[こちら](monitoring-overview-of-diagnostic-logs.md)をご覧ください。

##次のステップ
- [Azure 診断ログの詳細を確認する](monitoring-overview-of-diagnostic-logs.md)
- [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

<!---HONumber=AcomDC_0817_2016-->