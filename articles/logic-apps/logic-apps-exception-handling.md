---
title: Azure での Logic Apps のエラーと例外の処理 | Microsoft Docs
description: Logic Apps におけるエラーと例外の処理パターン。
services: logic-apps
documentationcenter: ''
author: dereklee
manager: anneta
editor: ''
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 70dd4e98dbffd9dac27752f0b4c2f5ce4ca70bdc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Logic Apps におけるエラーと例外の処理

依存システムによって発生するダウンタイムや問題を適切に処理することは、すべての統合アーキテクチャにとって課題になる可能性があります。 Logic Apps では、問題や障害に対して回復力の高い堅牢な統合を作成するために、エラーと例外を処理するための最上級のエクスペリエンスが提供されます。 

## <a name="retry-policies"></a>再試行ポリシー

例外とエラーを処理する最も基本的な方法としては再試行ポリシーを使用します。 最初の要求がタイムアウトになるか失敗した場合 (要求への応答として 429 または 5xx が返された場合) に、アクションによって要求を再試行するかどうかと再試行する方法は、このポリシーによって定義されます。 

再試行ポリシーには、既定、なし、固定間隔、および指数関数的な間隔の 4 種類があります。 ワークフロー定義に再試行ポリシーが指定されていない場合は、サービスによって定義された既定のポリシーが代わりに使用されます。

再試行ポリシーを設定するには、ロジック アプリ デザイナーでロジック アプリを開き (該当する場合)、そのロジック アプリで特定のアクションの **[設定]** に移動します。 または、ワークフロー定義内で、特定のアクションまたはトリガー (再試行できる場合) の **inputs** セクションに再試行ポリシーを定義できます。 一般的な構文を次に示します。

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

構文と **inputs** について詳しくは、[ワークフローのアクションとトリガーに関するページの再試行ポリシーのセクション][retryPolicyMSDN]をご覧ください。 再試行ポリシーの制限については、「[Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md)」をご覧ください。 

### <a name="default"></a>既定値

**retryPolicy** セクションに再試行ポリシーを定義しないと、ロジック アプリは既定ポリシーを使用します。これは、[指数関数的な間隔のポリシー](#exponential-interval)であり、7.5 秒で指数関数的に延長される間隔で、最大 4 回まで再試行を送信します。 間隔の範囲は 5 秒から 45 秒です。 このポリシーは、次の HTTP ワークフロー定義例で使用されているポリシーに相当します。

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT1H"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>なし

**retryPolicy** が **none** に設定されると、そのポリシーは、失敗した要求を再試行しません。

| 要素名 | 必須 | type | [説明] | 
| ------------ | -------- | ---- | ----------- | 
| 型 | [はい] | String | "**なし**" | 
||||| 

### <a name="fixed-interval"></a>固定間隔

**retryPolicy** が **fixed** に設定されると、そのポリシーは、指定された時間待機した後で次の要求を送信することで、失敗した要求を再試行します。

| 要素名 | 必須 | type | [説明] |
| ------------ | -------- | ---- | ----------- |
| 型 | [はい] | String | **fixed** |
| count | [はい] | 整数 | 再試行の回数。1 - 90 で指定する必要があります。 | 
| interval | [はい] | String | [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)の再試行の間隔。PT5S と PT1D の間で指定する必要があります。 | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>指数関数的な間隔

**retryPolicy**が **exponential** に設定されると、ポリシーは、指数関数的に延長されるランダムな時間間隔で、失敗した要求を再試行します。 このポリシーでは、各々の再試行は、**minimumInterval** より大きく **maximumInterval** より小さいランダムな間隔で送信されることも保証されています。 指数関数的なポリシーでは、**count** と **interval** が必須ですが、**minimumInterval** と **maximumInterval** の値はオプションです。 既定値の PT5S と PT1D それぞれを上書きする場合は、次の値を追加できます。

| 要素名 | 必須 | type | [説明] |
| ------------ | -------- | ---- | ----------- |
| 型 | [はい] | String | **exponential** |
| count | [はい] | 整数 | 再試行の回数。1 - 90 で指定する必要があります。  |
| interval | [はい] | String | [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)の再試行の間隔。PT5S と PT1D の間で指定する必要があります。 |
| minimumInterval | いいえ  | String | [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)の再試行の最小間隔。PT5S と **interval** の間で指定する必要があります。 |
| maximumInterval | いいえ  | String | [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)の再試行の最小間隔。**interval** と PT1D の間で指定する必要があります。 | 
||||| 

次の表では、示されている範囲の均一のランダム変数が、再試行ごとに最大 **count** 回生成されることがわかります。

**ランダム変数の範囲**

| 再試行回数 | 最小間隔 | 最大間隔 |
| ------------ | ---------------- | ---------------- |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>runAfter プロパティを使用してエラーをキャッチして処理する

ロジック アプリの各アクションは、そのアクションが開始する前に終了する必要があるアクションを宣言します。これは、ワークフローのステップの順序を指定する方法と同じです。 アクションの定義において、**runAfter** プロパティはこの順序を定義します。また、このプロパティは、特定のアクションを実行するうえでの前提条件となるアクションとその状態を記述するオブジェクトです。

既定では、ロジック アプリ デザイナーで追加されたすべてのアクションは、先行する手順の結果が **Succeeded** であれば、その手順の次に実行されるように設定されます。 ただし、**runAfter** の値をカスタマイズして、先行するアクションの結果が **Failed** や **Skipped** (または組み合わせ) の場合にもアクションが起動するようにできます。 たとえば、特定の **Insert_Row** アクションが失敗した後で、特定の Service Bus トピックに項目を追加するには、次の例の **runAfter** 定義を使用できます。

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

この **runAfter** プロパティは、**Insert_Row** アクションの状態が **Failed** のときに実行するように設定されています。 アクションの状態が **Succeeded**、**Failed**、または **Skipped** の場合にアクションを実行するには、次の構文を使用します。

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 先行するアクションが失敗した後で実行され、正常に完了したアクションは **Succeeded** とマークされます。 この動作は、ワークフロー内で発生したすべてのエラーが正常にキャッチされた場合、その実行そのものは **Succeeded** としてマークされることを意味します。

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>スコープとその結果を使用してアクションを評価する

**runAfter** プロパティを使用して個々のアクションの後で手順を実行するように、アクションを[スコープ](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)にまとめることができます。 アクションを論理的にグループ化し、スコープの集合的な状態を調査し、その状態に基づいてアクションを実行する場合は、スコープを使用できます。 そのスコープ内のすべてのアクションの実行が完了すると、スコープが独自の状態を取得します。 

スコープの状態を調べるには、ロジック アプリの実行状態を調べるのと同じ基準 (**Succeeded** や **Failed** など) を使用できます。 

既定では、スコープのすべてのアクションが成功すると、そのスコープの状態は **Succeeded** とマークされます。 スコープの最後のアクションが **Failed** または **Aborted** になると、スコープの状態は **Failed** とマークされます。 

**Failed** スコープの例外をキャッチして、そのエラーを処理するアクションを実行するには、その **Failed** スコープの **runAfter** プロパティを使用できます。 このように、スコープ内の "*いずれかの*" アクションが失敗したときに、そのスコープに対して **runAfter** プロパティを使用している場合、エラーをキャッチする 1 つのアクションを作成できます。

スコープの制限については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。

### <a name="get-context-and-results-for-failures"></a>エラーのコンテキストと結果を取得する

スコープ単位でエラーをキャッチできるのは便利ですが、失敗したアクションや返されたエラーまたは状態コードを正確に把握するためには、スコープの結果だけでなくコンテキストが必要となります。 **@result()** ワークフロー関数を使用すると、スコープ内のすべてのアクションの結果に関するコンテキストが得られます。

**@result()** 関数は、単一のパラメーター (スコープの名前) を受け取り、そのスコープに含まれるアクションの結果をすべて含んだ配列を返します。 これらのアクションのオブジェクトには、アクションの開始時刻、終了時刻、状態、入力、相関 ID、出力など、**@actions()** オブジェクトと同じ属性を含まれます。 **@result()** 関数と **runAfter** プロパティを組み合わせるだけで、スコープ内で失敗したすべてのアクションのコンテキストを受け取ることができます。

スコープ内の **Failed** となったアクション "*ごとに*" アクションを実行し、失敗したアクションに到達するまで結果の配列をフィルター処理するには、**@result()** を **[[配列のフィルター処理]](../connectors/connectors-native-query.md)** アクションと **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)** ループと組み合わせて使用します。 抽出した結果の配列を **ForEach** ループに渡すことで、それぞれのエラーに対してアクションを実行することができます。 

次の例では、"My_Scope" というスコープ内で失敗したすべてのアクションの応答本文を含む HTTP POST 要求が送信されます (詳細については例の後に記載)。

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

上の例における実際の動作についての詳細は以下のとおりです。

1. "My_Scope" 内のすべてのアクションの結果を取得するために、**[配列のフィルター処理]** アクションによって **@result('My_Scope')** をフィルター処理します。

2. **[配列のフィルター処理]** の条件は、状態が **Failed** と等しいすべての **@result()** 要素です。 この条件により、"My_Scope" のすべてのアクションの結果を含む配列がフィルター処理され、失敗したアクションの結果のみを抽出した配列が得られます。

3. *フィルター後の配列*の出力に対して **For Each** ループ アクションを実行します。 このステップでは、フィルター処理済みの失敗したアクションの結果 "*ごとに*" 特定のアクションが実行されます。

   スコープ内の 1 つのアクションが失敗した場合、**foreach** 内のアクションは 1 回だけ実行されます。 
   失敗したアクションが複数ある場合は、エラーごとに 1 つのアクションが実行されます。

4. **foreach** 要素の応答本文すなわち **@item()['outputs']['body']** で HTTP POST を送信します。 **@result()** 要素の構造は **@actions()** と同じであり、同じ方法で解析することができます。

5. **@item()['name']** と **@item()['clientTrackingId']** という 2 つのカスタム ヘッダーが含まれます。前者は失敗したアクションの名前、後者は失敗した実行のクライアント追跡 ID です。

参考例として、前述の例で解析した **name**、**body**、**clientTrackingId** の各プロパティを含む 1 つの **@result()** 要素を次に示します。 **foreach** アクションの外側では、**@result()** によってこれらのオブジェクトの配列が返されます。

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

この記事の前出の式を使用することで、さまざまな例外処理パターンを実行できます。 フィルターで抽出したエラーの配列全体を、スコープ外の単一の例外処理アクションに渡して実行してもかまいません。その場合、**foreach** アクションは不要です。 前述した **@result()** の応答には、他にも便利なプロパティがあるので、それらを含めることもできます。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 診断とテレメトリ

ここで取り上げたパターンは、発生したエラーや例外を実行中に処理するうえで、きわめて効果的な方法です。しかし実行そのものとは切り離して、エラーを特定し、対応することもできます。 
[Azure 診断](../logic-apps/logic-apps-monitor-your-logic-apps.md)を使用すると、ワークフローで発生したあらゆるイベントを、実行とアクションのすべての状態を含めて、簡単に Azure Storage アカウントや Azure Event Hubs で作成されたイベント ハブに送信できます。 

ログやメトリックを監視したり、それらを好きな監視ツールに発行したりすることによって、実行の状態を評価することができます。 その中の一つの方法として、すべてのイベントを Event Hubs を介して [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) にストリーミングすることが考えられます。 Stream Analytics では、診断ログから得られる異常、平均値、またはエラーに基づいて適宜必要なクエリを記述できます。 Stream Analytics を使用して、キュー、トピック、SQL、Azure Cosmos DB、Power BI などのその他のデータ ソースに情報を送信できます。

## <a name="next-steps"></a>次の手順

* [Azure Logic Apps を使用したエラー処理の構築方法を確認する](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [さらに他の Logic Apps の例とシナリオを見る](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9