<properties 
	pageTitle="Azure Notification Hubs の使用" 
	description="Azure Notification Hubs を使用してユーザーにプッシュ通知を行う方法について説明します" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-chrome" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="03/15/2015" 
	ms.author="piyushjo"/>
	
# Notification Hubs の使用

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

このトピックでは、Azure Notification Hubs を使用して Chrome アプリケーションにプッシュ通知を送信する方法について説明します。

Chrome アプリケーション通知を使用する主な利点の 1 つは、Google Chrome ブラウザーのコンテキスト内に通知が表示されるため、Chrome アプリケーションをブラウザーで実行したり開いたりしておく必要がないことです (ただし、Chrome ブラウザー自体は実行しておく必要があります)。また、Chrome の通知ウィンドウにはすべての通知がまとめて表示されます。 

>[AZURE.NOTE] これは一般的なブラウザー内のプッシュ通知ではなく、Chrome アプリケーションに固有の通知です。詳細については、[Chrome アプリケーションの概要]に関するページを参照してください。Chrome アプリケーションは以前は "パッケージ型アプリケーション" として知られており、単純な "ホスト型アプリケーション" とは異なります。この違いについては、「[Installable Web Apps]」を参照してください。Chrome アプリケーションは、Apache Cordova を使用してモバイル (Android や iOS) で実行することもできます。詳細については、[モバイルでの Chrome アプリケーションの実行]に関するページを参照してください。 

このチュートリアルでは、Google Cloud Messaging (GCM) を使用してプッシュ通知を受信する Chrome アプリケーションを作成します。完了すると、その Chrome アプリケーションをインストールしたすべての Chrome ユーザーにプッシュ通知をブロードキャストできるようになります。 

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

* [Google Cloud Messaging (GCM) を有効にする](#register)
* [Notification Hub を構成する](#configure-hub)
* [Notification Hub に Chrome アプリを接続する](#connect-app)
* [Chrome アプリに通知を送信する](#send)
* [次のステップ](#next-steps)

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。[Google Cloud Messaging for Chrome] は廃止されており、同じ GCM で Android デバイスと Chrome インスタンスの両方がサポートされるため、GCM と Azure Notification Hub の構成は Android 用の構成と同じです。 

Notification Hubs を使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、「次のステップ」に記載されているチュートリアルも一緒に参照してください。 

>[AZURE.NOTE] このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F)をご覧ください。

##<a id="register"></a>Google Cloud Messaging (GCM) を有効にする

1. [Google Cloud コンソール] Web サイトに移動し、Google アカウント資格情報でサインインして、**[Create Project]** ボタンをクリックします。**[Project Name]** に適切なプロジェクト名を指定して、**[Create]** ボタンをクリックします。 

   	![][1]

2. 作成したプロジェクトの [Projects] ページにある**プロジェクト番号**をメモしておきます。Chrome アプリケーションでこの番号を **GCM 送信者 ID** として使用して GCM に登録します。

   	![][2]

3. 左側のウィンドウで、**[APIs & auth]** をクリックして下にスクロールし、トグルをクリックして **[Google Cloud Messaging for Android]** を有効にします。 *Google Cloud Messaging for Chrome* を有効にする必要はありません。将来、名前を  *Google Cloud Messaging* に変更することも可能です。 

   	![][3]

4. 左側のウィンドウで、**[Credentials]**、**[Create New Key]**、**[Server Key]**、**[Create]** の順にクリックします。

   	![][4]

5. サーバーの **API キー**をメモしておきます。次の手順では、Notification Hub でこのキーを構成して、GCM にプッシュ通知を送信できるようにします。 

   	![][5]

##<a id="configure-hub"></a>Notification Hub を構成する

1. **[Azure の管理ポータル]**にログオンし、画面の左下にある **[+新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。Notification Hub の名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][6]

4. 前の手順で作成した Notification Hub に移動します。通知ハブを含む名前空間 (通常は *** 通知ハブ名*-ns**) をクリックします。   

   	![][7]

5. 上部にある **[通知ハブ]** タブをクリックします。

   	![][8]

6. 次に、上部にある **[構成]** タブをクリックします。 

   	![][9]

7. **[構成]** タブで、下にスクロールして **[Google Cloud Messaging 設定]** を表示し、前のセクションで取得した **API キー**値を入力して、**[保存]** をクリックします。

   	![][10]

8. 上部にある **[ダッシュボード]** タブを選択し、下部にある **[接続情報]** をクリックします。 

   	![][11]
 
9. **DefaultListenSharedAccessSignature** (Chrome アプリケーションで Notification Hub に登録するために必要) と **DefaultFullSharedAccessSignature** (通知を送信するために必要) をメモします。 

   	![][12]

これで、Notification Hub が GCM と連動するように構成されました。また、追加の構成を行うために必要な接続文字列を取得しました。

##<a id="connect-app"></a>Notification Hub に Chrome アプリケーションを接続する

###新しい Chrome アプリケーションの作成
以下のサンプルは、[Chrome アプリケーションの GCM サンプル]に基づいており、Chrome アプリケーションの推奨の作成方法を使用します。以降のセクションでは、Azure Notification Hub に関連する手順を重点的に説明しています。この Chrome アプリケーションのソースを [Chrome アプリケーションの Notification Hub のサンプル]のページからダウンロードすることをお勧めします。

Chrome アプリケーションは JavaScript を使用して作成されます。推奨される任意のワード エディターを使用して作成を行うことができます。 

1. この Chrome アプリケーションの外観は次のようになります。 

   	![][15]

2. フォルダーを作成して **ChromePushApp** という名前を付けます。任意の名前を指定できます。 

3. このフォルダーの[crypto-js ライブラリ] から *cryto-js library* をダウンロードします。このライブラリのフォルダーに、*components* と *rollups* の 2 つのサブフォルダーが含まれることになります。 

4. manifest.json ファイルを作成します。すべての Chrome アプリケーションは、アプリケーションのメタデータ (具体的にはアプリケーションに対するアクセス許可) を記述するマニフェスト ファイルに基づいています。

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}
	
	 *permissions* 要素に注目してください。これにより、この Chrome アプリが GCM からのプッシュ通知を受信できるよう、指定されます。また、この要素では、Chrome アプリケーションが REST を呼び出して登録を行うための Azure Notification Hub の URI も指定する必要があります。
	このサンプルでは、元の GCM サンプルから再利用されるソースにあるアイコン ファイル (gcm_128.png) を使用します。任意の画像を使用できます。 
 
5. 次のコードを含む background.js というファイルを作成します。

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}
		
		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.
		
		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);
		
		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}
		
		var registerWindowCreated = false;
		
		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {
		
		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}
		
		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);
		
		// Set up listeners to trigger the first time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	これは、Chrome アプリ ウィンドウの html (*register.html*) をポップアップ表示し、受信したプッシユ通知を処理するためのハンドラー  *messageReceived* を定義するファイルです。 

6. Chrome アプリの UI を定義する *register.html* というファイルを作成します。このサンプルでは  *CryptoJS v3.1.2* を使用します。その他のバージョンをダウンロードした場合は、スクリプトの src のパスを修正してください。 

		<html>
		
		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>
		
		<body>
		
		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>
		
		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/> 
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea> 
		
		<br/>
		
		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
		
		<br/>
		<br/>
		
		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea> 
		
		</body>
		
		</html>

7. 次のコードを含む  *register.js* というファイルを作成します。このファイルは、*register.html* の基盤となるスクリプトを指定します。Chrome アプリケーションではインライン実行が許可されないため、UI 用の基盤となるスクリプトを別途作成する必要があります。 

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
		
		window.onload = function() { 
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH; 
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs."); 
		} 
		
		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }
		
		  document.getElementById("console").innerHTML = currentStatus  + status;
		}
		
		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);
		
		  // Prevent register button from being clicked again before the registration finishes
		  document.getElementById("registerWithGCM").disabled = true;
		}
		
		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;
		
		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }
		
		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;
		
		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}
		
		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}
		
		// From http://msdn.microsoft.com/library/dn495627.aspx 
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";
		
		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });
		
		  originalUri = endpoint + hubName;
		}
		
		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration
		
		  // Set expiration in seconds
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;
		
		  // using CryptoJS
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);
		
		  // construct authorization string
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}
		
		function sendNHRegistrationRequest()
		{
		  var registrationPayload = 
		  "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		  "<entry xmlns=\"http://www.w3.org/2005/Atom\">" + 
		      "<content type=\"application/xml\">" + 
		          "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";
		
		  // Update the payload with the registration id obtained earlier
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
		
		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();
		
		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };
		
		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }
		
		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");
		
		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	上記のスクリプトの注意事項は以下のとおりです。
	- *window.onload* は、UI の 2 つのボタンのボタン クリック イベント (GCM への登録を行うイベントと、GCM への登録後に返される登録 ID を使用して Azure Notification Hubs への登録を行うイベント) を定義します。
	- *updateLog* 関数は、単純なログ関数を定義します。
	- *registerWithGCM* は、Chrome アプリ インスタンスを登録するために、GCM に対して  *chrome.gcm.register* を呼び出す最初のボタン クリック ハンドラーです。 
	- *registerCallback* は、上記の GCM 登録の呼び出しから戻った時点で呼び出されるコールバック関数です。
	- *registerWithNH* は、Notification Hubs に登録する 2 番目のボタン クリック ハンドラーです。また、ユーザーが指定した ** と ** を取得して、Notification Hubs 登録の REST API 呼び出しを作成します。 
	- *splitConnectionString* と *generateSaSToken* は、すべての REST API 呼び出しで送信する必要のある SaS トークン作成の JavaScript 実装です。これについての詳細は、http://msdn.microsoft.com/library/dn495627.aspxを参照してください。 
	- *sendNHRegistrationRequest*  は、HTTP REST 呼び出しを実行する関数です。
	- *registrationPayload* は、登録 xml ペイロードを定義します。詳細については、「[登録の作成]」を参照してください。このペイロードでは、GCM から受信した ID を使用して登録 ID を更新します。 
	- *client* は、HTTP POST 要求の発行のために使用する  *XMLHttpRequest* のインスタンスです。*Authorization* ヘッダーを sasToken によって更新していることに注意してください。この呼び出しが正常に完了すると、Chrome アプリケーション インスタンスが Azure Notification Hubs に登録されます。 
	

8. 最後に、フォルダーについて次のビューが表示されます。
   	![][21]

###Chrome アプリケーションのセットアップとテスト

1. Chrome ブラウザーを開きます。**Chrome の [Extensions]** を開いて **[Developer mode]** を有効にします。 

   	![][16]

2. **[Load unpacked extension]** をクリックして、ファイルを作成したフォルダーに移動します。必要に応じて、**Chrome App and Extensions Developer Tool** (本質的には Chrome アプリケーションであり、Chrome アプリケーションを開発するための高度なデバッグ機能を提供します) を使用することもできます。このツールは、Chrome Web ストアからインストールする必要があります。 

   	![][17]

3. Chrome アプリケーションの作成時にエラーが発生しなかった場合は、作成した Chrome アプリケーションが表示されます。 

   	![][18]

4. 前の手順で **Google Cloud Console** から取得した**プロジェクト番号**を [Sender ID] に入力し、**[Register with GCM]** をクリックします。 *GCM 登録が成功しました* というメッセージが表示されるはずです。

   	![][19]

5. **[Notification Hub Name]** に Notification Hub 名を入力し、前の手順で Azure の管理ポータルから取得した **DefaultListenSharedAccessSignature** を入力して、**[Register with Azure Notification Hub]** をクリックします。*通知ハブの登録に成功しました*というメッセージ、および Azure Notification Hubs Registration ID を含む登録応答の詳細が表示されます。 

   	![][20]  

##<a name="send"></a>Chrome アプリケーションに通知を送信する

Notification Hubs を使用すれば、<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を使用するどのバックエンドからでも通知を送信できますが、このチュートリアルでは、.NET コンソール アプリケーションを使用して通知を送信します。 

Notification Hubs に統合されている Azure Mobile Services バックエンドから通知を送信する方法の例については、「**Mobile Services アプリへのプッシュ通知の追加**」 ([.NET バックエンド](mobile-services-javascript-backend-android-get-started-push.md) | [JavaScript バックエンド](mobile-services-javascript-backend-android-get-started-push.md)) を参照してください。  
REST API を使用して通知を送信する方法の例については、**「Java/PHP/Python から Notification Hubs を使用する方法」**( [Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md)) を参照してください。

1. Visual Studio で、**[ファイル]** メニューの **[新規作成]**、**[プロジェクト...]** をクリックし、**[Visual C#]** で **[Windows]**、**[コンソール アプリケーション]**、**[OK]** の順にクリックします。これで、新しいコンソール アプリケーション プロジェクトが作成されます。

2. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。これで、パッケージ マネージャー コンソールが表示されます。

3. コンソール ウィンドウで、次のコマンドを実行します。

        Install-Package WindowsAzure.ServiceBus
    
   	これで、<a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure Service Bus SDK への参照が追加されます。 

4. Program.cs ファイルを開き、次の  `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

5. **Program** クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	"hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。また、接続文字列のプレースホルダーは、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えてください。 

	>[AZURE.NOTE] **リッスン** アクセスではなく、**フル** アクセスを持つ接続文字列を使用してください。リッスン アクセス文字列に通知を送信するアクセス許可はありません。

5. 次に、**Main** メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

6. Chrome ブラウザーが開いていることを確認します。Chrome アプリケーションを開く必要はありません。次の通知のポップアップがデスクトップに表示されます。 

   	![][13]

7. Chrome の実行中に、(Windows の) タスク バーからアクセスできる Chrome の通知ウィンドウですべての通知を確認することもできます。 

   	![][14]

## <a name="next-steps"> </a>次のステップ

この簡単な例では、Chrome アプリケーションに通知をブロードキャストします。 
Notification Hubs の詳細については、「[Notification Hubs の概要]」を参照してください。
特定のユーザーをターゲットとするには、「[Azure Notification Hubs によるユーザーへの通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。 

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome アプリケーションの Notification Hub のサンプル]: http://google.com
[Google Cloud コンソール]: http://cloud.google.com/console
[Azure 管理ポータル]: https://manage.windowsazure.com/
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[Chrome アプリケーションの概要]: https://developer.chrome.com/apps/about_apps
[Chrome アプリケーションの GCM サンプル]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[モバイルでの Chrome アプリケーションの実行]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[登録の作成]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js ライブラリ]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs によるユーザーへの通知]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[通知ハブを使用したニュース速報の送信]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!--HONumber=49-->