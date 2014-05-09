<properties linkid="dev-net-how-to-use-queue-storage-service-java" urlDisplayName="キュー サービス" pageTitle="キュー サービスの使用方法 (Java) | Azure" metaKeywords="Azure キュー サービス, Azure キュー ストレージ サービス, キューのピーク, キューへのメッセージの挿入, キューからのメッセージの取得, キューからのメッセージの削除, キューの作成, キューの削除, キュー サービス Java" description="Azure キュー サービスを使用して、キューを作成および削除する方法、メッセージを挿入、取得、削除する方法について説明します。コード サンプルは Java で記述されています。" metaCanonical="" services="storage" documentationCenter="Java" title="Java からキュー ストレージ サービスを使用する方法" authors="" solutions="" manager="" editor="" />





# Java からキュー ストレージ サービスを使用する方法

このガイドでは、Azure キュー ストレージ サービスを使用して
一般的なシナリオを実行する方法について説明します。サンプルは Java で記述され、[Azure 
SDK for Java][] を利用しています。キュー メッセージの挿入、
ピーク、取得、および削除と、キューの作成および削除の
各シナリオについて説明します。キューの詳細については、
「[次のステップ](#NextSteps)」のセクションを参照してください。

## <a name="Contents"> </a>目次

* [キュー ストレージとは](#what-is)
* [概念](#Concepts)
* [Azure のストレージ アカウントの作成](#CreateAccount)
* [Java アプリケーションの作成](#CreateApplication)
* [キュー ストレージにアクセスするようにアプリケーションを構成する](#ConfigureStorage)
* [Azure のストレージ接続文字列の設定](#ConnectionString)
* [方法: キューを作成する](#create-queue)
* [方法: メッセージをキューに追加する](#add-message)
* [方法: 次のメッセージをピークする](#peek-message)
* [方法: 次のメッセージをデキューする](#dequeue-message)
* [方法: キューに配置されたメッセージの内容を変更する](#change-message)
* [メッセージのデキュー用の追加オプション](#additional-options)
* [方法: キューの長さを取得する](#get-queue-length)
* [方法: キューを削除する](#delete-queue)
* [次のステップ](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a id="CreateAccount"></a>Azure のストレージ アカウントの作成</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"></a>Java アプリケーションの作成

このガイドで使用するストレージ機能は、Java アプリケーション内でローカルで
実行することも、Azure の Web ロールまたはワーカー ロールで動作するコード内で
実行することもできます。前提条件として、Java Development Kit (JDK) を
ダウンロードしてインストールしているとします。また、[Azure SDK for Java の
ダウンロード ページ][Azure SDK for Java]の指示に従って、
Azure Libraries for Java と Azure SDK をインストールし、
Azure サブスクリプションに Azure のストレージ アカウントを
作成しているとします。アプリケーションの作成には、メモ帳などの任意の
開発ツールを使用できます。要件は、Java プロジェクトをコンパイルできること、
および Azure Libraries for Java を参照できることのみです。

## <a name="ConfigureStorage"> </a>キュー ストレージにアクセスするようにアプリケーションを構成する

Azure ストレージ API を使用してキューにアクセスする Java ファイルの先頭には、
次の import ステートメントを追加します。

    // Include the following imports to use queue APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.queue.client.*;

## <a name="ConnectionString"></a>Azure のストレージ接続文字列の設定

Azure ストレージ クライアントでは、ストレージ接続文字列を使用して、
データ管理サービスにアクセスするためのエンドポイントおよび資格情報を保存します。クライアント アプリケーションの実行時、ストレージ接続文字列を次の
形式で指定する必要があります。*AccountName* と *AccountKey*
の値には、管理ポータルに表示されるストレージ アカウントの名前と
プライマリ アクセス キーを使用します。この例では、
接続文字列を保持する静的フィールドを宣言する方法を示しています。

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Azure 上のロール内で実行されるアプリケーションでは、この文字列はサービス
構成ファイルである ServiceConfiguration.cscfg に格納でき、
RoleEnvironment.getConfigurationSettings メソッドの呼び出しを使用
してアクセスできます。次の例では、
接続文字列をサービス構成ファイル内の **Setting** 要素 
(*StorageConnectionString*) から取得します。

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

## <a name="create-queue"> </a>方法: キューを作成する

CloudQueueClient オブジェクトを使用すると、キューの参照オブジェクトを取得できます。次の
コードでは、CloudQueueClient オブジェクトを作成します。

このガイドのすべてのコードでは、前に示した 2 つの方法のいずれかで宣言した
ストレージ接続文字列を使用します。CloudStorageAccount オブジェクトを
作成する方法は他にもあります。詳細については、Javadoc ドキュメントの 
CloudStorageAccount に関するページを参照してください。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

CloudQueueClient オブジェクトにより、使用するキューへの参照を
取得します。キューが存在しない場合は作成できます。

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

## <a name="add-message"> </a>方法: メッセージをキューに追加する

既存のキューにメッセージを挿入するには、最初に新しい 
CloudQueueMessage を作成します。次に、addMessage メソッドを呼び出します。CloudQueueMessage は、
文字列 (UTF-8 形式) またはバイト配列から作成できます。
次のコードでは、キューが存在しない場合は作成し、
メッセージ 'Hello, World' を挿入します。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);

## <a name="peek-message"> </a>方法: 次のメッセージをピークする

peekMessage を呼び出すと、キューの先頭にあるメッセージを
キューから削除せずにピークできます。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.peekMessage();

## <a name="dequeue-message"> </a>方法: 次のメッセージをデキューする

コードでは、2 つの手順でキューからメッセージをデキューします。retrieveMessage を
呼び出すと、キュー内の次のメッセージが取得されます。retrieveMessage から
返されたメッセージは、このキューからメッセージを読み取る他のコードから
参照できなくなります。既定では、このメッセージを参照できない状態は 
30 秒間続きます。また、キューからのメッセージの削除を完了するには、deleteMessage を
呼び出す必要があります。このようにメッセージを 2 つの手順で削除することで、
ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が
失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度
処理することができます。コードでは、メッセージが処理された直後に deleteMessage を
呼び出します。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    // Process the message in less than 30 seconds, and then delete the message.
    queue.deleteMessage(retrievedMessage);

## <a name="change-message"> </a>方法: キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。メッセージが
作業タスクを表している場合は、この機能を使用して、作業タスクの状態を
更新できます。次のコードでは、キュー メッセージを
新しい内容に更新し、表示タイムアウトを設定して、
60 秒延長します。これにより、メッセージに関連付けられている作業の状態が保存され、クライアントにメッセージの操作を続行する時間が 1 分与えられます。この方法を使用すると、キュー メッセージに対する複数の手順から成る
ワークフローを追跡でき、ハードウェアまたはソフトウェアの問題が
原因で処理手順が失敗した場合に最初からやり直す必要がなくなります。通常は、
さらに再試行回数を保持し、メッセージの再試行回数が n 回を超えた場合は
メッセージを削除するようにします。こうすることで、処理するたびに
アプリケーション エラーをトリガーするメッセージから保護されます。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage message = queue.retrieveMessage();

    // Modify the message content and set it to be visible in 60 seconds
    message.setMessageContent("Updated contents.");
    EnumSet<MessageUpdateFields> updateFields = 
        EnumSet.of(MessageUpdateFields.CONTENT, MessageUpdateFields.VISIBILITY);
    queue.updateMessage(message, 60, updateFields, null, null);

## <a name="additional-options"> </a>メッセージをデキューするための追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。
1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、
コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を
長くまたは短くすることができます。

次のコード例では、retrieveMessages メソッドを使用して、1 回の呼び出しで 20 
個のメッセージを取得します。その後、**for** ループを使用して、各メッセージを
処理します。また、各メッセージの非表示タイムアウトを 5 分 (300 
秒) に設定します。この 5 分の非表示期間は、すべての
メッセージに対して同時に開始されます。そのため、retrieveMessages の
呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが
再び表示されます。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes, 
        // deleting each message after processing.
        queue.deleteMessage(message);
    }

## <a name="get-queue-length"> </a>方法: キューの長さを取得する

キュー内のメッセージの概数を取得できます。
downloadAttributes メソッドは、キューにあるメッセージの数など、
現在の値をキュー サービスに要求します。キュー サービスが
要求に応答した後にメッセージが追加または削除される可能性があるため、
これらの値は概数にすぎません。getApproximateMethodCount 
メソッドは、キュー サービスを呼び出さずに、
downloadAttributes の呼び出しによって取得された最後の値を返します。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Download the approximate message count from the server
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count
    long cachedMessageCount = queue.getApproximateMessageCount();

## <a name="delete-queue"> </a>方法: キューを削除する

キューおよびキューに格納されているすべてのメッセージを削除するには、
キュー オブジェクトの delete メソッドを呼び出します。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue
    queue.delete();

## <a name="NextSteps"></a>次のステップ

これで、キュー ストレージの基本を学習できました。さらに複雑な
ストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス]
-   Azure のストレージ チーム ブログ: <http://blogs.msdn.com/b/windowsazurestorage/>

[Azure SDK for Java]: http://www.windowsazure.com/ja-jp/develop/java/
[Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx

