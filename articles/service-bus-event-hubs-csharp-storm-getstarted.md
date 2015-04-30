<properties 
	pageTitle="Event Hubs の使用" 
	description="このチュートリアルでは、C# でイベントを送信する Azure Event Hubs を使用する方法と、Apache Storm クラスターでそれを受信する方法について説明します。" 
	services="service-bus" 
	documentationCenter="" 
	authors="fsautomata" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="core" 
	ms.tgt_pltfrm="csharp" 
	ms.devlang="csharp" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Event Hubs の使用

[AZURE.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

## はじめに

Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、「[Event Hubs の概要]」をご覧ください。

このチュートリアルでは、C# のコンソール アプリケーションを使用して、イベント ハブにメッセージをインジェストし、Apache Storm を使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、以下が必要になります。

+ Microsoft Visual Studio Express 2013 for Windows

+ [Maven](http://maven.apache.org/) を実行するように構成された Java 開発環境。このチュートリアルでは、[Eclipse](https://www.eclipse.org/) を想定しています。

+ アクティブな Azure アカウント <br/>アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>をご覧ください

## イベント ハブの作成

1. [Azure 管理ポータル]にログオンし、画面の下部にある **[新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[イベント ハブ]**、**[簡易作成]** の順にクリックします。

   	![][1]

3. イベント ハブの名前を入力して、目的のリージョンを選択し、**[新しいイベント ハブを作成する]** をクリックします。

   	![][2]

4. 作成した名前空間 (通常は ***event hub name*-ns**) をクリックします。

   	![][3]

5. ページの上部にある **[イベント ハブ]** タブをクリックして、作成したばかりのイベント ハブをクリックします。

   	![][4]

6. 上部の **[構成]** タブをクリックし、**SendRule** という名前のルールに *Send* 権限を追加します。もう 1 つの **ReceiveRule** というルールには *Listen* 権限を追加し、**[保存]** をクリックします。

   	![][5]

7. 同じページで、**ReceiveRule** に対して生成されたキーをメモしておきます。.

   	![][6c]

8. 上部にある **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

   	![][6]

これでイベント ハブが作成され、イベントを送受信するために必要な接続文字列も用意できました。

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## アプリケーションの実行

これでアプリケーションを実行する準備ができました。

1.	Eclipse で **LogTopology** クラスを実行し、すべてのパーティションの受信側が起動するまで待機します。

2.	**Sender** プロジェクトを実行し、コンソール ウィンドウで **Enter** キーを押します。すると、レシーバーのウィンドウにイベントが表示されます。

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-getstarted/create-event-hub6c.png

[22]: ./media/service-bus-event-hubs-getstarted/receive-storm1.png

<!-- Links -->
[Azure 管理ポータル]: https://manage.windowsazure.com/
[イベント プロセッサ ホスト]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: http://msdn.microsoft.com/library/azure/dn836025.aspx

<!--HONumber=52-->