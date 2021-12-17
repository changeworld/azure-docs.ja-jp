---
title: Azure Event Hubs - クライアント SDK | Microsoft Docs
description: この記事では、Azure Event Hubs 用のクライアント SDK に関する情報を提供します。
ms.topic: article
ms.date: 09/21/2021
ms.openlocfilehash: b1a802e5d9829f533187f477bb28037c22c02f3c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215892"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs - クライアント SDK
この記事では、Azure Event Hubs でサポートされる SDK について、次の情報を提供します。 

- アプリケーションで使用できるパッケージの場所 
- ソース コード、サンプル、readme、変更ログ、報告された問題を見つけることができ、新しい問題を提起することもできる GitHub の場所 
- クイックスタート チュートリアルへのリンク 

## <a name="client-sdks"></a>クライアント SDK
以下の表では、利用できるすべての最新 Azure Event Hubs ランタイム クライアントについて説明します。 これらのライブラリの主な目的は、イベント ハブから **メッセージを送受信する** ことです。

| Language | Package | リファレンス | 
| -------- | ------- | --------------- | 
| . NET Standard | [Azure.Messaging.EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[チュートリアル](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure.Messaging.EventHubs.Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[チュートリアル](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[チュートリアル](event-hubs-java-get-started-send.md)</li></ul> |
|      | [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob)</li><li>[チュートリアル](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[チュートリアル](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[チュートリアル](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [azure/event-hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[チュートリアル](event-hubs-node-get-started-send.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[チュートリアル](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-event-hubs-go)</li><li>[チュートリアル](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-event-hubs-c)</li><li>[チュートリアル](event-hubs-c-getstarted-send.md)</li></ul> |

下の表には、以前の Azure Event Hubs ランタイム クライアントをまとめています。 これらのパッケージで重大なバグ修正を受け取った場合、そのパッケージの開発は停止しています。 代わりに、上の表に記載されている最新の SDK を使用することをお勧めします。

| Language | Package | リファレンス | 
| -------- | ------- | --------------- | 
| . NET Standard  | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) (**レガシ**) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[チュートリアル](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) (**レガシ**) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[チュートリアル](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) (**レガシ**) | |
|   Java   | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(レガシ)** | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[チュートリアル](event-hubs-java-get-started-send.md)</li></ul> |

## <a name="management-sdks"></a>Management SDK
現在利用可能な管理固有のライブラリの一覧を次に示します。 これらのライブラリは **Event Hubs エンティティの管理** 専用であり、ランタイム操作は含まれていません。

- [.NET Standard](/dotnet/api/microsoft.azure.management.eventhub)
- [Java](/java/api/com.microsoft.azure.management.eventhub)
- [Python](/python/api/azure-mgmt-eventhub)
- [JavaScript](/javascript/api/@azure/arm-eventhub/)


## <a name="next-steps"></a>次のステップ

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](./event-hubs-about.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.yml)
