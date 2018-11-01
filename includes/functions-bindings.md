---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 17e17b9847306de1971ebcfbb79f1d95516cba57
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132845"
---
次の表は、Azure Functions の 2 つのメジャー バージョンのランタイムでサポートされているバインディングを示します。

| type | 1.x | 2.x<sup>1</sup> | トリガー | 入力 | 出力 |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [外部ファイル](../articles/azure-functions/functions-bindings-external-file.md) <sup>2</sup>    |✔|| |✔|✔|  
| [外部テーブル](../articles/azure-functions/functions-bindings-external-table.md) <sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Excel テーブル](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>OneDrive ファイル](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Outlook メール](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>Events](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Auth トークン](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|  
| [Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|  
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|  
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Webhook](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> 2.x では、HTTP と Timer を除くすべてのバインドを登録する必要があります。 「[バインディング拡張機能を登録する](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions)」を参照してください。

<sup>2</sup> 実験的 &mdash; サポート対象外で、将来破棄される可能性があります。
