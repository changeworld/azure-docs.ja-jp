<properties
	pageTitle="キュー ストレージと Visual Studio 接続済みサービスの概要 (ASP.NET) | Microsoft Azure"
	description="Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後、Visual Studio の ASP.NET プロジェクトで Azure Queue ストレージの使用を開始する方法について説明します。"
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="tarcher"/>

# Azure キュー ストレージと Visual Studio 接続済みサービスの概要

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Overview

この記事では、Visual Studio の **[接続済みサービスの追加]** ダイアログを使用して ASP.NET プロジェクトで Azure ストレージ アカウントを参照または作成した後に、Visual Studio で Azure キュー ストレージの使用を開始する方法について説明します。

ストレージ アカウントで Azure キューを作成し、アクセスする方法を紹介します。また、キュー メッセージの追加、変更、読み取り、削除などの基本的なキュー操作を実行する方法についても説明します。サンプルは C# コードで記述され、[.NET 用 Microsoft Azure Storage クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn261237.aspx)を使用しています。ASP.NET の詳細については、[ASP.NET](http://www.asp.net) に関するページを参照してください。

Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。キューの 1 つのメッセージの最大サイズは 64 KB で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。

## コードでキューにアクセスする

ASP.NET プロジェクトでキューにアクセスするには、Azure キュー ストレージにアクセスする C# ソース ファイルに、次の項目を含める必要があります。

1. C# ファイル冒頭の名前空間宣言に、次の **using** ステートメントが含まれていることを確認します。

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;

2. ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. ストレージ アカウント内のキュー オブジェクトを参照する **CloudQueueClient** オブジェクトを取得します。

	    // Create the CloudQueueClient object for this storage account.
    	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 特定のキューを参照する **CloudQueue** オブジェクトを取得します。

    	// Get a reference to a queue named "messageQueue"
	    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**注**: 上記のコードはすべて、以下の例に示すコードの前に使用してください。

## コードでキューを作成する

**CreateIfNotExists** への呼び出しを上記のコードに追加するだけで、コード内に Azure キューが作成されます。

	// Create the messageQueue if it does not exist
	messageQueue.CreateIfNotExists();

## メッセージをキューに追加する

既存のキューにメッセージを挿入するには、新しい **CloudQueueMessage** オブジェクトを作成した後、**AddMessage** メソッドを呼び出します。

**CloudQueueMessage** オブジェクトは、文字列 (UTF-8 形式) またはバイト配列から作成できます。

次の例では、'Hello, World' というメッセージを挿入します。

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	messageQueue.AddMessage(message);

## キュー内のメッセージを読み取る

PeekMessage() メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。

	// Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## キュー内のメッセージを読み取って削除する

コードでは、2 つの手順でキューからメッセージを削除 (デキュー) できます。
1. GetMessage() を呼び出すと、キュー内の次のメッセージが取得されます。GetMessage() から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。既定では、このメッセージを参照できない状態は 30 秒間続きます。
2.	また、キューからのメッセージの削除を完了するには、**DeleteMessage** を呼び出します。

このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。次のコードでは、メッセージが処理された直後に **DeleteMessage** を呼び出しています。

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

	// Process the message in less than 30 seconds

	// Then delete the message.
	await messageQueue.DeleteMessage(retrievedMessage);


## 追加オプションを利用してメッセージをデキューする

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。次のコード例では、**GetMessages** メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。その後、**foreach** ループを使用して、各メッセージを処理します。また、各メッセージの非表示タイムアウトを 5 分に設定します。この 5 分の非表示期間は、すべてのメッセージに対して同時に開始します。そのため、**GetMessages** の呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## キューの長さを取得する

キュー内のメッセージの概数を取得できます。**FetchAttributes** メソッドは、メッセージ数などのキューの属性を取得するようにキュー サービスに要求します。**ApproximateMethodCount** プロパティは、キュー サービスを呼び出さずに、**FetchAttributes** メソッドによって取得された最後の値を返します。

	// Fetch the queue attributes.
	messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Async-Await パターンを一般的なキュー API で使用する

この例では、Async-Await パターンを一般的なキュー API で使用する方法を示します。このサンプルは、特定のメソッドの非同期バージョンをそれぞれ呼び出しています。これは、各メソッドの Async 接尾辞によって確認できます。非同期のメソッドを使用するとき、async-await パターンは、呼び出しが完了するまでローカルでの実行を中断します。この動作により、現在のスレッドで別の作業を実行できるようになるため、パフォーマンスのボトルネックを回避し、アプリケーションの全体的な応答性が向上させることができます。.NET での Async-Await パターンの使用方法の詳細については、[Async と Await (C# と Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) に関するページを参照してください。

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## キューを削除する

キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトの **Delete** メソッドを呼び出します。

    // Delete the queue.
    messageQueue.Delete();

## 次のステップ

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

<!---HONumber=AcomDC_0817_2016-->