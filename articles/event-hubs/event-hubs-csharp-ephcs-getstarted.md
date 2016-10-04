<properties
	pageTitle="C# での Event Hubs の使用 | Microsoft Azure"
	description="このチュートリアルでは、C# による Azure Event Hubs と EventProcessorHost の使用方法について説明します。"
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
	ms.date="09/02/2016"
	ms.author="jotaub;sethm"/>

# Event Hubs の使用

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## はじめに

Event Hubs は、接続されているデバイスとアプリケーションからの大量のイベント データ (テレメトリ) を処理するサービスです。Event Hubs にデータを収集した後、ストレージ クラスターを使用してデータを格納したり、リアルタイムの分析プロバイダーを使用して転送できます。この大規模なイベントの収集と処理の機能は、モノのインターネット (IoT) など最新アプリケーション アーキテクチャの重要なコンポーネントです。

このチュートリアルでは、Azure クラシック ポータルを使用してイベント ハブを作成する方法を説明します。また、C# に記述されるコンソール アプリケーションを使用して Event Hub にメッセージを収集し、C# の[イベント プロセッサ ホスト][] ライブラリを使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、次のものが必要です。

+ [Microsoft Visual Studio](http://visualstudio.com)

+ アクティブな Azure アカウント。アカウントがない場合は、無料アカウントを数分で作成できます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/free/)を参照してください。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. Visual Studio で、前に作成した **Receiver** プロジェクトを開きます。

2. **[Receiver]** ソリューションを右クリックし、**[追加]**、**[既存のプロジェクト]** の順にクリックします。
 
3. 既存の Sender.csproj ファイルを見つけ、ダブルクリックしてソリューションに追加します。
 
4. **[Receiver]** ソリューションをもう一度右クリックし、**[プロパティ]** をクリックします。**Receiver** プロパティ ページが表示されます。

5. **[スタートアップ プロジェクト]** をクリックし、**[マルチ スタートアップ プロジェクト]** ボタンをクリックします。**[Receiver]** プロジェクトと **[Sender]** プロジェクトの両方の **[アクション]** ボックスを、**[開始]** に設定します。

	![][19]

6. **[プロジェクトの依存関係]** をクリックします。**[プロジェクト]** ボックスで、**[Sender]** をクリックします。**[依存先]** ボックスで、**[Receiver]** がオンになっていることを確認します。

	![][20]

7. **[OK]** をクリックして、**[プロパティ]** ダイアログを閉じます。

1.	F5 キーを押して、Visual Studio 内で **Receiver** プロジェクトを実行し、すべてのパーティションに対してレシーバーが起動するまで待機します。

	![][21]

2.	**Sender** プロジェクトが自動的に実行されます。コンソール ウィンドウで **Enter** キーを押すと、レシーバーのウィンドウにイベントが表示されます。

	![][22]

**[Sender]** ウィンドウで **Ctrl + C** キーを押して、Sender アプリケーションを終了し、[Receiver] ウィンドウで **Enter** キーを押して、そのアプリケーションをシャットダウンします。

## 次のステップ

Event Hub を作成し、データを送受信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

- [Event Hub を使用する完全なサンプル アプリケーション][]
- [Event Hubs でイベント処理の拡張][]サンプル
- Service Bus キューを使用する[キューに格納されたメッセージング ソリューション][]
- [Event Hubs の概要][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[イベント プロセッサ ホスト]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: event-hubs-overview.md
[Event Hub を使用する完全なサンプル アプリケーション]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Event Hubs でイベント処理の拡張]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[キューに格納されたメッセージング ソリューション]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0928_2016-->