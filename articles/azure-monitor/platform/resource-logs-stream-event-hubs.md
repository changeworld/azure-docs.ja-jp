---
title: Azure プラットフォーム ログをイベント ハブにストリーミングする
description: サードパーティ製の SIEM やその他のログ分析ソリューションなどの外部システムにデータを送信するイベント ハブに、Azure リソース ログをストリーミングする方法について説明します。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658916"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Azure プラットフォーム ログを Azure Event Hubs にストリーミングする
Azure のアクティビティ ログとリソース ログを含む Azure の[プラットフォーム ログ](platform-logs-overview.md)では、Azure リソースとそれらが依存している Azure プラットフォームの詳細な診断情報と監査情報が提供されます。  この記事では、サードパーティ製の SIEM やその他のログ分析ソリューションなどの外部システムにデータを送信するイベント ハブに、プラットフォーム ログをストリーミングする方法について説明します。


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>イベント ハブに送信されたプラットフォーム ログを使用して何ができますか
Azure でプラットフォーム ログをイベント ハブにストリーミングして、次の機能を提供します。

* **サードパーティ製のログ記録およびテレメトリ システムへのログのストリーミング** – すべてのプラットフォーム ログを単一のイベント ハブにストリーミングして、ログ データをサードパーティ製の SIEM またはログ分析ツールにパイプします。
  
* **カスタムのテレメトリおよびログ プラットフォームを構築する** – 高い拡張性の公開サブスクライブを特長とするイベント ハブを使用することで、プラットフォーム ログをカスタム テレメトリ プラットフォームに柔軟に取り込むことができます。 詳細については、「[Azure Event Hubs でのグローバル スケール テレメトリ プラットフォームの設計とサイズ変更](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)」を参照してください。

* **データを Power BI にストリーミングしてサービスの正常性を表示する** - Event Hubs、Stream Analytics、Power BI を使用して、診断データをご利用の Azure サービスに関するほぼリアルタイムの分析情報に変換します。 このソリューションの詳細については、「[Stream Analytics と Power BI:ストリーミング データのリアルタイム分析ダッシュボード](../../stream-analytics/stream-analytics-power-bi-dashboard.md)」を参照してください。

    次の SQL コードは、Power BI テーブルのすべてのログ データの解析に使用できる Stream Analytics クエリの一例です。
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>前提条件
イベント ハブをまだ用意していない場合は、[イベント ハブを作成](../../event-hubs/event-hubs-create.md)する必要があります。 この Event Hubs 名前空間を使用している診断設定が既にある場合は、そのイベント ハブが再利用されます。

名前空間の共有アクセス ポリシーでは、ストリーミング メカニズムが保有するアクセス許可が定義されます。 Event Hubs にストリーミングするには、管理、送信、リッスンの各アクセス許可が必要です。 ご利用の Event Hubs 名前空間については、その共有アクセス ポリシーを Azure portal の [構成] タブで作成または変更できます。

ストリーミングを含めるように診断設定を更新するには、その Event Hubs の承認規則に対する ListKey アクセス許可が必要です。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っており、さらに両方のサブスクリプションが同じ AAD テナントに入っている限り、Event Hubs 名前空間は、ログを出力するサブスクリプションと同じサブスクリプションに属している必要はありません。

## <a name="create-a-diagnostic-setting"></a>診断設定の作成
Azure リソース用の診断設定を作成して、イベント ハブやその他の送信先にプラットフォーム ログを送信します。 詳細については、「[Azure でログとメトリックを収集するための診断設定を作成する](diagnostic-settings.md)」を参照してください。

## <a name="collect-data-from-compute-resources"></a>コンピューティング リソースからのデータの収集
診断設定によって、他のリソースと同様に、Azure コンピューティング リソースのリソース ログが収集されますが、ゲスト オペレーティング システムやワークロードについては収集されません。 このデータを収集するには、[Log Analytics エージェント](log-analytics-agent.md)をインストールします。 


## <a name="consuming-log-data-from-event-hubs"></a>イベント ハブからのログ データを使用する
イベント ハブからのプラットフォーム ログは、次の表内の要素を含む JSON 形式で使用されます。

| 要素名 | 説明 |
| --- | --- |
| records |このペイロード内のすべてのログ イベントの配列。 |
| time |イベントが発生した時刻。 |
| category |このイベントのログ カテゴリ。 |
| resourceId |このイベントを生成したリソースのリソース ID。 |
| operationName |操作の名前。 |
| level |省略可能。 ログ イベントのレベル。 |
| properties |イベントのプロパティ。 これらは、[]() で説明されているように、Azure サービスごとに異なります。 |


次に示すのは、リソース ログの場合の Event Hubs からのサンプル出力データです。

```json
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



## <a name="next-steps"></a>次のステップ

* [リソース ログの詳細について読む](platform-logs-overview.md)
* [Azure でログとメトリックを収集するための診断設定を作成する](diagnostic-settings.md)。
* [Azure Monitor による Azure Active Directory ログのストリーム](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。
* [Event Hubs の使用](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。

