---
title: "Azure Functions のタイマー トリガー"
description: "Azure Functions でタイマー トリガーを使用する方法について説明します。"
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: 
ms.openlocfilehash: fd9c1d40ba1398c7ca3f48f0423457482da9a483
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions のタイマー トリガー 

この記事では、Azure Functions でタイマー トリガーを使用する方法について説明します。 タイマー トリガーを使用すると、スケジュールに基づいて関数を実行できます。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>例

言語固有の例をご覧ください。

* [プリコンパイル済み C#](#trigger---c-example)
* [C# スクリプト](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C# の例

次の例は、5 分ごとに実行される[プリコンパイル済み C# 関数](functions-dotnet-class-library.md)を示します。

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>C# スクリプトの例

次の例は、*function.json* ファイルのタイマー トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数では、この関数呼び出しがスケジュールのミスの発生によるものかどうかを示すログが書き込まれます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# スクリプト コードを次に示します。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>F# の例

次の例は、*function.json* ファイルのタイマー トリガー バインドと、そのバインドが使用される [F# スクリプト関数](functions-reference-fsharp.md)を示しています。 この関数では、この関数呼び出しがスケジュールのミスの発生によるものかどうかを示すログが書き込まれます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

F# スクリプト コードを次に示します。

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript の例

次の例は、*function.json* ファイルのタイマー トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数では、この関数呼び出しがスケジュールのミスの発生によるものかどうかを示すログが書き込まれます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript スクリプト コードを次に示します。

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>属性

[プリコンパイル済み C#](functions-dotnet-class-library.md) 関数では、NuGet パッケージ [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) で定義されている [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs) を使用します。

次の例に示すように、属性のコンストラクターは CRON 式を受け取ります。

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

関数アプリが (従量課金プランではなく) App Service プランで実行されている場合、CRON 式の代わりに `TimeSpan` を指定できます。

完全な例については、[プリコンパイル済み C# の例](#c-example)をご覧ください。

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `TimerTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "timerTrigger" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "in" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のタイマー オブジェクトを表す変数の名前。 | 
|**schedule**|**ScheduleExpression**|従量課金プランでは、CRON 式を使用してスケジュールを定義することができます。 App Service プランを使用している場合は、`TimeSpan` 文字列も使用できます。 以降のセクションでは、CRON 式について説明します。 スケジュール式をアプリ設定に含めて、たとえば "%NameOfAppSettingWithCRONExpression%" のように、**%** 記号で囲まれた値にこのプロパティを設定できます。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>CRON の形式 

Azure Functions のタイマー トリガーの [CRON 式](http://en.wikipedia.org/wiki/Cron#CRON_expression)には、次の 6 つのフィールドが含まれます。 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>オンラインで見つかる CRON 式の多くでは、`{second}` フィールドが省略されています。 それらのいずれかをコピーして、欠けている `{second}` フィールドを追加します。

### <a name="cron-time-zones"></a>CRON のタイム ゾーン

CRON 式で使用する既定のタイム ゾーンは、協定世界時 (UTC) です。 別のタイム ゾーンに基づく CRON 式を使用するには、Function App 用に `WEBSITE_TIME_ZONE` という名前の新しいアプリ設定を作成します。 この値を、[Microsoft のタイム ゾーン インデックス](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx)に関するページに示されている目的のタイム ゾーンの名前に設定します。 

たとえば、"*東部標準時*" は UTC-05:00 です。 タイマー トリガーが毎日東部標準時の 10:00 AM に発生するように設定するには、UTC タイム ゾーンを考慮した次の CRON 式を使用できます。

```json
"schedule": "0 0 15 * * *",
``` 

また、Function App の新しいアプリ設定を `WEBSITE_TIME_ZONE` という名前で追加し、その値を "**東部標準時**" に設定することもできます。  その場合、東部標準時の 10:00 AM を表す次の CRON 式を使用できます。 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>CRON の例

Azure Functions のタイマー トリガーに使用できる CRON 式の例をいくつか示します。 

5 分に 1 回トリガーするには、次のように指定します。

```json
"schedule": "0 */5 * * * *"
```

毎正時に 1 回トリガーするには、次のように指定します。

```json
"schedule": "0 0 * * * *",
```

2 時間に 1 回トリガーするには、次のように指定します。

```json
"schedule": "0 0 */2 * * *",
```

午前 9 時から午後 5 時まで 1 時間ごとにトリガーするには、次のように指定します。

```json
"schedule": "0 0 9-17 * * *",
```

毎日の午前 9 時 30 分にトリガーするには、次のように指定します。

```json
"schedule": "0 30 9 * * *",
```

平日の午前 9 時 30 分にトリガーするには、次のように指定します。

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>使用法

タイマー トリガー関数が呼び出されると、[タイマー オブジェクト](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)が関数に渡されます。 次の JSON は、タイマー オブジェクトの 1 つの表現例です。 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>スケールアウト

タイマー トリガーでは、複数インスタンスのスケールアウトがサポートされます。特定のタイマー関数の 1 つのインスタンスが、すべてのインスタンスにわたって実行されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [タイマー トリガーを使用するクイックスタートに進む](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
