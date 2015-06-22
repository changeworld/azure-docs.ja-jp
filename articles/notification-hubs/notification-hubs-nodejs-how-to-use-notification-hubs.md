<properties 
	pageTitle="Node.js で Notification Hubs を使用する方法" 
	description="Notification Hubs を使用して Node.js アプリケーションからプッシュ通知を送信する方法について説明します。" 
	services="notification-hubs" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="mwasson"/>

# Node.js から Notification Hubs を使用する方法
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js" class="current">Node.js</a>
</div>

##概要

このガイドでは、Node.js アプリケーションから Notification Hubs を使用する方法について説明します。紹介するシナリオでは、**Android、iOS、Windows Phone、Windows ストア アプリへの通知の送信**を対象としています。Notification Hubs の詳細については、「[次のステップ](#next)」 のセクションを参照してください。

##Notification Hubs とは?

Azure Notification Hubs は、モバイル デバイスにプッシュ通知を送信するための、使いやすく拡張性の高いマルチプラットフォーム インフラストラクチャを提供します。詳細については、「[Azure 通知ハブ](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx)」をご覧ください。

##Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、「[Node.js Web サイトの構築と Azure へのデプロイ][nodejswebsite]」、「[Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ][Node.js クラウド サービス] (Windows PowerShell を使用)」、または「[WebMatrix を使用した Node.js Web サイトの構築と Azure へのデプロイ]」を参照してください。

##Notification Hub 使用のためにアプリケーションを構成する

Azure Notification Hub を使用するには、Node.js azure パッケージをダウンロードして使用する必要があります。これには、
REST サービスと通信するための便利なライブラリ セットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **PowerShell** (Windows)**、Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure**」と入力すると、次のような出力が生成されます。

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  手動で **ls** または **dir** コマンドを実行して、**node_modules** フォルダーが作成されたことを確認することもできます。そのフォルダーで **azure** パッケージを検索します。このパッケージには、Notification Hubs にアクセスするために必要なライブラリが含まれています。

### モジュールのインポート

テキスト エディターを使用して、アプリケーションの **server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure');

### Azure Notification Hub 接続の設定

**NotificationHubService** オブジェクトを使用すると、通知ハブを操作できます。次のコードは、**hubname** という名前の通知ハブのための **NotificationHubService** オブジェクトを作成します。**server.js** ファイルの先頭付近の、azure モジュールをインポートするステートメントの後に、このコードを追加します。

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

接続 **connectionstring** の値は、Azure 管理ポータルで次の手順を実行して取得できます。

1. Azure 管理ポータルで **[Service Bus]** を選択し、通知ハブが含まれている名前空間を選択します。

2. **[通知ハブ]** を選択し、使用するハブを選択します。

3. **[概要]** セクションで **[接続文字列の表示]** を選択し、接続文字列の値をコピーします。

> [AZURE.NOTE] また、Azure PowerShell に用意されている **Get-AzureSbNamespace** コマンドレット、または Azure コマンド ライン ツールで **azure sb namespace show** コマンドを使用して、接続文字列を取得することもできます。

</div>

##通知の送信方法

**NotificationHubService** オブジェクトは、特定のデバイスやアプリケーションに通知を送信するために、次のオブジェクト インスタンスを公開します。

* **Android** - **notificationHubService.gcm** で利用可能な **GcmService** オブジェクトを使用します。
* **iOS** - **notificationHubService.apns** でアクセス可能な **ApnsService** オブジェクトを使用します。
* **Windows Phone** - **notificationHubService.mpns** で利用可能な **MpnsService** オブジェクトを使用します。
* **Windows ストア アプリケーション** - **notificationHubService.wns** で利用可能な **WnsService** オブジェクトを使用します。

### Android アプリケーション通知の送信方法

**GcmService** オブジェクトには、Android アプリケーションに通知を送信するために使用される **send** メソッドが用意されています。**send** メソッドは、次のパラメーターを受け取ります。

* Tags - タグ識別子。タグが指定されない場合、通知はすべてのクライアントに送信されます。
* Payload - メッセージの JSON または文字列ペイロード
* Callback - コールバック関数

ペイロード形式の詳細については、「[Implementing GCM Server](http://developer.android.com/google/gcm/server.html#payload)」の「Payload」のセクションを参照してください。

次のコードは、**NotificationHubService** によって公開されている **GcmService** インスタンスを使用して、すべてのクライアントにメッセージを送信します。

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### iOS アプリケーション通知の送信方法

**ApnsService** オブジェクトには、iOS アプリケーションに通知を送信するために使用される **send** メソッドが用意されています。**send** メソッドは、次のパラメーターを受け取ります。

* Tags - タグ識別子。タグが指定されない場合、通知はすべてのクライアントに送信されます。
* Payload - メッセージの JSON または文字列ペイロード
* Callback - コールバック関数

ペイロード形式の詳細については、「[Local and Push Notification Programming Guide](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)」の「Notification Payload」のセクションを参照してください。

次のコードは、**NotificationHubService** によって公開されている **ApnsService** インスタンスを使用して、すべてのクライアントにアラート メッセージを送信します。

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Windows Phone 通知の送信方法

**MpnsService** オブジェクトには、Windows Phone アプリに通知を送信するために使用される **send** メソッドが用意されています。**send** メソッドは、次のパラメーターを受け取ります。

* Tags - タグ識別子。タグが指定されない場合、通知はすべてのクライアントに送信されます。
* Payload - メッセージの XML ペイロード
* TargetName - トースト通知の場合は "toast"。タイル通知の場合は 'token'。
* NotificationClass - 通知の優先度。有効な値については、「[サーバーからのプッシュ通知](http://msdn.microsoft.com/library/hh221551.aspx)」の「HTTP ヘッダー要素」のセクションを参照してください。
* Options - 省略可能な要求ヘッダー
* Callback - コールバック関数

有効な TargetName、NotificationClass、ヘッダー オプションについては、「[Push notifications from a server (サーバーからのプッシュ通知)](http://msdn.microsoft.com/library/hh221551.aspx)」を参照してください。

次のコードは、**NotificationHubService** によって公開されている **MpnsService** インスタンスを使用して、トースト アラートを送信します。

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Windows ストア アプリ通知の送信方法

**WnsService** オブジェクトには、Windows ストア アプリに通知を送信するために使用される **send** メソッドが用意されています。**send** メソッドは、次のパラメーターを受け取ります。

* Tags - タグ識別子。タグが指定されない場合、通知はすべてのクライアントに送信されます。
* Payload - XML メッセージ ペイロード
* Type - 通知の種類
* Options - 省略可能な要求ヘッダー
* Callback - コールバック関数

有効な Type と要求ヘッダーの一覧については、「[プッシュ通知サービスの要求ヘッダーと応答ヘッダー](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx)」を参照してください。

次のコードは、**NotificationHubService** によって公開されている **WnsService** インスタンスを使用して、トースト アラートを送信します。

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## 次のステップ

これで、Notification Hub の基本的な使用方法を理解できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス:[Azure Notification Hubs][]
-   GitHub の [Azure SDK for Node] リポジトリ

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [次のステップ]: #nextsteps
  [サービス バス トピックとサブスクリプションとは]: #what-are-service-bus-topics
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [Node.js アプリケーションの作成]: #Create_a_Nodejs_Application
  [Service Bus を使用するようにアプリケーションを構成する]: #Configure_Your_Application_to_Use_Service_Bus
  [方法:トピックを作成する]: #How_to_Create_a_Topic
  [方法:サブスクリプションを作成する]: #How_to_Create_Subscriptions
  [方法:メッセージをトピックに送信する]: #How_to_Send_Messages_to_a_Topic
  [方法:サブスクリプションからメッセージを受信する]: #How_to_Receive_Messages_from_a_Subscription
  [方法:アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [方法:トピックとサブスクリプションを削除する]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [トピックの概念]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [イメージ]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [WebMatrix を使用した Node.js Web サイトの構築と Azure へのデプロイ]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js クラウド サービス]: ../cloud-services-nodejs-develop-deploy-app.md
[以前の管理ポータル]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [ストレージを使用する Node.js クラウド サービス]: /develop/nodejs/tutorials/web-app-with-storage/
  [ストレージを使用する Node.js Web アプリケーション]: /develop/nodejs/tutorials/web-site-with-storage/


<!--HONumber=49--> 