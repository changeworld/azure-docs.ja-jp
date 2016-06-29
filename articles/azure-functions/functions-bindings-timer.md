<properties
	pageTitle="Azure Functions におけるタイマー トリガー | Microsoft Azure"
	description="Azure Functions でタイマー トリガーを使用する方法について説明します。"
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Azure Functions におけるタイマー トリガー

この記事では、Azure Functions でタイマー トリガーを構成する方法について説明します。タイマー トリガーでは、スケジュールに従って関数が 1 回または定期的に呼び出されます。

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## タイマー トリガーの function.json

*function.json* ファイルには、スケジュールの式が記述されています。

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

スケジュール式は、{second} {minute} {hour} {day} {month} {day of the week} の 6 つのフィールドを含む [CRON 式](http://en.wikipedia.org/wiki/Cron#CRON_expression)です。オンラインで見つかる CRON 式に関するドキュメントの多くは、{second} フィールドが省略されているため、これらのいずれかからコピーする場合は、この追加フィールドを調整する必要があります。

スケジュール式の例を以下に示します。

5 分に 1 回トリガーするには、次のように指定します。

```json
"schedule": "0 */5 * * * *"
```

2 時間に 1 回トリガーするには、次のように指定します。

```json
"schedule": "0 0 */2 * * *",
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

[AZURE.INCLUDE [次のステップ](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0615_2016-->