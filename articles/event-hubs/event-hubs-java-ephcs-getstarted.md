<properties
	pageTitle="Java での Event Hubs の使用 | Microsoft Azure"
	description="このチュートリアルでは、Java でイベントを送信し、EventProcessorHost を使用して C# でイベントを受信するための Azure Event Hubs の使用方法について説明します。"
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/05/2015"
	ms.author="sethm"/>

# Event Hubs の使用

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## はじめに

Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、「[Event Hubs の概要][]」を参照してください。

このチュートリアルでは、Java のコンソール アプリケーションを使用してイベント ハブにメッセージをインジェストし、C# の[イベント プロセッサ ホスト][] ライブラリを使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、以下が必要です。

+ Java 開発環境。このチュートリアルでは、[Eclipse](https://www.eclipse.org/) を想定しています。

+ Microsoft Visual Studio Express for Windows

+ アクティブな Azure アカウント<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。

## Event Hub を作成する

1. [Azure クラシック ポータル][]にログオンし、画面の下部にある **[新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[Event Hub]**、**[簡易作成]** の順にクリックします。

	![][1]

3. Event Hub の名前を入力して、目的のリージョンを選択し、**[Create a new Event Hub (新しい Event Hub を作成する)]** をクリックします。

	![][2]

4. 先ほど作成した名前空間 (通常は ***event hub name*-ns**) をクリックします。

	![][3]

5. ページ上部にある、**[Event Hubs]]** タブをクリックし、先ほど作成したイベント ハブをクリックします。

	![][4]

6. ページの上部にある **[構成]** タブをクリックし、*Send* 権限を持つ **SendRule** という名前のルールを追加し、*Manage、Send、Listen* 権限を持つ **ReceiveRule** という別のルールを追加して、**[保存]** をクリックします。

	![][5]

7. 同じページで、**SendRule** に対して生成されたキーをメモしておきます。

	![][6b]

8. ページ上部の **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

	![][6]

イベント ハブが作成され、イベントの送受信に必要な接続文字列が手元にあります。

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.	Visual Studio から **Receiver** プロジェクトを実行し、すべてのパーティションの受信側が起動するまで待機します。

	![][21]

2.	**Sender** プロジェクトを実行し、コンソール ウィンドウで **Enter** キーを押すと、レシーバーのウィンドウにイベントが表示されます。

	![][22]

## 次のステップ

Event Hub を作成し、データを送受信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

- [Event Hub を使用する完全なサンプル アプリケーション][]
- [Event Hubs でイベント処理の拡張][]サンプル
- Service Bus キューを使用する[キューに格納されたメッセージング ソリューション][]

詳細については、[Java デベロッパー センター](/develop/java/)を参照してください。

<!-- Images. -->
[1]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6.png
[6b]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6b.png


[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[イベント プロセッサ ホスト]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: event-hubs-overview.md
[Event Hub を使用する完全なサンプル アプリケーション]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Event Hubs でイベント処理の拡張]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[キューに格納されたメッセージング ソリューション]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_1203_2015-->