---
title: "Logic Apps の例外処理 | Microsoft Docs"
description: "Azure Logic Apps におけるエラーと例外の処理パターンについて説明します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 04d1e26cd88fa6324ba2ea3cc64ae0819b47f3f9


---
# <a name="logic-apps-error-and-exception-handling"></a>Logic Apps のエラーと例外処理
Logic Apps には、連携処理の堅牢性を強化すると共に障害からの回復力を高めるためのツールとパターンが豊富に用意されています。  あらゆる統合アーキテクチャに言えることですが、依存システムのダウンタイムや問題を適切に処理することが課題のひとつとして挙げられます。  Logic Apps は、ワークフロー内で発生した例外やエラーを処理するうえで必要なツールを備えており、きわめて高度なエラー処理が可能となっています。

## <a name="retry-policies"></a>再試行ポリシー
例外とエラーの最も基本的なタイプの処理は再試行ポリシーです。  初回要求がタイムアウトまたは失敗した (要求への応答として 429 または 5xx が返された) 場合にアクションを再試行するかどうかは、このポリシーによって定義されます。  すべてのアクションは、既定により 20 秒間隔で 4 回再試行されます。  つまり最初の要求から `500 Internal Server Error` 応答が返された場合、ワークフロー エンジンは 20 秒間の一時停止後、要求を再試行します。  全部の再試行を終えても例外またはエラーが返される場合、ワークフローは続行され、そのアクションの状態は `Failed`としてマークされます。

再試行ポリシーは、特定のアクションの **inputs** で構成できます。  1 時間間隔で 4 回試行するように再試行ポリシーを構成することができます。  input プロパティの詳細については、[MSDN][retryPolicyMSDN] をご覧ください。

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

HTTP アクションの再試行回数を 4 回とし、試行までの待機時間を 10 分とする場合、次のように定義します。

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

サポートされている構文の詳細については、[MSDN の再試行ポリシーに関するセクション][retryPolicyMSDN]をご覧ください。

## <a name="runafter-property-to-catch-failures"></a>エラーをキャッチする RunAfter プロパティ
ロジック アプリのアクションにはそれぞれ、どのアクションの完了を待って開始すべきかが宣言されています。  これは、ワークフローにおけるステップの順序指定と考えることができます。  アクションの定義では、この順序が `runAfter` プロパティで指定されます。  これは特定のアクションを実行するうえでの前提条件となるアクションとその状態を記述したオブジェクトです。  既定では、デザイナーを介して追加されたアクションはすべて、直前のステップの後、かつそのステップが `Succeeded` であった場合に実行されるよう `runAfter` で設定されます。  ただし、この値はカスタマイズすることができます。直前のアクションが `Failed` であった場合や `Skipped` であった場合にアクションを実行できるほか、これらの値を自由に組み合わせて実行条件とすることもできます。  `Insert_Row` という特定のアクションが失敗した後に、指定した Service Bus トピックに項目を追加する場合、`runAfter` を次のように設定します。

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

`Insert_Row` アクションが `Failed` であった場合に実行されるよう `runAfter` プロパティが設定されていることに注目してください。  アクションの状態が `Succeeded`、`Failed`、`Skipped` のいずれかであった場合にアクションを実行するのであれば、次の構文を使用することになります。

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 先行するアクションが失敗した後に実行されて正常に完了したアクションは `Succeeded` としてマークされます。  この動作は、ワークフロー内で発生したすべてのエラーが正常にキャッチされた場合、その実行そのものは `Succeeded`としてマークされることを意味します。
> 
> 

## <a name="scopes-and-results-to-evaluate-actions"></a>アクションを評価するためのスコープと結果
アクションの実行条件には、個々のアクションを指定するだけでなく、複数のアクションを[スコープ](../logic-apps/logic-apps-loops-and-scopes.md)としてグループ化して指定することもできます。このときスコープは、アクションの論理的なまとまりとして作用します。  ロジック アプリのアクションを体系化したり、スコープの状態に対して全体的な評価を実行したりする場合にスコープを活用できます。  スコープそのものには、そこに含まれるすべてのアクションが完了した後の状態が適用されます。  スコープの状態は、実行と同じ基準で決定されます。つまり実行分岐の最後のアクションが `Failed` または `Aborted` であった場合に状態が `Failed` になります。

スコープが `Failed` としてマークされたことを `runAfter` の条件とすることで、スコープ内で何らかのエラーが発生した場合に特定のアクションを実行することができます。  "スコープでエラーが発生した後" という実行条件によって、スコープ内の " *いずれかの* " アクションが失敗した場合に発生するエラーを&1; つのアクションでキャッチすることができます。

### <a name="getting-the-context-of-failures-with-results"></a>結果と共にエラーのコンテキストを取得する
スコープ単位でエラーをキャッチできるのはとても便利ですが、失敗したアクションや返されたエラー コード (または状態コード) を正確に把握するためには、スコープの結果だけでなくコンテキストが必要となります。  `@result()` ワークフロー関数を使用すると、スコープ内のすべてのアクションの結果にコンテキストが与えられます。

`@result()` は、単一のパラメーターとしてスコープ名を受け取り、そのスコープに含まれるアクションの結果をすべて含んだ配列を返します。  これらのアクション オブジェクトには、 `@actions()` オブジェクトと同じ属性が存在します (アクションの開始時刻と終了時刻、アクションの状態、アクションの入力、アクションの相関関係 ID、アクションの出力など)。  `runAfter` に `@result()` 関数を組み合わせるだけで、スコープ内で失敗したすべてのアクションのコンテキストを受け取ることができます。

スコープ内の失敗した (状態が `Failed` の) アクション "*ごと*" に特定のアクションを実行するには、配列の**[フィルター処理](../connectors/connectors-native-query.md)**アクションと **[ForEach](../logic-apps/logic-apps-loops-and-scopes.md)** ループに `@result()` を組み合わせます。  実行結果の配列から、失敗したアクションだけをフィルターで抽出することができます。  抽出した結果の配列を **ForEach** ループに渡すことで、それぞれのエラーに対してアクションを実行することができます。  以下に示したのはその例です。コード例に続けてその内容を詳しく説明しています。  この例では、`My_Scope` というスコープ内で失敗したすべてのアクションの応答本文で HTTP POST 要求を送信します。

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

以下、このコードの内容について詳しく解説します。

1. **配列のフィルター処理**アクションで `@result('My_Scope')` から、`My_Scope` 内のすべてのアクションの結果を抽出します。
2. **配列のフィルター処理**の条件は、状態が `Failed` と等しいすべての `@result()` 要素です。  これで、 `My_Scope` 内のすべてのアクションの結果を含んだ配列から、失敗したアクションの結果だけを含んだ配列が抽出されます。
3. **フィルター後の配列**の出力に対して **For Each** アクションを実行します。  これで既にフィルターで抽出してある失敗したアクションの結果に対し、" *個別に* " アクションが実行されます。
   * スコープ内で失敗したアクションが&1; つだけであれば、 `foreach` 内のアクションは&1; 回しか実行されません。  アクションが失敗した場合に実行するアクションは、エラーごとに&1; つとするのが一般的です。
4. `foreach` 要素の応答本文 (`@item()['outputs']['body']`) で HTTP POST を送信します。  `@result()` 要素の構造は `@actions()` と同じであり、同じ方法で解析することができます。
5. 加えて、`@item()['name']` と `@item()['clientTrackingId']` という&2; つのカスタム ヘッダーが存在します。前者は、失敗したアクションの名前、後者は、失敗した実行のクライアント追跡 ID です。

以下、参考までに単一の `@result()` 要素の例を記載します。  前出のコード例で解析した `name`、`body`、`clientTrackingId` の各プロパティが確認できます。  `foreach` がなければ、これらのオブジェクトの配列が `@result()` から返されることに注意してください。

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
            "message": "/docs/foo/bar does not exist"
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

前出の式を使用することで、さまざまな例外処理パターンを実行できます。  フィルターで抽出したエラーの配列全体を、スコープ外の単一の例外処理アクションに渡して実行してもかまいません。その場合、`foreach` は不要です。  前述した `@result()` の応答には、他にも便利なプロパティがあるので、それらを含めることもできます。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 診断とテレメトリ
ここで取り上げたパターンは、実行時に発生したエラーや例外を処理するうえで、きわめて効果的な方法です。しかし実行そのものとは切り離して、エラーを特定し、対応することもできます。  ワークフローで発生したあらゆるイベント (実行とアクションのすべての状態を含む) は、[Azure 診断](../logic-apps/logic-apps-monitor-your-logic-apps.md)を使用することで簡単に Azure Storage アカウントや Azure Event Hub に送信することができます。  ログやメトリックを監視したり、それらを好きな監視ツールに発行したりすることによって、実行の状態を評価することができます。  その中の一つの方法として、すべてのイベントを Azure Event Hub を介して [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) にストリーミングすることが考えられます。  Stream Analytics では、診断ログから得られる異常、平均、失敗に基づいて適宜必要なクエリを記述することができます。  Stream Analytics からの出力は、キューやトピック、SQL、DocumentDB、Power BI などの他のデータ ソースに簡単に渡すことができます。

## <a name="next-steps"></a>次のステップ
* [Logic Apps を使用した堅牢なエラー処理のユース ケースを見る](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [さらに他の Logic Apps の例とシナリオを見る](../logic-apps/logic-apps-examples-and-scenarios.md)
* [ロジック アプリのデプロイ テンプレートの作成方法を見る](../logic-apps/logic-apps-create-deploy-template.md)
* [Visual Studio からロジック アプリを設計してデプロイする](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9



<!--HONumber=Jan17_HO3-->


