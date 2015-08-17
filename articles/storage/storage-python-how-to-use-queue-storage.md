<properties 
	pageTitle="Python から Queue ストレージを使用する方法 | Microsoft Azure" 
	description="Python から Azure Queue サービスを使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。" 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="huvalo"/>

# Python から Queue ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概要

このガイドでは、Azure Queue ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは Python で記述され、[Python Azure パッケージ][]を使用しています。キュー メッセージの**挿入**、**ピーク**、**取得**、および**削除**と、**キューの作成および削除**の各シナリオについて説明します。キューの詳細については、「[次のステップ][]」セクションを参照してください。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


> [AZURE.NOTE]Python または [Python Azure パッケージ][]をインストールする場合は、[Python インストール ガイド](../python-how-to-install.md)を参照してください。

## 方法: キューを作成する

**QueueService** オブジェクトを使用して、キューを操作できます。次のコードでは、**QueueService** オブジェクトを作成します。プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage import QueueService

次のコードでは、ストレージ アカウント名とアカウント キーを使用して **QueueService** オブジェクトを作成します。'myaccount' と 'mykey' の部分は、実際のアカウントとキーに置き換えてください。

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## 方法: メッセージをキューに挿入する

キューにメッセージを挿入するには、**put\_message** メソッドを使用し、新しいメッセージを作成してキューに追加します。

	queue_service.put_message('taskqueue', 'Hello World')


## 方法: 次のメッセージをピークする

**peek\_messages** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。既定では、**peek\_messages** は 1 つのメッセージを対象としてピークします。

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## 方法: 次のメッセージをデキューする

コードでは、2 つの手順でキューからメッセージを削除します。**get\_messages** を呼び出すと、既定では、キュー内の次のメッセージを取得します。**get\_messages** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。既定では、このメッセージを参照できない状態は 30 秒間続きます。また、キューからのメッセージの削除を完了するには、**delete\_message** を呼び出す必要があります。2 段階の手順でメッセージを削除するこの方法では、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。コードでは、メッセージが処理された直後に **delete\_message** を呼び出します。

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## 方法: キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。メッセージが作業タスクを表している場合は、この機能を使用して、作業タスクの状態を更新できます。次のコードでは、**update\_message** メソッドを使用してメッセージを更新します。

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## 方法: メッセージをデキューするための追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。次のコード例では、**get\_messages** メソッドを使用して、1 回の呼び出しで 16 個のメッセージを取得します。その後、for ループを使用して、各メッセージを処理します。また、各メッセージの非表示タイムアウトを 5 分に設定します。

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## 方法: キューの長さを取得する

キュー内のメッセージの概数を取得できます。**get\_queue\_metadata** メソッドは、キューのメタデータを返すように Queue サービスに要求します。カウントを取得するには、返されたディクショナリに対するインデックスとして **x-ms-approximate-messages-count** を使用します。Queue サービスが要求に応答した後にメッセージが追加または削除される可能性があるため、取得される結果はおおよその数を示しているだけです。

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## 方法: キューを削除する

キューおよびキューに含まれているすべてのメッセージを削除するには、**delete\_queue** メソッドを呼び出します。

	queue_service.delete_queue('taskqueue')

## 次のステップ

これで、Queue ストレージの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス][]
-   [Azure Storage チームのブログ][]

[Azure のデータの格納とアクセス]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure Storage チームのブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure パッケージ]: https://pypi.python.org/pypi/azure
 

<!---HONumber=August15_HO6-->