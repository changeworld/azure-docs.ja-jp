<properties pageTitle="Event Hubs の使用" metaKeywords="Azure Service Bus, イベント ハブ, Event Hubs の開始方法" description="このチュートリアルでは、C でイベントを送信する Azure Event Hubs を使用する方法と、EventProcessorHost を使用して C# でイベントを受信する方法について説明します。" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="c" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Event Hubs の使用

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。データが Event Hubs に収集されると、リアルタイムの分析プロバイダーやストレージ クラスターを使用してデータを格納できます。

詳細については、「[Event Hubs の概要]」を参照してください。

このチュートリアルでは、C のコンソール アプリケーションを使用してイベント ハブにメッセージをインジェストし、C# の[イベント プロセッサ ホスト] ライブラリを使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、以下が必要です。

+ C の開発環境。このチュートリアルでは、Ubuntu 14.04 での [Azure Linux VM](http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-linux-tutorial/) 上の GCC スタックを想定しています。他の環境用の手順は、外部リンクで提供されます。

+ Microsoft Visual Studio Express 2013 for Windows

+ アクティブな Azure アカウント。<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。

## イベント ハブの作成

1. [Azure の管理ポータル]にログオンし、画面下部にある **[新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[イベント ハブ]**、**[簡易作成]** の順にクリックします。

   	![][1]

3. イベント ハブの名前を入力して、目的のリージョンを選択し、**[新しいイベント ハブを作成する]** をクリックします。

   	![][2]

4. 先ほど作成した名前空間 (通常 ***event hub name*-ns***) をクリックします。

   	![][3]

5. ページ上部にある、**[Event Hubs]** タブをクリックし、先ほど作成したイベント ハブをクリックします。

   	![][4]

6. ページ上部にある **[構成]** タブをクリックし **SendRule** という名前のルールを *"送信"* 権限で、**ReceiveRule** という別のルールを *"管理、送信、リッスン"* 権限で追加して、**[保存]** をクリックします。

   	![][5]

7. 同じページで、**SendRule** に対して生成されたキーをメモしておきます。

   	![][6b]

8. ページ上部にある **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

   	![][6]

イベント ハブが作成され、イベントの送受信に必要な接続文字列が手元にあります。

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-c](../includes/service-bus-event-hubs-get-started-send-c.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.	Visual Studio から **Receiver** プロジェクトを実行し、すべてのパーティションの受信側が起動するまで待機します。

   	![][21]

2.	**sender** プログラムを実行し、受信側ウィンドウに表示されるイベントを確認します。

   	![][24]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6b]: ./media/service-bus-event-hubs-getstarted/create-event-hub6b.png


[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/service-bus-event-hubs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure 管理ポータル]: https://manage.windowsazure.com/
[イベント プロセッサ ホスト]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: http://msdn.microsoft.com/ja-jp/library/azure/dn836025.aspx
