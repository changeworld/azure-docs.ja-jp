---
title: ワークフローのトリガーとアクション - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps のワークフロー定義のトリガーとアクションについて説明します
services: logic-apps
author: kevinlam1
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 5/8/2018
ms.author: klam; LADocs
ms.openlocfilehash: f44de1a316a8375618cfef2e4a98d40c2b21e019
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300149"
---
# <a name="triggers-and-actions-for-workflow-definitions-in-azure-logic-apps"></a>Azure Logic Apps のワークフロー定義のトリガーとアクション

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) では、すべてのロジック アプリのワークフローは、アクションに続くトリガーから開始されます。 この記事では、統合ソリューションのビジネス ワークフローまたはプロセスを自動化するロジック アプリのビルドに使用できる、トリガーとアクションについて説明します。 ロジック アプリは、Logic Apps デザイナーを使って視覚的にビルドするか、[ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)を使って基になるワークフロー定義を直接作成してビルドすることができます。 Azure Portal または Visual Studio のいずれかを使用できます。 [トリガーとアクションの価格](../logic-apps/logic-apps-pricing.md)に関するページをご覧ください。

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>トリガーの概要

すべてのロジック アプリはトリガーから開始されます。トリガーには、ロジック アプリのワークフローをインスタンス化して開始できる呼び出しが定義されています。 使用できるトリガーの種類を次に示します。

* *ポーリング* トリガー (定期的にサービスの HTTP エンドポイントをチェックします)
* *プッシュ* トリガー ([ワークフロー サービスの REST API](https://docs.microsoft.com/rest/api/logic/workflows) を呼び出します)
 
すべてのトリガーには以下の最上位要素がありますが、一部は省略可能です。  
  
```json
"<triggerName>": {
   "type": "<triggerType>",
   "inputs": { "<trigger-behavior-settings>" },
   "recurrence": { 
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "conditions": [ <array-with-required-conditions> ],
   "splitOn": "<property-used-for-creating-runs>",
   "operationOptions": "<optional-trigger-operations>"
}
```

*必須*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| <*triggerName*> | JSON オブジェクト | トリガーの名前。Javascript Object Notation (JSON) 形式で記述されたオブジェクトです  | 
| 型 | String | トリガーの種類。例: "Http" または "ApiConnection" | 
| inputs | JSON オブジェクト | トリガーの動作を定義するトリガーの入力 | 
| recurrence | JSON オブジェクト | トリガーが起動する頻度を記述する頻度と間隔 |  
| frequency | String | トリガーが起動する頻度を記述する時間の単位: "Second"、"Minute"、"Hour"、"Day"、"Week"、または "Month" | 
| interval | 整数 | トリガーの起動間隔を、frequency に指定された単位に基づいて記述する正の整数。 <p>間隔の最小値と最大値は次のとおりです。 <p>- month: 1 ～ 16 か月 </br>- day: 1 ～ 500 日 </br>- hour: 1 ～ 12,000 時間 </br>- minute: 1 ～ 72,000 分 </br>- second: 1 ～ 9,999,999 秒<p>たとえば間隔が 6 で、頻度が "month" である場合は、繰り返しは 6 か月ごとになります。 | 
|||| 

*省略可能*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| [conditions](#trigger-conditions) | Array | ワークフローを実行するかどうかを決定する 1 つまたは複数の条件 | 
| [splitOn](#split-on-debatch) | String | 処理のために配列を複数のワークフロー インスタンスに分割、つまり*バッチ解除*する式。 このオプションは、配列を返すトリガーに対して、コード ビューで直接作業する場合のみ使用できます。 | 
| [operationOptions](#trigger-operation-options) | String | 一部のトリガーには、既定のトリガー動作を変更できる追加のオプションがあります | 
||||| 

## <a name="trigger-types-and-details"></a>トリガーの種類と詳細  

各トリガーの種類には、トリガーの動作を定義するさまざまなインターフェイスと入力があります。 

| トリガーの種類 | 説明 | 
| ------------ | ----------- | 
| [**Recurrence**](#recurrence-trigger) | 定義されているスケジュールに基づいて呼び出されます。 今後このトリガーを呼び出す日時を設定できます。 頻度に基づいて、ワークフローを実行する日時を指定することもできます。 | 
| [**Request**](#request-trigger)  | ロジック アプリを呼び出し可能なエンドポイント ("手動" トリガーとも呼ばれます) にします。 例として、「[HTTP エンドポイントを通じてワークフローを呼び出し、トリガーし、入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」をご覧ください。 | 
| [**HTTP**](#http-trigger) | HTTP Web エンドポイントのチェックまたは*ポーリング*を行います。 HTTP エンドポイントは、"202" 非同期パターンを使うか、または配列を返すことによって、特定のトリガー コントラクトに従う必要があります。 | 
| [**ApiConnection**](#apiconnection-trigger) | HTTP トリガーと同様に動作しますが、[Microsoft が管理する API](../connectors/apis-list.md) を使用します。 | 
| [**HTTPWebhook**](#httpwebhook-trigger) | Request トリガーと同様に動作しますが、登録と登録解除には指定された URL を呼び出します。 |
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | HTTPWebhook トリガーと同様に動作しますが、[Microsoft が管理する API](../connectors/apis-list.md) を使用します。 | 
||| 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Recurrence トリガー  

このトリガーは、指定した繰り返しとスケジュールに基づいて実行され、ワークフローを定期的に実行するための簡単な方法として利用できます。 

次にトリガーの定義を示します。

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month",
      "interval": <recurrence-interval-based-on-frequency>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```
*必須*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| 繰り返し | JSON オブジェクト | トリガーの名前。Javascript Object Notation (JSON) 形式で記述されたオブジェクトです  | 
| 型 | String | トリガーの種類。"Recurrence" です | 
| inputs | JSON オブジェクト | トリガーの動作を定義するトリガーの入力 | 
| recurrence | JSON オブジェクト | トリガーが起動する頻度を記述する頻度と間隔 |  
| frequency | String | トリガーが起動する頻度を記述する時間の単位: "Second"、"Minute"、"Hour"、"Day"、"Week"、または "Month" | 
| interval | 整数 | トリガーの起動間隔を、frequency に指定された単位に基づいて記述する正の整数。 <p>間隔の最小値と最大値は次のとおりです。 <p>- month: 1 ～ 16 か月 </br>- day: 1 ～ 500 日 </br>- hour: 1 ～ 12,000 時間 </br>- minute: 1 ～ 72,000 分 </br>- second: 1 ～ 9,999,999 秒<p>たとえば間隔が 6 で、頻度が "month" である場合は、繰り返しは 6 か月ごとになります。 | 
|||| 

*省略可能*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| startTime | String | 次の形式の開始日時: <p>タイム ゾーンを指定する場合は YYYY-MM-DDThh:mm:ss <p>または <p>タイム ゾーンを指定しない場合は YYYY-MM-DDThh:mm:ssZ <p>たとえば、2017 年 9 月 18 日午後 2 時の場合は、「2017-09-18T14:00:00」と指定し、"太平洋標準時" などのタイム ゾーンを指定します。タイム ゾーンを指定しない場合は、「2017-09-18T14:00:00Z」と指定します。 <p>**注:** この開始時刻は、[UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を除いた [UTC 日時形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)で、[日付と時刻に関する ISO 8601 規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)に従って入力する必要があります。 タイム ゾーンを指定しなかった場合は、末尾にスペースを入れず、アルファベットの "Z" を追加してください。 この "Z" は、同等の[航海時間](https://en.wikipedia.org/wiki/Nautical_time)を表します。 <p>単純なスケジュールでは、開始時刻と最初の実行時刻が一致するのに対して、複雑なスケジュールでは、トリガーが作動するのは開始時刻以降となります。 開始日時の詳細については、[定期的に実行されるタスクの作成とスケジュール](../connectors/connectors-native-recurrence.md)に関するページを参照してください。 | 
| timeZone | String | 開始時刻を指定したときに限り適用されます。このトリガーに [UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を指定することはできないためです。 適用するタイム ゾーンを指定してください。 | 
| hours | 整数または整数配列 | `frequency` に "Day" または "Week" を指定した場合、ワークフローを実行する時刻として 0 ～ 23 の 1 つまたは複数の整数をコンマ区切りで指定できます。 <p>たとえば "10"、"12"、"14" を指定した場合、時刻のマークとして 10 AM、12 PM、2 PM が取得されます。 | 
| minutes | 整数または整数配列 | `frequency` に "Day" または "Week" を指定した場合、ワークフローを実行する時刻の分として 0 ～ 59 の 1 つまたは複数の整数をコンマ区切りで指定できます。 <p>たとえば上の例で指定した時を使用し、分の要素に「30」を指定した場合、実行時刻は 10:30 AM、12:30 PM、2:30 PM となります。 | 
| weekDays | 文字列または文字列配列 | `frequency` に "Week" を指定した場合、ワークフローを実行する 1 日または複数の日 ("Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday"、および "Sunday") をコンマ区切りで指定できます | 
| concurrency | JSON オブジェクト | 定期的なトリガーとポーリング トリガーの場合、このオブジェクトは、同時に実行できるワークフロー インスタンスの最大数を指定します。 この値を使用して、バックエンド システムを受信する要求を制限します。 <p>たとえば、`"concurrency": { "runs": 10 }` の値は同時実行の制限を 10 インスタンスに設定します。 | 
| operationOptions | String | `singleInstance` オプションは、トリガーがすべてのアクティブな実行の完了後にのみ起動することを指定します。 「[トリガー: アクティブな実行の完了後にのみ起動する](#single-instance)」をご覧ください。 | 
|||| 

*例 1*

次の基本的な Recurrence トリガーは、毎日実行されます。

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*例 2*

トリガーを起動する開始日時を指定できます。 次の Recurrence トリガーは、指定した日に開始され、毎日起動します。

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*例 3*

次の Recurrence トリガーは、2017 年 9 月 9 日午後 2 時に開始され、毎週月曜日の太平洋標準時の午前 10 時 30 分、午後 12 時 30 分、および午後 2 時 30 分に起動します。

``` json
"myRecurrenceTrigger": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

このトリガーの詳細と例については、[定期的に実行されるタスクの作成とスケジュール](../connectors/connectors-native-recurrence.md)に関するページをご覧ください。

<a name="request-trigger"></a>

## <a name="request-trigger"></a>Request トリガー

このトリガーは、受信 HTTP 要求を受け入れることができるエンドポイントを作成して、ロジック アプリを呼び出し可能にします。 このトリガーを呼び出すには、[ワークフロー サービスの REST API](https://docs.microsoft.com/rest/api/logic/workflows) の `listCallbackUrl` API を使用する必要があります。 このトリガーを HTTP エンドポイントとして使用する方法については、「[HTTP エンドポイントを通じてワークフローを呼び出し、トリガーし、入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」をご覧ください。

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "GET | POST | PUT | PATCH | DELETE | HEAD",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<propertyName>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   }
}
```

*必須*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| manual | JSON オブジェクト | トリガーの名前。Javascript Object Notation (JSON) 形式で記述されたオブジェクトです  | 
| 型 | String | トリガーの種類。"Request" です | 
| kind | String | 要求の種類。"Http" です | 
| inputs | JSON オブジェクト | トリガーの動作を定義するトリガーの入力 | 
|||| 

*省略可能*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| method | String | 要求がトリガーの呼び出しに使用する必要があるメソッド: "GET"、"PUT"、"POST"、"PATCH"、"DELETE"、または "HEAD" |
| relativePath | String | HTTP エンドポイントの URL が受け入れるパラメーターの相対パス | 
| schema | JSON オブジェクト | トリガーが受信要求から受信するペイロード、つまり入力を、記述および検証する JSON スキーマ。 このスキーマは、以降のワークフロー アクションを参照するプロパティを特定するのに役立ちます。 | 
| プロパティ | JSON オブジェクト | ペイロードを記述する JSON スキーマの 1 つまたは複数のプロパティ | 
| 必須 | Array | 値が必要な 1 つ以上のプロパティ | 
|||| 

*例*

この Request トリガーは、受信要求が HTTP POST メソッドを使用して、このトリガーと、受信要求からの入力を検証するスキーマを呼び出すことを指定します。 

```json
"myRequestTrigger": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "Object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "String"
                  },
                  "city": {
                     "type": "String"
                  }
               }
            }
         }
      }
   }
} 
```

<a name="http-trigger"></a>

## <a name="http-trigger"></a>HTTP トリガー  

このトリガーは、指定されたエンドポイントをポーリングし、応答を確認します。 応答は、ワークフローが実行されるかどうかを決定します。 `inputs` JSON オブジェクトには、HTTP 呼び出しを作成するために必要な `method` および `uri` パラメーターが含まれ、これらのパラメーターを必要とします。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "uri": "<HTTP-or-HTTPS-endpoint-to-poll>",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": <recurrence-interval-based-on-frequency>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*必須*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| HTTP | JSON オブジェクト | トリガーの名前。Javascript Object Notation (JSON) 形式で記述されたオブジェクトです  | 
| 型 | String | トリガーの種類。"Http" です | 
| inputs | JSON オブジェクト | トリガーの動作を定義するトリガーの入力 | 
| method | [はい] | String | 指定されたエンドポイントをポーリングする HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、"DELETE"、または"HEAD" | 
| uri | [はい]| String | トリガーがチェックまたはポーリングする HTTP または HTTPS エンドポイント URL <p>文字列の最大サイズ: 2 KB | 
| recurrence | JSON オブジェクト | トリガーが起動する頻度を記述する頻度と間隔 |  
| frequency | String | トリガーが起動する頻度を記述する時間の単位: "Second"、"Minute"、"Hour"、"Day"、"Week"、または "Month" | 
| interval | 整数 | トリガーの起動間隔を、frequency に指定された単位に基づいて記述する正の整数。 <p>間隔の最小値と最大値は次のとおりです。 <p>- month: 1 ～ 16 か月 </br>- day: 1 ～ 500 日 </br>- hour: 1 ～ 12,000 時間 </br>- minute: 1 ～ 72,000 分 </br>- second: 1 ～ 9,999,999 秒<p>たとえば間隔が 6 で、頻度が "month" である場合は、繰り返しは 6 か月ごとになります。 | 
|||| 

*省略可能*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| クエリ | JSON オブジェクト | URL に含める任意のクエリ パラメーター <p>たとえば、この要素は `?api-version=2015-02-01` クエリ文字列を URL に追加します。 <p>`"queries": { "api-version": "2015-02-01" }` <p>結果は次のとおりです。`https://contoso.com?api-version=2015-02-01` | 
| headers | JSON オブジェクト | 要求で送信される 1 つまたは複数のヘッダー <p>要求の言語と種類を設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | JSON オブジェクト | エンドポイントに送信されるペイロード (データ) | 
| [認証] | JSON オブジェクト | 受信要求が認証に使用する方法。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 Scheduler 以外に、`authority` プロパティがサポートされています。 指定しない場合の既定値は `https://login.windows.net` ですが、`https://login.windows\-ppe.net` など、別の値を使用できます。 | 
| retryPolicy | JSON オブジェクト | このオブジェクトは、状態コードが 4xx または 5xx の断続的なエラーの再試行動作をカスタマイズします。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| concurrency | JSON オブジェクト | 定期的なトリガーとポーリング トリガーの場合、このオブジェクトは、同時に実行できるワークフロー インスタンスの最大数を指定します。 この値を使用して、バックエンド システムを受信する要求を制限します。 <p>たとえば、次の値は同時実行の制限を 10 インスタンスに設定します。 <p>`"concurrency": { "runs": 10 }` | 
| operationOptions | String | `singleInstance` オプションは、トリガーがすべてのアクティブな実行の完了後にのみ起動することを指定します。 「[トリガー: アクティブな実行の完了後にのみ起動する](#single-instance)」をご覧ください。 | 
|||| 

HTTP トリガーがロジック アプリでうまく動作するには、HTTP API が特定のパターンに準拠している必要があります。 HTTP トリガーは、以下のプロパティを認識します。  
  
| Response | 必須 | 説明 | 
| -------- | -------- | ----------- |  
| 状態コード | [はい] | 状態コード "200 OK" によって実行が開始されます。 その他のすべての状態コードでは実行は開始されません。 | 
| Retry-after ヘッダー | いいえ  | ロジック アプリがエンドポイントを再度ポーリングするまでの秒数 | 
| Location ヘッダー | いいえ  | 次のポーリング間隔で呼び出す URL です。 指定されていない場合は、元の URL が使われます。 | 
|||| 

*さまざまな要求の動作の例*

| 状態コード | 再試行までの時間 | 動作 | 
| ----------- | ----------- | -------- | 
| 200 | {なし} | ワークフローを実行し、定義済みの繰り返しの後に、まだデータがあるかどうかを再確認します。 | 
| 200 | 10 秒 | ワークフローを実行し、10 秒後に、まだデータがあるかどうかを再確認します。 |  
| 202 | 60 秒 | ワークフローをトリガーしません。 次の試行は、定義済みの繰り返しに従って 1 分後に行われます。 定義済みの繰り返しが 1 分未満の場合は、retry-after ヘッダーが優先されます。 それ以外の場合、定義済みの繰り返しが使用されます。 | 
| 400 | {なし} | 正しくない要求です。ワークフローを実行しないでください。 `retryPolicy` が定義されていない場合は、既定のポリシーが使用されます。 再試行回数に達すると、定義済みの繰り返しの後に、トリガーはデータがあるかどうかを再確認します。 | 
| 500 | {なし}| サーバー エラーです。ワークフローを実行しないでください。 `retryPolicy` が定義されていない場合は、既定のポリシーが使用されます。 再試行回数に達すると、定義済みの繰り返しの後に、トリガーはデータがあるかどうかを再確認します。 | 
|||| 

### <a name="http-trigger-outputs"></a>HTTP トリガーの出力

| 要素名 | type | 説明 |
| ------------ | ---- | ----------- |
| headers | JSON オブジェクト | HTTP 応答からのヘッダー | 
| body | JSON オブジェクト | HTTP 応答からの本文 | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>APIConnection トリガー  

このトリガーは [HTTP トリガー](#http-trigger)と同様に動作します。ただし、[Microsoft が管理する API](../connectors/apis-list.md) を使用するため、このトリガーのパラメーターは異なります。 

次にトリガーの定義を示します。ただし、多くのセクションは省略可能なため、トリガーの動作はセクションが含まれているかどうかによって異なります。

```json
"<APIConnectionTriggerName>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "<managed-API-endpoint-URL>"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         },
      },
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*必須*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| *APIConnectionTriggerName* | JSON オブジェクト | トリガーの名前。Javascript Object Notation (JSON) 形式で記述されたオブジェクトです  | 
| 型 | String | トリガーの種類。"ApiConnection" です | 
| inputs | JSON オブジェクト | トリガーの動作を定義するトリガーの入力 | 
| host | JSON オブジェクト | マネージ API のホスト ゲートウェイと ID を記述する JSON オブジェクト <p>`host` JSON オブジェクトには要素 `api` と `connection` が含まれます | 
| api | JSON オブジェクト | マネージ API のエンドポイント URL。 <p>`"runtimeUrl": "<managed-API-endpoint-URL>"` | 
| connection | JSON オブジェクト | ワークフローが使用し、`$connection` という名前のパラメーターへの参照でなければならない、マネージ API 接続の名前。 <p>`"name": "@parameters('$connections')['<connection-name>'].name"` | 
| method | String | マネージ API と通信するための HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、"DELETE"、または "HEAD" | 
| recurrence | JSON オブジェクト | トリガーが起動する頻度を記述する頻度と間隔 |  
| frequency | String | トリガーが起動する頻度を記述する時間の単位: "Second"、"Minute"、"Hour"、"Day"、"Week"、または "Month" | 
| interval | 整数 | トリガーの起動間隔を、frequency に指定された単位に基づいて記述する正の整数。 <p>間隔の最小値と最大値は次のとおりです。 <p>- month: 1 ～ 16 か月 </br>- day: 1 ～ 500 日 </br>- hour: 1 ～ 12,000 時間 </br>- minute: 1 ～ 72,000 分 </br>- second: 1 ～ 9,999,999 秒<p>たとえば間隔が 6 で、頻度が "month" である場合は、繰り返しは 6 か月ごとになります。 | 
|||| 

*省略可能*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| クエリ | JSON オブジェクト | URL に含める任意のクエリ パラメーター <p>たとえば、この要素は `?api-version=2015-02-01` クエリ文字列を URL に追加します。 <p>`"queries": { "api-version": "2015-02-01" }` <p>結果は次のとおりです。`https://contoso.com?api-version=2015-02-01` | 
| headers | JSON オブジェクト | 要求で送信される 1 つまたは複数のヘッダー <p>要求の言語と種類を設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | JSON オブジェクト | マネージ API に送信されるペイロード (データ) を記述する JSON オブジェクト | 
| [認証] | JSON オブジェクト | 受信要求が認証に使用する方法。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 |
| retryPolicy | JSON オブジェクト | このオブジェクトは、状態コードが 4xx または 5xx の断続的なエラーの再試行動作をカスタマイズします。 <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| concurrency | JSON オブジェクト | 定期的なトリガーとポーリング トリガーの場合、このオブジェクトは、同時に実行できるワークフロー インスタンスの最大数を指定します。 この値を使用して、バックエンド システムを受信する要求を制限します。 <p>たとえば、`"concurrency": { "runs": 10 }` の値は同時実行の制限を 10 インスタンスに設定します。 | 
| operationOptions | String | `singleInstance` オプションは、トリガーがすべてのアクティブな実行の完了後にのみ起動することを指定します。 「[トリガー: アクティブな実行の完了後にのみ起動する](#single-instance)」をご覧ください。 | 
||||

*例*

```json
"Create_daily_report": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "https://myReportsRepo.example.com/"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         }     
      },
      "method": "POST",
      "body": {
         "category": "statusReports"
      }  
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

### <a name="apiconnection-trigger-outputs"></a>APIConnection トリガーの出力
 
| 要素名 | type | 説明 |
| ------------ | ---- | ----------- |
| headers | JSON オブジェクト | HTTP 応答からのヘッダー | 
| body | JSON オブジェクト | HTTP 応答からの本文 | 
|||| 

<a name="httpwebhook-trigger"></a>

## <a name="httpwebhook-trigger"></a>HTTPWebhook トリガー  

このトリガーは、ロジック アプリの呼び出し可能なエンドポイントを作成することによって、[Request トリガー](#request-trigger)と同様に動作します。 ただし、このトリガーは、サブスクリプションの登録と登録解除に指定されたエンドポイント URL も呼び出します。 webhook トリガーに対する制限は、[HTTP 非同期制限](#asynchronous-limits)と同じ方法で指定できます。 

次にトリガーの定義を示します。ただし、多くのセクションは省略可能であり、トリガーの動作は使用または省略するセクションによって異なります。

```json
"HTTP_Webhook": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "<subscribe-to-endpoint-URL>",
            "headers": { "<headers-for-request>" },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "<unsubscribe-from-endpoint-URL>",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {}
        }
    },
}
```

*必須*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| HTTP_Webhook | JSON オブジェクト | トリガーの名前。Javascript Object Notation (JSON) 形式で記述されたオブジェクトです  | 
| 型 | String | トリガーの種類。"HttpWebhook" です | 
| inputs | JSON オブジェクト | トリガーの動作を定義するトリガーの入力 | 
| subscribe | JSON オブジェクト| トリガーが作成されるときに初期登録を呼び出して実行する送信要求。 この呼び出しは、トリガーがエンドポイントでイベントに対するリッスンを開始できるように行われます。 詳細については、「[subscribe と unsubscribe](#subscribe-unsubscribe)」をご覧ください。 | 
| method | String | サブスクリプション要求に使用される HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、"DELETE"、または "HEAD" | 
| uri | String | サブスクリプション要求の送信先のエンドポイント URL | 
|||| 

*省略可能*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| unsubscribe | JSON オブジェクト | 操作によってトリガーが無効になったときに、サブスクリプションを自動的に呼び出して取り消す送信要求。 詳細については、「[subscribe と unsubscribe](#subscribe-unsubscribe)」をご覧ください。 | 
| method | String | 取り消し要求に使用する HTTP メソッド: "GET"、"PUT"、"POST"、"PATCH"、"DELETE"、または "HEAD" | 
| uri | String | 取り消し要求の送信先のエンドポイント URL | 
| body | JSON オブジェクト | サブスクリプションまたは取り消し要求のペイロード (データ) を記述する JSON オブジェクト | 
| [認証] | JSON オブジェクト | 受信要求が認証に使用する方法。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 |
| retryPolicy | JSON オブジェクト | このオブジェクトは、状態コードが 4xx または 5xx の断続的なエラーの再試行動作をカスタマイズします。 <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
|||| 

*例*

```json
"myAppSpotTrigger": {
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
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      }
   },
}
```

<a name="subscribe-unsubscribe"></a>

### <a name="subscribe-and-unsubscribe"></a>`subscribe` と `unsubscribe`

`subscribe` 呼び出しは、何らかの方法でワークフローが変更されると行われます。たとえば、資格情報が更新されたときや、トリガーの入力パラメーターが変化したときなどです。 この呼び出しは、標準の HTTP アクションと同じパラメーターを使用します。 
 
`unsubscribe` 呼び出しは、以下のような操作によって HTTPWebhook トリガーが無効になると、自動的に行われます。

* トリガーを削除または無効にする。 
* ワークフローを削除または無効にする。 
* サブスクリプションを削除または無効にする。 

これらの呼び出しをサポートするために、`@listCallbackUrl()` 関数はこのトリガーの一意の "コールバック URL" を返します。 この URL は、サービスの REST API を使用するエンドポイントの一意識別子を表します。 この関数のパラメーターは、HTTP トリガーと同じです。

### <a name="httpwebhook-trigger-outputs"></a>HTTPWebhook トリガーの出力

| 要素名 | type | 説明 |
| ------------ | ---- | ----------- |
| headers | JSON オブジェクト | HTTP 応答からのヘッダー | 
| body | JSON オブジェクト | HTTP 応答からの本文 | 
|||| 

<a name="apiconnectionwebhook-trigger"></a>

## <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook トリガー

このトリガーは [HTTPWebhook](#httpwebhook-trigger) トリガーと同様に動作しますが、[Microsoft が管理する API](../connectors/apis-list.md) を使用します。 

次にトリガーの定義を示します。

```json
"<ApiConnectionWebhookTriggerName>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },        
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "queries": "<query-parameters>"
   }
}
```

*必須*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| <*ApiConnectionWebhookTriggerName*> | JSON オブジェクト | トリガーの名前。Javascript Object Notation (JSON) 形式で記述されたオブジェクトです  | 
| 型 | String | トリガーの種類。"ApiConnectionWebhook" です | 
| inputs | JSON オブジェクト | トリガーの動作を定義するトリガーの入力 | 
| host | JSON オブジェクト | マネージ API のホスト ゲートウェイと ID を記述する JSON オブジェクト <p>`host` JSON オブジェクトには要素 `api` と `connection` が含まれます | 
| connection | JSON オブジェクト | ワークフローが使用し、`$connection` という名前のパラメーターへの参照でなければならない、マネージ API 接続の名前。 <p>`"name": "@parameters('$connections')['<connection-name>']['connectionId']"` | 
| body | JSON オブジェクト | マネージ API に送信されるペイロード (データ) を記述する JSON オブジェクト | 
| NotificationUrl | String | このトリガーにマネージ API が使用できる一意の "コールバック URL" を返します | 
|||| 

*省略可能*

| 要素名 | type | 説明 | 
| ------------ | ---- | ----------- | 
| クエリ | JSON オブジェクト | URL に含める任意のクエリ パラメーター <p>たとえば、この要素は `?folderPath=Inbox` クエリ文字列を URL に追加します。 <p>`"queries": { "folderPath": "Inbox" }` <p>結果は次のとおりです。`https://<managed-API-URL>?folderPath=Inbox` | 
|||| 

<a name="trigger-conditions"></a>

## <a name="triggers-conditions"></a>トリガー: 条件

すべてのトリガーについて、ワークフローを実行する必要があるかどうかを決定する 1 つまたは複数の条件のある配列を含めることができます。 この例では、ワークフローの `sendReports` パラメーターが true に設定されている間だけレポート トリガーが起動します。 

```json
"myDailyReportTrigger": {
   "type": "Recurrence",
   "conditions": [ {
      "expression": "@parameters('sendReports')"
   } ],
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

また、条件はトリガーの状態コードを参照することもできます。 たとえば、Web サイトが状態コード "500" を返したときにのみ、ワークフローを開始するとします。

``` json
"conditions": [ {
   "expression": "@equals(triggers().code, 'InternalServerError')"  
} ]  
```  

> [!NOTE]
> 既定では、"200 OK" を受信したときのみトリガーが起動します。 何らかの方法で式がトリガーの状態コードを参照すると、トリガーの既定動作が置き換えられます。 つまり、複数の状態コード (状態コード 200 と状態コード 201 など) に対してトリガーを起動したい場合は、条件として次のステートメントを含める必要があります。 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-split-an-array-into-multiple-runs"></a>トリガー: 配列を複数の実行に分割する

トリガーによって返される配列をロジック アプリが処理する場合、"for each" ループに時間がかかりすぎて、配列の各項目を処理できないことがあります。 この場合は、トリガーで **SplitOn** プロパティを使用すると、配列を "*バッチ解除*" できます。 

バッチ解除すると、配列の項目が分割され、配列の各項目に対して実行する新しいロジック アプリ インスタンスが起動されます。 このアプローチが役立つのは、たとえば、エンドポイントをポーリングするときに、ポーリング間隔のたびに複数の新しい項目が返される場合です。
**SplitOn** が 1 つのロジック アプリ実行で処理できる配列項目の最大数については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。 

> [!NOTE]
> 
>   **SplitOn** をトリガーに追加する方法としては、ロジック アプリの JSON 定義のコード ビューで手動で定義するかオーバーライドするしかありません。 同期応答パターンを実装しようとするときは **SplitOn** を使用できません。 **SplitOn** を使用して、応答アクションを含むワークフローは、非同期で実行して、すぐに `202 ACCEPTED` 応答を送信します。

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
        "interval": 1
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

<a name="trigger-operation-options"></a>

## <a name="triggers-operation-options"></a>トリガー: 操作オプション

これらのトリガーには、既定のトリガー動作を変更できる追加のオプションがあります。

| トリガー | 操作オプション | 説明 |
|---------|------------------|-------------|
| [Recurrence](#recurrence-trigger)、 <br>[HTTP](#http-trigger)、 <br>[ApiConnection](#apiconnection-trigger) | singleInstance | すべてのアクティブな実行の完了後にのみ、トリガーを起動します。 |
||||

<a name="single-instance"></a>

### <a name="triggers-fire-only-after-active-runs-finish"></a>トリガー: アクティブな実行の完了後にのみ起動する

繰り返しを設定できるトリガーの場合は、すべてのアクティブな実行の完了後にのみトリガーが起動することを指定できます。 ワークフロー インスタンスが実行中にスケジュールされた繰り返しが発生した場合、トリガーはスキップし、次のスケジュールされた繰り返しを再確認するまで待機します。 例: 

```json
"myRecurringTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Hour",
        "interval": 1,
    },
    "operationOptions": "singleInstance"
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>アクションの概要

アクションには多くの種類があり、それぞれが固有の動作を持ちます。 各アクションの種類には、アクションの動作を定義するさまざまな入力があります。 コレクション アクション内には、他の多くのアクションを含めることができます。 

### <a name="standard-actions"></a>標準アクション  

| アクションの種類 | 説明 | 
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

| アクションの種類 | 説明 | 
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

| 要素名 | 必須 | type | 説明 | 
| ------------ | -------- | ---- | ----------- | 
| method | [はい] | String | HTTP メソッド "GET"、"POST"、"PUT"、"DELETE"、"PATCH"、または "HEAD" のいずれかを使用します。 | 
| uri | [はい]| String | チェックをトリガーする HTTP または HTTPS エンドポイント。 文字列の最大サイズ: 2 KB | 
| クエリ | いいえ  | JSON オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | JSON オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | JSON オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
| retryPolicy | いいえ  | JSON オブジェクト | 4xx または 5xx エラーの再試行動作のカスタマイズにこのオブジェクトを使用します。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| operationsOptions | いいえ  | String | オーバーライドする特殊な動作のセットを定義します。 | 
| [認証] | いいえ  | JSON オブジェクト | 要求が認証に使用する方法を表します。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 <p>Scheduler 以外に、もう 1 つのプロパティ `authority` がサポートされています。 指定しない場合の既定値は `https://login.windows.net` ですが、`https://login.windows\-ppe.net` など、異なる値を使用できます。 | 
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

| 要素名 | 必須 | type | 説明 | 
| ------------ | -------- | ---- | ----------- | 
| host | [はい] | JSON オブジェクト | `runtimeUrl` などのコネクタ情報と、接続オブジェクトへの参照を表します。 | 
| method | [はい] | String | HTTP メソッド "GET"、"POST"、"PUT"、"DELETE"、"PATCH"、または "HEAD" のいずれかを使用します。 | 
| パス | [はい] | String | API 操作のパスです。 | 
| クエリ | いいえ  | JSON オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | JSON オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | JSON オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
| retryPolicy | いいえ  | JSON オブジェクト | 4xx または 5xx エラーの再試行動作のカスタマイズにこのオブジェクトを使用します。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| operationsOptions | いいえ  | String | オーバーライドする特殊な動作のセットを定義します。 | 
| [認証] | いいえ  | JSON オブジェクト | 要求が認証に使用する方法を表します。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 |
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

| 要素名 | 必須 | type | 説明 | 
| ------------ | -------- | ---- | ----------- | 
| host | [はい] | JSON オブジェクト | `runtimeUrl` などのコネクタ情報と、接続オブジェクトへの参照を表します。 | 
| パス | [はい] | String | API 操作のパスです。 | 
| クエリ | いいえ  | JSON オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | JSON オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | JSON オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
| retryPolicy | いいえ  | JSON オブジェクト | 4xx または 5xx エラーの再試行動作のカスタマイズにこのオブジェクトを使用します。 詳細については、「[Retry policies (再試行ポリシー)](../logic-apps/logic-apps-exception-handling.md)」をご覧ください。 | 
| operationsOptions | いいえ  | String | オーバーライドする特殊な動作のセットを定義します。 | 
| [認証] | いいえ  | JSON オブジェクト | 要求が認証に使用する方法を表します。 詳細については、「[Scheduler 送信認証](../scheduler/scheduler-outbound-authentication.md)」を参照してください。 |
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

| 要素名 | 必須 | type | 説明 | 
| ------------ | -------- | ---- | ----------- |  
| function id | [はい] | String | 呼び出す Azure 関数のリソース ID。 | 
| method | いいえ  | String | 関数の呼び出しに使用する HTTP メソッド。 指定しない場合、既定のメソッドは "POST" です。 | 
| クエリ | いいえ  | JSON オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | JSON オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | JSON オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
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

| Name | 必須 | type | 説明 | 
| ---- | -------- | ---- | ----------- | 
| from | [はい] | Array | ソース配列 |
| select | [はい] | 任意 | ソース配列の各要素に適用するプロジェクション |
||||| 

`select`アクションの出力は、入力配列と同じカーディナリティを持つ配列です。 各要素は、`select` プロパティの定義に従って変換されます。 入力が空の配列の場合、出力も空の配列になります。

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

| Name | 必須 | type | 説明 | 
| ---- | -------- | ---- | ----------- | 
| runStatus | [はい] | String | ターゲットの実行の状態 (`Failed` または `Cancelled`) |
| runError | いいえ  | JSON オブジェクト | エラーの詳細です。 `runStatus` が `Failed` に設定されているときにのみサポートされます |
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

| Name | 必須 | type | 説明 | 
| ---- | -------- | ---- | ----------- | 
| from | [はい] | Array | ソース配列 |
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

| Name | 必須 | type | 説明 | 
| ---- | -------- | ---- | ----------- | 
| from | [はい] | Array | ソース配列です。 `from` プロパティ値が空の配列の場合、出力は空のテーブルになります。 | 
| format | [はい] | String | 使用するテーブル形式 ("CSV" または "HTML") | 
| columns | いいえ  | Array | 使用するテーブルの列。 既定のテーブル シェイプのオーバーライドに使用します。 | 
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

<table><thead><tr><th>Produce ID</th><th>説明</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>

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

| 要素名 | 必須 | type | 説明 | 
| ------------ | -------- | ---- | ----------- | 
| until | いいえ  | JSON オブジェクト | 特定の時刻に基づく待機期間です。 | 
| until timestamp | [はい] | String | 待機の期限を示す [UTC 日時形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)の時点です。 | 
| interval | いいえ  | JSON オブジェクト | 間隔の単位とカウントに基づく待機時間 | 
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

| 要素名 | 必須 | type | 説明 | 
| ------------ | -------- | ---- | ----------- |  
| host id | [はい] | String| 呼び出すワークフローのリソース ID | 
| host triggerName | [はい] | String | 呼び出すトリガーの名前 | 
| クエリ | いいえ  | JSON オブジェクト | URL に含める任意のクエリ パラメーターを表します。 <p>たとえば、`"queries": { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。 | 
| headers | いいえ  | JSON オブジェクト | 要求で送信される各ヘッダーを表します。 <p>言語と種類を要求に設定する場合の例を次に示します。 <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | いいえ  | JSON オブジェクト | エンドポイントに送信されるペイロードを表します。 | 
||||| 

アクションの出力は、子ワークフローの `Response` アクションの定義内容に基づきます。 子ワークフローに `Response` アクションが定義されていない場合、出力は空になります。

## <a name="collection-actions-overview"></a>コレクション アクションの概要

ワークフローの実行を制御するために、コレクション アクションには他のアクションを含めることができます。 コレクション内の参照アクションは、コレクションの外部で直接参照できます。 たとえば、スコープに `Http` アクションを定義すると、ワークフローのどの場所でも `@body('http')` が有効です。 また、コレクション内のアクションは、同じコレクション内の他のアクションの後にしか実行できません。

## <a name="if-action"></a>If アクション

このアクション (条件付きステートメント) を使うと、条件を評価し、式が true に評価されたかどうかに基づいて分岐を実行できます。 条件が正常に true に評価されると、条件は "Succeeded" 状態とマークされます。 `actions` オブジェクトまたは `else` オブジェクト内のアクションは、次のいずれかの値に評価されます。

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

| Name | 必須 | type | 説明 | 
| ---- | -------- | ---- | ----------- | 
| アクション | [はい] | JSON オブジェクト | `expression` が `true` に評価されるときに実行する内部アクション | 
| expression | [はい] | String | 評価する式です。 |
| else | いいえ  | JSON オブジェクト | `expression` が `false` に評価されるときに実行する内部アクション |
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
| `"expression": "parameters('hasSpecialAction')"` | この式ではエラーが生成されます。有効な条件ではありません。 条件では "\@\" 記号を使用する必要があります。 | 
||| 

## <a name="switch-action"></a>switch アクション

このアクション (switch ステートメント) は、オブジェクト、式、またはトークンの特定の値に基づいてさまざまなアクションを実行します。 このアクションは、オブジェクト、式、トークンを評価し、その結果と一致するケースを選択し、そのケース専用のアクションを実行します。 結果と一致するケースがない場合は、既定のアクションが実行されます。 switch ステートメントが実行されるときは、1 つのケースのみが結果と一致する必要があります。 詳しくは、[ロジック アプリの switch ステートメント](../logic-apps/logic-apps-control-flow-switch-statement.md)に関するページをご覧ください。

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1": {
         "actions": {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions": {
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

| Name | 必須 | type | 説明 | 
| ---- | -------- | ---- | ----------- | 
| expression | [はい] | String | 評価するオブジェクト、式、トークン | 
| cases | [はい] | JSON オブジェクト | 式の結果に基づいて実行する内部アクションのセットを含みます。 | 
| case | [はい] | String | 結果と照合する値 | 
| アクション | [はい] | JSON オブジェクト | 式の結果と一致するケースで実行する内部アクション | 
| default | いいえ  | JSON オブジェクト | 結果と一致するケースがない場合に実行する内部アクション | 
||||| 

例: 

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
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

| Name | 必須 | type | 説明 | 
| ---- | -------- | ---- | ----------- | 
| アクション | [はい] | JSON オブジェクト | ループ内で実行する内部アクション | 
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

| Name | 必須 | type | 説明 | 
| ---- | -------- | ---- | ----------- | 
| アクション | [はい] | JSON オブジェクト | ループ内で実行する内部アクション | 
| expression | [はい] | String | 反復のたびに評価する式です。 | 
| limit | [はい] | JSON オブジェクト | ループの制限。 1 つ以上の制限を定義する必要があります。 | 
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

| Name | 必須 | type | 説明 | 
| ---- | -------- | ---- | ----------- |  
| アクション | [はい] | JSON オブジェクト | スコープ内で実行する内部アクション |
||||| 

## <a name="next-steps"></a>次の手順

* [ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)
* [ワークフローの REST API](https://docs.microsoft.com/rest/api/logic/workflows)