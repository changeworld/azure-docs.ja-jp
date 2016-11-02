<properties
    pageTitle="C での Event Hubs の使用 | Microsoft Azure"
    description="このチュートリアルでは、C でイベントを送信し、イベント プロセッサ ホストを使用して Java でそのイベントを受信するための Azure Event Hubs の使用方法について説明します。"
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="csharp"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>


# <a name="get-started-with-event-hubs"></a>Event Hubs の使用

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>はじめに

Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、「 [Event Hubs の概要][]」を参照してください。

このチュートリアルでは、C のコンソール アプリケーションを使用してイベント ハブにメッセージをインジェストし、C# [イベント プロセッサ ホスト][] ライブラリを使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、以下が必要になります。

+ C の開発環境。 このチュートリアルでは、Ubuntu 14.04 での [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md) 上の GCC スタックを想定しています。 他の環境用の手順は、外部リンクで提供されます。

+ Microsoft Visual Studio Express for Windows

+ アクティブな Azure アカウント。 <br/>アカウントがない場合は、無料アカウントを数分で作成することができます。 詳細については、「<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>」をご覧ください。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1.  **Receiver** プロジェクトを実行します。

    ![][21]

2.  **Sender** プログラムを実行し、イベントが受信側ウィンドウに表示されることを確認します。

    ![][24]

## <a name="next-steps"></a>次のステップ

Event Hub を作成し、データを送受信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

- [Event Hub を使用する完全なサンプル アプリケーション][]
- [Event Hubs でイベント処理の拡張][] サンプル
- [Event Hubs の概要][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[イベント プロセッサ ホスト]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs の概要]: event-hubs-overview.md
[Event Hub を使用する完全なサンプル アプリケーション]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Event Hubs でイベント処理の拡張]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Oct16_HO2-->


