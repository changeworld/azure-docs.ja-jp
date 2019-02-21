---
title: チュートリアル:コードを 1 行も書かずに Azure Data Explorer で診断とアクティビティのログ データを取り込む
description: このチュートリアルでは、コードを 1 行も書かずに Azure Data Explorer にデータを取り込み、そのデータのクエリを実行する方法について説明します。
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 145a56bee857debdbf028834a3ed378efd8671c8
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447499"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>チュートリアル:コードを 1 行も書かずに Azure Data Explorer にデータを取り込む

このチュートリアルでは、1 行のコードも書かずに、診断とアクティビティのログ データを Azure Data Explorer クラスターに取り込む方法について説明します。 この簡単な取り込み方法を使用すると、データ分析のための Azure Data Explorer のクエリをすぐに始めることができます。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure Data Explorer のデータベースに、テーブルと取り込みのマッピングを作成します。
> * 更新ポリシーを使用して、取り込まれたデータの書式を設定します。
> * [イベント ハブ](/azure/event-hubs/event-hubs-about)を作成し、Azure Data Explorer に接続します。
> * [Azure Monitor 診断ログ](/azure/azure-monitor/platform/diagnostic-logs-overview)と [Azure Monitor アクティビティ ログ](/azure/azure-monitor/platform/activity-logs-overview)からイベント ハブにデータをストリーム配信します。
> * Azure Data Explorer を使用して取り込まれたデータのクエリを実行します。

> [!NOTE]
> 同じ Azure の場所/リージョンに、すべてのリソースを作成します。 これは、Azure Monitor 診断ログの要件です。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)。 このチュートリアルでのデータベース名は *AzureMonitoring* です。

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Azure Monitoring データ プロバイダー - 診断ログとアクティビティ ログ

Azure Monitoring の診断ログとアクティビティ ログによって提供されたデータを表示して理解します。 これらのデータ スキーマに基づいて、取り込みパイプラインを作成します。

### <a name="diagnostic-logs-example"></a>診断ログの例

Azure 診断ログは、Azure サービスによって生成され、そのサービスの動作に関するデータを提供するメトリックです。 データは、1 分の時間グレインで集計されます。 各診断ログ イベントには、1 つのレコードが含まれます。 クエリ期間についての Azure Data Explorer メトリック イベント スキーマの例を次に示します。

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>アクティビティ ログの例

Azure アクティビティ ログは、レコードのコレクションが含まれるサブスクリプション レベルのログです。 ログでは、サブスクリプション内のリソースで実行された操作についての分析情報が提供されます。 診断ログとは異なり、アクティビティ ログのイベントにはレコードの配列が含まれます。 このチュートリアルでは後ほど、このレコードの配列を分割する必要があります。 アクセスを確認するためのアクティビティ ログ イベントの例を次に示します。

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>Azure Data Explorer で取り込みパイプラインを設定する 

Azure Data Explorer のパイプラインの設定には、[テーブルの作成やデータ インジェスト](/azure/data-explorer/ingest-sample-data#ingest-data)など、さまざまなステップが含まれています。 データの操作、マップ、更新を行うこともできます。

### <a name="connect-to-azure-data-explorer-web-ui"></a>Azure Data Explorer の Web UI に接続する

1. Azure Data Explorer の *AzureMonitoring* データベースで **[クエリ]** を選択して、Azure Data Explorer の Web UI を開きます。

    ![Query](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>ターゲット テーブルを作成する

Azure Data Explorer の Web UI を使用して、Azure Data Explorer データベースにターゲット テーブルを作成します。

#### <a name="diagnostic-logs-table"></a>診断ログ テーブル

1. `.create table` 制御コマンドを使用して、*AzureMonitoring* データベースに、診断ログ レコードを受け取る *DiagnosticLogsRecords* テーブルを作成します。

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. **[実行]** を選択してテーブルを作成します。

    ![クエリを実行する](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>アクティビティ ログ テーブル

アクティビティ ログの構造は表形式ではないため、データを操作し、各イベントを 1 つまたは複数のレコードに展開する必要があります。 生データは、中間テーブル *ActivityLogsRawRecords* に取り込まれます。 その時点で、データを操作および展開します。 その後、展開されたデータは更新ポリシーを使用して *ActivityLogsRecords* テーブルに取り込まれます。 そのため、アクティビティ ログの取り込みには、2 つの異なるテーブルを作成する必要があります。

1. アクティビティ ログ レコードを受け取る *ActivityLogsRecords* テーブルを *AzureMonitoring* データベースに作成します。 次の Azure Data Explorer クエリを実行して、テーブルを作成します。

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. *AzureMonitoring* データベース内にデータ操作用の中間データ テーブル *ActivityLogsRawRecords* を作成します。

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>テーブル マッピングを作成する

 データの形式は `json` なので、データのマッピングが必要です。 `json` のマッピングでは、json の各パスがテーブルの列名にマップされます。

#### <a name="diagnostic-logs-table-mapping"></a>診断ログ テーブルのマッピング

データをテーブルにマップするには、次のクエリを使用します。

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[{"column":"Timestamp","path":"$.time"},{"column":"ResourceId","path":"$.resourceId"},{"column":"MetricName","path":"$.metricName"},{"column":"Count","path":"$.count"},{"column":"Total","path":"$.total"},{"column":"Minimum","path":"$.minimum"},{"column":"Maximum","path":"$.maximum"},{"column":"Average","path":"$.average"},{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>アクティビティ ログ テーブルのマッピング

データをテーブルにマップするには、次のクエリを使用します。

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>更新ポリシーを作成する

1. コレクション内の各値が個別の行を受け取るようにレコードのコレクションを展開する[関数](/azure/kusto/management/functions)を作成します。 [`mvexpand`](/azure/kusto/query/mvexpandoperator) 演算子を使用します。

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. [更新ポリシー](/azure/kusto/concepts/updatepolicy)をターゲット テーブルに追加します。 *ActivityLogsRawRecords* 中間データ テーブルに新しく取り込まれたデータに対してクエリが自動的に実行され、*ActivityLogsRecords* テーブルにその結果が取り込まれます。

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>イベント ハブの名前空間を作成する

Azure 診断ログでは、ストレージ アカウントまたはイベント ハブにメトリックをエクスポートできます。 このチュートリアルでは、イベント ハブ経由でメトリックをルーティングします。 イベント ハブの名前空間と診断ログ用のイベント ハブを、次の手順で作成します。 Azure Monitoring では、アクティビティ ログ用にイベント ハブ *insights-operational-logs* が作成されます。

1. Azure portal で Azure Resource Manager テンプレートを使用してイベント ハブを作成します。 次のボタンを使用してデプロイを開始します。 この記事の残りの手順を実行できるよう、右クリックして **[新しいウィンドウで開く]** を選択してください。 **[Azure へのデプロイ]** ボタンをクリックすると、Azure portal に移動します。

    [![Azure へのデプロイ](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. イベント ハブの名前空間と、診断ログ用のイベント ハブを作成します。

    ![イベント ハブの作成](media/ingest-data-no-code/event-hub.png)

    フォームに次の情報を入力します。 次の表に記載されていない設定については、既定値のままにしてください。

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | イベント ハブに使用する Azure サブスクリプションを選択します。|
    | リソース グループ | *test-resource-group* | 新しいリソース グループを作成します。 |
    | Location | ニーズに最も適したリージョンを選択します。 | 他のリソースと同じ場所に、イベント ハブの名前空間を作成します。
    | 名前空間名 | *AzureMonitoringData* | 名前空間を識別する一意の名前を選択します。
    | イベント ハブ名 | *DiagnosticLogsData* | イベント ハブは、固有のスコープ コンテナーを提供する名前空間以下にあります。 |
    | コンシューマー グループ名 | *adxpipeline* | コンシューマー グループ名を作成します。 複数の使用アプリケーションで、それぞれ異なるイベント ストリームのビューを持つことができるようになります。 |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>Azure Monitoring ログをイベント ハブに接続する

### <a name="diagnostic-logs-connection-to-event-hub"></a>イベント ハブへの診断ログの接続

メトリックをエクスポートするリソースを選択します。 イベント ハブ名前空間、KeyVault、IoT Hub、Azure Data Explorer クラスターなど、複数のリソースの種類から診断ログをエクスポートできます。 このチュートリアルでは、リソースとして Azure Data Explorer クラスターを使用します。

1. Azure portal でお使いの Kusto クラスターを選択します。

    ![診断設定](media/ingest-data-no-code/diagnostic-settings.png)

1. 左側のメニューから **[診断設定]** を選択します。
1. **[診断をオンにする]** リンクをクリックします。 **[診断設定]** ウィンドウが開きます。

    ![[診断設定] ウィンドウ](media/ingest-data-no-code/diagnostic-settings-window.png)

1. **[診断設定]** ウィンドウで次のようにします。
    1. 診断ログ データの名前を指定します:*ADXExportedData*
    1. **[AllMetrics]\(すべてのメトリック\)** チェック ボックスをオンにします (省略可能)。
    1. **[イベント ハブへのストリーム]** チェック ボックスをオンにします。
    1. **[構成]** をクリックします

1. **[イベント ハブの選択]** ウィンドウで、作成したイベント ハブへのエクスポートを構成します。
    1. **[イベント ハブの名前空間の選択]** ドロップダウンから *AzureMonitoringData* を選択します。
    1. **[イベント ハブ名の選択]** ドロップダウンから *diagnosticlogsdata* を選択します。
    1. **[イベント ハブ ポリシー名の選択]** ドロップダウンからイベント ハブ ポリシー名を選択します。
    1. Click **OK**.

1. **[Save]** をクリックします。 イベント ハブの名前空間、名前、およびポリシー名がウィンドウに表示されます。

    ![診断設定を保存する](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>イベント ハブへのアクティビティ ログの接続

1. Azure portal の左側のメニューで、**[アクティビティ ログ]** を選択します
1. **[アクティビティ ログ]** ウィンドウが開きます。 **[イベント ハブにエクスポート]** をクリックします

    ![アクティビティ ログ](media/ingest-data-no-code/activity-log.png)

1. **[アクティビティ ログのエクスポート]** ウィンドウで次のようにします。
 
    ![アクティビティ ログのエクスポート](media/ingest-data-no-code/export-activity-log.png)

    1. サブスクリプションを選択します。
    1. **[リージョン]** ドロップダウンで、**[すべて選択]** を選択します
    1. **[イベント ハブにエクスポート]** チェック ボックスをオンにします。
    1. **[Service Bus 名前空間の選択]** をクリックして、**[イベント ハブの選択]** ウィンドウを開きます。
    1. **[イベント ハブの選択]** ウィンドウのドロップダウン メニューから、お使いのサブスクリプション、お使いのイベント名前空間 *AzureMonitoringData*、既定のイベント ハブ ポリシー名を選択します。
    1. Click **OK**.
    1. ウィンドウの右上にある **[保存]** をクリックします。 *insights-operational-logs* という名前のイベント ハブが作成されます。

### <a name="see-data-flowing-to-your-event-hubs"></a>イベント ハブに送られてくるデータを確認する

1. 接続が定義されて、イベント ハブへのアクティビティ ログのエクスポートが完了するまで、数分待ちます。 お使いのイベント ハブ名前空間に移動し、作成したイベント ハブを確認します。

    ![作成されたイベント ハブ](media/ingest-data-no-code/event-hubs-created.png)

1. イベント ハブに送られてくるデータを確認します。

    ![イベント ハブのデータ](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>イベント ハブを Azure Data Explorer に接続する

### <a name="diagnostic-logs-data-connection"></a>診断ログ データの接続

1. お使いの Azure Data Explorer クラスター *kustodocs* で、左側のメニューの **[データベース]** を選択します。
1. **[データベース]** ウィンドウで、お使いのデータベース名 *AzureMonitoring* を選択します
1. 左側のメニューで、**[データ インジェスト]** を選択します
1. **[データ インジェスト]** ウィンドウで、**[+ データ接続の追加]** をクリックします
1. **[データ接続]** ウィンドウで、次の情報を入力します。

    ![イベント ハブの接続](media/ingest-data-no-code/event-hub-data-connection.png)

    データ ソース: 

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | データ接続名 | *DiagnosticsLogsConnection* | Azure データ エクスプローラーで作成する接続の名前。|
    | イベント ハブの名前空間 | *AzureMonitoringData* | 以前に選択した、名前空間を識別する名前。 |
    | イベント ハブ | *diagnosticlogsdata* | 作成したイベント ハブ。 |
    | コンシューマー グループ | *adxpipeline* | 作成したイベント ハブに定義されているコンシューマー グループ。 |
    | | |

    ターゲット テーブル: 

    ルーティングには、"*静的*" と "*動的*" という 2 つのオプションがあります。 このチュートリアルでは、静的ルーティング (既定) を使用し、テーブル名、ファイル形式、およびマッピングを指定します。 そのため、**[My data includes routing info]\(データにルーティング情報が含まれている\)** はオフのままにしておきます。

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | テーブル | *DiagnosticLogsRecords* | *AzureMonitoring* データベース内に作成したテーブル。 |
    | データ形式 | *JSON* | テーブルの形式。 |
    | 列マッピング | *DiagnosticLogsRecordsMapping* | *AzureMonitoring* に作成したマッピング。受信 JSON データを *DiagnosticLogsRecords* の列名とデータ型にマッピングします。|
    | | |

1. **[作成]**  

### <a name="activity-logs-data-connection"></a>アクティビティ ログ データの接続

「[診断ログ データの接続](#diagnostic-logs-data-connection)」セクションの手順を繰り返して、アクティビティ ログ データの接続を作成します。

1. **[データ接続]** ウィンドウで、次の設定を入力します。

    データ ソース: 

    **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | データ接続名 | *ActivityLogsConnection* | Azure データ エクスプローラーで作成する接続の名前。|
    | イベント ハブの名前空間 | *AzureMonitoringData* | 以前に選択した、名前空間を識別する名前。 |
    | イベント ハブ | *insights-operational-logs* | 作成したイベント ハブ。 |
    | コンシューマー グループ | *$Default* | 既定のコンシューマー グループ。 必要な場合は、異なるコンシューマー グループを作成できます。 |
    | | |

    ターゲット テーブル: 

    ルーティングには、"*静的*" と "*動的*" という 2 つのオプションがあります。 このチュートリアルでは、静的ルーティング (既定) を使用し、テーブル名、ファイル形式、およびマッピングを指定します。 そのため、**[My data includes routing info]\(データにルーティング情報が含まれている\)** はオフのままにしておきます。

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | テーブル | *ActivityLogsRawRecords* | *AzureMonitoring* データベース内に作成したテーブル。 |
    | データ形式 | *JSON* | テーブルの形式。 |
    | 列マッピング | *ActivityLogsRawRecordsMapping* | *AzureMonitoring* に作成したマッピング。受信 JSON データを *ActivityLogsRawRecords* の列名とデータ型にマッピングします。|
    | | |

1. **[作成]**  

## <a name="query-the-new-tables"></a>新しいテーブルのクエリを実行する

パイプラインをデータが流れるようになりました。 クラスター経由での取り込みには既定で 5 分かかるので、データが流れるまで数分待ってから、クエリを始めます。

### <a name="diagnostic-logs-table-query-example"></a>診断ログ テーブルのクエリの例

次のクエリでは、Azure Data Explorer の診断ログ レコードからクエリ期間のデータを分析します。

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

クエリの結果:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="activity-logs-table-query-example"></a>アクティビティ ログ テーブルのクエリの例

次のクエリでは、Azure Data Explorer のアクティビティ ログ レコードのデータを分析します。

```kusto
ActivityLogsRecords
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

## <a name="next-steps"></a>次の手順

次の記事で、Azure Data Explorer から抽出したデータに対するさらに多くのクエリを記述する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Explorer のクエリを記述する](write-queries.md)
