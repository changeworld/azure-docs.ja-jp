---
title: "C# での Event Hubs の使用 | Microsoft Docs"
description: "このチュートリアルでは、C# でイベントを送信し、EventProcessorHost を使用して Java でそのイベントを受信するための Azure Event Hubs の使用方法について説明します。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 059fb733-a397-400e-8e43-0c7ea5930b8b
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: c9aa7cebab12eafe71476ee45ef578d3201e69e5
ms.openlocfilehash: e7c38aa5a778d5e8a653e753e5794d7c7da31dc1


---
# <a name="get-started-with-event-hubs"></a>Event Hubs の使用
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>はじめに
Event Hubs は、接続されているデバイスとアプリケーションからの大量のイベント データ (テレメトリ) を処理するサービスです。 Event Hubs にデータを収集した後、ストレージ クラスターを使用してデータを格納したり、リアルタイムの分析プロバイダーを使用して転送できます。 この大規模なイベントの収集と処理の機能は、モノのインターネット (IoT) など最新アプリケーション アーキテクチャの重要なコンポーネントです。

このチュートリアルでは、Azure ポータルを使用して Event Hub を作成する方法を説明します。 また、C# に記述されるコンソール アプリケーションを使用して Event Hub にメッセージを収集し、Java のイベント プロセッサ ホスト ライブラリを使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、次のものが必要です。

* [Microsoft Visual Studio](http://visualstudio.com)
* アクティブな Azure アカウント。 <br/>アカウントがない場合は、無料アカウントを数分で作成できます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank")」をご覧ください。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. **Receiver** プロジェクトを実行します。
   
   ![][21]
2. **Sender** プロジェクトを実行します。
   
   ![][22]

## <a name="next-steps"></a>次のステップ
Event Hub を作成し、データを送受信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

* [Event Hubs を使用する完全なサンプル アプリケーション][sample application that uses Event Hubs]
* [Event Hubs でのイベント処理のスケールアウト][Scale out Event Processing with Event Hubs] サンプル
* [Event Hubs の概要][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO1-->


