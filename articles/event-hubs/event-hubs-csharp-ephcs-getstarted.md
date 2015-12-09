<properties
	pageTitle="C# での Event Hubs の使用 | Microsoft Azure"
	description="このチュートリアルでは、C# による Azure Event Hubs と EventProcessorHost の使用方法について説明します。"
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/05/2015"
	ms.author="sethm"/>

# Event Hubs の使用

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## はじめに

Event Hubs は、接続されているデバイスとアプリケーションからの大量のイベント データを処理するサービスです。Event Hubs にデータを収集した後、ストレージ クラスターを使用してデータを格納したり、リアルタイムの分析プロバイダーを使用して転送できます。この大規模なイベントの収集と処理の機能は、モノのインターネット (IoT) など最新アプリケーション アーキテクチャの重要なコンポーネントです。

このチュートリアルでは、Azure クラシック ポータルを使用してイベント ハブを作成する方法を説明します。また、C# に記述されるコンソール アプリケーションを使用して Event Hub にメッセージを収集し、C# の[イベント プロセッサ ホスト] ライブラリを使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、以下が必要になります。

+ Microsoft Visual Studio 2013、Microsoft Visual Studio Express 2013 for Windows。

+ アクティブな Azure アカウント。<br/>アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、「Azure の無料試用版サイト」(http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="\_blank") を参照してください。

## Event Hub を作成する

1. [Azure クラシック ポータル][]にログオンし、画面の下部にある **[新規]** をクリックします。

2. **[App Services]**、**[Service Bus]**、**[Event Hub]**、**[簡易作成]** の順にクリックします。

   	![][1]

3. Event Hub の名前を入力して、目的のリージョンを選択し、**[Create a new Event Hub (新しい Event Hub を作成する)]** をクリックします。

   	![][2]

4. 先ほど作成した名前空間 (通常は ***event hub name*-ns**) をクリックします。

   	![][3]

5. ページ上部にある、**[Event Hubs]** タブをクリックし、先ほど作成したイベント ハブをクリックします。

   	![][4]

6. 上部の **[構成]** タブをクリックし、**SendRule** という名前のルールに *Send* 権限を追加します。もう 1 つの R**eceiveRule** というルールには *Manage、Send、Listen* 権限を追加し、**[保存]** をクリックします。

   	![][5]

7. ページ上部の **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモするか、このチュートリアルで後で使用する場所にコピーします。

   	![][6]

イベント ハブが作成され、イベントの送受信に必要な接続文字列が手元にあります。

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.	Visual Studio 内で **Receiver** プロジェクトを実行し、すべてのパーティションに対してレシーバーが起動するまで待機します。

   	![][21]

2.	**Sender** プロジェクトを実行し、コンソール ウィンドウで **Enter** キーを押します。すると、レシーバーのウィンドウにイベントが表示されます。

   	![][22]

## 次のステップ

Event Hub を作成し、データを送受信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

- [Event Hubs を使用する完全なサンプル アプリケーション][]
- [Event Hubs でイベント処理の拡張][]サンプル
- Service Bus キューを使用する[キューに格納されたメッセージング ソリューション][]
- [Event Hubs の概要][]

<!-- Images. -->
[1]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub6.png

[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[イベント プロセッサ ホスト]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: event-hubs-overview.md
[Event Hubs を使用する完全なサンプル アプリケーション]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Event Hubs でイベント処理の拡張]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[キューに格納されたメッセージング ソリューション]: ../service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_1203_2015-->