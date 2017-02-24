---
title: ".NET Standard を使用して Azure Event Hubs にイベントを送信する | Microsoft Docs"
description: ".NET Standard で Event Hubs へのイベント送信を開始する"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: c549676b2126448d52ed43af509671d0b593a064

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>.NET Standard で Event Hubs へのメッセージ送信を開始する

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender) で入手できます。

## <a name="what-will-be-accomplished"></a>作業内容

このチュートリアルでは、既存のソリューション **SampleSender** を (このフォルダー内に) 作成する方法について説明します。 ソリューションをそのまま実行するには、`EhConnectionString`、`EhEntityPath`、および `StorageAccount` 文字列を Event Hub の値に置き換えるか、このチュートリアルに従って独自のものを作成します。

このチュートリアルでは、.NET Core コンソール アプリケーションを作成して、Event Hub にメッセージを送信します。

## <a name="prerequisites"></a>前提条件

1. [Visual Studio 2015](http://www.visualstudio.com)。

2. [.NET Core Visual Studio 2015 ツール](http://www.microsoft.com/net/core)。

3. Azure サブスクリプション。

4. Event Hubs 名前空間。

## <a name="send-messages-to-an-event-hub"></a>Event Hub へのメッセージ送信

Event Hub にメッセージを送信するために、Visual Studio を使って C# コンソール アプリケーションを記述します。

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

* Visual Studio を起動し、.NET Core コンソール アプリケーションを作成します。

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。

2. **[参照]** タブをクリックし、"Microsoft Azure Event Hubs" を検索して、**Microsoft Azure Event Hubs** の項目を選択します。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。

### <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Event Hub にメッセージを送信するコードの記述

1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。

    ```cs
    using Microsoft.Azure.EventHubs;
    ```

2. Event Hubs 接続文字列とエンティティ パス (個別の Event Hub 名) の `Program` クラスに定数を追加します。 中かっこ内のプレースホルダーを、Event Hub の作成時に取得した適切な値に置き換えます。

    ```cs
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. 次のように、`Program` クラスに `MainAsync` という名前の新しいメソッドを追加します。

    ```cs
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
        // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();
    }
    ```
    
4. 次のように、`Program` クラスに `SendMessagesToEventHub` という名前の新しいメソッドを追加します。

    ```cs
    // Creates an Event Hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. `Program` クラスの `Main` メソッドに次のコードを追加します。

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs は次のようになります。

    ```cs
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
    
        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
                // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };
    
                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
    
                await SendMessagesToEventHub(100);
    
                await eventHubClient.CloseAsync();
    
                Console.WriteLine("Press any key to exit.");
                Console.ReadLine();
            }
    
            // Creates an Event Hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }
    
                    await Task.Delay(10);
                }
    
                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```
  
6. プログラムを実行して、エラーがスローされていないことを確認します。
  
お疲れさまでした。 メッセージを Event Hub に送信しました。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs からイベントを受信する](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


