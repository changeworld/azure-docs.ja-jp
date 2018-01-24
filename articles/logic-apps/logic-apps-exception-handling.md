---
title: "Azure での Logic Apps のエラーと例外の処理 | Microsoft Docs"
description: "Logic Apps におけるエラーと例外の処理パターン。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Logic Apps におけるエラーと例外の処理

Azure の Logic Apps には、安定した統合を実現すると共に障害からの回復力を高めるためのツールとパターンが豊富に用意されています。 すべての統合アーキテクチャには、依存システムによって発生するダウンタイムや問題を適切に処理できるようにするという課題があります。 Logic Apps は、最上級のエクスペリエンスでエラーを処理します。 ワークフローの例外とエラーを操作するために必要なツールが用意されています。

## <a name="retry-policies"></a>再試行ポリシー

例外とエラーを処理するうえで最も基本的な方法が再試行ポリシーです。 最初の要求がタイムアウトになるか失敗した場合 (要求への応答として 429 または 5xx が返された場合)、再試行ポリシーは、アクションを再試行するかどうかと再試行方法を定義します。 

再試行ポリシーには、既定、なし、固定間隔、および指数関数的な間隔の 4 種類があります。 ワークフロー定義に再試行ポリシーが指定されていない場合は、サービスによって定義された既定のポリシーが使用されます。 

再試行ポリシーを特定のアクションの *inputs* で構成するか、再試行可能な場合にトリガーできます。 同様に、ロジック アプリ デザイナーで再試行ポリシーを構成できます (該当する場合)。 再試行ポリシーを設定するには、ロジック アプリ デザイナーで特定のアクションの **[設定]**に移動します。

再試行ポリシーの制限については、「[Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md)」を参照してください。 サポートされる構文の詳細については、「[ワークフローのトリガーとアクション][retryPolicyMSDN]」の再試行ポリシーに関するセクションを参照してください。

### <a name="default"></a>既定

再試行ポリシーを定義しない (**retryPolicy** が未定義) 場合は、既定のポリシーが使用されます。 既定のポリシーは、指数関数的な間隔を使用するポリシーであり、7.5 秒で指数関数的に増加する間隔で、最大 4 回の再試行を送信します。 間隔は 5 ～ 45 秒に制限されます。 この既定のポリシーは、次の HTTP ワークフロー定義例で使用されているポリシーに相当します。

```json
"HTTP":
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

### <a name="none"></a>なし

**retryPolicy** が **none** に設定されている場合、失敗した要求は再試行されません。

| 要素名 | 必須 | 型 | 説明 |
| ------------ | -------- | ---- | ----------- |
| type | はい | String | "**なし**" |

### <a name="fixed-interval"></a>固定間隔

**retryPolicy** が **fixed** に設定されている場合、ポリシーは、指定された時間待機した後で次の要求を送信することで、失敗した要求を再試行します。

| 要素名 | 必須 | 型 | 説明 |
| ------------ | -------- | ---- | ----------- |
| type | はい | String | **fixed** |
| count | はい | Integer | 再試行回数。 1 ～ 90 の間である必要があります。 |
| interval | はい | String | [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)の再試行間隔。 PT5S ～ PT1D の間である必要があります。 |

### <a name="exponential-interval"></a>指数関数的な間隔

**retryPolicy**が **exponential** に設定されている場合、ポリシーは、指数関数的に増加する範囲のランダムな時間間隔で、失敗した要求を再試行します。 各々の再試行は、**minimumInterval** より大きく **maximumInterval** より小さいランダムな間隔で送信されることが保証されています。 次の表に示されている範囲の均一のランダム変数が、再試行ごとに最大 **count** 回生成されます。

**ランダム変数の範囲**

| 再試行回数 | 最小間隔 | 最大間隔 |
| ------------ |  ------------ |  ------------ |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| ... |

指数関数的な種類のポリシーでは、**count** と **interval** は必須です。 **minimumInterval** と **maximumInterval** の値は省略可能です。 それらを追加して、既定値である PT5S と PT1D をそれぞれ上書きできます。

| 要素名 | 必須 | 型 | 説明 |
| ------------ | -------- | ---- | ----------- |
| 型 | はい | String | **exponential** |
| count | はい | Integer | 再試行回数。 1 ～ 90 の間である必要があります。  |
| interval | はい | String | [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)の再試行間隔。 PT5S ～ PT1D の間である必要があります。 |
| minimumInterval | いいえ  | String | [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)の再試行の最小間隔。 PT5S ～ **interval** の間である必要があります。 |
| maximumInterval | いいえ  | String | [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)の再試行の最小間隔。 **interval** ～ PT1D の間である必要があります。 |

## <a name="catch-failures-with-the-runafter-property"></a>runAfter プロパティを使用してエラーをキャッチする

ロジック アプリの各アクションは、そのアクションを開始する前にどのアクションが完了されている必要があるかを宣言します。 それは、ワークフローのステップを順序付けすることに似ています。 アクション定義では、この順序付けは、**runAfter** プロパティで指定されます。 

**runAfter** プロパティは、特定のアクションを実行するうえでの前提条件となるアクションとその状態を記述するオブジェクトです。 既定では、ロジック アプリ デザイナーを使用して追加されたすべてのアクションは、先行する手順の結果が **Succeeded** であれば、その手順の次に実行されるように設定されます。 

ただし、**runAfter** の値をカスタマイズして、先行するアクションの結果が **Failed**、**Skipped**、またはこれらの値の可能なセットである場合に、アクションを開始するように設定できます。 特定のアクションである **Insert_Row** が失敗した後、指定した Azure Service Bus トピックに項目を追加する場合は、次の **runAfter** 構成を使用できます。

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

**runAfter** は、**Insert_Row** アクションの結果が **Failed** の場合に開始するように設定されていることに注意してください。 アクションの状態が **Succeeded**、**Failed**、または **Skipped** の場合にアクションを実行するには、次の構文を使用します。

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 先行するアクションが失敗した後で実行され、正常に完了したアクションは **Succeeded** とマークされます。 この動作は、ワークフロー内で発生したすべてのエラーが正常にキャッチされた場合、実行そのものは **Succeeded** とマークされることを意味します。

## <a name="scopes-and-results-to-evaluate-actions"></a>アクションを評価するためのスコープと結果

複数のアクションを[スコープ](../logic-apps/logic-apps-loops-and-scopes.md)内にグループ化して、個々のアクションを実行するのと同じように実行できます。 スコープは、アクションの論理グループとして機能します。 

スコープを活用して、ロジック アプリのアクションを体系化したり、スコープの状態に対して全体的な評価を実行したりできます。 スコープ自体には、スコープ内のすべてのアクションが完了した後に状態が返されます。 スコープの状態は、実行条件と同じ条件で判定されます。 実行分岐の最後のアクションが **Failed** または **Aborted** である場合、状態は **Failed** になります。

スコープ内で何らかのエラーが発生した場合に特定のアクションを実行するには、**Failed** とマークされたスコープで **runAfter** を使用できます。 スコープ内の "*いずれかの*" アクションが失敗したときに、スコープに対して **runAfter** を使用する場合は、エラーをキャッチする 1 つのアクションを作成できます。

### <a name="get-the-context-of-failures-with-results"></a>結果を使用してエラーのコンテキストを取得する

スコープ単位でエラーをキャッチできるのは便利ですが、失敗したアクションや返されたエラーまたは状態コードを正確に把握するためには、スコープの結果だけでなくコンテキストが必要となります。 **@result()** ワークフロー関数を使用すると、スコープ内のすべてのアクションの結果に関するコンテキストが得られます。

**@result()** 関数は、単一のパラメーター (スコープ名) を受け取り、そのスコープに含まれるアクションの結果をすべて含んだ配列を返します。 これらのアクション オブジェクトには、**@actions()** オブジェクトと同じ属性が存在します (アクションの開始時刻と終了時刻、アクションの状態、アクションの入力、アクションの相関関係 ID、アクションの出力など)。 

**@result()** 関数と **runAfter** プロパティを組み合わせるだけで、スコープ内で失敗したすべてのアクションのコンテキストを受け取ることができます。

スコープ内の **Failed** となったアクション "*ごとに*" 特定のアクションを実行し、結果の配列をフィルター処理して失敗したアクションを抽出するには、**@result()** を [Filter_array](../connectors/connectors-native-query.md) アクションと [foreach](../logic-apps/logic-apps-loops-and-scopes.md) ループと組み合わせて使用します。 抽出した結果の配列を **foreach** ループに渡すことで、それぞれのエラーに対してアクションを実行することができます。 

My_Scope というスコープ内で失敗したアクションの応答本文で HTTP POST 要求を送信する例を次に示します。

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

1. My_Scope 内のすべてのアクションの結果を取得するために、**Filter_array** アクションによって **@result('My_Scope')** をフィルター処理します。

2. **Filter_array** の条件は、状態が **Failed** と等しいすべての **@result()** 要素です。 この条件により、My_Scope のすべてのアクションの結果を含む配列がフィルター処理され、失敗したアクションの結果のみを抽出した配列が得られます。

3. *フィルター後の配列*の出力に対して **foreach** アクションを実行します。 このステップでは、フィルター処理済みの失敗したアクションの結果 "*ごとに*" 特定のアクションが実行されます。

    スコープ内の 1 つのアクションが失敗した場合、**foreach** 内のアクションは 1 回だけ実行されます。 失敗したアクションが複数ある場合は、エラーごとに 1 つのアクションが実行されます。

4. **foreach** 要素の応答本文または **@item()['outputs']['body']** で HTTP POST を送信します。 **@result()** 要素のシェイプは、**@actions()** のシェイプと同じです。 それは同じ方法で解析できます。

5. **@item()['name']** と **@item()['clientTrackingId']** という 2 つのカスタム ヘッダーが含まれます。前者は失敗したアクションの名前、後者は失敗した実行のクライアント追跡 ID です。

参考までに単一の **@result()** 要素の例を次に示します。 それは、前の例で解析される **name**、**body**、および **clientTrackingId** の各プロパティを示しています。 **foreach** アクションの外側では、**@result()** によってこれらのオブジェクトの配列が返されます。

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

さまざまな例外処理パターンで、この記事で説明した式を使用できます。 フィルターで抽出したエラーの配列全体を、スコープ外の単一の例外処理アクションに渡して実行してもかまいません。その場合、**foreach** は不要です。 前述した **@result()** の応答には、他にも便利なプロパティがあるので、それらを含めることもできます。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 診断とテレメトリ

この記事で取り上げたパターンは、実行時に発生したエラーや例外を処理するうえで、きわめて効果的な方法ですが、実行そのものとは切り離して、エラーを特定して対応することもできます。 ワークフローで発生したあらゆるイベント (実行とアクションのすべての状態を含みます) は、[Azure 診断](../logic-apps/logic-apps-monitor-your-logic-apps.md)を使用することで簡単に Azure Storage アカウントや Azure Event Hub のイベント ハブに送信できます。 

ログやメトリックを監視したり、それらを好きな監視ツールに発行したりすることによって、実行の状態を評価できます。 その中の一つの方法として、すべてのイベントを Event Hub を介して [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) にストリーミングすることが考えられます。 Stream Analytics では、診断ログから得られる異常、平均値、またはエラーに基づいて適宜必要なクエリを記述できます。 Stream Analytics を使用して、キュー、トピック、SQL、Azure Cosmos DB、Power BI などのその他のデータ ソースに情報を送信できます。

## <a name="next-steps"></a>次の手順

* [Azure での Logic Apps を使用したエラー処理の構築方法を確認する](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)。
* [さらに他の Logic Apps の例とシナリオを見る](../logic-apps/logic-apps-examples-and-scenarios.md)。
* [ロジック アプリの自動デプロイの作成方法を確認する](../logic-apps/logic-apps-create-deploy-template.md)。
* [Visual Studio でロジック アプリをビルドしてデプロイする方法を確認する](logic-apps-deploy-from-vs.md)。

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
