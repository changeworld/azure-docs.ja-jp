<properties pageTitle="プッシュ通知の使用 (Appcelerator) | モバイル デベロッパー センター" metaKeywords="" description="Azure Mobile Services を使用して Appcelerator アプリにプッシュ通知を送信する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# モバイル サービスでのプッシュ通知の使用 (従来のプッシュ)
<div class="dev-center-tutorial-selector sublanding">
	<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows ストア C#</a>
	<a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows ストア JavaScript</a>
	<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
	<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
	<a href="/ja-jp/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

このトピックでは、Windows Azure Mobile Services を使用して、Appcelerator Titanium Studio で作成した iOS アプリケーションと Android アプリケーションの両方にプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple Push Notification Service (APNS) と Google Cloud Messaging を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

>[WACOM.NOTE] モバイル サービスは Azure 通知ハブに統合され、テンプレート、複数のプラットフォーム、改良されたスケールなど、追加のプッシュ通知機能をサポートするようになりました。このトピックは、まだ Notification Hubs 統合を使用するようにアップグレードされていない、既存のモバイル サービスを対象とします。新しいモバイル サービスを作成すると、この統合機能は自動的に有効になります。可能であれば、Notification Hubs を使用するようにサービスをアップグレードすることをお勧めします。**Appcelerator で Notification Hubs のプッシュを利用するチュートリアルを近日提供予定です。**

1.	[証明書の署名要求を作成する]
2.	[アプリケーションを登録し、プッシュ通知を有効にする]
3.	[アプリケーションのプロビジョニング ファイルを作成する]
4.	[Google クラウド メッセージングを有効にする]
5.  [Titanium 用の GCM モジュールを作成する]
6.	[モバイル サービスを構成する]
7.	[アプリケーションにプッシュ通知を追加する]
8.	[プッシュ通知を送信するようにスクリプトを更新する]
9.	[データを挿入して通知を受け取る]

このチュートリアルには、次のものが必要です。

* Appcelerator Azure Mobile Services モジュール
* Appcelerator Titanium Studio 3.2.1 またはそれ以降
* iOS 7.0 (またはそれ以降のバージョン) および Android 4.3 (またはそれ以降のバージョン) 対応のデバイス
* iOS Developer Program メンバーシップ
* アクティブな Google アカウント

> [WACOM.NOTE] プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[Mobile Services の使用]」を完了している必要があります。

[WACOM.INCLUDE [Apple Push Notifications を有効にする](../includes/enable-apple-push-notifications.md)]

## <a name="register-gcm"></a>Google Cloud Messaging を有効にする

>[WACOM.NOTE]この手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、「<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">Google アカウントの作成」</a>を参照してください。

[WACOM.INCLUDE [GCM を有効にする](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##  <a name="gcm-module"></a>Titanium 用の GCM モジュールを作成する

### モジュール作成のための Appcelerator Titanium Studio を準備する

Android モジュールを作成する場合は、Appcelerator Titanium Studio 内に Java サポートをインストールする必要があります。インストールしていない場合は、Appcelerator の「[Installing the Java Development Tools (Java Development Tools のインストール)]」で簡単な手順を参照してください。

Android NDK をインストールする必要があります。該当する .zip ファイルを [http://developer.android.com/sdk/ndk/index.html](http://developer.android.com/sdk/ndk/index.html)  からダウンロードして、ディスク上のいずれかの場所で解凍してください。この場所を忘れないでください。 

### 新しいモジュールを作成する

1. Appcelerator Titanium studio を開きます。

2. [File]、[New]、[Mobile Module Project] の順にクリックします。

    ![][0]

3. 次のウィンドウで、プロジェクト設定のデータを入力します。 

    * **Project name:** 例では、&quot;notificationhub&quot; を使用しています (同じにもできます)。

    * **Module Id:** 例では、&quot;com.winwire.notificationhub&quot; を使用しています。これは、"application Id" と一致している必要があります。

    * **Deployment Targets:** 例では Android を選択しています。

    > [WACOM.NOTE] ワークスペース名にはスペースを使用できません。スペースが含まれると、コンパイルした場合に問題が生じます。

    ![][1]

4. [Next] をクリックし、モジュールの情報を入力します。

    ![][2]

5. 最後に、[Finish] をクリックします。

6. timodule.xml ファイルを開きます。[android] タグで次の変更を追加します。


        <manifest package="com.winwire.notificationhub" android:versionCode="1" android:versionName="1.0">
        <supports-screens android:anyDensity="true"/>
        <uses-sdk android:minSdkVersion="8"/>
        <uses-permission android:name= "com.google.android.c2dm.permission.RECEIVE"/>
        <permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
        <uses-permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE"/>
        <uses-permission android:name= "android.permission.WAKE_LOCK"/>
        <uses-permission android:name= "android.permission.VIBRATE"/>
        <uses-permission android:name= "android.permission.INTERNET"/>
        <uses-permission android:name= "android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name= "android.permission.USE_CREDENTIALS"/>
        <uses-permission android:name= "android.permission.ACCESS_NETWORK_STATE"/>
        <application>
        <service android:name= "com.winwire.notificationhub.GCMIntentService" />
        <receiver android:name= "com.google.android.gcm.GCMBroadcastReceiver" android:permission= "com.google.android.c2dm.permission.SEND">
        <!-- Start receiver on boot -->
        <intent-filter>
        <action android:name= "android.intent.action.BOOT_COMPLETED"/>
        <category android:name= "android.intent.category.HOME"/>
        </intent-filter>
        <!-- Receive the actual message -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.RECEIVE" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        <!-- Receive the registration id -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        </receiver>
        </application>
        </manifest>




> [WACOM.NOTE] 上のコードでは、テキスト *com.winwire.notificationhub* のすべてのインスタンスをアプリケーションのパッケージ名 (モジュール ID) で置き換える必要があります。

7. Notification Hub モジュールで、[src] フォルダーを右クリックし、[新規] に移動して [フォルダー] を選択します。フォルダー名に「com.google.android.gcm」と入力します。

> [WACOM.NOTE] [新規] オプションに [フォルダー] が表示されない場合、[その他] を選択して [全般] を展開し、次に [フォルダー] を選択します。

8. ここで &quot;.java&quot; ファイル (gcm.zip) をダウンロードして、新しく作成したフォルダー (com.google.android.gcm) にコピーします。

9. モジュール ID として指定したフォルダーを探して展開します。フォルダーには、".java" ファイルの一覧が表示されます。これらのファイルから、プロジェクト名 + module.java のファイル (たとえば、プロジェクト名が notificationhub の場合、&quot;NotificationhubModule.java&quot; となります) を開き、先頭に以下のコード行を追加します。

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

10. 同じファイルでコンストラクターを見つけ、以下のコードで置き換えます。

        public NotificationhubModule() {
	        super();
	        _THIS = this;
        }

11. 同じファイルで、下部に以下のコード行を追加します。

        @Kroll.method
        public void registerC2dm(HashMap options) {
	        successCallback = (KrollFunction) options.get("success");
	        errorCallback = (KrollFunction) options.get("error");
	        messageCallback = (KrollFunction) options.get("callback");
	        String registrationId = getRegistrationId();
	        if (registrationId != null && registrationId.length() > 0) {
		        sendSuccess(registrationId);
	        } else {
		        GCMRegistrar.register(TiApplication.getInstance(), getSenderId());
	        }
        }
        @Kroll.method
        public void unregister() {
	        GCMRegistrar.unregister(TiApplication.getInstance());
        }
        @Kroll.method
        public String getRegistrationId() {
	        return GCMRegistrar.getRegistrationId(TiApplication.getInstance());
        }
        @Kroll.method
        public String getSenderId() {
	        return TiApplication.getInstance().getAppProperties()
	        .getString("com.winwire.notificationhub.sender_id", "");
        }

        public void sendSuccess(String registrationId) {
	        if (successCallback != null) {
		        HashMap data = new HashMap();
		        data.put("registrationId", registrationId);
		        successCallback.callAsync(getKrollObject(), data);
	        }
        }

        public void sendError(String error) {
	        if (errorCallback != null) {
		        HashMap data = new HashMap();
		        data.put("error", error);
		        errorCallback.callAsync(getKrollObject(), data);
	        }
        }

        public void sendMessage(HashMap messageData) {
	        if (messageCallback != null) {
		        HashMap data = new HashMap();
		        data.put("data", messageData);
		        messageCallback.call(getKrollObject(), data);
	        }
        }
        public static NotificationhubModule getInstance() {
	        return _THIS;
        }

12. ここで、module.zip をダウンロードし、モジュール ID の名前が付いたフォルダーにコピーします。

> [WACOM.NOTE] 上のファイルでは、テキスト *com.winwire.notificationhub* のすべてのインスタンスをアプリケーションのパッケージ名 (モジュール Id) で置き換える必要があります。&quot;NotificationhubModule&quot; もプロジェクト名 + モジュール (つまり &quot;NotificationhubModule&quot) で置き換えます。

### モジュールを作成/パッケージ化する

**[デプロイ]、[パッケージ - Android モジュール]** の順にクリックします。Studio を使用して BlackBerry モジュールを構築することはできません。BlackBerry NDK CLI ツールまたは BlackBerry NDK CLI を使用してください。 

![][3]


次に、すべてのプロジェクト、または特定のプロジェクトに対してモジュールをデプロイできます。ここで「[Titanium モジュールの使用]」で説明されるインストールの規則に従って行います。まとめると次のようになります。

- すべてのプロジェクトの場合: モジュールの .zip ファイルを Titanium SDK のインストール場所のルートにドロップします。 

- 特定のプロジェクトの場合: モジュールの .zip ファイルをプロジェクトのルートにドロップします。 


## <a name="configure"></a>プッシュ要求を送信するようにモバイル サービスを構成する


[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

5.	前の手順で GCM から取得した API キー値を入力して、[保存] をクリックします。

    ![][4]

これで、モバイル サービスが APNS と GCM の両方と連携するように構成されました。

## <a name="add-push"></a>アプリケーションにプッシュ通知を追加する

1.	tiapp.xml で、[tiapp.xml] タブ (このタブは [概要] タブの隣にあります) をクリックして `<android>` タグを見つけます。タグの下に次のコードを追加します。

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

>[WACOM.NOTE] 上のコードで、**ModuleId**、**ApplicationId** を置き換える必要があります。GCM モジュールの作成時に指定したモジュール ID とプロジェクト作成時に入力したアプリケーション ID です。また、**ModuleId** と **ApplicationId** は必ず同じにしてください。**ApplicationId.AppNameActivity** の変更も必要です。com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity のようにします。


2.	前に作成した GCM モジュールをコピーして、次の場所に格納します。	

    <table><tr>
    <td>OSX
    </td>
    <td>/Library/Application Support/Titanium or  ~/Library/Application Support/Titanium
    </td>
    </tr>
    <td>Windows 7
    </td>
    <td>C:\Users\username\AppData\Roaming (or C:\ProgramData\Titanium on Titanium Studio 1.0.1 and earlier)
    </td>
    </tr><td>Windows XP
    </td>
    <td>C:\Documents and Settings\username\Application Data (or C:\Documents and Settings\All Users\Application Data\Titanium on Titanium Studio 1.0.1 and earlier)
    </td>
    </tr><td>Linux
    </td>
    <td>~/.titanium
    </td>
    </tr>
    </tr>
    
    </table>

>[WACOM.NOTE]  Mac OS では、ライブラリは非表示フォルダーです。表示するには、次のコマンドを実行してから、Finder を再起動します。`**defaults write com.apple.finder AppleShowAllFiles TRUE**`

3.	Appcelerator Titanium Studio で、index.js ファイルを開き、次のコードを下部に追加します。このコードは、プッシュ通知用にデバイスを登録します。

            Alloy.Globals.tempRegId = '';
    	        if (OS_ANDROID) {
    	        var gcm = require('com.winwire.notificationhub');
            gcm.registerC2dm({
            success : function(e) {
            var regId = e.registrationId;
            Alloy.Globals.tempRegId = regId;
            },
            error : function(e) {
            alert("Error during registration : " + e.error);
            var message;
            if (e.error == "ACCOUNT_MISSING") {
            message = "No Google account found; you will need to add on in order to activate notifications";
            }
            Titanium.UI.createAlertDialog({
            title : 'Push Notification Setup',
            message : message,
            buttonNames : ['OK']
            }).show();
            },
            callback : function(e)// called when a push notification is received
            {
            var data = JSON.stringify(e.data);
            var intent = Ti.Android.createIntent({
            action : Ti.Android.ACTION_MAIN,
            className : 'com.winwire.notificationhub. WindowsAzureActivity',
            flags : Ti.Android.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED | Ti.Android.FLAG_ACTIVITY_SINGLE_TOP
            });
            intent.addCategory(Titanium.Android.CATEGORY_LAUNCHER);
            var NotificationClickAction = Ti.Android.createPendingIntent({
            activity : Ti.Android.currentActivity,
            intent : intent,
            flags : Ti.Android.FLAG_UPDATE_CURRENT,
            type : Ti.Android.PENDING_INTENT_FOR_ACTIVITY
            });
            var NotificationMembers = {
            contentTitle : 'Notification Hub Demo',
            contentText : e.data.message,
            defaults : Titanium.Android.NotificationManager.DEFAULT_SOUND,
            tickerText : 'Notification Hub Demo',
            icon : Ti.App.Android.R.drawable.appicon,
            when : new Date(),
            flags : Ti.Android.FLAG_AUTO_CANCEL,
            contentIntent : NotificationClickAction
            };
            Ti.Android.NotificationManager.notify(1, .Android.createNotification(NotificationMembers));
            var toast = Titanium.UI.createNotification({
            duration : 2000,
            message : e.data.message
            });
            toast.show();
            alert(e.data.message);
            }
            });
            } else  if (OS_IOS) {
            Titanium.Network.registerForPushNotifications({
            types : [Titanium.Network.NOTIFICATION_TYPE_BADGE, .Network.NOTIFICATION_TYPE_ALERT, .Network.NOTIFICATION_TYPE_SOUND],
            success : function(e) {
            deviceToken = e.deviceToken;
            Alloy.Globals.tempRegId = deviceToken;
            },
            error : function(e) {
            Ti.API.info("Error during registration: " + e.error);
            },
            callback : function(e) {
            var data = e.data.inAppMessage;
            if (data != null && data != '') {
            alert(data);
            }
            }
            });
            }
   

4.	ここで、TableData.js ファイルを開き、関数 **addOrUpdateDataFromAndroid** 内で次の行を見つけます。

        var request = {
    	    'text' : alertTextField.getValue(),
    	    'complete' : false
        };
  

    上のコードを (新しい項目を挿入するための) else 条件でのみ置き換えます。

5.	Android 用に、次のコードで置き換えます。

       var request = {
    	'text' : alertTextField.getValue(),
    	'complete' : false,
    	'handle' : Alloy.Globals.tempRegId
    }; 
    
    

6.	次に、関数 **updateOrAddData** 内で次の行を見つけます。

           var request = {
    	    'text' : alertTextField.getValue(),
    	    'complete' : false
        };
    
    上のコードを (新しい項目を挿入するための) else 条件でのみ置き換えます。

7.	iOS 用に、次のコードで置き換えます。

       var request = {
    	'text' : alertTextField.getValue(),
    	'complete' : false,
    	'deviceToken' : Alloy.Globals.tempRegId
    };
    
 
アプリケーションは、iOS と Android の両方のプラットフォームでプッシュ通知をサポートするように更新されました。


## <a name="update-scripts"></a>管理ポータルで登録されている挿入スクリプトを更新する

1.	管理ポータルで、[データ] タブをクリックし、TodoItem テーブルをクリックします。

    ![][5]

2.	[todoitem] で、[スクリプト] タブをクリックし、[挿入] をクリックします。

    ![][6]

    TodoItem テーブルで挿入が発生したときに呼び出される関数が表示されます。

3.	insert 関数を次のコードに書き換え、**[保存]** をクリックします。

**iOS の場合:** 

        function insert(item, user, request) { 
            request.execute();
            // Set timeout to delay the notification, to provide time for the  
            // app to be closed on the device to demonstrate toast notifications 
            setTimeout(function() { 
                push.apns.send(item.deviceToken, { 
                    alert: "Toast: " + item.text, 
                    payload: { 
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'" 
                    }
                 }); 
            }, 2500);
        }
  
   	> [WACOM.NOTE] このスクリプトでは、プッシュ通知を受け取るためにアプリケーションを閉じる時間を与えるために通知の送信を遅らせています。  

**Android の場合:** 

          function insert(item, user, request) {
            request.execute({ 
                success: function() {  
                    // Write to the response and then send the notification in the background 
                    request.respond();  
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) { 
                            console.log('Push notification sent: ', response);
                        }, error: function(error)   { 
                            console.log('Error sending push notification: ', error);      
                            }      
                   }); 
                }  
          });  
        }



これで、新しい挿入スクリプトが生成されます。このスクリプトは [Mobile Services の push オブジェクト]を使用して、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。


<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png

<!-- Anchors. -->
[証明書の署名要求を作成する]:  #certificates
[アプリケーションを登録し、プッシュ通知を有効にする]:  #register
[アプリケーションのプロビジョニング プロファイルを作成する]:  #profile
[Google Cloud Messaging を有効にする]:  #register-gcm
[Titanium 用の GCM モジュールを作成する]:  #gcm-module
[モバイル サービスを構成する]:  #configure
[アプリケーションにプッシュ通知を追加する]:  #add-push
[プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
[データを挿入して通知を受け取る]:  #test

<!-- URLs. -->
[Mobile Services の使用]: /ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
[Titanium モジュールの使用]:  http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
[Windows Azure の管理ポータル]:  https://manage.windowsazure.com/
[Mobile Services の push オブジェクト]:  http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
[Java 開発ツールのインストール]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools

<!--HONumber=35.1-->
