<properties linkid="develop-python-queue-service" UrlDisplayName="キュー サービス" pageTitle="キュー サービスの使用方法 (Python) - Windows Azure" MetaKeywords="Windows Azure キュー サービス メッセージング Python" description="Windows Azure キュー サービスを使用して、キューを作成および削除する方法、メッセージを挿入、取得、削除する方法について説明します。コード サンプルは PHP で記述されています。" metaCanonical="" services="storage" documentationCenter="Python" title="Python からキュー ストレージ サービスを使用する方法" authors=""  solutions="" writer="" manager="" editor=""  />



# Python からキュー ストレージ サービスを使用する方法
このガイドでは、Windows Azure キュー ストレージ サービスを使用して一般的な
シナリオを実行する方法について説明します。サンプルは Python API を使用して記述されて
います。キュー メッセージの**挿入**、**ピーク**、**取得**、
および**削除**と、**キューの作成および削除**の各シナリオについて
説明します。キューの詳細については、「[次のステップ][]」のセクションを参照してください。

## 目次

[キュー ストレージとは][]   
 [概念][]   
 [Windows Azure ストレージ アカウントの作成][]   
 [キューの作成方法][]   
 [メッセージをキューに挿入する方法][]   
 [次のメッセージをピークする方法][]   
 [次のメッセージをデキューする方法][]   
 [キューに配置されたメッセージの内容を変更する方法][]   
 [メッセージをデキューするための追加オプションの使用方法][]   
 [キューの長さを取得する方法][]   
 [キューを削除する方法][]   
 [次のステップ][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a name="create-account"> </a>Windows Azure ストレージ アカウントの作成
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]


**注:** Python またはクライアント ライブラリをインストールする場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/)」を参照してください。

## <a name="create-queue"> </a>キューの作成方法

**QueueService** オブジェクトを使用して、キューを操作できます。次のコードでは、**QueueService** オブジェクトを作成します。プログラムを使用して Windows Azure のストレージにアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage import *

次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、**QueueService** オブジェクトを作成します。"myaccount" と "mykey" の部分は、実際のアカウントとキーに置き換えます。

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## <a name="insert-message"> </a>メッセージをキューに挿入する方法

キューにメッセージを挿入するには、**put\_message** メソッドを使用し、
新しいメッセージを作成してキューに追加します。

	queue_service.put_message('taskqueue', 'Hello World')


## <a name="peek-message"> </a>次のメッセージをピークする方法

**peek\_messages** メソッドを呼び出すと、キューの先頭にあるメッセージを
キューから削除せずにピークできます。既定では、
**peek\_messages** は 1 つのメッセージを対象としてピークします。

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## <a name="get-message"> </a>次のメッセージをデキューする方法

コードでは、2 つの手順でキューからメッセージを削除します。**get\_messages** を
呼び出すと、既定では、キュー内の次のメッセージを取得します。**get\_messages** から
返されたメッセージは、このキューからメッセージを読み取る他のコードから
参照できなくなります。既定では、このメッセージを
参照できない状態は 30 秒間続きます。また、キューからのメッセージの削除を完了するには、
**delete\_message** を呼び出す必要があります。2 段階の手順でメッセージを削除する
この方法では、ハードウェアまたはソフトウェアの問題が原因でコードによる
メッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを
取得し、もう一度処理することができます。コードでは、メッセージが処理された直後に
**delete\_message** を呼び出します。

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## <a name="change-contents"> </a>キューに配置されたメッセージの内容を変更する方法

キュー内のメッセージの内容をインプレースで変更できます。メッセージが
作業タスクを表している場合は、この機能を使用して、作業タスクの状態を
更新できます。次のコードでは、**update\_message** メソッドを
使用してメッセージを更新します。

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## <a name="advanced-get"> </a>メッセージをデキューするための追加オプションの使用方法

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。
1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、
コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を
長くまたは短くすることができます。次のコード例では、**get\_messages**
メソッドを使用して、1 回の呼び出しで 16 個のメッセージを取得します。その後、for ループを
使用して、各メッセージを処理します。また、各メッセージの非表示タイムアウトを
5 分に設定します。

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## <a name="get-queue-length"> </a>キューの長さを取得する方法

キュー内のメッセージの概数を取得できます。**get\_queue\_metadata**
メソッドでは、キューのメタデータを返すようにキュー サービスに要求します。また、
**x-ms-approximate-messages-count** は、カウントを検索するために返されるディクショナリに対するインデックスとして使用する必要があります。
キュー サービスが要求に応答した後にメッセージが追加または削除される可能性があるため、
この結果は概数にすぎません。

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## <a name="delete-queue"> </a>キューを削除する方法

キューおよびキューに含まれているすべてのメッセージを削除するには、
**delete\_queue** メソッドを呼び出します。

	queue_service.delete_queue('taskqueue')

## <a name="next-steps"> </a>次のステップ

これで、キュー ストレージの基本を学習できました。さらに複雑なストレージ
タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Windows Azure のデータの格納とアクセス][]
-   [Windows Azure ストレージ チーム ブログ][] (このページは英語の場合があります)

  [次のステップ]: #next-steps
[キュー ストレージとは]: #what-is
[概念]: #concepts
[Windows Azure ストレージ アカウントの作成]: #create-account
[キューの作成方法]: #create-queue
[メッセージをキューに挿入する方法]: #insert-message
[次のメッセージをピークする方法]: #peek-message
[次のメッセージをデキューする方法]: #get-message
[キューに配置されたメッセージの内容を変更する方法]: #change-contents
[メッセージをデキューするための追加オプションの使用方法]: #advanced-get
[キューの長さを取得する方法]: #get-queue-length
[キューを削除する方法]: #delete-queue
[Windows Azure のデータの格納とアクセス]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
[Windows Azure ストレージ チーム ブログ (このページは英語の場合があります)]: http://blogs.msdn.com/b/windowsazurestorage/

