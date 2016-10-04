<properties
    pageTitle="Service Bus キューの使用 | Microsoft Azure"
    description="Service Bus メッセージング用の C# コンソール アプリケーションを記述する方法"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/23/2016"
    ms.author="jotaub;sethm"/>

# Service Bus キューの使用

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## 作業内容

このチュートリアルでは次の作業を行います。

1. Azure ポータルを使用して Service Bus 名前空間を作成する。

2. Azure ポータルを使用して Service Bus メッセージング キューを作成する。

3. メッセージを送信するコンソール アプリケーションを記述する。

4. メッセージを受信するコンソール アプリケーションを記述する。

## 前提条件

1. [Visual Studio 2013 または Visual Studio 2015](http://www.visualstudio.com)。このチュートリアルの例では、Visual Studio 2015 を使用します。

2. Azure サブスクリプション。

[AZURE.INCLUDE [アカウント作成メモ](../../includes/create-account-note.md)]

## 1\.Azure ポータルを使用した名前空間の作成

Service Bus 名前空間を既に作成している場合は、「[Azure ポータルを使用したキューの作成](#2-create-a-queue-using-the-azure-portal)」セクションに進んでください。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 2\.Azure ポータルを使用したキューの作成

Service Bus キューを既に作成している場合は、「[キューへのメッセージの送信](#3-send-messages-to-the-queue)」セクションに進んでください。

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## 手順 3.キューへのメッセージの送信

キューにメッセージを送信するために、Visual Studio を使用して C# コンソール アプリケーションを記述します。

### コンソール アプリケーションの作成

1. Visual Studio を起動し、新しいコンソール アプリケーションを作成します。

### Service Bus NuGet パッケージの追加

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。

2. **[参照]** タブをクリックし、"Microsoft Azure Service Bus" を検索して、**[Microsoft Azure Service Bus]** の項目を選択します。**[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。

    ![Select a NuGet package][nuget-pkg]

### キューにメッセージを送信するコードの記述

1. Program.cs ファイルの先頭に次の using ステートメントを追加します。

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. `Main` メソッドに次のコードを追加し、**connectionString** 変数に名前空間の作成時に取得した接続文字列を設定して、**queueName** にキューの作成時に使用したキュー名を設定します。

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```

    Program.cs は次のようになります。

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;

    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                client.Send(message);
            }
        }
    }
    ```
  
3. プログラムを実行し、Azure ポータルを確認します。名前空間の **[概要]** ブレードでキューの名前をクリックします。**[アクティブなメッセージ数]** の値が 1 になっていることに注目してください。
    
      ![Message count][queue-message]
    
## 4\.キューからメッセージを受け取る

1. 前の手順で説明した送信アプリケーションと同じように、新しいコンソール アプリケーションを作成し、Service Bus NuGet パッケージへの参照を追加します。

2. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. `Main` メソッドに次のコードを追加し、**connectionString** 変数に名前空間の作成時に取得した接続文字列を設定して、**queueName** にキューの作成時に使用したキュー名を設定します。

    ```
    var connectionString = "";
    var queueName = "samplequeue";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
  
    Console.ReadLine();
    ```

	Program.cs ファイルは次のようになります。

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;
  
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
  
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
  
          Console.ReadLine();
        }
      }
    }
    ```
  
4. プログラムを実行し、ポータルを確認します。**[キューの長さ]** の値が 0 になっていることに注目してください。

    ![Queue length][queue-message-receive]
  
お疲れさまでした。 これで、キューを作成し、メッセージを送信して、メッセージを受信しました。

## 次のステップ

Azure Service Bus メッセージングのさらに高度な機能を紹介する[サンプルが含まれる GitHub リポジトリ](https://github.com/Azure-Samples/azure-servicebus-messaging-samples)を参照してください。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0928_2016-->