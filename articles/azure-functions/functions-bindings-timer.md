---
title: Azure Functions におけるタイマー トリガー | Microsoft Docs
description: Azure Functions でタイマー トリガーを使用する方法について説明します。
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2016
ms.author: chrande; glenga

---
# Azure Functions におけるタイマー トリガー
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions でタイマー トリガーを構成する方法について説明します。タイマー トリガーでは、スケジュールに従って関数が 1 回または定期的に呼び出されます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## タイマー トリガーの function.json
*function.json* ファイルには、スケジュールの式が記述されています。たとえば、次のスケジュールでは、関数は毎分実行されます。

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

タイマー トリガーは、マルチインスタンスのスケールアウトを自動的に処理します。すべてのインスタンスで特定のタイマー関数の 1 つのインタンスだけが実行されます。

## スケジュール式の形式
スケジュール式は、[CRON 式](http://en.wikipedia.org/wiki/Cron#CRON_expression)で、次の 6 個のフィールドが含まれます: `{second} {minute} {hour} {day} {month} {day of the week}`。

オンラインで見つかる CRON 式の多くは、{second} フィールドが省略されているため、これらのいずれかからコピーする場合は、この追加フィールドを調整する必要があることに注意してください。

スケジュール式の他の例を以下に示します。

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

## タイマー トリガーの C# コードの例
この C# コードの例では、関数がトリガーされるたびにログを 1 つだけ書き込みます。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## 次のステップ
[!INCLUDE [次のステップ](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->