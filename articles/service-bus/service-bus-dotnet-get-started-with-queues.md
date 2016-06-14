<properties
   pageTitle="Service Bus キューの使用 | Microsoft Azure"
   description="Service Bus メッセージング用の C# コンソール アプリケーションを記述する方法"
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Service Bus キューの使用
[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

##作業内容
このチュートリアルでは次の作業を行います。

1. Azure ポータルを使用して Service Bus 名前空間を作成する。

2. Azure ポータルを使用して Service Bus メッセージング キューを作成する。

3. メッセージを送信するコンソール アプリケーションを記述する。

4. メッセージを受信するコンソール アプリケーションを記述する。

##前提条件
1. [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com)

2. Azure サブスクリプション

##1\.Azure ポータルを使用した名前空間の作成
Service Bus 名前空間を既に作成している場合は、「[Azure ポータルを使用したキューの作成](#2-create-a-queue-using-the-azure-portal)」セクションに進んでください。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

##2\.Azure ポータルを使用したキューの作成
Service Bus キューを既に作成している場合は、「[キューへのメッセージの送信](#3-sending-messages-to-the-queue)」セクションに進んでください。

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

##3\.キューへのメッセージの送信
キューにメッセージを送信するために、Visual Studio を使用して C# コンソール アプリケーションを記述します。

###コンソール アプリケーションの作成
1. Visual Studio を起動し、新しいコンソール アプリケーションを作成します。

###Service Bus NuGet パッケージの追加
1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。

2. "Microsoft Azure Service Bus" を検索して、**[Microsoft Azure Service Bus]** 項目を選択します。**[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。

    ![Select a NuGet package][1]

###キューにメッセージを送信するコードの記述
1. Program.cs ファイルの先頭に次の using ステートメントを追加します。

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Main メソッドに次のコードを追加し、**connectionString** 変数に名前空間の作成時に取得した接続文字列を設定して、**queueName** にキューの作成時に使用したキュー名を設定します。

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
  
3. プログラムを実行し、Azure ポータルを確認します。**[キューの長さ]** が 1 になっていることに注目してください。
    
      ![Queue length][2]
    
##4\.キューからのメッセージの受信
1. 新しいコンソール アプリケーションを作成し、上記の送信元アプリケーションと同じように Service Bus NuGet パッケージへの参照を追加します。

2. Program.cs ファイルの先頭に次の using ステートメントを追加します。
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Main メソッドに次のコードを追加し、**connectionString** 変数に名前空間の作成時に取得した接続文字列を設定して、**queueName** にキューの作成時に使用したキュー名を設定します。

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
  
4. プログラムを実行し、Azure ポータルを確認します。**[キューの長さ]** が 0 になっていることに注目してください。

    ![Queue length][3]
  
ご利用ありがとうございます。 これで、キューを作成し、メッセージを送信して、メッセージを受信しました。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##次のステップ

GitHub リポジトリにある Azure Service Bus メッセージングの高度な機能をいくつか紹介しているサンプルを確認してください。[https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples]

<!--Image references-->

[1]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[2]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-send.png
[3]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->