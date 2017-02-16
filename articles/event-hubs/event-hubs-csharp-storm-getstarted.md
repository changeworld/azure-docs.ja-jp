---
title: "C# および Apache Storm での Event Hubs の使用 | Microsoft Docs"
description: "このチュートリアルでは、C# でイベントを送信し、Apache Storm クラスターでそれを受信するするための Azure Event Hubs の使用方法について説明します。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: f64cb2bd-2e38-4c78-8d0d-9af14c000d9f
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f603a3c3304af3e64ef40f2cdef3745e3b2085b3
ms.openlocfilehash: 508b1390bee1e61514e6e7499071d0503a051c10


---
# <a name="get-started-with-event-hubs"></a>Event Hubs の使用
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>はじめに
Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、「 [Event Hubs の概要]」を参照してください。

このチュートリアルでは、C# のコンソール アプリケーションを使用して、イベント ハブにメッセージをインジェストし、Apache Storm を使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、以下が必要になります。

* [Microsoft Visual Studio](http://visualstudio.com)
* [Maven](http://maven.apache.org/)を実行するように構成された Java 開発環境。 このチュートリアルでは、 [Eclipse](https://www.eclipse.org/)
* アクティブな Azure アカウント。 <br/>アカウントがない場合は、無料アカウントを数分で作成することができます。 詳細については、 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>を参照してください。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. Eclipse で **LogTopology** クラスを実行し、すべてのパーティションの受信側が起動するまで待機します。
2. **Sender** プロジェクトを実行し、コンソール ウィンドウで **Enter** キーを押して、受信側ウィンドウに表示されるイベントを確認します。
   
   ![][22]

## <a name="next-steps"></a>次のステップ
Event Hub を作成し、データを送受信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

* [Event Hubs を使用する完全なサンプル アプリケーション][sample application that uses Event Hubs]
* [Event Hubs でのイベント処理のスケールアウト][Scale out Event Processing with Event Hubs] サンプル

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Event Hubs の概要]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO2-->


