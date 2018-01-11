---
title: "Azure Data Factory でトリガーを作成する方法 | Microsoft Docs"
description: "スケジュールでパイプラインを実行するトリガーを Azure Data Factory で作成する方法について説明します。"
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
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>スケジュールでパイプラインを実行するトリガーを作成する方法
この記事では、トリガーを作成、起動、および監視する手順について説明します。 トリガーの概念情報については、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)」をご覧ください。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 の使用](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。


## <a name="use-azure-powershell"></a>Azure PowerShell の使用
ここでは、Azure PowerShell を使用してトリガーを作成、起動、および監視する方法について説明します。 このサンプルの動作を確認する場合は、「[PowerShell を使用した Azure データ ファクトリの作成](quickstart-create-data-factory-powershell.md)」を最初にご覧ください。 続いて、メインのメソッドに以下のコードを追加します。このコードは、15 分ごとに実行するスケジュールのトリガーを作成して起動します。 このトリガーは、クイックスタートの一環として作成するパイプライン (**Adfv2QuickStartPipeline**) に関連付けられます。

1. 以下の内容を記述した MyTrigger.json という名前の JSON ファイルを C:\ADFv2QuickStartPSH\ フォルダー内に作成します。 

    > [!IMPORTANT]
    > JSON ファイルを保存する前に、**startTime** を現在の UTC 時間に設定し、**endTime** を現在の UTC 時間の 1 時間後に設定してください。

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```
    
    JSON スニペットにおける設定は以下のとおりです。 
    - トリガーの **type** が **ScheduleTrigger** に設定されます。 
    - **frequency** が **Minute** に設定され、**interval** が **15** に設定されます。 そのため、トリガーは開始時刻と終了時刻の間で 15 分ごとにパイプラインを実行します。 
    - **endTime** は **startTime** の 1 時間後であるため、トリガーは startTime の 15 分後、30 分後、45 分後にパイプラインを実行します。 必ず startTime を現在の UTC 時間に更新し、endTime を startTime の 1 時間後に更新してください。  
    - トリガーは、**Adfv2QuickStartPipeline** パイプラインに関連付けられます。 複数のパイプラインをトリガーに関連付けるには、**pipelineReference** セクションを追加します。 
    - このクイックスタートのパイプラインは 2 つの**パラメーター**を受け取ります。 そのため、これらのパラメーターの値をトリガーから渡します。 
2. **Set-AzureRmDataFactoryV2Trigger** コマンドレットを使用してトリガーを作成します。

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. **Get-AzureRmDataFactoryV2Trigger** コマンドレットを使用して、トリガーの状態が **Stopped** であることを確認します。 

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

## <a name="use-net-sdk"></a>.NET SDK の使用
ここでは、.NET SDK を使用してトリガーを作成、起動、および監視する方法について説明します。 このコードの動作を確認する場合は、「[.NET SDK を使用してデータ ファクトリとパイプラインを作成する](quickstart-create-data-factory-dot-net.md)」を最初にご覧ください。 続いて、メインのメソッドに以下のコードを追加します。このコードは、15 分ごとに実行するスケジュールのトリガーを作成して起動します。 このトリガーは、クイックスタートの一環として作成するパイプライン (**Adfv2QuickStartPipeline**) に関連付けられます。 

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method. 
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

トリガー実行を監視するには、最後の `Console.WriteLine` ステートメントの前に次のコードを追加します。 

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Azure Portal でトリガー実行/パイプライン実行を監視するには、「[パイプラインの監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)」をご覧ください。

## <a name="use-python-sdk"></a>Python SDK の使用
ここでは、Python SDK を使用してトリガーを作成、起動、および監視する方法について説明します。 このコードの動作を確認する場合は、「[Python を使用してデータ ファクトリとパイプラインを作成する](quickstart-create-data-factory-python.md)」を最初にご覧ください。 続いて、Python スクリプトの "パイプライン実行を監視する" コード ブロックの後に次のコード ブロックを追加します。 このコードは、指定された開始時刻と終了時刻の間で 15 分ごとに実行するスケジュールのトリガーを作成します。 start_time を現在の UTC 時間に更新し、end_time を現在の UTC 時間の 1 時間後に更新してください。 

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC') 
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Azure Portal でトリガー実行/パイプライン実行を監視するには、「[パイプラインの監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)」をご覧ください。

## <a name="use-resource-manager-template"></a>Resource Manager テンプレートの使用
Azure Resource Manager テンプレートを使用してトリガーを作成できます。 詳しい手順については、「[チュートリアル: Azure Resource Manager テンプレートを使用した Azure データ ファクトリの作成](quickstart-create-data-factory-resource-manager-template.md)」をご覧ください。  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>トリガーの開始時刻をパイプラインに渡す
Azure Data Factory のバージョン 1 では、パーティション分割されたデータの読み取りと書き込みを SliceStart/SliceEnd/WindowStart/WindowEnd システム変数を使用してサポートしていました。 バージョン 2 では、パイプライン パラメーターと、そのパラメーターの値としてのトリガーの開始時刻/スケジュールされた時刻を使用してこの動作を実現できます。 次の例では、トリガーのスケジュールされた時刻が scheduledRunTime パイプライン パラメーターに値として渡されます。 

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
詳しくは、「[Azure Data Factory バージョン 2 に対してパーティション分割されたデータの読み取りまたは書き込みを行う方法](how-to-read-write-partitioned-data.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#triggers)」をご覧ください。