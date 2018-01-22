---
title: "Azure Data Factory でタンブリング ウィンドウ トリガーを作成する方法 | Microsoft Docs"
description: "タンブリング ウィンドウでパイプラインを実行するトリガーを Azure Data Factory で作成する方法について説明します。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>タンブリング ウィンドウでパイプラインを実行するトリガーを作成する方法
この記事では、タンブリング ウィンドウ トリガーを作成、起動、および監視する手順について説明します。 トリガーとサポートされる種類の全般的な情報については、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)」を参照してください。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 の使用](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。

タンブリング ウィンドウ トリガーは、状態を維持しながら、指定した開始時刻から定期的に実行される種類のトリガーです。 タンブリング ウィンドウとは、固定サイズで重複しない一連の連続する時間間隔です。 タンブリング ウィンドウ トリガーはパイプラインと 1 対 1 の関係を持ち、単一のパイプラインのみを参照できます。

## <a name="tumbling-window-trigger-type-properties"></a>タンブリング ウィンドウ トリガーの種類のプロパティ
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

次の表に、タンブリング ウィンドウ トリガーでの繰り返しとスケジュール設定に関連する主要な要素の概要を示します。

| **JSON での名前** | **説明** | **使用できる値** | **必須** |
|:--- |:--- |:--- |:--- |
| **type** | トリガーの種類。 これは "TumblingWindowTrigger" に固定されています。 | String | ○ |
| **runtimeState** | <readOnly> 指定できる値: Started、Stopped、Disabled | String | ○、readOnly |
| **frequency** |*frequency* の文字列は、トリガーが繰り返される頻度の単位を表します。 サポートされる値は "minute" と "hour" です。 開始時刻に頻度よりも細かい日付部分がある場合、ウィンドウの境界を計算するために考慮されます。 例: 頻度が時間単位で、開始時刻が 2016-04-01T10:10:10Z の場合、最初のウィンドウは (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z) です。  | String。 サポートされる種類は "minute"、"hour" | ○ |
| **interval** |*interval* は正の整数で、トリガーの実行頻度を決定する *frequency* の間隔を示します。 たとえば、*interval* が 3 で *frequency* が "hour" の場合は、トリガーは 3 時間ごとに繰り返されます。 | 整数 | ○ |
| **startTime**|*startTime* は、日付/時刻です。 *startTime* は最初の発生で、過去の可能性があります。 最初のトリガー間隔は (startTime、startTime + interval) になります。 | Datetime | ○ |
| **endTime**|*endTime* は、日付/時刻です。 *endTime* は最後の発生で、過去の可能性があります。 | Datetime | ○ |
| **delay** | ウィンドウのデータ処理を開始する前の遅延を指定します。 パイプラインの実行は、予想実行時間 + 遅延の後に開始されます。 遅延で、トリガーが期限を過ぎてから新しい実行をトリガーするまでの待機時間を定義します。 遅延でウィンドウの開始時刻は変わりません。 | Timespan (例: 00:10:00 は 10 分の遅延を意味します) |  ×。 既定値は "00:00:00" です |
| **max concurrency** | 準備ができているウィンドウに対して発生する同時トリガー実行の数。 例: 昨日について 1 時間ごとにバックフィルを試行する場合は 24 ウィンドウになります。 concurrency = 10 の場合、トリガー イベントは最初の 10 ウィンドウ (00:00-01:00 - 09:00-10:00) に対してのみ発生します。 最初の 10 回がトリガーされたパイプラインの実行が完了すると、次の 10 ウィンドウ (10:00-11:00 - 19:00-20:00) に対してトリガーの実行が発生します。 concurrency = 10 の例を続けると、準備ができているウィンドウが 10 個ある場合は、10 個のパイプラインが実行されます。 準備ができているウィンドウが 1 つしかない場合は、1 つのパイプラインのみが実行されます。 | 整数 | ○。 指定できる値は 1-50 です |
| **retryPolicy: Count** | パイプライン実行前の再試行回数には "Failed" とマークされます  | 整数 |  ×。 既定値は 0 回の再試行です |
| **retryPolicy: intervalInSeconds** | 再試行の間の遅延 (秒単位) | 整数 |  ×。 既定値は 30 秒です |

### <a name="using-system-variables-windowstart-and-windowend"></a>システム変数の使用: WindowStart と WindowEnd

**パイプライン**定義 (つまりクエリの一部) でタンブリング ウィンドウ トリガーの WindowStart と WindowEnd を使用する場合は、次のように**トリガー**定義のパイプラインにパラメーターとして変数を渡す必要があります。
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

次に、パイプライン定義で WindowStart 値と WindowEnd 値を使用するには、それぞれ "MyWindowStart" と "MyWindowEnd" のパラメーターを使用します。

### <a name="notes-on-backfill"></a>バックフィルに関する注意事項
実行のためのウィンドウが複数ある場合 (特にバックフィル シナリオの場合)、ウィンドウの実行順序は決定論的であり、最も古い間隔から最も新しい間隔の順になります。 現在のところ、この動作を変更する方法はありません。

### <a name="updating-an-existing-triggerresource"></a>既存の TriggerResource の更新
* トリガーの頻度 (またはウィンドウ サイズ) が変更されると、既に処理されたウィンドウの状態は*リセットされません*。 トリガーは、新しいウィンドウ サイズを使用して実行された最後のトリガーからウィンドウの発生を継続します。
* トリガーの終了時刻が変更 (追加または更新) されると、既に処理されたウィンドウの状態は*リセットされません*。 トリガーは単純に新しい終了時刻に従います。 終了時刻が既に実行されたウィンドウの前の場合、トリガーは停止します。 それ以外の場合は、新しい終了時刻になると停止します。

## <a name="sample-using-azure-powershell"></a>Azure PowerShell を使用するサンプル
ここでは、Azure PowerShell を使用してトリガーを作成、起動、および監視する方法について説明します。

1. 以下の内容を記述した MyTrigger.json という名前の JSON ファイルを C:\ADFv2QuickStartPSH\ フォルダー内に作成します。

   > [!IMPORTANT]
   > JSON ファイルを保存する前に、**startTime** を現在の UTC 時間に設定し、**endTime** を現在の UTC 時間の 1 時間後に設定してください。

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  
2. **Set-AzureRmDataFactoryV2Trigger** コマンドレットを使用してトリガーを作成します。

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. **Start-AzureRmDataFactoryV2Trigger** コマンドレットを使用してトリガーを起動します。

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. **Get-AzureRmDataFactoryV2Trigger** コマンドレットを使用して、トリガーの状態が **Started** であることを確認します。

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  **Get-AzureRmDataFactoryV2TriggerRun** コマンドレットを使用して、PowerShell によるトリガー実行を取得します。 トリガー実行に関する情報を取得するには、次のコマンドを定期的に実行してください。トリガー定義の値に合わせて **TriggerRunStartedAfter** と **TriggerRunStartedBefore** の値を更新します。

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
Azure Portal でトリガー実行/パイプライン実行を監視するには、「[パイプラインの監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)」をご覧ください。

## <a name="next-steps"></a>次の手順
トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#triggers)」をご覧ください。
