<properties pageTitle="Event Hubs の使用" metaKeywords="Azure Service Bus, Event Hub, getting started Event Hubs" description="Follow this tutorial to get started using Azure Event Hubs with C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Event Hubs の使用

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Event Hubs は、毎秒数百万件の処理が可能な高度にスケーラブルなインジェスト システムです。これを使用するアプリケーションは、接続するデバイスおよびアプリケーションで生成される大量のデータを処理および分析することができます。Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送および格納することができます。Event Hubs の詳細については、「[Event Hubs 開発者ガイド]」を参照してください。 

詳細については、「[Event Hubs の概要]」を参照してください。

このチュートリアルでは、C# を使用して、コンソール アプリケーションから Event Hub にメッセージをインジェストし、C# の[イベント プロセッサ ホスト] ライブラリに並行して取得する方法について説明します。

このチュートリアルを完了するには、以下が必要になります。

+ Microsoft Visual Studio Express 2013 for Windows

+ アクティブな Azure アカウント <br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、 <a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 無料評価版</a>を参照してください。

## イベント ハブの作成

1. [Azure の管理ポータル]にログオンし、画面の下部にある **[新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[イベント ハブ]**、**[簡易作成]** の順にクリックします。

   	![][1]

3. イベント ハブの名前を入力して、目的のリージョンを選択し、**[新しいイベント ハブの作成]** をクリックします。

   	![][2]

4. 作成した名前空間 (通常は ***event hub name*-ns**) をクリックします。

   	![][3]

5. ページの上部にある **[イベント ハブ]** タブをクリックして、作成したばかりのイベント ハブをクリックします。

   	![][4]

6. 上部の **[構成]** タブをクリックし、**SendRule** という名前のルールに *Send* 権限を追加します。もう 1 つの **ReceiveRule** というルールには *Manage、Send、Listen* 権限を追加し、**[保存]** をクリックします。

   	![][5]

7. 上部の **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

   	![][6]

これでイベント ハブが作成され、イベントを送受信するために必要な接続文字列も用意できました。

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## アプリケーションの実行

これでアプリケーションを実行する準備ができました。

1.	Visual Studio 内で **Receiver** プロジェクトを実行し、すべてのパーティションに対してレシーバーが起動するまで待機します。

   	![][21]

2.	**Sender** プロジェクトを実行し、コンソール ウィンドウで **Enter** キーを押します。すると、レシーバーのウィンドウにイベントが表示されます。

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure の管理ポータル]: https://manage.windowsazure.com/
[イベント プロセッサ ホスト]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: http://msdn.microsoft.com/ja-jp/library/azure/dn836025.aspx

<!--HONumber=35_1-->
