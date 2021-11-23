---
title: Event Grid の問題のトラブルシューティング
description: この記事では、Azure Event Grid の問題をトラブルシューティングするためのさまざまな方法を紹介します。
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: c055c3ce926844a976c95aece7888b595125f414
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517560"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Azure Event Grid の問題のトラブルシューティング
この記事では、Azure Event Grid の問題をトラブルシューティングするのに役立つ情報を提供します。 

## <a name="diagnostic-logs"></a>診断ログ
Event Grid のトピックまたはドメインの診断設定を有効にして、発行および配信のエラー ログをキャプチャして表示します。 詳細については、[診断ログ](enable-diagnostic-logs-topic.md)に関するページを参照してください。

## <a name="metrics"></a>メトリック
Event Grid のトピックとサブスクリプションのメトリックを表示し、それらのアラートを作成することができます。 詳細については、[Event Grid のメトリック](monitor-event-delivery.md)に関するページを参照してください。

## <a name="alerts"></a>警告
Azure Event Grid メトリックとアクティビティ ログ操作に関するアラートを作成します。 詳細については、[Event Grid のメトリックとアクティビティ ログのアラート](set-alerts.md)に関するページを参照してください。

## <a name="subscription-validation-issues"></a>サブスクリプション検証の問題
イベント サブスクリプションの作成中に、指定されたエンドポイントの検証に失敗したというエラー メッセージが表示される場合があります。 サブスクリプション検証の問題のトラブルシューティングについては、[Event Grid サブスクリプション検証のトラブルシューティング](troubleshoot-subscription-validation.md)に関するページを参照してください。 

## <a name="network-connectivity-issues"></a>ネットワーク接続の問題
クライアント アプリケーションが Event Grid トピックまたはドメインに接続できない理由はさまざまです。 発生した接続の問題は、永続的である場合もあれば、一時的なものである場合もあります。 これらの問題を解決する方法については、[接続の問題のトラブルシューティング](troubleshoot-network-connectivity.md)に関するページを参照してください。

## <a name="error-codes"></a>エラー コード
400、409、403 などのエラー コードを含むエラー メッセージが表示された場合は、[Event Grid エラーのトラブルシューティング](troubleshoot-errors.md)に関するページを参照してください。 

## <a name="distributed-tracing"></a>分散トレース 

.NET、Java、Python、JavaScript の Event Grid ライブラリでは、トレースを分散できます。 分散トレースに関する [CloudEvents 仕様のガイダンス](https://github.com/cloudevents/spec/blob/v1.0.1/extensions/distributed-tracing.md)に従うため、分散トレースが有効になっている場合、ライブラリでは `CloudEvent` 拡張機能の `traceparent` 属性と `tracestate` 属性が設定されます。

アプリケーションで分散トレースを有効にする方法の詳細については、Azure SDK の分散トレースに関するドキュメントを参照してください。

- [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)
- [Java](/azure/developer/java/sdk/tracing)
- [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/core/azure-core-tracing-opentelemetry)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/core/README.md#tracing)

[Azure Event Hubs](handler-event-hubs.md) または [Azure Service Bus](handler-service-bus.md) Event Grid サブスクリプションでエンドツーエンドのトレースを有効にするには、`traceparent` CloudEvent 拡張属性を `Diagnostic-Id` AMQP アプリケーション プロパティに転送するように[カスタム配信プロパティ](delivery-properties.md)を構成します。 

次は、Event Hubs にトレース配信プロパティが構成されているサブスクリプションの例です。

```azurecli
az eventgrid event-subscription create --name <event-grid-subscription-name> \
    --source-resource-id <event-grid-resource-id>
    --endpoint-type eventhub \
    --endpoint <event-hubs-endpoint> \
    --delivery-attribute-mapping Diagnostic-Id dynamic traceparent
```

### <a name="sample"></a>サンプル
[行カウンターに関するサンプル](/samples/azure/azure-sdk-for-net/line-counter/)を参照してください。 このサンプル アプリでは、Storage、Event Hubs、および Event Grid クライアントを ASP.NET Core 統合、分散トレース、ホステッド サービスと共に使用する方法を示しています。 ユーザーはファイルを BLOB にアップロードでき、これにより、ファイル名を含む Event Hubs イベントがトリガーされます。 Event Hubs プロセッサはイベントを受け取り、その後、アプリは BLOB をダウンロードし、ファイル内の行数をカウントします。 アプリには、行数を含むページへのリンクが表示されます。 リンクをクリックすると、Event Grid を使用してファイルの名前を含む CloudEvent が発行されます。

## <a name="next-steps"></a>次のステップ
さらにサポートが必要な場合は、[Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-eventgrid)に問題を投稿するか、[サポート チケット](https://azure.microsoft.com/support/options/)を開いてください。 
