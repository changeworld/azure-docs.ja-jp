---
title: "C# での Event Hubs の使用 | Microsoft Docs"
description: "このチュートリアルでは、C# による Azure Event Hubs とイベント プロセッサ ホストの使用方法について説明します。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 2ec2378a-34f7-44c3-b976-cc444c98c338
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/07/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 015dbab81f807cd05d1fedb97f5fc62a012f2084
ms.openlocfilehash: 54aaf68864fc83501b860ecc3daef1c0216c0140


---
# <a name="get-started-with-event-hubs"></a>Event Hubs の使用
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>はじめに
Event Hubs は、接続されているデバイスとアプリケーションからの大量のイベント データ (テレメトリ) を処理するサービスです。 Event Hubs にデータを収集した後、ストレージ クラスターを使用してデータを格納したり、リアルタイムの分析プロバイダーを使用して転送できます。 この大規模なイベントの収集と処理の機能は、モノのインターネット (IoT) など最新アプリケーション アーキテクチャの重要なコンポーネントです。

このチュートリアルでは、Azure ポータルを使用して Event Hub を作成する方法を説明します。 また、C# で記述されたコンソール アプリケーションを使用してイベント ハブにメッセージを収集し、C# での[イベント プロセッサ ホスト][Event Processor Host] ライブラリを使用して並列で取得する方法を学習します。

このチュートリアルを完了するには、次のものが必要です。

* [Microsoft Visual Studio](http://visualstudio.com)
* アクティブな Azure アカウント。 アカウントがない場合は、無料アカウントを数分で作成できます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/free/)」をご覧ください。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. Visual Studio で、前に作成した **Receiver** プロジェクトを開きます。
2. **[Receiver]** ソリューションを右クリックし、**[追加]**、**[既存のプロジェクト]** の順にクリックします。
3. 既存の Sender.csproj ファイルを見つけ、ダブルクリックしてソリューションに追加します。
4. **[Receiver]** ソリューションをもう一度右クリックし、**[プロパティ]** をクリックします。 **Receiver** プロパティ ページが表示されます。
5. **[スタートアップ プロジェクト]** をクリックし、**[マルチ スタートアップ プロジェクト]** ボタンをクリックします。 **[Receiver]** プロジェクトと **[Sender]** プロジェクトの両方の **[アクション]** ボックスを、**[開始]** に設定します。
   
    ![][19]
6. **[プロジェクトの依存関係]**をクリックします。 **[プロジェクト]** ボックスで、**[Sender]** をクリックします。 **[依存先]** ボックスで、**[Receiver]** がオンになっていることを確認します。
   
    ![][20]
7. **[OK]** をクリックして、**[プロパティ]** ダイアログを閉じます。
8. F5 キーを押して、Visual Studio 内で **Receiver** プロジェクトを実行し、すべてのパーティションに対してレシーバーが起動するまで待機します。
   
   ![][21]
9. **Sender** プロジェクトが自動的に実行されます。 コンソール ウィンドウで **Enter** キーを押すと、レシーバーのウィンドウにイベントが表示されます。
   
   ![][22]

**[Sender]** ウィンドウで **Ctrl + C** キーを押して Sender アプリケーションを終了し、[Receiver] ウィンドウで **Enter** キーを押してそのアプリケーションをシャットダウンします。

## <a name="next-steps"></a>次のステップ
Event Hub を作成し、データを送受信する実用的なアプリケーションが構築できたので、次のシナリオに移動します。

* [Event Hubs を使用する完全なサンプル アプリケーション][sample application that uses Event Hubs]
* [イベント プロセッサ ホスト](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Event Hubs でのイベント処理のスケールアウト][Scale out Event Processing with Event Hubs] サンプル
* [Event Hubs の概要][Event Hubs overview]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Dec16_HO2-->


