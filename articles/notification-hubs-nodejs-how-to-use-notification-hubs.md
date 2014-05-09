<properties linkid="develop-nodejs-how-to-guides-service-bus-notification-hubs" urlDisplayName="通知ハブ" pageTitle="サービス バス通知ハブ - Node.js デベロッパー センター" metaKeywords="" description="サービス バス通知ハブを使用してプッシュ通知を送信する方法について説明します。コード サンプルは Node.js アプリケーション向けに作成されています。" metaCanonical="" services="service-bus" documentationCenter="Node.js" title="サービス バス通知ハブの使用方法" authors="larryfr" solutions="" manager="" editor="" />





# サービス バス通知ハブの使用方法

このガイドでは、Node.js アプリケーションからサービス バス通知ハブを
使用する方法について説明します。紹介するシナリオでは、**Android、iOS、Windows Phone、Windows ストア アプリへの通知の送信**を対象としています。通知ハブの詳細については、「[次のステップ](#next)」のセクションを参照してください。

## 目次

-   [サービス バス 通知ハブとは](#hub)
-   [Node.js アプリケーションの作成](#create)
-   [サービス バスを使用するためのアプリケーションの構成](#config)
-   [方法: 通知を送信する](#send)
-   [次のステップ](#next)

##<a id="hub"></a> サービス バス通知ハブとは

Azure のサービス バス通知ハブによって、モバイル デバイスにプッシュ通知を送信するための、使いやすく拡張性の高いマルチプラットフォーム インフラストラクチャが提供されます。詳細については、「[Azure のサービス バス通知ハブ](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj927170.aspx)」を参照してください。

##<a id="create"></a> Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、「[Node.js アプリケーションの作成と Azure Web サイトへの展開]」、「[Node.js クラウド サービス]」(Windows PowerShell の使用)、または「[WebMatrix を使用した Web サイト]」を参照してください。

##<a id="config"></a> サービス バスを使用するようにアプリケーションを構成する

Azure のサービス バスを使用するには、Node.js azure パッケージを
ダウンロードして使用する必要があります。このパッケージには、サービス バス REST サービスと通信
するための便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure**」と入力すると、
    次のような出力が生成されます。

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

3. 手動で **ls** または **dir** コマンドを実行して、
    **node\_modules** フォルダーが作成されたことを確認することもできます。このフォルダーで
    **azure** パッケージを検索します。このパッケージには、
    サービス バス通知ハブにアクセスするために必要なライブラリが含まれています。

### モジュールのインポート

テキスト エディターを使用して、アプリケーションの 
**server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure');

### Azure のサービス バス接続の設定

**NotificationHubService** オブジェクトを使用すると、通知ハブを操作できます。次の
コードは、**hubname** という名前の通知ハブ用の **NotificationHubService** オブジェクトを作成します。**server.js** ファイルの
先頭付近の、azure モジュールをインポートするステートメントの後に、このコードを追加します。

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

接続 **connectionstring** の値は、Azure 管理ポータルで次の手順を実行して取得できます。

1. Azure 管理ポータルで **[サービス バス]** を選択し、通知ハブが含まれている名前空間を選択します。

2. **[通知ハブ]** を選択し、使用するハブを選択します。

3. **[概要]** セクションで **[接続文字列の表示]** を選択し、接続文字列の値をコピーします。

<div class="dev-callout">
<strong>注</strong>
<p>また、Azure PowerShell に用意されている <b>Get-AzureSbNamespace</b> コマンドレット、または Azure コマンド ライン ツールで <b>azure sb namespace show</b> コマンドを使用して、接続文字列を取得することもできます。</p>

</div>

##<a id="send"></a> 通知の送信方法

**NotificationHubService** オブジェクトは、特定のデバイスやアプリケーションに通知を送信するために、次のオブジェクト インスタンスを公開します。

* **Android** - **notificationHubService.gcm** で利用可能な **GcmService** オブジェクトを使用します。
* **iOS** - **notificationHubService.apns** でアクセス可能な **ApnsService** オブジェクトを使用します。
* **Windows Phone** - **notificationHubService.mpns** で利用可能な **MpnsService** オブジェクトを使用します。
* **Windows Store applications** - **notificationHubService.wns** で利用可能な **WnsService** オブジェクトを使用します。

### Android アプリケーション通知の送信方法

**GcmService** オブジェクトには、Android アプリケーションに通知を送信するために使用される **send** メソッドが用意されています。**send** メソッドは、次のパラメーターを受け取ります。

* Tags - タグ識別子。タグが指定されない場合、通知はすべてのクライアントに送信されます。
* Payload - メッセージの JSON または文字列ペイロード
* Callback - コールバック関数

ペイロード形式の詳細については、「[Implementing GCM Server (GCM サーバーの実装)](http://developer.android.com/google/gcm/server.html#payload)」の「Payload (ペイロード)」のセクションを参照してください。

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

ペイロード形式の詳細については、「[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)」の「Notification Payload (通知ペイロード)」のセクションを参照してください。

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
* TargetName - トースト通知の場合は 'toast'。タイル通知の場合は 'token'。
* NotificationClass - 通知の優先度。有効な値については、「[Push notifications from a server (サーバーからのプッシュ通知)](http://msdn.microsoft.com/ja-jp/library/hh221551.aspx)」の「HTTP Header Elements (HTTP ヘッダー要素)」のセクションを参照してください。
* Options - 省略可能な要求ヘッダー
* Callback - コールバック関数

有効な TargetName、NotificationClass、ヘッダー オプションについては、「[Push notifications from a server (サーバーからのプッシュ通知)](http://msdn.microsoft.com/ja-jp/library/hh221551.aspx)」を参照してください。

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

有効な Type と要求ヘッダーの一覧については、「[プッシュ通知サービスの要求ヘッダーと応答ヘッダー](http://msdn.microsoft.com/ja-jp/library/windows/apps/hh465435.aspx)」を参照してください。

次のコードは、**NotificationHubService** によって公開されている **WnsService** インスタンスを使用して、トースト アラートを送信します。

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

##<a id="next"></a> 次のステップ

これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が
必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス:「[Azure のサービス バス通知ハブ][]」。
-   GitHub の [Azure SDK for Node] リポジトリ。

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [次のステップ]: #nextsteps
  [サービス バス トピックとサブスクリプションとは]: #what-are-service-bus-topics
  [サービス名前空間の作成]: #create-a-service-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-default-credentials
  [Node.js アプリケーションの作成]: #Create_a_Nodejs_Application
  [サービス バスを使用するようにアプリケーションを構成する]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: トピックを作成する]: #How_to_Create_a_Topic
  [How to: サブスクリプションを作成する]: #How_to_Create_Subscriptions
  [How to: メッセージをトピックに送信する]: #How_to_Send_Messages_to_a_Topic
  [How to: サブスクリプションからメッセージを受信する]: #How_to_Receive_Messages_from_a_Subscription
  [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: トピックとサブスクリプションを削除する]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [トピックの概念]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure のサービス バス通知ハブ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [WebMatrix を使用した Web サイト]: /ja-jp/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js クラウド サービス]: /ja-jp/documentation/articles/cloud-services-nodejs-develop-deploy-app/
[管理ポータルを開く]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [Node.js アプリケーションの作成と Azure Web サイトへの展開]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
  [ストレージを使用する Node.js クラウド サービス]: /ja-jp/develop/nodejs/tutorials/web-app-with-storage/
  [ストレージを使用する Node.js Web アプリケーション]: /ja-jp/develop/nodejs/tutorials/web-site-with-storage/

