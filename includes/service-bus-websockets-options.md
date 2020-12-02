---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022144"
---
WebSocket 経由の AMQP プロトコル オプションは、HTTP/REST API と同様に、ポート TCP 443 で実行されますが、それ以外については通常の AMQP と機能的に同じです。 このオプションを使用すると、HTTPS ポートを共有するためのトレードオフとして、追加のハンドシェイクのラウンドトリップが発生し、若干のオーバーヘッドが生じるため、初期接続の待機時間がやや長くなります。 このモードが選択されている場合は、通信のためには TCP ポート 443 で十分です。 次のオプションでは、通常の AMQP または AMQP WebSocket モードを選択できます。

| 言語 | オプション   |
| -------- | ----- |
| .NET     | [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet) プロパティが [TransportType.Amqp](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) または [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) |
| Java     | [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) が [com.microsoft.azure.servicebus.primitives.TransportType.AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) または [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) |
| Node  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) には `webSocket` コンストラクター引数があります。 |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) が [TransportType.Amqp](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) または [TransportType.AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |