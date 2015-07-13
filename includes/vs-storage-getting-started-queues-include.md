#####キューを作成する
**CloudQueueClient** オブジェクトを使用すると、キューの参照オブジェクトを取得できます。次のコードでは、**CloudQueueClient** オブジェクトを作成します。このトピックのすべてのコードでは、Azure アプリケーションのサービス構成に格納されているストレージ接続文字列を使用します。**CloudStorageAccount** オブジェクトを作成する方法は他にもあります。詳細については、[CloudStorageAccount メソッド](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount")に関するドキュメントを参照してください。

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

**queueClient** オブジェクトを使用して、使用するキューへの参照を取得します。コードは "myqueue" という名前のキューを参照しようとします。 この名前のキューが見つからない場合は作成します。

	// Get a reference to a queue named “myqueue”.
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn’t already there, then create it.
	queue.CreateIfNotExists();

**注:** このコード ブロックは、以下のセクションで扱うコードの前に使用してください。

#####メッセージをキューに挿入する
既存のキューにメッセージを挿入するには、最初に新しい **CloudQueueMessage** オブジェクトを作成します。次に、AddMessage() メソッドを呼び出します。**CloudQueueMessage** オブジェクトは、文字列 (UTF-8 形式) またはバイト配列から作成できます。次のコードでは、キューが存在しない場合は作成し、メッセージ "Hello, World" を挿入します。

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	queue.AddMessage(message);

#####次のメッセージをピークする
PeekMessage() メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	Console.WriteLine(peekedMessage.AsString);

#####次のメッセージを削除する
コードでは、2 つの手順でキューからメッセージを削除 (デキュー) できます。


1. GetMessage() を呼び出すと、キュー内の次のメッセージが取得されます。GetMessage() から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。既定では、このメッセージを参照できない状態は 30 秒間続きます。 
2.	また、キューからのメッセージの削除を完了するには、DeleteMessage() を呼び出します。 

このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。次のコードでは、メッセージが処理された直後に DeleteMessage() を呼び出します。

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = queue.GetMessage();

	// Process the message in less than 30 seconds, and then delete the message.
	queue.DeleteMessage(retrievedMessage);

[Azure Storage の詳細](http://azure.microsoft.com/documentation/services/storage/)をご覧ください。[サーバー エクスプローラーを使用したストレージ リソースの参照](http://msdn.microsoft.com/library/azure/ff683677.aspx)に関するページもご覧ください。

<!---HONumber=62-->