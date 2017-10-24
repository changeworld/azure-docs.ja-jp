---
title: "Azure のDurable Functions での HTTP API"
description: "Azure Functions の Durable Functions 拡張機能で HTTP API を実装する方法を説明します。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: bb5361022e4c9693812753ae33df5aeb037b5aaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) での HTTP API

Durable Task 拡張機能は、次のタスクの実行で使用できる一連の HTTP API を公開します。

* オーケストレーション インスタンスの状態を取得します。
* 待機中のオーケストレーション インスタンスにイベントを送信します。
* 実行中のオーケストレーション インスタンスを終了します。

これらの各 HTTP API は、Durable Task 拡張機能によって直接処理される webhook 操作です。 関数アプリの関数に固有のものではありません。

> [!NOTE]
> これらの操作は、[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスのインスタンス管理 API を使用して直接呼び出すこともできます。 詳しくは、[インスタンス管理](durable-functions-instance-management.md)に関する記事をご覧ください。

## <a name="http-api-url-discovery"></a>HTTP API URL の検出

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスでは、[CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API を公開します。これは、サポートされるすべての操作へのリンクを含む HTTP 応答ペイロードを生成するのに使用できます。 この API の使用方法を示す HTTP トリガー関数の例を次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

この例の関数では、次の JSON 応答データが生成されます。 すべてのフィールドのデータ型は `string` です。

| フィールド             |Description                           |
|-------------------|--------------------------------------|
| id                |オーケストレーション インスタンスの ID。 |
| statusQueryGetUri |オーケストレーション インスタンスの状態の URL。 |
| sendEventPostUri  |オーケストレーション インスタンスの "イベント発生" URL。 |
| terminatePostUri  |オーケストレーション インスタンスの "終了" URL。 |

次は応答の例です。

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> webhook URL の形式は、実行している Azure Functions ホストのバージョンによって異なる場合があります。 上記の例は、Azure Functions 2.0 ホスト用の形式です。

## <a name="async-operation-tracking"></a>非同期操作の追跡

上記の HTTP 応答は、Durable Functions で実行時間の長い HTTP 非同期 API を実装するのに役立つように設計されています。 これは、*ポーリング コンシューマー パターン*と呼ばれる場合もあります。 クライアント/サーバー フローは次のように動作します。

1. クライアントが、オーケストレーター関数など、実行時間の長いプロセスを開始する HTTP 要求を発行します。
2. 対象の HTTP トリガーが、`statusQueryGetUri` 値の `Location` ヘッダーを含む HTTP 202 応答を返します。
3. クライアントが `Location` ヘッダー内の URL をポーリングします。 クライアントは引き続き、`Location` ヘッダーを含む HTTP 202 応答を参照します。
4. インスタンスが完了 (または失敗) すると、`Location` ヘッダー内のエンドポイントから HTTP 200 が返されます。

このプロトコルでは、HTTP エンドポイントのポーリングと `Location` ヘッダーのフォローをサポートする、外部のクライアントまたはサービスでの実行時間の長いプロセスを調整できます。 基本の部分は、Durable Functions HTTP API に既に組み込まれています。

> [!NOTE]
> 既定では、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) によって提供される HTTP ベースのすべてのアクションで、標準的な非同期操作パターンがサポートされています。 これにより、実行時間の長い Durable Functions を Logic Apps ワークフローの一部として組み込むことができます。 Logic Apps での非同期 HTTP パターンのサポートについて詳しくは、[Azure Logic Apps ワークフロー アクションおよびトリガーに関するドキュメント](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns)をご覧ください。

## <a name="http-api-reference"></a>HTTP API リファレンス

拡張機能によって実装されるすべての HTTP API では、次のパラメーターを指定します。 すべてのパラメーターのデータ型は `string` です。

| パラメーター  | パラメーターのタイプ  | Description |
|------------|-----------------|-------------|
| instanceId | URL             | オーケストレーション インスタンスの ID。 |
| taskHub    | クエリ文字列    | [タスク ハブ](durable-functions-task-hubs.md)の名前。 指定しない場合は、現在の関数アプリのタスク ハブの名前が想定されます。 |
| connection | クエリ文字列    | ストレージ アカウントの接続文字列の**名前**。 指定しない場合は、関数アプリの既定の接続文字列が想定されます。 |
| systemKey  | クエリ文字列    | API の呼び出しに必要な承認キー。 |

`systemKey` は、Azure Functions ホストによって自動生成される承認キーです。 このキーにより、Durable Task 拡張機能 API へのアクセスが特別に許可されます。また、このキーは[他の承認キー](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)と同じ方法で管理できます。 前述の `CreateCheckStatusResponse` API を使用すると、`systemKey` 値を最も簡単に検出できます。

以降のセクションで、この拡張機能でサポートされる特定の HTTP API とその使用方法の例について説明します。

### <a name="get-instance-status"></a>インスタンスの状態を取得する

指定されたオーケストレーション インスタンスの状態を取得します。

#### <a name="request"></a>要求

Functions 1.0 の場合、要求形式は次のようになります。

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0 形式では、パラメーターはすべて同じですが、URL プレフィックスが若干異なります。

```http
GET /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="response"></a>応答

返される可能性がある状態コード値は、いくつかあります。

* **HTTP 200 (OK)**: 指定されたインスタンスが完了状態。
* **HTTP 202 (Accepted)**: 指定されたインスタンスが処理中。
* **HTTP 400 (Bad Request)**: 指定されたインスタンスが失敗したか終了した。
* **HTTP 404 (Not Found)**: 指定されたインスタンスが存在しないか実行開始されていない。

**HTTP 200** と **HTTP 202** の場合の応答ペイロードは、次のフィールドを持つ JSON オブジェクトです。

| フィールド           | データ型 | Description |
|-----------------|-----------|-------------|
| runtimeStatus   | string    | インスタンスの実行時状態。 値には、*Running*、*Pending*、*Failed*、*Canceled*、*Terminated*、*Completed* があります。 |
| input           | JSON      | インスタンスを初期化するために使用される JSON データ。 |
| output          | JSON      | インスタンスの JSON の出力。 インスタンスが完了状態でない場合、このフィールドは `null` です。 |
| createdTime     | string    | インスタンスが作成された時刻。 ISO 8601 の拡張された表記を使用します。 |
| lastUpdatedTime | string    | インスタンスが最後に保持されていた時刻。 ISO 8601 の拡張された表記を使用します。 |

応答ペイロードの例を次に示します (読みやすいように書式設定されています)。

```json
{
  "runtimeStatus": "Completed",
  "input": null,
  "output": [
    "Hello Tokyo!",
    "Hello Seattle!",
    "Hello London!"
  ],
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T18:30:30Z"
}
```

**HTTP 202** の応答には、前述の `statusQueryGetUri` フィールドと同じ URL を参照する **Location** 応答ヘッダーも含まれています。

### <a name="raise-event"></a>イベントを発生させる

実行中のオーケストレーション インスタンスにイベント通知メッセージを送信します。

#### <a name="request"></a>要求

Functions 1.0 の場合、要求形式は次のようになります。

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Functions 2.0 形式では、パラメーターはすべて同じですが、URL プレフィックスが若干異なります。

```http
POST /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれています。

| フィールド       | パラメーターのタイプ  | データ型 | Description |
|-------------|-----------------|-----------|-------------|
| eventName   | URL             | string    | ターゲット オーケストレーション インスタンスが待機しているイベントの名前。 |
| {content}   | 要求内容 | JSON      | JSON 形式のイベント ペイロード。 |

#### <a name="response"></a>応答

返される可能性がある状態コード値は、いくつかあります。

* **HTTP 202 (Accepted)**: 発生したイベントが受け入れられて処理された。
* **HTTP 400 (Bad request)**: 要求内容が `application/json` タイプまたは有効な JSON でなかった。
* **HTTP 404 (Not Found)**: 指定されたインスタンスが見つからなかった。
* **HTTP 410 (Gone)**: 指定されたインスタンスが完了または失敗し、発生したイベントを処理できない。

**operation** という名前のイベント ([Counter](durable-functions-counter.md) サンプルから取得) を待機するインスタンスに JSON 文字列 `"incr"` を送信する要求の例を次に示します。

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

この API の応答には内容は含まれません。

### <a name="terminate-instance"></a>インスタンスを終了する

実行中のオーケストレーション インスタンスを終了します。

#### <a name="request"></a>要求

Functions 1.0 の場合、要求形式は次のようになります。

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0 形式では、パラメーターはすべて同じですが、URL プレフィックスが若干異なります。

```http
DELETE /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

この API の要求パラメーターには、前述の既定のセットと、次の固有のパラメーターが含まれます。

| フィールド       | パラメーターのタイプ  | データ型 | Description |
|-------------|-----------------|-----------|-------------|
| reason      | クエリ文字列    | string    | 省略可能。 オーケストレーション インスタンスの終了の理由。 |

#### <a name="response"></a>応答

返される可能性がある状態コード値は、いくつかあります。

* **HTTP 202 (Accepted)**: 終了要求が受け入れられて処理された。
* **HTTP 404 (Not Found)**: 指定されたインスタンスが見つからなかった。
* **HTTP 410 (Gone)**: 指定されたインスタンスが完了したか失敗した。

実行中のインスタンスを終了させ、**バグ**の理由を示す要求の例を次に示します。

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

この API の応答には内容は含まれません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [エラーの対処方法を知る](durable-functions-error-handling.md)
