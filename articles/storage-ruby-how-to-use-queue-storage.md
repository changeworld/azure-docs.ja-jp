<properties 
	pageTitle="キュー サービスを使用する方法 (Ruby) | Microsoft Azure" 
	description="Azure Queue サービスを使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。コード サンプルは Ruby で記述されています。" 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>





# Ruby からキュー ストレージ サービスを使用する方法

このガイドでは、Microsoft Azure キュー ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは Ruby Azure API を使用して記述されています。
キュー メッセージの**挿入**、**ピーク**、**取得**、および**削除**と、**キューの作成および削除**の各シナリオについて説明します。キューの詳細については、「[次のステップ](#next-steps)」 セクションを参照してください。

## 目次

* [キュー ストレージとは](#what-is)
* [概念](#concepts)
* [Azure のストレージ アカウントの作成](#CreateAccount)
* [Ruby アプリケーションの作成](#create-a-ruby-application)
* [アプリケーションからストレージへのアクセスの構成](#configure-your-application-to-access-storage)
* [Azure のストレージ接続文字列の設定](#setup-a-windows-azure-storage-connection)
* [方法:キューを作成する](#how-to-create-a-queue)
* [方法:メッセージをキューに挿入する](#how-to-insert-a-message-into-a-queue)
* [方法:次のメッセージをピークする](#how-to-peek-at-the-next-message)
* [方法:次のメッセージをデキューする](#how-to-dequeue-the-next-message)
* [方法:キューに配置されたメッセージの内容を変更する](#how-to-change-the-contents-of-a-queued-message)
* [方法:メッセージのデキュー用の追加オプション](#how-to-additional-options-for-dequeuing-messages)
* [方法:キューの長さを取得する](#how-to-get-the-queue-length)
* [方法:キューを削除する](#how-to-delete-a-queue)
* [次のステップ](#next-steps)

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a id="CreateAccount"></a>Azure のストレージ アカウントの作成

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## <a id="create-a-ruby-application"></a>Ruby アプリケーションの作成

Ruby アプリケーションを作成します。手順については、 
「[Azure VM での Ruby on Rails Web アプリケーション](/ja-jp/develop/ruby/tutorials/web-app-with-linux-vm/)。」

## <a id="configure-your-application-to-access-storage"></a>アプリケーションからストレージへのアクセスの構成

Azure ストレージを使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### RubyGems を使用してパッケージを取得する

1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。

2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

### パッケージをインポートする

任意のテキスト エディターを使用して、ストレージを使用する Ruby ファイルの先頭に次のコードを追加します。

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>Azure のストレージ接続文字列の設定

azure モジュールは、Azure Storage アカウントに接続するために必要な情報として、環境変数 **AZURE\_STORAGE\_ACCOUNT** および **AZURE\_STORAGE\_ACCESS_KEY** 
を読み取ります。これらの環境変数が設定されていない場合は、**Azure::QueueService** を使用する前に、次のコードを使用してアカウント情報を指定する必要があります。

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

これらの値を取得するには、次の手順を実行します。

1. [Azure 管理ポータル](https://manage.windowsazure.com/)にログインします。
2. 使用するストレージ アカウントを表示します。
3. ナビゲーション ウィンドウの下部にある **[キーの管理]** をクリックします。
4. ポップアップ ダイアログに、ストレージ アカウント名、プライマリ アクセス キー、およびセカンダリ アクセス キーが表示されます。アクセス キーには、プライマリとセカンダリのどちらでも選択できます。

## <a id="how-to-create-a-queue"></a>方法:キューを作成する

次のコードは、**Azure::QueueService** オブジェクトを作成し、これによってキューを操作できるようにします。

	azure_queue_service = Azure::QueueService.new

**create_queue()** メソッドを使用して、指定した名前のキューを作成します。

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## <a id="how-to-insert-a-message-into-a-queue"></a>方法:メッセージをキューに挿入する

メッセージをキューに挿入するには、**create_message()** メソッドを使用し、新しいメッセージを作成してキューに追加します。

	azure_queue_service.create_message("test-queue", "test message")

## <a id="how-to-peek-at-the-next-message"></a>方法:次のメッセージをピークする

**peek\_messages()** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。既定では、**peek\_messages()** は 1 つのメッセージを対象としてピークします。ピークするメッセージ数を指定することもできます。

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## <a id="how-to-dequeue-the-next-message"></a>方法:次のメッセージをデキューする

キューからのメッセージの削除は、2 段階の手順で実行できます。

1. **list\_messages()** を呼び出すと、既定では、キュー内の次のメッセージを取得します。取得するメッセージ数を指定することもできます。**list\_messages()** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。パラメーターとして、表示タイムアウトを秒単位で指定します。

2. キューからのメッセージの削除を完了するには、**delete_message()** も呼び出す必要があります。

2 段階の手順でメッセージを削除するこの方法では、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。コードでは、メッセージが処理された直後に **delete\_message()** を呼び出します。

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## <a id="how-to-change-the-contents-of-a-queued-message"></a>方法:キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。次のコードでは、**update_message()** メソッドを使用してメッセージを更新します。このメソッドは、キュー メッセージの PopReceipt と、メッセージがキューに配置される日時を表す UTC 日付/時刻値を含むタプルを返します。

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## <a id="how-to-additional-options-for-dequeuing-messages"></a>方法:メッセージをデキューするための追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。

1. メッセージのバッチを取得できます。

2. コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。

次のコード例では、**list\_messages()** メソッドを使用して、1 回の呼び出しで 15 個のメッセージを取得します。その後、各メッセージを出力して削除します。また、各メッセージの非表示タイムアウトを 5 分に設定します。

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## <a id="how-to-get-the-queue-length"></a>方法:キューの長さを取得する

キュー内のメッセージの概数を取得できます。**get\_queue\_metadata()** メソッドを使用して、おおよそのメッセージ数とキューのメタデータを返すようにキュー サービスに要求します。

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## <a id="how-to-delete-a-queue"></a>方法:キューを削除する

キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトの **delete\_queue()** メソッドを呼び出します。

	azure_queue_service.delete_queue("test-queue")

## <a id="next-steps"></a>次のステップ

これで、キュー ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

- MSDN リファレンス:[Azure のデータの格納とアクセス](http://msdn.microsoft.com/library/windowsazure/gg433040.aspx)
- [Azure のストレージ チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)
- GitHub の [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリ

この記事で説明されている Azure キュー サービスと、「[Service Bus キューの使用方法](/ja-jp/develop/ruby/how-to-guides/service-bus-queues/)」で説明されている Azure Service Bus キューの比較については、「[Windows Azure キューと Windows Azure サービス バス キューの比較](http://msdn.microsoft.com/library/windowsazure/hh767287.aspx)」を参照してください。
<!--HONumber=42-->
