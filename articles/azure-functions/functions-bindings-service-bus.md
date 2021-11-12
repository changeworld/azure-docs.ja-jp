---
title: Azure Functions における Azure Service Bus のバインド
description: Azure Functions で Azure Service Bus トリガーとバインドを送信する方法を説明します。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 124e1f104bf7607b63eac916754c86b7b28cd8a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470435"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions における Azure Service Bus のバインド

Azure Functions は[トリガーとバインド](./functions-triggers-bindings.md)を使用して [Azure Service Bus](https://azure.microsoft.com/services/service-bus) と統合されます。 Service Bus と統合すると、キューまたはトピック メッセージに応答して送信する関数を構築できます。

| アクション | Type |
|---------|---------|
| Service Bus キューまたはトピック メッセージが作成されたときに関数を実行する | [トリガー](./functions-bindings-service-bus-trigger.md) |
| Azure Service Bus メッセージを送信する |[出力バインド](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 4.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

#### <a name="service-bus-extension-5x-and-higher"></a>Service Bus 拡張機能 5.x 以降

Service Bus のバインド拡張機能の新しいバージョンをご利用いただけます。 [シークレットではなく ID を使用して接続する](./functions-reference.md#configure-an-identity-based-connection)機能が導入されています。 マネージド ID を使用して関数アプリを構成するチュートリアルについては、[ID ベースの接続を使用した関数アプリの作成に関するチュートリアル](./functions-identity-based-connections-tutorial.md)を参照してください。 .NET アプリケーションの場合は、新しい拡張機能バージョンによって、バインドできる型も変更されます。これにより、`Microsoft.ServiceBus.Messaging` および `Microsoft.Azure.ServiceBus` の型が [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus) の新しい型に置き換えられます。

この拡張機能のバージョンは、[NuGet パッケージ] バージョン 5.x をインストールすることによって利用できます。または、`host.json` ファイルに次のものを追加して、拡張機能バンドル v3 から追加することもできます。

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[3.3.0, 4.0.0)"
  }
}
```

詳細については、[ユーザーの更新]に関するページを参照してください。

[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。


<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md)」を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            },
            "batchOptions": {
                "maxMessageCount": 1000,
                "operationTimeout": "00:01:00",
                "autoComplete": true
            }
        }
    }
}
```

`isSessionsEnabled` を `true` に設定している場合は、`sessionHandlerOptions` が優先されます。  `isSessionsEnabled` を `false` に設定している場合は、`messageHandlerOptions` が優先されます。

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|prefetchCount|0|メッセージの受信者が同時に要求できるメッセージ数を取得または設定します。|
|messageHandlerOptions.maxAutoRenewDuration|00:05:00|メッセージ ロックが自動的に更新される最大間隔。|
|messageHandlerOptions.autoComplete|true|トリガーが処理後に自動的に complete を呼び出す必要があるか、または関数コードで complete を手動で呼び出すかどうか。<br><br>`false` に設定することは、C# でのみサポートされています。<br><br>`true` に設定した場合、関数の実行が正常に完了するとトリガーによって自動的にメッセージが完了され、それ以外の場合はメッセージが破棄されます。<br><br>`false` に設定する場合は、[MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) を呼び出し、メッセージを完了、破棄、または配信不能にする必要があります。 例外がスローされた場合 (かつ `MessageReceiver` メソッドが呼び出されなかった場合)、ロックは維持されます。 ロックが期限切れになると、メッセージはキューに再登録されて `DeliveryCount` はインクリメントされ、ロックは自動的に更新されます。<br><br>C# 以外の関数では、関数で例外が発生すると、ランタイムによってバックグラウンドで `abandonAsync` が呼び出されます。 例外が発生しなかった場合は、バックグラウンドで `completeAsync` が呼び出されます。 |
|messageHandlerOptions.maxConcurrentCalls|16|スケーリングされたインスタンスごとにメッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数。 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。|
|sessionHandlerOptions.maxConcurrentSessions|2000|スケーリングされたインスタンスごとに同時に処理できるセッションの最大数。|
|batchOptions.maxMessageCount|1000| トリガーされたときに、関数に送信されるメッセージの最大数。 |
|batchOptions.operationTimeout|00:01:00| `hh:mm:ss` で表される期間の値。 |
|batchOptions.autoComplete|true| `messageHandlerOptions.autoComplete` については、上記の説明を参照してください。 |

### <a name="additional-settings-for-version-5x"></a>バージョン 5.x 以降の追加設定

次の host.json ファイルの例には、バージョン 5.0.0 以降の Service Bus 拡張機能の設定のみが含まれています。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "clientRetryOptions":{
                "mode": "exponential",
                "tryTimeout": "00:01:00",
                "delay": "00:00:00.80",
                "maxDelay": "00:01:00",
                "maxRetries": 3
            },
            "prefetchCount": 0,
            "autoCompleteMessages": true,
            "maxAutoLockRenewalDuration": "00:05:00",
            "maxConcurrentCalls": 16,
            "maxConcurrentSessions": 8,
            "maxMessages": 1000,
            "sessionIdleTimeout": "00:01:00"
        }
    }
}
```

Service bus 拡張機能バージョン 5.x 以降を使用する場合、`ServiceBusOptions` にある 2.x の設定に加えて、次のグローバル構成設定がサポートされます。

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|prefetchCount|0|メッセージの受信者が同時に要求できるメッセージ数を取得または設定します。|
|autoCompleteMessages|true|関数の実行に成功した後、メッセージを自動的に完了するかどうかを決定します。これは、`autoComplete` 構成設定の代わりに使用します。|
|maxAutoLockRenewalDuration|00:05:00|これは、`maxAutoRenewDuration` の代わりに使用する必要があります|
|maxConcurrentCalls|16|スケーリングされたインスタンスごとにメッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数。 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。|
|maxConcurrentSessions|8|スケーリングされたインスタンスごとに同時に処理できるセッションの最大数。|
|maxMessages|1000|各関数呼び出しに渡されるメッセージの最大数。 これは、メッセージをバッチで受け取る関数にのみ適用されます。|
|sessionIdleTimeout|該当なし|現在アクティブなセッションでメッセージを受信するまでの最大待機時間。 この時間が経過すると、プロセッサのセッションが終了し、別のセッションの処理が試みられます。|

### <a name="retry-settings"></a>再試行の設定

バージョン 5.x 以降の Service Bus 拡張機能を使用している場合、上記の構成プロパティだけでなく、`RetryOptions` の中から `ServiceBusOptions` も構成できます。 これらの設定では、失敗した操作を再試行するかどうか、および再試行の間隔を指定します。 このオプションは、メッセージの受信や Service Bus サービスとのその他のやり取りに許容される時間も制御します。

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|mode|指数|再試行の遅延を計算するために使用する方法です。 既定の指数モードでは、バックオフ戦略に基づいて再試行が行われます。この場合、再試行の度に待機する期間が増加します。 `Fixed` モードでは、一定の間隔で再試行され、遅延はそれぞれ一定です。|
|tryTimeout|00:01:00|試行ごとに操作を待機する最大期間です。|
|delay|00:00:00.80|再試行の間に適用する遅延またはバックオフ係数です。|
|maxDelay|00:01:00|許容される再試行の間の最大遅延です。|
|maxRetries|3|関連する操作が失敗したと判断するまでの再試行の最大回数です。|

---

## <a name="next-steps"></a>次のステップ

- [Service Bus キューまたはトピック メッセージが作成されたときに関数を実行する (トリガー)](./functions-bindings-service-bus-trigger.md)
- [Azure Functions から Azure Service Bus メッセージを送信する (出力バインド)](./functions-bindings-service-bus-output.md)
