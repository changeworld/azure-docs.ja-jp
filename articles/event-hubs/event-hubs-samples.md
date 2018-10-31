---
title: Azure Event Hubs アーカイブ | Microsoft Docs
description: Azure Event Hubs サンプル
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: shvija
ms.openlocfilehash: ed337689360428d66657e5391ee52bb0ae39dc14
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365647"
---
# <a name="event-hubs-samples"></a>Event Hubs サンプル 
Event Hubs サンプルは [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples) で見つけることができます。 これらのサンプルは [Azure Event Hubs](/azure/event-hubs/) の主な機能を示しています。 この記事では、使用可能なサンプルを分類して説明しています。また、各サンプルへのリンクも含まれています。

## <a name="net-samples"></a>.NET のサンプル

| サンプル名 | 説明 | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | このサンプルでは、イベント ハブに一連のインベントリを送信する .NET Core コンソール アプリケーションの記述方法を示します。 |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | このサンプルでは、イベント プロセッサ ホスト ライブラリを使ってイベント ハブから一連のイベントを受信する .NET Core コンソール アプリケーションを作成する方法を説明します。  | 

## <a name="java-samples"></a>Java のサンプル

| サンプル名 | 説明 | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | このサンプルは、イベント ハブにイベントのバッチを取り込む方法を示しています。 | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | このサンプルは、イベント ハブにイベントを取り込む方法を示しています。 |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | このサンプルは、Event Hubs がイベントを取り込むために利用できるさまざまなオプションを示しています。 |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | このサンプルは、日時のオフセットを指定してイベント ハブ パーティションからイベントを受信する方法を示しています。 |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | このサンプルは、データのオフセットを指定してイベント ハブ パーティションからイベントを受信する方法を示しています。 |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | このサンプルは、シーケンス番号を使用してイベント ハブ パーティションから受信する方法を示しています。 |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |このサンプルは、イベント プロセッサ ホストを使用して、イベント ハブからイベントを受信する方法を示しています。パーティションの自動選択機能と、複数の並列する受信側全体にフェールオーバー機能を提供します。 | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | このサンプルは、高負荷の場合にイベント ハブを自動的にスケールアップする方法を示しています。 このサンプルを実行すると、構成したイベント ハブのレートをやや超えるレートでイベントが送信され、イベント ハブのスケールアップが実行されます。 | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | このサンプルでは、受信レートを測定できます。 | 

## <a name="python-samples"></a>Python のサンプル
Azure Event Hubs の Python サンプルは、[azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) GitHub リポジトリにあります。

## <a name="nodejs-samples"></a>Node.js のサンプル
Azure Event Hubs の Node.js サンプルは、[azure-event-hubs-node](https://github.com/Azure/azure-event-hubs-node) GitHub リポジトリにあります。

## <a name="go-samples"></a>Go のサンプル
Azure Event Hubs の Go サンプルは、[azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples) GitHub リポジトリにあります。

## <a name="azure-cli-samples"></a>Azure CLI のサンプル
Azure Event Hubs の Azure CLI サンプルは、[azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI) GitHub リポジトリにあります。

## <a name="azure-powershell-samples"></a>Azure PowerShell のサンプル
Azure Event Hubs の Azure PowerShell サンプルは、[azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell) GitHub リポジトリにあります。
 


## <a name="next-steps"></a>次の手順
Event Hubs の詳細については、次の記事を参照してください。

- [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
- [Event Hubs の機能](event-hubs-features.md)
- [Event Hubs の FAQ](event-hubs-faq.md)