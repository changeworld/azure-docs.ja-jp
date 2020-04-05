---
title: チュートリアル:コードを使わずに Azure Data Explorer で監視データを取り込む
description: このチュートリアルでは、コードを 1 行も書かずに Azure Data Explorer に監視データを取り込み、そのデータにクエリを実行する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 3a53a660da2257540f23bc6438fc5933e5229c76
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "78198050"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>チュートリアル:Azure Data Explorer で監視データを取り込んでクエリを実行する 

このチュートリアルでは、コードを書くことなく、診断ログとアクティビティ ログから Azure Data Explorer クラスターにデータを取り込む方法について説明します。 この簡単な取り込み方法を使用すると、データ分析のために Azure Data Explorer のクエリをすぐに開始できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Data Explorer のデータベースに、テーブルと取り込みのマッピングを作成します。
> * 更新ポリシーを使用して、取り込まれたデータの形式を設定します。
> * [イベント ハブ](/azure/event-hubs/event-hubs-about)を作成し、それを Azure Data Explorer に接続します。
> * Azure Monitor の[診断メトリックとログ](/azure/azure-monitor/platform/diagnostic-settings)、[アクティビティ ログ](/azure/azure-monitor/platform/activity-logs-overview)からイベント ハブにデータをストリーム配信します。
> * Azure Data Explorer を使用して、取り込まれたデータのクエリを実行します。

> [!NOTE]
> 同じ Azure の場所またはリージョンに、すべてのリソースを作成します。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)。 このチュートリアルでのデータベース名は *TestDatabase* です。

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure Monitor データ プロバイダー: 診断メトリックとログ、アクティビティ ログ

以下の Azure Monitor の診断メトリックとログ、アクティビティ ログによって提供されるデータを見て理解します。 これらのデータ スキーマに基づいて、インジェスト パイプラインを作成してみましょう。 ログの各イベントにはレコードの配列が含まれます。 このチュートリアルの後半では、このレコードの配列が分割されます。

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>診断メトリックとログおよびアクティビティ ログの例

Azure の診断メトリックとログ、アクティビティ ログは、Azure サービスによって生成され、そのサービスの動作に関するデータを提供します。 

# <a name="diagnostic-metrics"></a>[診断メトリック](#tab/diagnostic-metrics)
#### <a name="example"></a>例

診断メトリックは、1 分の時間グレインで集計されます。 クエリ期間に対する Azure Data Explorer メトリックイベント スキーマの例を次に示します。

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[診断ログ](#tab/diagnostic-logs)
#### <a name="example"></a>例

Azure Data Explorer の[診断インジェスト ログ](using-diagnostic-logs.md#diagnostic-logs-schema)の例を次に示します。

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[アクティビティ ログ](#tab/activity-logs)
#### <a name="example"></a>例

Azure アクティビティ ログは、サブスクリプションのリソースに対して実行された操作を分析できるサブスクリプション レベルのログです。 アクセスを確認するためのアクティビティログ イベントの例を次に示します。

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Azure Data Explorer で取り込みパイプラインを設定する

Azure Data Explorer のパイプラインの設定には、[テーブルの作成やデータの取り込み](/azure/data-explorer/ingest-sample-data#ingest-data)など、いくつかの手順が含まれています。 データの操作、マップ、更新を行うこともできます。

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Azure Data Explorer の Web UI に接続する

Azure Data Explorer の *TestDatabase* データベースで **[クエリ]** を選択して、Azure Data Explorer の Web UI を開きます。

![[クエリ] ページ](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>ターゲット テーブルを作成する

Azure Monitor ログの構造は表形式ではありません。 データを操作し、各イベントを 1 つまたは複数のレコードに展開します。 生データは、アクティビティ ログの場合は *ActivityLogsRawRecords*、診断メトリックとログの場合は *DiagnosticRawRecords* という中間テーブルに取り込まれます。 その時点で、データを操作および展開します。 更新ポリシーを使用すると、展開されたデータは、アクティビティ ログの場合は *ActivityLogs* テーブル、診断メトリックの場合は *DiagnosticMetrics*、診断ログの場合は *DiagnosticLogs* に取り込まれます。 つまり、アクティビティ ログを取り込むには 2 つの異なるテーブルを作成し、診断メトリックと診断ログを取り込むには 3 つの異なるテーブルを作成する必要があります。

Azure Data Explorer の Web UI を使用して、Azure Data Explorer データベースにターゲット テーブルを作成します。

# <a name="diagnostic-metrics"></a>[診断メトリック](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>診断メトリックのためのテーブルを作成する

1. *TestDatabase* データベースで、診断メトリック レコードを格納する *DiagnosticMetrics* という名前のテーブルを作成します。 次の `.create table` 管理コマンドを使用します。

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. **[実行]** を選択してテーブルを作成します。

    ![Run query](media/ingest-data-no-code/run-query.png)

1. 次のクエリを使用して、データ操作用の *DiagnosticRawRecords* という名前の中間データ テーブルを *TestDatabase* データベース内に作成します。 **[実行]** を選択してテーブルを作成します。

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. 中間テーブルの[アイテム保持ポリシー](/azure/kusto/management/retention-policy)をゼロに設定します。

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[診断ログ](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>診断ログのためのテーブルを作成する 

1. *TestDatabase* データベースで、診断ログ レコードを格納する *DiagnosticLogs* という名前のテーブルを作成します。 次の `.create table` 管理コマンドを使用します。

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. **[実行]** を選択してテーブルを作成します。

1. 次のクエリを使用して、データ操作用の *DiagnosticRawRecords* という名前の中間データ テーブルを *TestDatabase* データベース内に作成します。 **[実行]** を選択してテーブルを作成します。

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. 中間テーブルの[アイテム保持ポリシー](/azure/kusto/management/retention-policy)をゼロに設定します。

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[アクティビティ ログ](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>アクティビティ ログのためのテーブルを作成する 

1. アクティビティ ログ レコードを受け取る *ActivityLogs* という名前のテーブルを *TestDatabase* データベース内に作成します。 テーブルを作成するには、次の Azure Data Explorer クエリを実行します。

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. データ操作用の *ActivityLogsRawRecords* という名前の中間データ テーブルを *TestDatabase* データベース内に作成します。

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. 中間テーブルの[アイテム保持ポリシー](/azure/kusto/management/retention-policy)をゼロに設定します。

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>テーブル マッピングを作成する

 データ形式が `json` であるため、データのマッピングが必須です。 `json` のマッピングでは、json の各パスがテーブルの列名にマップされます。

# <a name="diagnostic-metrics--diagnostic-logs"></a>[診断メトリックと診断ログ](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>診断メトリックとログをテーブルにマップする

診断メトリックとログのデータをテーブルにマップするには、次のクエリを使用します。

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[アクティビティ ログ](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>アクティビティ ログをテーブルにマップする

アクティビティ ログのデータをテーブルにマップするには、次のクエリを使用します。

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>メトリックおよびログのデータの更新ポリシーを作成する

# <a name="diagnostic-metrics"></a>[診断メトリック](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>診断メトリックのデータ更新ポリシーを作成する

1. コレクション内の各値が個別の行を受け取るように診断メトリック レコードのコレクションを展開する[関数](/azure/kusto/management/functions)を作成します。 [`mv-expand`](/azure/kusto/query/mvexpandoperator) 演算子を使用します。
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. [更新ポリシー](/azure/kusto/concepts/updatepolicy)をターゲット テーブルに追加します。 このポリシーでは、*DiagnosticRawRecords* 中間データ テーブルに新しく取り込まれたデータに対してクエリが自動的に実行され、その結果が *DiagnosticMetrics* テーブルに取り込まれます。

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[診断ログ](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>診断ログのデータ更新ポリシーを作成する

1. コレクション内の各値が個別の行を受け取るように診断ログ レコードのコレクションを展開する[関数](/azure/kusto/management/functions)を作成します。 Azure Data Explorer クラスターでインジェスト ログを有効にし、[インジェスト ログ スキーマ](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema)を使用します。 成功したインジェストと失敗したインジェスト用にテーブルを 1 つ作成します。インジェストが成功した場合は、いくつかのフィールド (ErrorCode など) が空になります。 [`mv-expand`](/azure/kusto/query/mvexpandoperator) 演算子を使用します。

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. [更新ポリシー](/azure/kusto/concepts/updatepolicy)をターゲット テーブルに追加します。 このポリシーでは、*DiagnosticRawRecords* 中間データ テーブルに新しく取り込まれたデータに対してクエリが自動的に実行され、その結果が *DiagnosticLogs* テーブルに取り込まれます。

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[アクティビティ ログ](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>アクティビティ ログのデータ更新ポリシーを作成する

1. コレクション内の各値が個別の行を受け取るようにアクティビティ ログ レコードのコレクションを展開する[関数](/azure/kusto/management/functions)を作成します。 [`mv-expand`](/azure/kusto/query/mvexpandoperator) 演算子を使用します。

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. [更新ポリシー](/azure/kusto/concepts/updatepolicy)をターゲット テーブルに追加します。 このポリシーでは、*ActivityLogsRawRecords* 中間データ テーブルに新しく取り込まれたデータに対してクエリが自動的に実行され、その結果が *ActivityLogs* テーブルに取り込まれます。

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs 名前空間を作成する

Azure 診断設定により、ストレージ アカウントまたはイベント ハブへのメトリックとログのエクスポートが可能になります。 このチュートリアルでは、イベント ハブ経由でメトリックとログをルーティングします。 診断メトリックとログ用のイベント ハブと Event Hubs 名前空間を次の手順で作成します。 Azure Monitor では、アクティビティ ログ用にイベント ハブ *insights-operational-logs* が作成されます。

1. Azure portal で Azure Resource Manager テンプレートを使用して、イベント ハブを作成します。 この記事の残りの手順を実行するには、 **[Azure へのデプロイ]** ボタンを右クリックして、 **[新しいウィンドウで開く]** を選択します。 **[Azure へのデプロイ]** ボタンをクリックすると、Azure portal に移動します。

    [![[Azure へのデプロイ] ボタン](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Event Hubs 名前空間と、診断ログ用のイベント ハブを作成します。

    ![イベント ハブの作成](media/ingest-data-no-code/event-hub.png)

1. フォームに次の情報を入力します。 次の表に記載されていない設定については、既定値を使用してください。

    **設定** | **推奨値** | **説明**
    |---|---|---|
    | **サブスクリプション** | *該当するサブスクリプション* | イベント ハブに使用する Azure サブスクリプションを選択します。|
    | **リソース グループ** | *test-resource-group* | 新しいリソース グループを作成します。 |
    | **場所** | ニーズに最も適したリージョンを選択します。 | 他のリソースと同じ場所に、Event Hubs 名前空間を作成します。
    | **名前空間名** | *AzureMonitoringData* | 名前空間を識別する一意の名前を選択します。
    | **イベント ハブ名** | *DiagnosticData* | イベント ハブは、固有のスコープ コンテナーを提供する名前空間以下にあります。 |
    | **コンシューマー グループ名** | *adxpipeline* | コンシューマー グループ名を作成します。 コンシューマー グループを使用すると、複数の使用アプリケーションがそれぞれイベント ストリーム ビューを持つことができるようになります。 |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Azure Monitor のメトリックとログをイベント ハブに接続する

ここで、診断メトリックとログ、アクティビティ ログをイベント ハブに接続する必要があります。

# <a name="diagnostic-metrics--diagnostic-logs"></a>[診断メトリックと診断ログ](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>診断メトリックとログをイベント ハブに接続する

メトリックをエクスポートするリソースを選択します。 診断データのエクスポートは、Event Hubs 名前空間、Azure Key Vault、Azure IoT Hub、Azure Data Explorer クラスターなど、複数のリソースの種類でサポートされます。 このチュートリアルでは、Azure Data Explorer クラスターをリソースとして使用し、クエリ パフォーマンスのメトリックとインジェスト結果のログを確認します。

1. Azure portal でお使いの Kusto クラスターを選択します。
1. **[診断設定]** を選択してから、 **[診断をオンにする]** リンクを選択します。 

    ![診断設定](media/ingest-data-no-code/diagnostic-settings.png)

1. **[診断設定]** ウィンドウが開きます。 次の手順を実行します。
   1. 診断ログ データに *ADXExportedData* という名前を付けます。
   1. **[ログ]** で、 **[SucceededIngestion]** と **[FailedIngestion]** の両方のチェック ボックスをオンにします。
   1. **[メトリック]** の **[クエリ パフォーマンス]** チェック ボックスをオンにします。
   1. **[イベント ハブへのストリーム]** チェック ボックスをオンにします。
   1. **[構成]** をクリックします。

      ![[診断設定] ウィンドウ](media/ingest-data-no-code/diagnostic-settings-window.png)

1. **[イベント ハブの選択]** ウィンドウで、自分が作成したイベント ハブに診断ログからデータをエクスポートする方法を構成します。
    1. **[イベント ハブの名前空間の選択]** リストで、*AzureMonitoringData* を選択します。
    1. **[イベント ハブ名を選択する]** リストで、*DiagnosticData* を選択します。
    1. **[イベント ハブ ポリシー名の選択]** リストで、**RootManagerSharedAccessKey** を選択します。
    1. **[OK]** を選択します。

1. **[保存]** を選択します。

# <a name="activity-logs"></a>[アクティビティ ログ](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>アクティビティ ログをイベント ハブに接続する

1. Azure portal の左側のメニューで、 **[アクティビティ ログ]** を選択します。
1. **[アクティビティ ログ]** ウィンドウが開きます。 **[イベント ハブにエクスポート]** を選択します。

    ![[アクティビティ ログ] ウィンドウ](media/ingest-data-no-code/activity-log.png)

1. **[アクティビティ ログのエクスポート]** ウィンドウが開きます。
 
    ![[アクティビティ ログのエクスポート] ウィンドウ](media/ingest-data-no-code/export-activity-log.png)

1. **[アクティビティ ログのエクスポート]** ウィンドウで、次の手順を実行します。
      1. サブスクリプションを選択します。
      1. **[リージョン]** リストで、 **[すべて選択]** を選択します。
      1. **[イベント ハブにエクスポート]** チェック ボックスをオンにします。
      1. **[Service Bus 名前空間を選択してください。]** を選択して、 **[イベント ハブの選択]** ウィンドウを開きます。
      1. **[イベント ハブの選択]** ウィンドウで、ご自分のサブスクリプションを選択します。
      1. **[イベント ハブの名前空間の選択]** リストで、*AzureMonitoringData* を選択します。
      1. **[イベント ハブ ポリシー名の選択]** リストで、既定のイベント ハブ ポリシー名を選択します。
      1. **[OK]** を選択します。
      1. ウィンドウの左上隅にある **[保存]** を選択します。
   *insights-operational-logs* という名前のイベント ハブが作成されます。
---

### <a name="see-data-flowing-to-your-event-hubs"></a>イベント ハブに送られてくるデータを確認する

1. 接続が定義され、イベント ハブへのアクティビティログのエクスポートが完了するまで、数分待ちます。 ご自分の Event Hubs 名前空間に移動して、自分が作成したイベント ハブを確認します。

    ![作成されたイベント ハブ](media/ingest-data-no-code/event-hubs-created.png)

1. イベント ハブに送られてくるデータを確認します。

    ![イベント ハブのデータ](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>イベント ハブを Azure Data Explorer に接続する

次に、診断メトリックとログ、アクティビティ ログのデータ接続を作成する必要があります。

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>診断メトリックとログ、アクティビティ ログのデータ接続を作成する

1. *kustodocs* という名前の Azure Data Explorer クラスターで、左側のメニューの **[データベース]** を選択します。
1. **[データベース]** ウィンドウで、ご自分の *TestDatabase* データベースを選択します。
1. 左側のメニューで、 **[データ インジェスト]** を選択します。
1. **[データ インジェスト]** ウィンドウで、 **[+ データ接続の追加]** をクリックします。
1. **[データ接続]** ウィンドウで、次の情報を入力します。

    ![イベント ハブのデータ接続](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[診断メトリックと診断ログ](#tab/diagnostic-metrics+diagnostic-logs) 

1. **[データ接続]** ウィンドウで、次の設定を使用します。

    データ ソース:

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | **データ接続名** | *DiagnosticsLogsConnection* | Azure データ エクスプローラーで作成する接続の名前。|
    | **イベント ハブの名前空間** | *AzureMonitoringData* | 以前に選択した、名前空間を識別する名前。 |
    | **イベント ハブ** | *DiagnosticData* | 作成したイベント ハブ。 |
    | **コンシューマー グループ** | *adxpipeline* | 作成したイベント ハブに定義されているコンシューマー グループ。 |
    | | |

    ターゲット テーブル:

    ルーティングには、"*静的*" と "*動的*" という 2 つのオプションがあります。 このチュートリアルでは、静的ルーティング (既定) を使用し、テーブル名、データ形式、およびマッピングを指定します。 **[My data includes routing info]\(データにルーティング情報が含まれている\)** をオフのままにしておきます。

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | **Table** | *DiagnosticRawRecords* | 自分が *TestDatabase* データベースに作成したテーブル。 |
    | **データ形式** | *JSON* | テーブルで使用される形式。 |
    | **列マッピング** | *DiagnosticRawRecordsMapping* | 自分が *TestDatabase* データベースに作成したマッピング。これにより、受信 JSON データが *DiagnosticRawRecords* テーブルの列名とデータ型にマッピングされます。|
    | | |

1. **［作成］** を選択します  

# <a name="activity-logs"></a>[アクティビティ ログ](#tab/activity-logs)

1. **[データ接続]** ウィンドウで、次の設定を使用します。

    データ ソース:

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | **データ接続名** | *ActivityLogsConnection* | Azure データ エクスプローラーで作成する接続の名前。|
    | **イベント ハブの名前空間** | *AzureMonitoringData* | 以前に選択した、名前空間を識別する名前。 |
    | **イベント ハブ** | *insights-operational-logs* | 作成したイベント ハブ。 |
    | **コンシューマー グループ** | *$Default* | 既定のコンシューマー グループ。 必要な場合は、異なるコンシューマー グループを作成できます。 |
    | | |

    ターゲット テーブル:

    ルーティングには、"*静的*" と "*動的*" という 2 つのオプションがあります。 このチュートリアルでは、静的ルーティング (既定) を使用し、テーブル名、データ形式、およびマッピングを指定します。 **[My data includes routing info]\(データにルーティング情報が含まれている\)** をオフのままにしておきます。

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | **Table** | *ActivityLogsRawRecords* | 自分が *TestDatabase* データベースに作成したテーブル。 |
    | **データ形式** | *JSON* | テーブルで使用される形式。 |
    | **列マッピング** | *ActivityLogsRawRecordsMapping* | 自分が *TestDatabase* データベースに作成したマッピング。これにより、受信 JSON データが *ActivityLogsRawRecords* の列名とデータ型にマッピングされます。|
    | | |

1. **［作成］** を選択します  
---

## <a name="query-the-new-tables"></a>新しいテーブルのクエリを実行する

パイプラインをデータが流れるようになりました。 クラスター経由での取り込みには既定で 5 分かかるので、データが流れるのを数分待ってから、クエリを始めます。

# <a name="diagnostic-metrics"></a>[診断メトリック](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>診断メトリック テーブルに対してクエリを実行する

次のクエリでは、Azure Data Explorer の診断メトリック レコードからクエリ期間のデータを分析します。

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

クエリの結果:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logs"></a>[診断ログ](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>診断ログ テーブルに対してクエリを実行する

このパイプラインからは、イベント ハブを介してインジェストが生成されます。 それらのインジェストの結果を確認します。
次のクエリでは、1 分間に生じたインジェストの数を分析します (各間隔の `Database`、`Table`、`IngestionSourcePath` のサンプルを含む)。

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

クエリの結果:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logs"></a>[アクティビティ ログ](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>アクティビティ ログ テーブルに対してクエリを実行する

次のクエリでは、Azure Data Explorer のアクティビティ ログ レコードのデータを分析します。

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

クエリの結果:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>次のステップ

* 「[Azure Data Explorer のクエリを記述する](write-queries.md)」を使用して、Azure Data Explorer から抽出したデータに対するさらに多くのクエリを記述する方法を学習します。
* [診断ログを使用して Azure Data Explorer インジェスト操作を監視する](using-diagnostic-logs.md)
* [メトリックを使用してクラスターの正常性を監視する](using-metrics.md)
