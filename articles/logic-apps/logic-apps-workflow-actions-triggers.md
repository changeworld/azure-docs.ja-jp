---
title: ワークフローのトリガーとアクション - Azure Logic Apps | Microsoft Docs
description: ロジック アプリで自動のワークフローとプロセスを作成するためのトリガーとアクションについて説明します。
services: logic-apps
author: divyaswarnkar
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/13/2017
ms.author: klam; LADocs
ms.openlocfilehash: 28d28888ce66c354da39dc636579655aadbb9e51
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2018
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>ロジック アプリ ワークフローのトリガーとアクション

すべてのロジック アプリは、アクションに続くトリガーから開始されます。 この記事では、ロジック アプリを構築してシステム統合の作成とビジネス ワークフローおよびプロセスの自動化に使用できるトリガーとアクションの種類について説明します。 
  
## <a name="triggers-overview"></a>トリガーの概要 

すべてのロジック アプリはトリガーから開始されます。トリガーには、ロジック アプリの実行を開始できる呼び出しが指定されています。 使用できるトリガーの種類を次に示します。

* *ポーリング* トリガー (定期的にサービスの HTTP エンドポイントをチェックします)
* *プッシュ* トリガー ([ワークフロー サービスの REST API](https://docs.microsoft.com/rest/api/logic/workflows) を呼び出します)
  
すべてのトリガーには、以下の最上位要素が含まれます。  
  
```json
"<myTriggerName>": {
    "type": "<triggerType>",
    "inputs": { <callSettings> },
    "recurrence": {  
        "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
        "interval": "<recurrence-interval-based-on-frequency>"
    },
    "conditions": [ <array-with-required-conditions> ],
    "splitOn": "<property-used-for-creating-runs>",
    "operationOptions": "<options-for-operations-on-the-trigger>"
}
```

## <a name="trigger-types-and-inputs"></a>トリガーの種類と入力  

各トリガーの種類には、その動作を定義するさまざまなインターフェイスとさまざまな*入力*があります。 

| トリガーの種類 | [説明] | 
| ------------ | ----------- | 
| **定期的なアイテム** | 定義されているスケジュールに基づいて呼び出されます。 今後このトリガーを呼び出す日時を設定できます。 頻度に基づいて、ワークフローを実行する日時を指定することもできます。 | 
| **要求**  | ロジック アプリを呼び出し可能なエンドポイント ("手動" トリガーとも呼ばれます) にします。 | 
| **HTTP** | HTTP Web エンドポイントのチェックまたは*ポーリング*を行います。 HTTP エンドポイントは、"202" 非同期パターンを使うか、または配列を返すことによって、特定のトリガー コントラクトに従う必要があります | 
| **ApiConnection** | HTTP トリガーと同様にポーリングしますが、[Microsoft が管理する API](../connectors/apis-list.md) を使用します。 | 
| **HTTPWebhook** | **要求**トリガーと同様に、ロジック アプリを呼び出し可能なエンドポイントにします。ただし、登録と登録解除には指定された URL を呼び出します。 |
| **ApiConnectionWebhook** | **HTTPWebhook** トリガーと同様に動作しますが、Microsoft が管理する API を使用します。 | 
||| 

詳しくは、[ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)に関するページをご覧ください。 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Recurrence トリガー  

このトリガーは、指定した繰り返しとスケジュールに基づいて実行されます。ワークフローを定期的に実行するための簡単な方法として利用できます。 

毎日実行される基本の定期的なトリガー例を紹介します。

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

トリガーを呼び出す開始日時をスケジュールすることもできます。 たとえば、毎週月曜日に週間レポートを開始するには、次の例のように特定の月曜日に開始されるようにロジック アプリをスケジュールできます。 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime": "2017-09-18T00:00:00Z"
    }
}
```

このトリガーの定義は次のとおりです。

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": <recurrence-interval-based-on-frequency>,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ <one-or-more-hour-marks> ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ <one-or-more-minute-marks> ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
        "timeZone": "<specify-time-zone>"
    }
}
```

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- | 
| frequency | [はい] | String | トリガーが呼び出される頻度の時間単位。 使用できる値は "second"、"minute"、"hour"、"day"、"week"、または "month" のみです | 
| interval | [はい] | 整数 | ワークフローの実行間隔を、[頻度] に指定された単位に基づいて表す正の整数。 <p>間隔の最小値と最大値は次のとおりです。 <p>- month: 1 ～ 16 か月 </br>- day: 1 ～ 500 日 </br>- hour: 1 ～ 12,000 時間 </br>- minute: 1 ～ 72,000 分 </br>- second: 1 ～ 9,999,999 秒<p>たとえば間隔が 6 で、頻度が "month" である場合は、繰り返しは 6 か月ごとになります。 | 
| timeZone | いいえ  | String | 開始時刻を指定したときに限り適用されます。このトリガーに [UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を指定することはできないためです。 適用するタイム ゾーンを指定してください。 | 
| startTime | いいえ  | String | この形式の開始日時を指定します。 <p>タイム ゾーンを指定する場合は YYYY-MM-DDThh:mm:ss <p>または <p>タイム ゾーンを指定しない場合は YYYY-MM-DDThh:mm:ssZ <p>たとえば、2017 年 9 月 18 日午後 2:00 の場合は、「2017-09-18T14:00:00」と指定し、"太平洋標準時" などのタイム ゾーンを指定します。 または、タイム ゾーンなしで「2017-09-18T14:00:00Z」と指定します。 <p>**注:** この開始時刻は、[UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を除いた [UTC 日時形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)で、[日付と時刻に関する ISO 8601 規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)に従って入力する必要があります。 タイム ゾーンを指定しなかった場合は、末尾にスペースを入れず、アルファベットの "Z" を追加してください。 この "Z" は、同等の[航海時間](https://en.wikipedia.org/wiki/Nautical_time)を表します。 <p>単純なスケジュールでは、開始時刻と最初の実行時刻が一致するのに対して、複雑なスケジュールでは、トリガーが作動するのは開始時刻以降となります。 開始日時の詳細については、[定期的に実行されるタスクの作成とスケジュール](../connectors/connectors-native-recurrence.md)に関するページを参照してください。 | 
| weekDays | いいえ  | 文字列または文字列配列 | `frequency` に "Week" を指定した場合、ワークフローを実行する 1 日または複数の日 ("Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday"、および "Sunday") をコンマ区切りで指定できます | 
| hours | いいえ  | 整数または整数配列 | `frequency` に "Day" または "Week" を指定した場合、ワークフローを実行する時刻として 0 ～ 23 の 1 つまたは複数の整数をコンマ区切りで指定できます。 <p>たとえば "10"、"12"、"14" を指定した場合、時刻のマークとして 10 AM、12 PM、2 PM が取得されます。 | 
| minutes | いいえ  | 整数または整数配列 | `frequency` に "Day" または "Week" を指定した場合、ワークフローを実行する時刻の分として 0 ～ 59 の 1 つまたは複数の整数をコンマ区切りで指定できます。 <p>たとえば、分のマークとして「30」を指定し、時刻に前の例を使用すると、10:30 AM、12:30 PM、2:30 PM が取得されます。 | 
||||| 

たとえば、この定期的なトリガーは、2017 年 9 月 9 日の 2:00 PM から、ロジック アプリが毎週月曜日の太平洋標準時の 10:30 AM、12:30 PM、および 2:30 PM に実行されることを指定します。

``` json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": 1,
        "schedule": {
            "hours": [
                10,
                12,
                14
            ],
            "minutes": [
                30
            ],
            "weekDays": [
                "Monday"
            ]
        },
       "startTime": "2017-09-07T14:00:00",
       "timeZone": "Pacific Standard Time"
    }
}
```

このトリガーの定期的な例と開始時刻の例の詳細については、[定期的に実行されるタスクの作成とスケジュール](../connectors/connectors-native-recurrence.md)に関するページを参照してください。

## <a name="request-trigger"></a>Request トリガー

このトリガーは、HTTP 要求を介してロジック アプリを呼び出すために使用できるエンドポイントとして機能します。 Request トリガーの例を次に示します。  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

このトリガーには、`schema` という省略可能なプロパティもあります。
  
| 要素名 | 必須 | type | [説明] |
| ------------ | -------- | ---- | ----------- |
| schema | いいえ  | オブジェクト | 受信要求を検証する JSON スキーマです。 以降のワークフロー手順において参照するプロパティを特定するのに役立ちます。 | 
||||| 

このトリガーをエンドポイントとして起動するには、`listCallbackUrl`API を呼び出す必要があります。 [ワークフロー サービスの REST API に関するページ](https://docs.microsoft.com/rest/api/logic/workflows)をご覧ください。

## <a name="http-trigger"></a>HTTP トリガー  

このトリガーは、指定されたエンドポイントをポーリングし、応答を調べて、ワークフローを実行する必要があるかどうかを判断します。 この `inputs` オブジェクトは、HTTP 呼び出しの構築に必要な以下のパラメーターを受け取ります。 

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- | 
| method | [はい] | String | HTTP メソッド "GET"、"POST"、"PUT"、"DELETE"、"PATCH"、または "HEAD" のいずれかを使用します。 | 
| uri | [はい]| String | チェックをトリガーする HTTP または HTTPS エンドポイント。 文字列の最大サイズ: 2 KB | 
| クエリ | いいえ  | オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
| retryPolicy | いいえ  | オブジェクト | 4xx または 5xx エラーの再試行動作のカスタマイズにこのオブジェクトを使用します。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| [認証] | いいえ  | オブジェクト | 要求が認証に使用する方法を表します。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 <p>Scheduler 以外に、もう 1 つのプロパティ `authority` がサポートされています。 指定しない場合の既定値は `https://login.windows.net` ですが、`https://login.windows\-ppe.net` など、異なる値を使用できます。 | 
||||| 

*再試行*ポリシーは、接続の例外に加えて、HTTP 状態コード 408、429、5xx などの断続的エラーに適用されます。 以下のように、`retryPolicy` オブジェクトを使用してこのポリシーを定義できます。
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

HTTP トリガーがロジック アプリでうまく動作するには、HTTP API が特定のパターンに準拠している必要があります。 トリガーは、以下のプロパティを認識します。  
  
| Response | 必須 | [説明] | 
| -------- | -------- | ----------- |  
| 状態コード | [はい] | 状態コード 200 ("OK") によって実行されます。 その他のすべての状態コードでは実行されません。 | 
| Retry-after ヘッダー | いいえ  | ロジック アプリがエンドポイントを再度ポーリングするまでの秒数です。 | 
| Location ヘッダー | いいえ  | 次のポーリング間隔で呼び出す URL です。 指定されていない場合は、元の URL が使われます。 | 
|||| 

異なる要求の種類に対する動作の例を次に示します。
  
| 応答コード | 再試行までの時間 | 動作 | 
| ------------- | ----------- | -------- | 
| 200 | {なし} | ワークフローを実行し、定義済みの繰り返しの後に、まだデータがあるかどうかを再確認します。 | 
| 200 | 10 秒 | ワークフローを実行し、10 秒後に、まだデータがあるかどうかを再確認します。 |  
| 202 | 60 秒 | ワークフローをトリガーしません。 次の試行は、定義済みの繰り返しに従って 1 分後に行われます。 定義済みの繰り返しが 1 分未満の場合は、retry-after ヘッダーが優先されます。 それ以外の場合、定義済みの繰り返しが使用されます。 | 
| 400 | {なし} | 正しくない要求です。ワークフローを実行しないでください。 `retryPolicy` が定義されていない場合は、既定のポリシーが使用されます。 再試行回数に達すると、定義済みの繰り返しの後に、トリガーはデータがあるかどうかを再確認します。 | 
| 500 | {なし}| サーバー エラーです。ワークフローを実行しないでください。 `retryPolicy` が定義されていない場合は、既定のポリシーが使用されます。 再試行回数に達すると、定義済みの繰り返しの後に、トリガーはデータがあるかどうかを再確認します。 | 
|||| 

HTTP トリガーの出力は次のとおりです。 
  
| 要素名 | type | [説明] |
| ------------ | ---- | ----------- |
| headers | オブジェクト | HTTP 応答のヘッダー | 
| body | オブジェクト | HTTP 応答の本文 | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>APIConnection トリガー  

このトリガーは基本的には HTTP トリガーと同様に動作します。 ただし、アクションを識別するためのパラメーターは異なります。 たとえば次のようになります。   
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "myCategory"
    }
}
```

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- | 
| host | [はい] | オブジェクト | ホストされているゲートウェイと、API アプリの ID | 
| method | [はい] | String | HTTP メソッド "GET"、"POST"、"PUT"、"DELETE"、"PATCH"、または "HEAD" のいずれかを使用します。 | 
| クエリ | いいえ  | オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
| retryPolicy | いいえ  | オブジェクト | 4xx または 5xx エラーの再試行動作のカスタマイズにこのオブジェクトを使用します。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| [認証] | いいえ  | オブジェクト | 要求が認証に使用する方法を表します。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 | 
||||| 

`host` オブジェクトの場合、プロパティは以下のとおりです。  
  
| 要素名 | 必須 | [説明] | 
| ------------ | -------- | ----------- | 
| api runtimeUrl | [はい] | マネージ API のエンドポイントです。 | 
| connection name |  | ワークフローが使用するマネージ API 接続の名前です。 `$connection` というパラメーターを参照する必要があります。 |
|||| 

*再試行*ポリシーは、接続の例外に加えて、HTTP 状態コード 408、429、5xx などの断続的エラーに適用されます。 以下のように、`retryPolicy` オブジェクトを使用してこのポリシーを定義できます。
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

API 接続トリガーの出力は以下のとおりです。
  
| 要素名 | type | [説明] |
| ------------ | ---- | ----------- |
| headers | オブジェクト | HTTP 応答のヘッダー | 
| body | オブジェクト | HTTP 応答の本文 | 
|||| 

詳細については、[API 接続トリガーの料金体系](../logic-apps/logic-apps-pricing.md#triggers)に関するページを参照してください。

## <a name="httpwebhook-trigger"></a>HTTPWebhook トリガー  

このトリガーは、`Request` トリガーと同様にエンドポイントを提供しますが、HTTPWebhook トリガーは登録と登録解除のために指定された URL も呼び出します。 HTTPWebhook トリガーの例を次に示します。

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": {},
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {}
        }
    },
    "conditions": []
}
```

これらのセクションの多くは省略可能であり、HTTPWebhook トリガーの動作は、指定または省略するセクションによって変わります。 HTTPWebhook トリガーのプロパティを次に示します。
  
| 要素名 | 必須 | [説明] | 
| ------------ | -------- | ----------- |  
| subscribe | いいえ  | トリガーが作成されて初期登録を実行するときに呼び出される送信要求を指定します。 | 
| unsubscribe | いいえ  | トリガーが削除されるときに呼び出す送信要求を指定します。 | 
|||| 

webhook トリガーに対する制限は、[HTTP 非同期制限](#asynchronous-limits)と同じ方法で指定できます。 ここでは、`subscribe` および `unsubscribe` アクションについて詳細に説明します。

* トリガーがイベントに対するリッスンを開始できるように `subscribe` が呼び出されます。 この送信呼び出しは、標準の HTTP アクションと同じパラメーターから始まります。 この呼び出しは、何らかの方法でワークフローが変更されると行われます。たとえば、資格情報が展開されたときや、トリガーの入力パラメーターが変化したときなどです。 
  
  この呼び出しをサポートするために、`@listCallbackUrl()` 関数はワークフローのこの特定のトリガーに一意の URL を返します。 この URL は、サービスの REST API を使用するエンドポイントの一意識別子を表します。
  
* `unsubscribe` は、以下の操作など、ある操作によってこのトリガーが無効になると自動的に呼び出されます。

  * トリガーを削除または無効にする。 
  * ワークフローを削除または無効にする。 
  * サブスクリプションを削除または無効にする。 
  
  この関数のパラメーターは、HTTP トリガーと同じです。

HTTPWebhook トリガーの出力は以下のとおりです。これは受信要求の内容です。
  
| 要素名 | type | [説明] |
| ------------ | ---- | ----------- |
| headers | オブジェクト | HTTP 応答のヘッダー | 
| body | オブジェクト | HTTP 応答の本文 | 
|||| 

## <a name="triggers-conditions"></a>トリガー: 条件

すべてのトリガーについて、1 つまたは複数の条件を使って、ワークフローを実行する必要があるかどうかを決定できます。 この例では、ワークフローの `sendReports` パラメーターが true に設定されている間だけレポートはトリガーします。 

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

最後に、条件はトリガーの状態コードを参照できます。 たとえば、Web サイトが状態コード 500 を返したときにのみ、ワークフローを開始できます。
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  

> [!NOTE]
> 既定では、"200 OK" を受信したときのみトリガーが起動します。 何らかの方法で式がトリガーの状態コードを参照すると、トリガーの既定動作が置き換えられます。 つまり、複数の状態コード (状態コード 200 と状態コード 201 など) に基づいてトリガーを起動したい場合は、条件として次のステートメントを含める必要があります。 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-process-an-array-with-multiple-runs"></a>トリガー: 複数の実行を含む配列を処理する

トリガーによって返される配列をロジック アプリが処理する場合、"for each" ループに時間がかかりすぎて、配列の各項目を処理できないことがあります。 この場合は、トリガーで **SplitOn** プロパティを使用すると、配列を "*バッチ解除*" できます。 

バッチ解除すると、配列の項目が分割され、配列の各項目に対して実行する新しいロジック アプリ インスタンスが起動されます。 このアプローチが役立つのは、たとえば、エンドポイントをポーリングするときに、ポーリング間隔のたびに複数の新しい項目が返される場合です。
**SplitOn** が 1 つのロジック アプリ実行で処理できる配列項目の最大数については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。 

> [!NOTE]
> **SplitOn** をトリガーに追加する方法としては、ロジック アプリの JSON 定義のコード ビューで手動で定義するか上書きするしかありません。 同期応答パターンを実装しようとするときは **SplitOn** を使用できません。 **SplitOn** を使用して、応答アクションを含むワークフローは、非同期で実行して、すぐに `202 ACCEPTED` 応答を送信します。

トリガーの Swagger ファイルに、配列であるペイロードが記述されている場合、**SplitOn** プロパティが自動的にトリガーに追加されます。 それ以外の場合は、バッチ解除する配列を含む応答ペイロード内にこのプロパティを追加します。 

たとえば、次の応答を返す API があるものとします。 
  
```json
{
    "Status": "Succeeded",
    "Rows": [ 
        { 
            "id": 938109380,
            "name": "customer-name-one"
        },
        {
            "id": 938109381,
            "name": "customer-name-two"
        }
    ]
}
```
  
ロジック アプリで必要なのは `Rows` の内容だけなので、次の例のようなトリガーを作成できます。

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": "1"
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> `SplitOn` コマンドを使う場合、配列の外側にあるプロパティを取得することはできません。 つまり、この例では、API から返される応答で `status` プロパティを取得できません。
> 
> `Rows`プロパティが存在しない場合のエラーを回避するために、この例では `?` 演算子を使用しています。

現在、ワークロー定義では `@triggerBody().name` を使用して、最初の実行から `customer-name-one`、次の実行から `customer-name-two` を取得できます。 つまり、トリガーの出力は次の例のようになります。

```json
{
    "body": {
        "id": 938109380,
        "name": "customer-name-one"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "customer-name-two"
    }
}
```
  
## <a name="triggers-fire-only-after-all-active-runs-finish"></a>トリガー: すべてのアクティブな実行の完了後に起動する

すべてのアクティブな実行が完了したときにのみ呼び出される定期実行のトリガーを構成できます。 この設定を構成するには設定、`operationOptions` プロパティを `singleInstance` に設定します。

```json
"myTrigger": {
    "type": "Http",
    "inputs": { },
    "recurrence": { },
    "operationOptions": "singleInstance"
}
```

ワークフロー インスタンスが実行中にスケジュールされた繰り返しが発生した場合、トリガーはスキップし、次のスケジュールされた反復間隔を再確認するまで待機します。

## <a name="actions-overview"></a>アクションの概要

アクションには多くの種類があり、それぞれが固有の動作を持ちます。 各アクションの種類には、アクションの動作を定義するさまざまな入力があります。 コレクション アクション内には、他の多くのアクションを含めることができます。 

### <a name="standard-actions"></a>標準アクション  

| アクションの種類 | [説明] | 
| ----------- | ----------- | 
| **HTTP** | HTTP Web エンドポイントを呼び出します。 | 
| **ApiConnection**  | HTTP アクションと同様に動作しますが、[Microsoft が管理する API](https://docs.microsoft.com/azure/connectors/apis-list) を使用します。 | 
| **ApiConnectionWebhook** | HTTPWebhook と同様に動作しますが、Microsoft が管理する API を使用します。 | 
| **応答** | 着信呼び出しの応答を定義します。 | 
| **作成** | アクションの入力から任意のオブジェクトを構築します。 | 
| **Function** | Azure 関数を表します。 | 
| **Wait** | 一定の時間または特定の時刻まで待機します。 | 
| **Workflow** | 入れ子になったワークフローを表します。 | 
| **作成** | アクションの入力から任意のオブジェクトを構築します。 | 
| **クエリ** | 条件に基づいて配列をフィルター処理します。 | 
| **Select** | 配列の各要素を新しい値に射影します。 たとえば、数値の配列をオブジェクトの配列に変換することができます。 | 
| **テーブル** | アイテムの配列を CSV または HTML テーブルに変換します。 | 
| **Terminate** | ワークフローの実行を停止します。 | 
| **Wait** | 一定の時間または特定の時刻まで待機します。 | 
| **Workflow** | 入れ子になったワークフローを表します。 | 
||| 

### <a name="collection-actions"></a>コレクション アクション

| アクションの種類 | [説明] | 
| ----------- | ----------- | 
| **If** | 式を評価し、結果に基づいて対応する分岐を実行します。 | 
| **Switch** | オブジェクトの特定の値に基づいてさまざまなアクションを実行します。 | 
| **ForEach** | このループ アクションは、配列を反復処理し、各配列アイテムの内部アクションを実行します。 | 
| **Until** | このループ アクションは、条件が true になるまで内部アクションを実行します。 | 
| **スコープ** | 他のアクションを論理的にグループ化するために使用します。 | 
|||  

## <a name="http-action"></a>HTTP アクション  

HTTP アクションは、指定されたエンドポイントをポーリングし、応答を調べて、ワークフローを実行する必要があるかどうかを判断します。 例: 
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

この `inputs` オブジェクトは、HTTP 呼び出しの構築に必要な以下のパラメーターを受け取ります。 

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- | 
| method | [はい] | String | HTTP メソッド "GET"、"POST"、"PUT"、"DELETE"、"PATCH"、または "HEAD" のいずれかを使用します。 | 
| uri | [はい]| String | チェックをトリガーする HTTP または HTTPS エンドポイント。 文字列の最大サイズ: 2 KB | 
| クエリ | いいえ  | オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
| retryPolicy | いいえ  | オブジェクト | 4xx または 5xx エラーの再試行動作のカスタマイズにこのオブジェクトを使用します。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| operationsOptions | いいえ  | String | オーバーライドする特殊な動作のセットを定義します。 | 
| [認証] | いいえ  | オブジェクト | 要求が認証に使用する方法を表します。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 <p>Scheduler 以外に、もう 1 つのプロパティ `authority` がサポートされています。 指定しない場合の既定値は `https://login.windows.net` ですが、`https://login.windows\-ppe.net` など、異なる値を使用できます。 | 
||||| 

HTTP アクションと APIConnection アクションは、*再試行ポリシー*をサポートします。 再試行ポリシーは、接続の例外に加えて、HTTP 状態コード 408、429、5 xx などの断続的エラーに適用されます。 以下のように、`retryPolicy` オブジェクトを使用してこのポリシーを定義できます。
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

次の例の HTTP アクションでは、合計 3 回と 30 秒の再試行間隔の間に断続的なエラーがある場合、最新ニュースの取得を 2 回再試行します。
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy": {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

再試行間隔は、[ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)で指定します。 この間隔の既定値および最小値は 20 秒で、最大値は 1 時間です。 既定および最大の再試行回数は 4 回です。 再試行ポリシーの定義を指定しないと、既定の再試行回数と間隔の値で `fixed` 戦略が使われます。 再試行ポリシーを無効にするには、種類を `None` に設定します。

### <a name="asynchronous-patterns"></a>非同期パターン

既定では、HTTP ベースのすべてのアクションは、標準的な非同期操作パターンをサポートします。 したがって、リモート サーバーが要求の処理を応答 "202 ACCEPTED" で受け付けた場合、Logic Apps エンジンは、終了状態 202 以外の応答 になるまで、応答の location ヘッダーで指定された URL へのポーリングを続けます。
  
前述した非同期動作を無効にするには、アクションの入力で `operationOptions` を `DisableAsyncPattern` に設定します。 この場合、アクションの出力は、サーバーからの最初の 202 応答に基づきます。 例: 
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>非同期の制限

非同期パターンの期間は、特定の間隔に制限できます。 その間隔に終了状態にならなかった場合、アクションの状態は `ActionTimedOut` コードで `Cancelled` にマークされます。 タイムアウト制限は、ISO 8601 形式で指定します。 次のように制限を指定できます。


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection アクション

このアクションは、Microsoft が管理するコネクタを参照し、有効な接続の参照と、API およびパラメーターについての情報が必要です。 APIConnection アクションの例を次に示します。

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- | 
| host | [はい] | オブジェクト | `runtimeUrl` などのコネクタ情報と、接続オブジェクトへの参照を表します。 | 
| method | [はい] | String | HTTP メソッド "GET"、"POST"、"PUT"、"DELETE"、"PATCH"、または "HEAD" のいずれかを使用します。 | 
| パス | [はい] | String | API 操作のパスです。 | 
| クエリ | いいえ  | オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
| retryPolicy | いいえ  | オブジェクト | 4xx または 5xx エラーの再試行動作のカスタマイズにこのオブジェクトを使用します。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| operationsOptions | いいえ  | String | オーバーライドする特殊な動作のセットを定義します。 | 
| [認証] | いいえ  | オブジェクト | 要求が認証に使用する方法を表します。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 |
||||| 

再試行ポリシーは、接続の例外に加えて、HTTP 状態コード 408、429、5 xx などの断続的エラーに適用されます。 以下のように、`retryPolicy` オブジェクトを使用してこのポリシーを定義できます。

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

## <a name="apiconnection-webhook-action"></a>APIConnection webhook アクション

APIConnectionWebhook アクションは、Microsoft が管理するコネクタを参照します。 このアクションには、有効な接続の参照と、API およびパラメーターについての情報が必要です。 webhook アクションに対する制限は、[HTTP 非同期制限](#asynchronous-limits)とおなじ方法で指定できます。

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- | 
| host | [はい] | オブジェクト | `runtimeUrl` などのコネクタ情報と、接続オブジェクトへの参照を表します。 | 
| パス | [はい] | String | API 操作のパスです。 | 
| クエリ | いいえ  | オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
| retryPolicy | いいえ  | オブジェクト | 4xx または 5xx エラーの再試行動作のカスタマイズにこのオブジェクトを使用します。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| operationsOptions | いいえ  | String | オーバーライドする特殊な動作のセットを定義します。 | 
| [認証] | いいえ  | オブジェクト | 要求が認証に使用する方法を表します。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 |
||||| 

## <a name="response-action"></a>応答アクション  

このアクションには、HTTP 要求からの応答ペイロード全体と、`statusCode`、`body`、および `headers` が含まれています。
  
```json
"myResponseAction": {
    "type": "Response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

応答アクションには、次のように、他のアクションには適用されない特別な制限があります。  
  
* 着信要求には決定論的な応答が必要なので、ロジック アプリ定義内の並列する分岐に応答アクションを持つことはできません。
  
* 着信要求が既に応答を受け取った後に、ワークフローが応答アクションに達した場合、応答アクションは失敗または競合状態と見なされます。 その結果、ロジック アプリの実行は `Failed` にマークされます。
  
* 呼び出しによって複数の実行が作成されるため、応答アクションを含むワークフローは、トリガー定義で `splitOn` コマンドを使用できません。 そのため、ワークフロー操作が PUT で "無効な要求" 応答が返される場合は、このケースを確認してください。

## <a name="compose-action"></a>作成アクション

このアクションで、任意のオブジェクトを構築できます。出力は、アクションの入力の評価結果です。 

> [!NOTE]
> XML およびバイナリなどのロジック アプリでネイティブでサポートされているオブジェクト、配列などの種類を含め、任意の出力を構築に `Compose` アクションを使用できます。

たとえば、複数のアクションの出力をマージするために `Compose` アクションを使用できます。

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="function-action"></a>Function アクション

このアクションを使用すると、次のような [Azure 関数](../azure-functions/functions-overview.md)を表し、呼び出すことができます。

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- |  
| function id | [はい] | String | 呼び出す Azure 関数のリソース ID。 | 
| method | いいえ  | String | 関数の呼び出しに使用する HTTP メソッド。 指定しない場合、既定のメソッドは "POST" です。 | 
| クエリ | いいえ  | オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
|||||

ロジック アプリを保存すると、参照される関数に対するいくつかのチェックが Logic Apps エンジンによって実行されます。

* その関数にアクセスできる必要があります。
* 使用できるのは、標準 HTTP トリガーまたは汎用 JSON webhook トリガーだけです。
* 関数にはルートを定義しないことをお勧めします。
* 使用できる承認レベルは、"function" と "anonymous" だけです。

> [!NOTE]
> Logic Apps エンジンは、実行時に使用されるトリガー URL を取得してキャッシュします。 そのため、操作でキャッシュされた URL を無効にすると、実行時にアクションが失敗します。 この問題を回避するには、ロジック アプリをもう一度保存します。これにより、ロジック アプリはトリガー URL を再度取得し、キャッシュするようになります。

## <a name="select-action"></a>選択アクション

このアクションでは、配列の各要素を新しい値に射影することができます。 この例では、数値の配列をオブジェクトの配列に変換します。

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Name | 必須 | type | [説明] | 
| ---- | -------- | ---- | ----------- | 
| from | [はい] | array | ソース配列 |
| select | [はい] | 任意 | ソース配列の各要素に適用するプロジェクション |
||||| 

`select` アクションの出力は、入力配列と同じ基数を持つ配列です。 各要素は、`select` プロパティの定義に従って変換されます。 入力が空の配列の場合、出力も空の配列になります。

## <a name="terminate-action"></a>終了アクション

このアクションは、ワークフローの実行を停止し、実行中のすべてのアクションを取消し、残りのすべてのアクションをスキップします。 終了アクションは、既に完了したアクションには影響しません。

たとえば、`Failed` 状態の実行を停止するには次のようにします。

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| Name | 必須 | type | [説明] | 
| ---- | -------- | ---- | ----------- | 
| runStatus | [はい] | String | ターゲットの実行の状態 (`Failed` または `Cancelled`) |
| runError | いいえ  | オブジェクト | エラーの詳細です。 `runStatus` が `Failed` に設定されているときにのみサポートされます |
| runError code | いいえ  | String | 実行のエラー コード |
| runError message | いいえ  | String | 実行のエラー メッセージ | 
||||| 

## <a name="query-action"></a>クエリ アクション

このアクションでは、条件に基づいて配列をフィルター処理できます。 

> [!NOTE]
> 作成アクションを使うと、オブジェクト、配列、およびロジック アプリによってネイティブにサポートされている XML やバイナリなどの他の型など、任意の出力を作成できます。

たとえば、2 より大きい数値を選ぶには、次のようにします。

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| Name | 必須 | type | [説明] | 
| ---- | -------- | ---- | ----------- | 
| from | [はい] | array | ソース配列 |
| 各値の説明: | [はい] | String | ソース配列の各要素に適用される条件。 `where` の条件を満たす値がない場合、結果は空の配列になります。 |
||||| 

`query` アクションからの出力は、入力配列の要素のうち条件を満たす要素の配列です。

## <a name="table-action"></a>テーブル アクション

このアクションを使用して、配列を CSV または HTML テーブルに変換できます。 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| Name | 必須 | type | [説明] | 
| ---- | -------- | ---- | ----------- | 
| from | [はい] | array | ソース配列です。 `from` プロパティ値が空の配列の場合、出力は空のテーブルになります。 | 
| format | [はい] | String | 使用するテーブル形式 ("CSV" または "HTML") | 
| columns | いいえ  | array | 使用するテーブルの列。 既定のテーブル シェイプの上書きに使用します。 | 
| column header | いいえ  | String | 列ヘッダー | 
| column value | [はい] | String | 列の値 | 
||||| 

この例のようにテーブル アクションを定義するとします。

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

`@triggerBody()` には次の配列を使用します。

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

この例の出力を次に示します。

<table><thead><tr><th>ID</th><th>Name</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>

このテーブルをカスタマイズするには、次の例のように列を明示的に指定できます。

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

この例の出力を次に示します。

<table><thead><tr><th>Produce ID</th><th>[説明]</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>

## <a name="wait-action"></a>待機アクション  

このアクションは、指定した時間だけワークフローの実行を中断します。 この例では、ワークフローが 15 分待機します。
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
または、特定の時点まで待機するには、次の例を使うことができます。
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> 待機期間は `interval` オブジェクトまたは `until` オブジェクトを使って指定できますが、両方を使うことはできません。

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- | 
| until | いいえ  | オブジェクト | 特定の時刻に基づく待機期間です。 | 
| until timestamp | [はい] | String | 待機の期限を示す [UTC 日時形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)の時点です。 | 
| interval | いいえ  | オブジェクト | 間隔の単位とカウントに基づく待機時間 | 
| interval unit | [はい] | String | 時間の単位。 使用できる値は "second"、"minute"、"hour"、"day"、"week"、または "month" のみです | 
| interval count | [はい] | 整数 | 待機時間に使用される間隔の単位数を表す整数値 | 
||||| 

## <a name="workflow-action"></a>ワークフロー アクション

このアクションを使用すると、ワークフローを入れ子にすることができます。 Logic Apps エンジンは、子ワークフロー (より具体的にはトリガー) に対してアクセス チェックを実行します。つまり、子ワークフローに対してアクセス権を持っている必要があります。 例: 

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- |  
| host id | [はい] | String| 呼び出すワークフローのリソース ID | 
| host triggerName | [はい] | String | 呼び出すトリガーの名前 | 
| クエリ | いいえ  | オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
||||| 

アクションの出力は、子ワークフローの `Response` アクションの定義内容に基づきます。 子ワークフローに `Response` アクションが定義されていない場合、出力は空になります。

## <a name="collection-actions-overview"></a>コレクション アクションの概要

ワークフローの実行を制御するために、コレクション アクションには他のアクションを含めることができます。 コレクション内の参照アクションは、コレクションの外部で直接参照できます。 たとえば、スコープに `Http` アクションを定義すると、ワークフローのどの場所でも `@body('http')` が有効です。 また、コレクション内のアクションは、同じコレクション内の他のアクションの後にしか実行できません。

## <a name="if-action"></a>If アクション

このアクション (条件付きステートメント) を使うと、条件を評価し、式が true に評価されたかどうかに基づいて分岐を実行できます。 条件が正常に true に評価されると、条件は "Succeeded" とマークされます。 `actions` オブジェクトまたは `else` オブジェクト内のアクションは、次のいずれかの値に評価されます。

* "Succeeded": 実行して成功した場合
* "Failed": 実行して失敗した場合
* "Skipped": それぞれの分岐が実行されない場合

詳しくは、[ロジック アプリの条件付きステートメント](../logic-apps/logic-apps-control-flow-conditional-statement.md)に関するページをご覧ください。

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| Name | 必須 | type | [説明] | 
| ---- | -------- | ---- | ----------- | 
| アクション | [はい] | オブジェクト | `expression` が `true` に評価されるときに実行する内部アクション | 
| expression | [はい] | String | 評価する式です。 |
| else | いいえ  | オブジェクト | `expression` が `false` に評価されるときに実行する内部アクション |
||||| 

例: 

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://this-url"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>条件でアクションの式を使用する方法

条件で式を使用する方法の例を次に示します。
  
| JSON 式 | 結果 | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | true に評価されるすべての値は、この条件に合格します。 ブール式のみがサポートされています。 他の型をブールに変換するには、`empty` または `equals` 関数を使用します。 | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | 比較関数をサポートします。 この例では、action1 の出力がしきい値より大きい場合にのみ、アクションが実行されます。 | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | 入れ子になったブール式を作成する場合、論理関数がサポートされます。 この例では、action1 の出力がしきい値より大きいか 100 未満の場合に、アクションが実行されます。 | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | 配列にアイテムがあるかどうかを確認するには、配列関数を使用できます。 この例では、errors 配列が空のときにアクションが実行されます。 | 
| `"expression": "parameters('hasSpecialAction')"` | この式ではエラーが生成されます。有効な条件ではありません。 条件では "@" 記号を使用する必要があります。 | 
||| 

## <a name="switch-action"></a>switch アクション

このアクション (switch ステートメント) は、オブジェクト、式、またはトークンの特定の値に基づいてさまざまなアクションを実行します。 このアクションは、オブジェクト、式、トークンを評価し、その結果と一致するケースを選択し、そのケース専用のアクションを実行します。 結果と一致するケースがない場合は、既定のアクションが実行されます。 switch ステートメントが実行されるときは、1 つのケースのみが結果と一致する必要があります。 詳しくは、[ロジック アプリの switch ステートメント](../logic-apps/logic-apps-control-flow-switch-statement.md)に関するページをご覧ください。

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1" : {
         "actions" : {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions" : {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| Name | 必須 | type | [説明] | 
| ---- | -------- | ---- | ----------- | 
| expression | [はい] | String | 評価するオブジェクト、式、トークン | 
| cases | [はい] | オブジェクト | 式の結果に基づいて実行する内部アクションのセットを含みます。 | 
| case | [はい] | String | 結果と照合する値 | 
| アクション | [はい] | オブジェクト | 式の結果と一致するケースで実行する内部アクション | 
| default | いいえ  | オブジェクト | 結果と一致するケースがない場合に実行する内部アクション | 
||||| 

例: 

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions" : {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions" : {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Foreach アクション

このループ アクションは、配列を反復処理し、各配列アイテムの内部アクションを実行します。 既定では、Foreach ループは並列で実行されます。 "for each" ループが実行できる並列サイクルの最大数については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。各サイクルを順に実行するには、`operationOptions` パラメーターを `Sequential` に設定します。 詳しくは、[ロジック アプリの Foreach ループ](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)に関するページをご覧ください。

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| Name | 必須 | type | [説明] | 
| ---- | -------- | ---- | ----------- | 
| アクション | [はい] | オブジェクト | ループ内で実行する内部アクション | 
| foreach | [はい] | String | 反復処理する配列です | 
| operationOptions | いいえ  | String | 動作をカスタマイズする任意の操作オプションを指定します。 既定動作が並列の反復処理を順に実行する場合、現在 `Sequential` のみがサポートされています。 |
||||| 

例: 

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Until アクション

このループ アクションは、条件が true に評価されるまで内部アクションを実行します。 詳しくは、[ロジック アプリの "until" ループ](../logic-apps/logic-apps-control-flow-loops.md#until-loop)に関するページをご覧ください。

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Name | 必須 | type | [説明] | 
| ---- | -------- | ---- | ----------- | 
| アクション | [はい] | オブジェクト | ループ内で実行する内部アクション | 
| expression | [はい] | String | 反復のたびに評価する式です。 | 
| limit | [はい] | オブジェクト | ループの制限。 1 つ以上の制限を定義する必要があります。 | 
| count | いいえ  | 整数 | 実行する反復処理回数の制限 | 
| timeout | いいえ  | String | ループの実行期間を指定する [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)のタイムアウト制限 |
||||| 

例: 

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>スコープ アクション

このアクションを使うと、ワークフロー内のアクションを論理的にグループ化できます。 また、そのスコープ内のすべてのアクションの実行が完了すると、スコープ独自の状態が取得されます。 詳しくは、[スコープ](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)に関するページをご覧ください。

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| Name | 必須 | type | [説明] | 
| ---- | -------- | ---- | ----------- |  
| アクション | [はい] | オブジェクト | スコープ内で実行する内部アクション |
||||| 

## <a name="next-steps"></a>次の手順

* [ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)
* [ワークフローの REST API](https://docs.microsoft.com/rest/api/logic/workflows)