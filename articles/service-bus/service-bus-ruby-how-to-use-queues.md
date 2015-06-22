<properties 
	pageTitle="Service Bus キューの使用方法 (Ruby) - Azure" 
	description="Azure での Service Bus キューの使用方法を学習します。コード サンプルは Ruby で記述されています。" 
	services="service-bus" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="tomfitz"/>




#Service Bus キューの使用方法

このガイドでは、Service Bus キューの使用方法について説明します。サンプルは Ruby で記述され、Azure gem を利用しています。紹介するシナリオは、**キューの作成、メッセージの送受信**、**キューの削除**です。キューの詳細については、「[次のステップ](#next-steps)」 セクションをご覧ください。

##Service Bus キューとは

Service Bus キューは、**メッセージ通信仲介**モデルをサポートしています。キューを使用すると、分散アプリケーションのコンポーネントが互いに直接通信することがなくなり、仲介者の役割を果たすキューを介してメッセージをやり取りすることになります。メッセージ プロデューサー (送信者) はキューにメッセージを送信した後で、それまでの処理を引き続き実行します。
メッセージ コンシューマー (受信者) は、キューからメッセージを非同期に受信して処理します。メッセージ プロデューサーは、それ以降のメッセージの処理と送信を続ける場合、メッセージ コンシューマーからの応答を待つ必要がありません。キューでは、コンシューマーが競合している場合のメッセージ配信に**先入先出法 (FIFO)** を使用します。つまり、通常はキューに追加された順番にメッセージが受信され、処理されます。このとき、メッセージを受信して処理できるメッセージ コンシューマーは、メッセージ 1 件につき 1 つだけです。

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Service Bus キューは汎用テクノロジであり、幅広いシナリオで使用できます。

-   多層 
    Azure アプリケーションでの Web ロールと Worker ロールとの間の通信
-   ハイブリッド ソリューションでの内部設置型アプリケーションと Azure によってホストされるアプリケーションとの間の通信
-   複数の組織で実行される分散アプリケーションまたは 1 つの組織内の異なる部署で実行される分散アプリケーションのコンポーネント間の通信

キューを使用すると、アプリケーションのスケール アウト性とアーキテクチャの復元性を有効にできます。

##サービス名前空間の作成
Azure の Service Bus キューを使用するには、最初にサービス名前空間を作成する必要があります。サービス名前空間は、アプリケーション内でサービス バス リソースをアドレス指定するためのスコープ コンテナーを提供します。ポータルではサービス バスの作成に ACS 接続が使用されないため、コマンド ライン インターフェイスを使用して名前空間を作成する必要があります。

サービス名前空間を作成するには:

1. Azure PowerShell コンソールを開きます。

2. コマンドを下に示すように入力して、Azure のサービス バス名前空間を作成します。独自の名前空間値と、アプリケーションと同じリージョンを指定します。 

    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)

##名前空間の管理資格情報の取得
新規作成した名前空間に対してキューの作成などの管理操作を実行するには、名前空間の管理資格情報を取得する必要があります。

1. [Azure 管理ポータル](http://manage.windowsazure.com/)へのログオン

2. 作成した Service Bus の名前空間を選択します。

     ![Select namespace](./media/service-bus-ruby-how-to-use-queues/selectns.png)

3. 下部で、**[接続情報]** を選択します。

      ![Select connection](./media/service-bus-ruby-how-to-use-queues/selectconnection.png)

4. 既定のキーをコピーします。この値は後にコードで使用します。

       ![Copy key](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

##Ruby アプリケーションの作成

Ruby アプリケーションを作成します。手順については、[Azure での Ruby アプリケーションの作成に関するページ](/develop/ruby/tutorials/web-app-with-linux-vm/)をご覧ください。

##Service Bus を使用するようにアプリケーションを構成する

Azure サービス バスを使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

###RubyGems を使用してパッケージを取得する

1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。

2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

###パッケージをインポートする

任意のテキスト エディターを使用して、ストレージを使用する Ruby ファイルの先頭に次のコードを追加します。

    require "azure"

##Azure Service Bus 接続の設定

azure モジュールは、Azure Service Bus 名前空間に接続するために必要な情報として、環境変数 **AZURE_SERVICEBUS_NAMESPACE** と **AZURE_SERVICEBUS_ACCESS_KEY** を読み取ります。これらの環境変数が設定されていない場合は、**Azure::ServiceBusService** を使用する前に、次のコードを使用して名前空間情報を指定する必要があります。

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

作成した値には、URL 全体ではなく、Service Bus の名前空間値を設定してください。たとえば、"yourexamplenamespace.servicebus.windows.net" ではなく、**"yourexamplenamespace"** を使用します。 

##キューの作成方法

**Azure::ServiceBusService** オブジェクトを使用して、キューを操作できます。キューを作成するには、**create_queue()** メソッドを使用します。次の例では、キューを作成し、既に存在している場合はエラーを出力します。

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

**Azure::ServiceBus::Queue** オブジェクトに追加のオプションを渡すこともできます。これにより、メッセージの有効期間やキューの最大サイズなどの既定のキューの設定をオーバーライドできます。次の例は、キューの最大サイズを 5 GB に、有効期間を 1 分に設定する方法を示しています。

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

##メッセージをキューに送信する方法

メッセージを Service Bus キューに送信するには、アプリケーションで **Azure::ServiceBusService** オブジェクトの **send_queue_message()** メソッドを呼び出します。Service Bus キューに送信された (またキューから受信された) メッセージは **Azure::ServiceBus::BrokeredMessage** オブジェクトであり、このオブジェクトには、一連の標準的なプロパティ (**label**、**time_to_live** など)、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリや任意のアプリケーション データの本体が備わっています。アプリケーションでは、メッセージとして文字列値を渡すことによってメッセージの本文を設定でき、必須の標準プロパティは既定値に設定されます。

次の例では、**send_queue_message()** を使用して、"test-queue" というキューにテスト メッセージを送信する方法を示しています。

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

Service Bus キューでは、最大 256 KB までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーは、64 KB が最大サイズです)。キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。このキュー サイズは作成時に定義され、上限は 5 GB です。

##キューからメッセージを受信する方法

キューからメッセージを受信するには、**Azure::ServiceBusService** オブジェクトの **receive_queue_message()** メソッドを使用します。既定では、メッセージは読み取られて (ピークされて) ロックされますが、キューからは削除されません。ただし、**:peek_lock** オプションを **false** に設定すると、読み取ったメッセージをキューから削除できます。

既定の動作では、読み取りと削除が 2 段階の操作になるため、メッセージが失われることを許容できないアプリケーションにも対応できます。サービス バスは要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**delete_queue_message()** メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第 2 段階を完了します。**delete_queue_message()** メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

**:peek_lock** パラメーターを **false** に設定すると、メッセージの読み取りと削除は最もシンプルなモデルになります。これは、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

次の例では、**receive_queue_message()** を使用したメッセージの受信と処理の方法を示しています。この例では、まず **:peek_lock** を **false** に設定し、メッセージを受信して削除します。次に、別のメッセージを受信してから、**delete_queue_message()** を使用してメッセージを削除します。

    message = azure_service_bus_service.receive_queue_message("test-queue", 
	  { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

##アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、**Azure::ServiceBusService** オブジェクトの **unlock_queue_message()** メソッドを呼び出すことができます。このメソッドが呼び出されると、サービス バスによってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージには、タイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete_queue_message()** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージがアプリケーションに再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題は、メッセージの **message_id** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

##次のステップ

これで、サービス バスキューの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

-   MSDN リファレンス:[キュー、トピック、サブスクリプション](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx)
-   GitHub の [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリを参照

この記事で説明されている Azure Service Bus キューと、「[Service Bus の使用方法](/develop/ruby/how-to-guides/queue-service/)」で説明されている Azure Service Bus キューの比較については、「[Azure キューと Azure Service Bus キューの比較](http://msdn.microsoft.com/library/windowsazure/hh767287.aspx)」をご覧ください。

<!--HONumber=47-->
 