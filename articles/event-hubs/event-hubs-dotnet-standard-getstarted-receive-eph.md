---
title: .NET Core を使用してイベントを受信する - Azure Event Hubs | Microsoft Docs
description: この記事では、EventProcessorHost を使用してメッセージを受信する .NET Core アプリケーションを作成するためのチュートリアルを提供します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 18b37fd7c85fced4cc57b57320daf195f6f33e3e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082383"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-core"></a>.NET Core で イベント プロセッサ ホストを使用したメッセージ受信を開始する
Event Hubs は、接続されているデバイスとアプリケーションからの大量のイベント データ (テレメトリ) を処理するサービスです。 Event Hubs にデータを収集した後、ストレージ クラスターを使用してデータを格納したり、リアルタイムの分析プロバイダーを使用して転送できます。 この大規模なイベントの収集と処理の機能は、モノのインターネット (IoT) など最新アプリケーション アーキテクチャの重要なコンポーネントです。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、[イベント プロセッサ ホスト](event-hubs-event-processor-host.md)を使用して Event Hub からメッセージを受信する .NET Core コンソール アプリケーションの記述方法を説明します。 [イベント プロセッサ ホスト](event-hubs-event-processor-host.md)は、永続的なチェックポイントの管理によってイベント ハブのイベントの受信を簡素化し、並列してそれらのイベント ハブから受信する .NET クラスです。 イベント プロセッサ ホストを使用すると、さまざまなノードでホストされている場合でも、複数の受信側間でイベントを分割できます。 この例では、受信側が単一の場合にイベント プロセッサ ホストを使用する方法を示します。 [イベント処理のスケールアウト][Event Hubs でイベント処理の拡張]のサンプルは、受信者側が複数の場合にイベント プロセッサ ホストを使用する方法を示します。

> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) からダウンロードし、`EventHubConnectionString`、`EventHubName`、`StorageAccountName`、`StorageAccountKey`、`StorageContainerName` の各文字列を対象のイベント ハブの値に置き換え、実行します。 または、このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件
* [Microsoft Visual Studio 2015 または 2017](https://www.visualstudio.com)。 このチュートリアルの例では Visual Studio 2015 を使用しますが、Visual Studio 2017 もサポートされています。
* [.NET Core Visual Studio 2015 または 2017 ツール](https://www.microsoft.com/net/core)。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 名前空間とイベント ハブを作成する
最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順を実行した後、このチュートリアルに示されている手順に進みます。

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio を起動します。 **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。 .NET Core コンソール アプリケーションを作成します。

![新しいプロジェクト][2]

## <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

次の手順に従って、.NET Standard ライブラリ NuGet パッケージの [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) と [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) をプロジェクトに追加します。 

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
2. **[参照]** タブをクリックし、**Microsoft.Azure.EventHubs** を探して、**Microsoft.Azure.EventHubs** パッケージを選びます。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。
3. 手順 1 と 2 を繰り返し、**Microsoft.Azure.EventHubs.Processor** パッケージをインストールします。

## <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor インターフェイスの実装

1. ソリューション エクスプローラーで、プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 新しいクラスに **SimpleEventProcessor** の名前を付けます。

2. SimpleEventProcessor.cs ファイルを開き、ファイルの先頭に `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. `IEventProcessor` インターフェイスを実装します。 `SimpleEventProcessor` クラスの内容全体を次のコードに置き換えます。

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>SimpleEventProcessor を使用する Main メソッドを更新する

1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. イベント ハブ接続文字列、イベント ハブ名、ストレージ アカウント コンテナー名、ストレージ アカウント名、およびストレージ アカウント キーの `Program` クラスに定数を追加します。 プレースホルダーを対応する値に置き換えて、次のコードを追加します。

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 次のように、`Program` クラスに `MainAsync` という名前の新しいメソッドを追加します。

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. `Main` メソッドに次のコード行を追加します。

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs ファイルは次のようになります。

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. プログラムを実行し、エラーがないことを確認します。

お疲れさまでした。 イベント プロセッサ ホストを使用して、イベント ハブからメッセージを受信しました。

> [!NOTE]
> このチュートリアルでは、 [EventProcessorHost](event-hubs-event-processor-host.md)の単一のインスタンスを使用します。 スループットを向上させるには、[EventProcessorHost](event-hubs-event-processor-host.md) の複数のインスタンスを実行することをお勧めします ([イベント処理のスケールアウト](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3)のサンプルを参照してください)。 このような場合、受信したイベントの負荷を分散するために、複数のインスタンスが自動的に連携します。 

## <a name="next-steps"></a>次の手順
このクイック スタートでは、イベント ハブからメッセージを受信した .NET Core アプリケーションを作成しました。 .NET Core を使用してイベント ハブにイベントを送信する方法については、[.NET Core でのイベント ハブからのイベントの送信](event-hubs-dotnet-standard-getstarted-send.md)に関するページを参照してください。

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
