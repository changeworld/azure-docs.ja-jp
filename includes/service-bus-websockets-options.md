---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304421"
---
WebSocket 経由の AMQP プロトコル オプションは、HTTP/REST API と同様に、ポート TCP 443 で実行されますが、それ以外については通常の AMQP と機能的に同じです。 このオプションでは、HTTPS ポートを共有するためのトレードオフとして、追加のハンドシェイクのラウンドトリップが発生し、若干のオーバーヘッドが生じるため、初期接続の待機時間が長くなります。 このモードが選択されている場合は、通信のためには TCP ポート 443 で十分です。 次のオプションを使用すると、AMQP WebSocket モードを選択できます。 

| 言語 | オプション   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) をパラメーターとして受け取るコンストラクターを使用して [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) を作成します。 [ServiceBusClientOptions.TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) を [ServiceBusTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) に設定します。 |
| .NET (Microsoft.Azure.ServiceBus)    | クライアント オブジェクトを作成する場合は、[TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype)、[ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection)、または [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) をパラメーターとして受け取るコンストラクターを使用します。 <p>`transportType` をパラメーターとして受け取る構造の場合は、パラメーターを [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) に設定します。</p> <p>`ServiceBusConnection` をパラメーターとして受け取るコンストラクターの場合は、[ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) を [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) に設定します。</p> <p>`ServiceBusConnectionStringBuilder` を使用する場合は、`transportType` を指定するオプションを提供するコンストラクターを使用します。</p> |
| Java (com.azure.messaging.servicebus)     | クライアントを作成する場合は、[ServiceBusClientBuilder.transportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) を [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) に設定します。 |
| Java (com.microsoft.azure.servicebus)    | クライアントを作成する場合は、[com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_) の `transportType` を [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) に設定します。 |
| JavaScript  | Service Bus クライアント オブジェクトを作成する場合は、[ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) の `webSocketOptions` プロパティを使用します。 |
| Python | Service Bus クライアントを作成する場合は、[ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) を [TransportType.AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype) に設定します。 |

