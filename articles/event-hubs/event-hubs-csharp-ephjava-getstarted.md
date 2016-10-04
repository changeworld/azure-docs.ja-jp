<properties
	pageTitle="C# での Event Hubs の使用 | Microsoft Azure"
	description="このチュートリアルでは、C# でイベントを送信し、EventProcessorHost を使用して Java でそのイベントを受信するための Azure Event Hubs の使用方法について説明します。"
	services="event-hubs"
	documentationCenter=""
	authors="jtaubensee"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/27/2016"
	ms.author="jotaub;sethm"/>

# Event Hubs の使用

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## はじめに

Event Hubs は、接続されているデバイスとアプリケーションからの大量のイベント データ (テレメトリ) を処理するサービスです。Event Hubs にデータを収集した後、ストレージ クラスターを使用してデータを格納したり、リアルタイムの分析プロバイダーを使用して転送できます。この大規模なイベントの収集と処理の機能は、モノのインターネット (IoT) など最新アプリケーション アーキテクチャの重要なコンポーネントです。

このチュートリアルでは、Azure クラシック ポータルを使用してイベント ハブを作成する方法を説明します。また、C# に記述されるコンソール アプリケーションを使用して Event Hub にメッセージを収集し、Java のイベント プロセッサ ホスト ライブラリを使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、次のものが必要です。

+ [Microsoft Visual Studio](http://visualstudio.com)

+ アクティブな Azure アカウント。<br/>アカウントがない場合は、無料アカウントを数分で作成できます。詳細については、「Azure の無料試用版サイト」(https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="\_blank") を参照してください。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.	**Receiver** プロジェクトを実行します。

	![][21]

2.	**Sender** プロジェクトを実行します。

	![][22]

## 次のステップ

Event Hub を作成し、データを送受信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

- [Event Hub を使用する完全なサンプル アプリケーション][]
- [Event Hubs でイベント処理の拡張][]サンプル
- Service Bus キューを使用する[キューに格納されたメッセージング ソリューション][]
- [Event Hubs の概要][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Hubs の概要]: event-hubs-overview.md
[Event Hub を使用する完全なサンプル アプリケーション]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Event Hubs でイベント処理の拡張]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[キューに格納されたメッセージング ソリューション]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0928_2016-->