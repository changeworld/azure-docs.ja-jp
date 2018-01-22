---
title: "Azure Data Factory でスケジュール トリガーを作成する方法 | Microsoft Docs"
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
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>スケジュールでパイプラインを実行するトリガーを作成する方法
この記事では、スケジュール トリガーと、トリガーの作成、開始、および監視の手順について説明します。 他の種類のトリガーについては、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 の使用](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事をご覧ください。

### <a name="schedule-trigger-json-definition"></a>スケジュール トリガー JSON 定義
スケジュール トリガーを作成するときには、このセクションの例に示されているように、JSON を使用してスケジュールと繰り返しを指定できます。

スケジュール トリガーにパイプライン実行を開始させるには、特定のパイプラインのパイプライン参照をトリガー定義に組み込みます。 パイプラインとトリガーには多対多の関係があります。 複数のトリガーが 1 つのパイプラインを開始することができます。 1 つのトリガーが複数のパイプラインを開始することもできます。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  **parameters** プロパティは、**pipelines** 内の必須プロパティです。 パイプラインがパラメーターを受け取らない場合でも、parameters に空の json を含めます。それは、このプロパティを必ず指定する必要があるためです。


### <a name="overview-schedule-trigger-schema"></a>概要: スケジュール トリガーのスキーマ
次の表に、トリガーでの繰り返しとスケジュール設定に関連する主要な要素の概要を示します。

JSON プロパティ |     [説明]
------------- | -------------
startTime | startTime は、日付/時刻です。 単純なスケジュールでは、startTime は最初の発生日時です。 複雑なスケジュールでは、トリガーは startTime になるとすぐに起動します。
endTime | トリガーの終了日付/時刻を指定します。 トリガーはこの時刻より後には実行されません。 過去の日時を endTime に指定するのは無効です。 これは省略可能なプロパティです。
timeZone | 現時点では、UTC のみがサポートされています。
recurrence | recurrence オブジェクトは、トリガーの繰り返し規則を指定します。 この recurrence オブジェクトは、frequency、interval、endTime、count、schedule という要素をサポートします。 recurrence を定義する場合は、frequency が必要です。recurrence の他の要素は省略可能です。
frequency | トリガーが繰り返す頻度の単位を表します。 サポートされる値は `minute`、`hour`、`day`、`week`、`month` です。
interval | interval は正の整数です。 トリガーを実行する頻度を決定する frequency の間隔を示します。 たとえば、interval が 3 で frequency が "week" の場合は、トリガーは 3 週間ごとに繰り返されます。
schedule | トリガーに頻度を指定すると、繰り返しのスケジュールに基づいて、そのジョブの繰り返しを変更できます。 schedule には、分、時間、曜日、月の日、週番号に基づいた変更を指定します。


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>概要: スケジュール トリガーのスキーマの既定値、制限、例

JSON での名前 | 値の型 | 必須 | 既定値 | 有効な値 | 例
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | String | [はい] | なし | ISO-8601 の日付/時刻 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | オブジェクト | [はい] | なし | recurrence オブジェクト | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | いいえ  | 1 | 1 ～ 1000。 | ```"interval":10```
endTime | String | [はい] | なし | 将来の時刻を表す日付/時刻の値 | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | オブジェクト | いいえ  | なし | schedule オブジェクト | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>詳細情報: startTime
次の表では、startTime による、トリガーの実行の制御方法を説明しています。

startTime の値 | スケジュールなしの繰り返し | スケジュールありの繰り返し
--------------- | --------------------------- | ------------------------
開始時刻が過去に設定されている | 開始時刻より後の将来の最初の実行時刻を計算し、その時点で実行します。<p>2 回目以降のジョブは、最後の実行時刻から計算して実行します。</p><p>この表の後の例を参照してください。</p> | トリガーは、指定した開始時刻_になるとすぐに_開始します。 最初は、開始時刻から計算したスケジュールに基づいて実行します。 <p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行</p>
開始時刻が将来または現在に設定されている | 指定した開始時刻に 1 回実行します。 <p>2 回目以降のジョブは、最後の実行時刻から計算して実行します。</p> | トリガーは、指定した開始時刻_になるとすぐに_開始します。 最初は、開始時刻から計算したスケジュールに基づいて実行します。<p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行します。</p>

ここでは、startTime が過去であり、recurrence を設定し、schedule を設定していない場合についての動作の例を説明します。 現在の時刻が `2017-04-08 13:00`、startTime が `2017-04-07 14:00` で、recurrence は 2 日ごと (frequency に day、interval に 2 を指定) であると仮定します。startTime が過去、つまり現在の時刻よりも前であることにご注意ください。

この条件では、最初の実行は `2017-04-09 at 14:00` になります。 Scheduler エンジンは、開始時刻から実行を計算します。 過去のインスタンスはすべて破棄されます。 エンジンは、将来発生する次回のインスタンスを使用します。 したがって、この場合は、startTime が `2017-04-07 at 2:00pm` となり、次回のインスタンスは 2 日後、つまり `2017-04-09 at 2:00pm` となります。

startTime が `2017-04-05 14:00` または `2017-04-01 14:00` であっても、最初の実行時間は同じです。 最初の実行後、後続の実行はスケジュールを使用して計算されます。 したがって、`2017-04-11 at 2:00pm`、`2017-04-13 at 2:00pm`、そして `2017-04-15 at 2:00pm` などとなります。

最後に、トリガーにスケジュールが設定されている場合に、スケジュールに時/分 (一方または両方) が設定されていないと、既定では、最初の実行に指定した時/分が、それぞれ使用されます。

### <a name="deep-dive-schedule"></a>詳細情報: schedule
一方で、schedule は、トリガーの実行回数を制限できます。 たとえば、frequency が "month" のトリガーに、31 日にのみ実行する schedule を設定すると、トリガーは 31 日がある月にのみ実行されます。

他方、schedule によりトリガーの実行回数を増やすこともできます。 たとえば、frequency が "month" のトリガーに、月の 1 日と 2 日に実行する schedule を設定すると、トリガーは、月に 1 回ではなく、毎月 1 日と 2 日に実行されます。

schedule の要素を複数指定した場合は、評価の順序は大きい要素から小さい要素の順序となります。つまり、週番号、月の日、曜日、時間、分という順序です。

schedule の要素を次の表に詳しく示します。


JSON での名前 | [説明] | 有効な値
--------- | ----------- | ------------
minutes | トリガーを実行する時刻 (分)。 | <ul><li>整数</li><li>整数の配列</li></ul>
hours | トリガーを実行する時刻 (時)。 | <ul><li>整数</li><li>整数の配列</li></ul>
weekDays | トリガーを実行する曜日。 週単位の頻度だけを指定できます。 | <ul><li>Monday、Tuesday、Wednesday、Thursday、Friday、Saturday、Sunday</li><li>任意の値の配列 (最大配列サイズは 7)</li></p>大文字/小文字は区別されません</p>
monthlyOccurrences | トリガーを実行する月の日にちを指定します。 月単位の頻度だけを指定できます。 | monthlyOccurence オブジェクトの配列: `{ "day": day,  "occurrence": occurence }`。 <p> day はトリガーを実行する曜日です。たとえば、`{Sunday}` は、月の毎週日曜日という意味です。 必須。<p>occurrence は、月の第何週に実行するかを表します。たとえば、`{Sunday, -1}` は月の最終日曜日という意味です。 省略可能。
monthDays | トリガーが実行される月の日にち。 月単位の頻度だけを指定できます。 | <ul><li>-1 以下かつ -31 以上の任意の値</li><li>1 以上かつ 31 以下の任意の値</li><li>値の配列。</li>


## <a name="examples-recurrence-schedules"></a>例: 繰り返しのスケジュール
このセクションでは、schedule オブジェクトとそのサブ要素に注目して、さまざまな繰り返しの例を示します。

スケジュール例では、間隔が 1 に設定されていると仮定します。 また、schedule の値に合った適切な頻度が指定されていると仮定します。たとえば、frequency に "day" を使用し、schedule に "monthDays" の変更を指定することはできません。 これらの制限は、前のセクションの表で説明されています。

例 | [説明]
------- | -----------
`{"hours":[5]}` | 毎日午前 5 時に実行
`{"minutes":[15], "hours":[5]}` | 毎日午前 5 時 15 分に実行
`{"minutes":[15], "hours":[5,17]}` | 毎日午前 5 時 15 分と午後 5 時 15 分に実行
`{"minutes":[15,45], "hours":[5,17]}` | 毎日午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行
`{"minutes":[0,15,30,45]}` | 15 分ごとに実行
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 1 時間ごとに実行。 このトリガーは 1 時間おきに実行されます。 分は、指定されている場合は startTime によって制御されます。指定されていない場合は、作成時刻によって制御されます。 たとえば、開始時刻または作成時刻が午後 12 時 25 分である場合 (どちらが適用される場合でも)、トリガーは 0 時 25 分、1 時 25 分、2 時 25 分...23 時 25 分に実行されます。 このスケジュールは、frequency に "hour"、interval に 1 を指定して、schedule を指定しないトリガーと同等です。 両者の違いは、このスケジュールは、frequency と interval を変えることで、他のトリガーの作成に使用できる点にあります。 たとえば、frequency が "month" の場合には、月 1 回だけ実行されます。frequency が "day" の場合には、スケジュールは毎日実行されます。
`{"minutes":[0]}` | 毎正時に実行。 このトリガーも 1 時間ごとに実行されますが、正時に実行されます。(例: 午前 0 時、午前 1 時、午前 2 時)。 この設定は、frequency に "hour"、startTime に 0 分を設定し、さらに頻度が "day" であれば、schedule を設定しなかったトリガーと同等です。ただし、頻度が "week" または "month" の場合は、このスケジュールは、それぞれ週に 1 日だけ、または月に 1 日だけ実行します。
`{"minutes":[15]}` | 毎正時から 15 分経過後に実行。 1 時間ごとに実行。午前 0 時 15 分から開始し、午前 1 時 15 分、午前 2 時 15 分午後 10 時 15 分と続き、最後に午後 11 時 15 分に実行します。
`{"hours":[17], "weekDays":["saturday"]}` | 毎週土曜日の午後 5 時に実行
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 毎週月曜日、水曜日、金曜日の午後 5 時に実行
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 毎週月曜日、水曜日、金曜日の午後 5 時 15 分と午後 5 時 45 分に実行
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 平日に 15 分ごとに実行
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 平日の午前 9 時と午後 4 時 45 分の間、15 分ごとに実行
`{"weekDays":["tuesday", "thursday"]}` | 火曜日と木曜日の指定された開始時刻に実行。
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | 毎月 28 日の午前 6 時に実行 (頻度を月と仮定した場合)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 月の最終日の午前 6 時に実行。 月の最終日にトリガーを実行する場合は、28、29、30、31 という日にちではなく -1 を使用します。
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 毎月の最初と最後の日の午前 6 時に実行
`{monthDays":[1,14]}` | 毎月 1 日と 14 日の指定された開始時刻に実行。
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 毎月の最初の金曜日の午前 5 時に実行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 毎月の最初の金曜日の指定された開始時刻に実行。
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 毎月、月の最後から 3 番目の金曜日の開始時刻に実行
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 毎月の最初と最後の金曜日の午前 5 時 15 分に実行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 毎月の最初と最後の金曜日の指定された開始時刻に実行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 毎月の第 5 金曜日の開始時刻に実行。 第 5 金曜日にのみ実行するスケジュールとなっているため、月に第 5 金曜日がない場合は、パイプラインは実行されません。  月の最終金曜日にトリガーを実行する場合は、occurrence に 5 ではなく -1 を使用することをご検討ください。
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 月の最終金曜日に 15 分ごとに実行。
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 毎月第 3 水曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行。


## <a name="use-azure-powershell"></a>Azure PowerShell の使用
ここでは、Azure PowerShell を使用してスケジューラ トリガーを作成、起動、および監視する方法について説明します。 このサンプルの動作を確認する場合は、「[PowerShell を使用した Azure データ ファクトリの作成](quickstart-create-data-factory-powershell.md)」を最初にご覧ください。 続いて、メインのメソッドに以下のコードを追加します。このコードは、15 分ごとに実行するスケジュールのトリガーを作成して起動します。 このトリガーは、クイックスタートの一環として作成するパイプライン (**Adfv2QuickStartPipeline**) に関連付けられます。

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

## <a name="next-steps"></a>次の手順
トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#triggers)」をご覧ください。
