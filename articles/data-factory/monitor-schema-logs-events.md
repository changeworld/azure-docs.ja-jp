---
title: ログとイベントのスキーマ
description: 監視のために Azure Data Factory のログとイベントによって使用されるスキーマについて説明します。
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 0245d988ab9b116a515ec76ecb9d7031936918be
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853930"
---
# <a name="schema-of-logs-and-events"></a>ログとイベントのスキーマ

この記事では、監視のために Azure Data Factory のログとイベントによって使用されるスキーマについて説明します。

## <a name="monitor-schema"></a>スキーマの監視
監視には、次の属性リストが使用されます。

### <a name="activity-run-log-attributes"></a>アクティビティ実行ログの属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| プロパティ | Type | 説明 | 例 |
| --- | --- | --- | --- |
| **Level** |String | 診断ログのレベル。 アクティビティ実行ログの場合は、プロパティ値を 4 に設定します。 | `4` |
| **correlationId** |String | 特定の要求を追跡するための一意の ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | timespan でのイベントの時刻 (UTC 形式 `YYYY-MM-DDTHH:MM:SS.00000Z`)。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| アクティビティの実行の ID。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| パイプラインの実行の ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | データ ファクトリのリソースに関連付けられている ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 診断ログのカテゴリ。 プロパティ値は `ActivityRuns` に設定してください。 | `ActivityRuns` |
|**level**| String | 診断ログのレベル。 プロパティ値は `Informational` に設定してください。 | `Informational` |
|**operationName**| String | 状態付きのアクティビティの名前。 アクティビティが開始ハートビートである場合、プロパティ値は `MyActivity -` です。 アクティビティが終了ハートビートである場合、プロパティ値は `MyActivity - Succeeded` です。 | `MyActivity - Succeeded` |
|**pipelineName**| String | パイプラインの名前。 | `MyPipeline` |
|**activityName**| String | アクティビティの名前。 | `MyActivity` |
|**start**| String | timespan でのアクティビティの実行の開始時刻 (UTC 形式)。 | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | timespan でのアクティビティの実行の終了時刻 (UTC 形式)。 アクティビティが開始したがまだ終了していないことが診断ログに示されている場合、プロパティ値は `1601-01-01T00:00:00Z` です。 | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-log-attributes"></a>パイプライン実行ログの属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| プロパティ | Type | 説明 | 例 |
| --- | --- | --- | --- |
| **Level** |String | 診断ログのレベル。 アクティビティ実行ログの場合は、プロパティ値を 4 に設定します。 | `4` |
| **correlationId** |String | 特定の要求を追跡するための一意の ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | timespan でのイベントの時刻 (UTC 形式 `YYYY-MM-DDTHH:MM:SS.00000Z`)。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| パイプラインの実行の ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | データ ファクトリのリソースに関連付けられている ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 診断ログのカテゴリ。 プロパティ値は `PipelineRuns` に設定してください。 | `PipelineRuns` |
|**level**| String | 診断ログのレベル。 プロパティ値は `Informational` に設定してください。 | `Informational` |
|**operationName**| String | パイプラインの名前とその状態。 パイプラインの実行が完了すると、プロパティ値は `Pipeline - Succeeded` になります。 | `MyPipeline - Succeeded`. |
|**pipelineName**| String | パイプラインの名前。 | `MyPipeline` |
|**start**| String | timespan でのアクティビティの実行の開始時刻 (UTC 形式)。 | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | timespan でのアクティビティの実行の終了時刻 (UTC 形式)。 アクティビティが開始したがまだ終了していないことが診断ログに示されている場合、プロパティ値は `1601-01-01T00:00:00Z` です。  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | パイプライン実行の最終的な状態。 プロパティ値は `Succeeded` または `Failed` のいずれかです。 | `Succeeded`|

### <a name="trigger-run-log-attributes"></a>トリガー実行ログの属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| プロパティ | Type | 説明 | 例 |
| --- | --- | --- | --- |
| **Level** |String | 診断ログのレベル。 アクティビティ実行ログの場合は、プロパティ値を 4 に設定します。 | `4` |
| **correlationId** |String | 特定の要求を追跡するための一意の ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | timespan でのイベントの時刻 (UTC 形式 `YYYY-MM-DDTHH:MM:SS.00000Z`)。 | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| トリガー実行の ID。 | `08587023010602533858661257311` |
|**resourceId**| String | データ ファクトリのリソースに関連付けられている ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 診断ログのカテゴリ。 プロパティ値は `PipelineRuns` に設定してください。 | `PipelineRuns` |
|**level**| String | 診断ログのレベル。 プロパティ値は `Informational` に設定してください。 | `Informational` |
|**operationName**| String | トリガーの名前と、トリガーが正常に起動されたかどうかを示す最終的な状態。 ハートビートが成功した場合、プロパティ値は `MyTrigger - Succeeded` になります。 | `MyTrigger - Succeeded` |
|**triggerName**| String | トリガーの名前。 | `MyTrigger` |
|**triggerType**| String | トリガーの種類。 プロパティ値は `Manual Trigger` または `Schedule Trigger` のいずれかです。 | `ScheduleTrigger` |
|**triggerEvent**| String | トリガーのイベント。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | timespan でのトリガーの起動の開始時刻 (UTC 形式)。 | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | トリガーが正常に起動されたかどうかを示す最終的な状態。 プロパティ値は `Succeeded` または `Failed` のいずれかです。 | `Succeeded`|

### <a name="ssis-integration-runtime-log-attributes"></a>SSIS 統合ランタイム ログの属性

SQL Server Integration Services (SSIS) の統合ランタイム (IR) の開始、停止、およびメンテナンス操作のログ属性を次に示します。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| プロパティ                   | Type   | 説明                                                   | 例                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | SSIS IR 操作の名前                            | `Start/Stop/Maintenance/Heartbeat` |
| **category**               | String | 診断ログのカテゴリ                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | データ ファクトリの名前                                          | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                      | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                  | `Informational` |
| **resultType**             | String | SSIS IR 操作の結果                          | `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` |
| **message**                | String | SSIS IR 操作の出力メッセージ                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | データ ファクトリ リソースの一意の ID                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-message-context-log-attributes"></a>SSIS イベント メッセージ コンテキスト ログの属性

SSIS IR での SSIS パッケージ実行により生成されるイベント メッセージに関連する条件のログの属性を次に示します。 これらは、多くの SSIS パッケージのプロパティの実行時の値を示す [SSIS カタログ (SSISDB) イベント メッセージ コンテキスト テーブルまたはビュー](/sql/integration-services/system-views/catalog-event-message-context)と類似の情報を伝えます。 これらは、`Basic/Verbose` ログ レベルを選択するときに生成されるもので、デバッグやコンプライアンス チェックに役立ちます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| プロパティ                   | Type   | 説明                                                          | 例                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | 診断ログのカテゴリ                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | データ ファクトリの名前                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                             | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                         | `Informational` |
| **operationId**            | String | SSISDB での特定の操作を追跡するための一意の ID          | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて "*いない*" パッケージに関連する操作を示します)。 |
| **contextDepth**           | String | イベント メッセージ コンテキストの深さ                              | `0` (0 はパッケージの実行が開始される前のコンテキストを示し、1 はエラーが発生したときのコンテキストを示します。これは、コンテキストがエラーから離れていくにつれて増加します)。 |
| **packagePath**            | String | イベント メッセージ コンテキストのソースとしてのパッケージ オブジェクトのパス      | `\Package` |
| **contextType**            | String | イベント メッセージ コンテキストのソースとしてのパッケージ オブジェクトの種類      | `60` ([その他のコンテキストの種類](/sql/integration-services/system-views/catalog-event-message-context#remarks)を参照)。 |
| **contextSourceName**      | String | イベント メッセージ コンテキストのソースとしてのパッケージ オブジェクトの名前      | `MyPackage` |
| **contextSourceId**        | String | イベント メッセージ コンテキストのソースとしてのパッケージ オブジェクトの一意の ID | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | イベント メッセージ コンテキストのソースのパッケージ プロパティの名前   | `DelayValidation` |
| **propertyValue**          | String | イベント メッセージ コンテキストのソースのパッケージ プロパティの値  | `False` |
| **resourceId**             | String | データ ファクトリ リソースの一意の ID                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-messages-log-attributes"></a>SSIS イベント メッセージ ログの属性

SSIS IR での SSIS パッケージ実行により生成されるイベント メッセージのログの属性を次に示します。 これらは、イベント メッセージの詳細なテキストまたはメタデータを示す [SSISDB イベント メッセージ テーブルまたはビュー](/sql/integration-services/system-views/catalog-event-messages)と類似の情報を伝えます。 これらは、`None` 以外のすべてのログ レベルで生成されます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| プロパティ                   | Type   | 説明                                                        | 例                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | 診断ログのカテゴリ                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | データ ファクトリの名前                                               | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                           | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                       | `Informational` |
| **operationId**            | String | SSISDB での特定の操作を追跡するための一意の ID        | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて "*いない*" パッケージに関連する操作を示します)。 |
| **messageTime**            | String | イベントメッセージが作成された時刻 (UTC 形式)          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | イベント メッセージの種類                                     | `70` ([その他のメッセージの種類](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)を参照。) |
| **messageSourceType**      | String | イベント メッセージ ソースの種類                              | `20` ([その他のメッセージ ソースの種類](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)を参照。) |
| **message**                | String | イベント メッセージのテキスト                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | 実行されたパッケージ ファイルの名前                             | `MyPackage.dtsx` |
| **eventName**              | String | 関連する実行時イベントの名前                                 | `OnPreValidate` |
| **messageSourceName**      | String | イベント メッセージ ソースとしてのパッケージ コンポーネントの名前         | `Data Flow Task` |
| **messageSourceId**        | String | イベント メッセージ ソースとしてのパッケージ コンポーネントの一意の ID    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | String | イベント メッセージ ソースとしてのデータ フロー コンポーネントの名前       | `SSIS.Pipeline` |
| **packagePath**            | String | イベント メッセージ ソースとしてのパッケージ オブジェクトのパス            | `\Package\Data Flow Task` |
| **executionPath**          | String | 親パッケージから、実行されたコンポーネントまでの完全なパス            | `\Transformation\Data Flow Task` (このパスではコンポーネントの繰り返しもキャプチャされます)。 |
| **threadId**               | String | イベント メッセージがログに記録されたときに実行されたスレッドの一意の ID | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

### <a name="ssis-executable-statistics-log-attributes"></a>SSIS 実行可能ファイル統計ログの属性

SSIS IR での SSIS パッケージ実行により生成される実行可能ファイル統計ログの属性を次に示します。この場合の実行可能ファイルは、パッケージ制御フローのコンテナーまたはタスクです。 これらは、実行中の各実行可能ファイルとその繰り返しを 1 行で表示する [SSISDB 実行可能ファイル統計テーブルまたはビュー](/sql/integration-services/system-views/catalog-executable-statistics)と類似の情報を伝えます。 これらは、`None` 以外のすべてのログ レベルで生成され、タスク レベルのボトルネックや障害を特定するのに役立ちます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| プロパティ                   | Type   | 説明                                                      | 例                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | 診断ログのカテゴリ                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | データ ファクトリの名前                                             | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                         | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                     | `Informational` |
| **executionId**            | String | SSISDB での特定の実行を追跡するための一意の ID      | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて "*いない*" パッケージに関連する実行を示します)。 |
| **executionPath**          | String | 親パッケージから、実行されたコンポーネントまでの完全なパス          | `\Transformation\Data Flow Task` (このパスではコンポーネントの繰り返しもキャプチャされます)。 |
| **startTime**              | String | 実行可能ファイルが実行前フェーズに入ったときの時刻 (UTC 形式)  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 実行可能ファイルが実行後フェーズに入ったときの時刻 (UTC 形式) | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | 実行可能ファイルの実行時間 (ミリ秒)                   | `1,125` |
| **executionResult**        | String | 実行可能ファイルの実行結果                                 | `0` (0 は成功、1 は失敗、2 は完了、3 はキャンセルを意味します)。 |
| **executionValue**         | String | 実行可能ファイルを実行したときに返されるユーザー定義値            | `1` |
| **resourceId**             | String | データ ファクトリ リソースの一意の ID                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS 実行コンポーネントのフェーズに関するログの属性

SSIS IR での SSIS パッケージ実行により生成されるデータ フロー コンポーネントの実行時の統計情報に関するログの属性を次に示します。 これらは、すべての実行フェーズでデータ フロー コンポーネントによって費やされた時間を示す [SSISDB 実行コンポーネントのフェーズ テーブルまたはビュー](/sql/integration-services/system-views/catalog-execution-component-phases)と類似の情報を伝えます。 これらは、`Performance/Verbose` ログ レベルを選択するときに生成されるもので、データ フロー実行の統計情報をキャプチャするのに役立ちます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| プロパティ                   | Type   | 説明                                                         | 例                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | 診断ログのカテゴリ                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | データ ファクトリの名前                                                | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                            | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                        | `Informational` |
| **executionId**            | String | SSISDB での特定の実行を追跡するための一意の ID         | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて "*いない*" パッケージに関連する実行を示します)。 |
| **packageName**            | String | 実行されたパッケージ ファイルの名前                              | `MyPackage.dtsx` |
| **taskName**               | String | 実行されたデータ フロー タスクの名前                                 | `Data Flow Task` |
| **subcomponentName**       | String | データ フロー コンポーネントの名前                                     | `Derived Column` |
| **phase**                  | String | 実行フェーズの名前                                         | `AcquireConnections` |
| **startTime**              | String | 実行フェーズの開始時刻 (UTC 形式)                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 実行フェーズの終了時刻 (UTC 形式)                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | データ フロー タスクの実行パス                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | データ ファクトリ リソースの一意の ID                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS 実行データの統計情報に関するログの属性

データ フロー パイプラインの各区間を経由して上流から下流のコンポーネントに移動する、SSIS IR での SSIS パッケージ実行により生成されるデータ移動に関するログの属性を次に示します。 これらは、データ フロー タスクで移動されたデータの行数を示す [SSISDB 実行データの統計情報テーブルまたはビュー](/sql/integration-services/system-views/catalog-execution-data-statistics)と類似の情報を伝えます。 これらは、`Verbose` ログ レベルを選択するときに生成されるもので、データ フロー スループットを計算するのに役立ちます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| プロパティ                     | Type   | 説明                                                        | 例                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | 診断ログのカテゴリ                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | 特定の操作を追跡するための一意の ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | データ ファクトリの名前                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | SSIS IR の名前                                           | `MySSISIR` |
| **level**                    | String | 診断ログのレベル                                       | `Informational` |
| **executionId**              | String | SSISDB での特定の実行を追跡するための一意の ID        | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて "*いない*" パッケージに関連する実行を示します)。 |
| **packageName**              | String | 実行されたパッケージ ファイルの名前                             | `MyPackage.dtsx` |
| **taskName**                 | String | 実行されたデータ フロー タスクの名前                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | データ フロー パスを追跡するための一意の ID                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | データ フロー パスの名前                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | データを送信するデータ フロー コンポーネントの名前                    | `SQLDB Table3` |
| **destinationComponentName** | String | データを受信するデータ フロー コンポーネントの名前                 | `Derived Column` |
| **rowsSent**                 | String | ソース コンポーネントによって送信された行数                        | `500` |
| **createdTime**              | String | 行の値が取得された時刻 (UTC 形式)                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | データ フロー タスクの実行パス                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | データ ファクトリ リソースの一意の ID                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

## <a name="log-analytics-schema"></a>Log Analytics のスキーマ

Log Analytics は、Azure Monitor からスキーマを継承します。ただし、次の例外があります。

* 各列名の最初の文字は、大文字になります。 たとえば、Monitor の列名 "correlationId" は、Log Analytics では "CorrelationId" です。
* "Level" 列はありません。
* 動的な "properties" 列は、次の動的 JSON BLOB の種類として維持されます。

    | Azure Monitor の列 | Log Analytics の列 | Type |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | 動的 |
    | $.properties.Annotations | 注釈 | 動的 |
    | $.properties.Input | 入力 | 動的 |
    | $.properties.Output | 出力 | 動的 |
    | $.properties.Error.errorCode | ErrorCode | INT |
    | $.properties.Error.message | ErrorMessage | string |
    | $.properties.Error | エラー | 動的 |
    | $.properties.Predecessors | Predecessors | 動的 |
    | $.properties.Parameters | パラメーター | 動的 |
    | $.properties.SystemParameters | SystemParameters | 動的 |
    | $.properties.Tags | Tags | 動的 |

## <a name="next-steps"></a>次のステップ

[Azure Marketplace から Azure Data Factory Analytics ソリューションをインストールする](monitor-install-analytics.md)
