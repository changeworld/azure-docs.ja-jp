<properties
	pageTitle="Azure Notification Hubs の使用"
	description="Azure Notification Hubs を使用してプッシュ通知を行う方法について説明します。"
	services="notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.devlang="java"
	ms.topic="hero-article" 
	ms.tgt_pltfrm="mobile-baidu"
	ms.workload="mobile"
	ms.date="06/16/2015"
	ms.author="wesmc"/>

# 通知ハブの使用

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概要

Baidu クラウド プッシュは、プッシュ通知をモバイル デバイスに送信するために使用することのできる中国のクラウド サービスです。異なるアプリケーション ストアやプッシュサービスの存在に加え、GCM (Google Cloud Messaging) に接続されていることが少ない Android デバイスの可用性にも差があるために Android へのプッシュ通知の送信方法が複雑な中国では、このサービスは特に便利です。

##前提条件

このチュートリアルには、次のものが必要です。

+ Android SDK (Eclipse を使用していることが前提です)。<a href="http://go.microsoft.com/fwlink/?LinkId=389797">ここ</a>からダウンロードできます。
+ [Mobile Services Android SDK]
+ [Baidu Push Android SDK]

>[AZURE.NOTE]このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F)を参照してください。


##Baidu アカウントを作成する

Baidu を使用するには、アカウントを作成する必要があります。既にアカウントを持っている場合、Baidu アカウントで [Baidu ポータル] にログインし、次の手順は省略します。持っていない場合、新しい Baidu アカウントの作成方法について次の手順を参照してください。

1. [Baidu ポータル]で [登录 (ログイン)] のリンクをクリックします。[立即注册] をクリックして新しいアカウントの登録プロセスを開始します。

   	![][1]

2. 必要な情報 - 電話番号/電子メール アドレス、パスワード、および認証コードを入力し、[サインアップ] をクリックします。

   	![][2]

3. 入力した電子メール アドレスに、Baidu アカウントをアクティブ化するリンクが貼られた電子メールが届きます。

   	![][3]

4. 電子メール アカウントにログインし、Baidu のアクティブ化メールを開き、リンクをクリックして Baidu アカウントをアクティブ化します。

   	![][4]

アクティブな Baidu アカウントを取得した後、そのアカウントで [Baidu ポータル]にログインします。

##Baidu 開発者として登録する

1. [Baidu ポータル]にログインしたら、**[更多 (詳細)>>]** をクリックします。

  	![][5]

2. **[站长与开发者服务 (Web 管理者および開発者向けサービス)]** セクションを下にスクロールし、**[百度开放云平台 (Baidu オープン クラウド プラットフォーム)]** をクリックします。

  	![][6]

3. 次のページで、右上にある **[开发者服务 (開発者向けサービス)]** をクリックします。

  	![][7]

4. 次のページで、右上のメニューから **[注册开发者 (登録されている開発者)]** をクリックします。

  	![][8]

5. 検証のテキスト メッセージを受信するために名前、説明、および携帯電話の番号を入力し、**[送验证码 (確認コードを送信する)]** をクリックします。国際電話番号では、国コードを含める必要があります。たとえば米国の電話番号だと、**(1)1234567890** になります。

  	![][9]

6. その後、次の例に示すような、認証番号を含むテキスト メッセージが届きます。

  	![][10]

7. メッセージからの確認番号を **[验证码 (確認コード)]** に入力します。

8. 最後に、Baidu 契約書に同意して **[提交 (送信)]** をクリックし、開発者の登録を完了します。登録が正常に終了すると、次のページが表示されます。

  	![][11]

##Baidu クラウド プッシュ プロジェクトを作成する

Baidu クラウド プッシュ プロジェクトを作成すると、アプリケーション ID、API キー、およびシークレット キーが届きます。

1. [Baidu ポータル]にログインしたら、**[更多 (詳細)>>]** をクリックします。

  	![][5]

2. **[站长与开发者服务 (Web 管理者および開発者向けサービス)]** セクションを下にスクロールし、**[百度开放云平台 (Baidu オープン クラウド プラットフォーム)]** をクリックします。

  	![][6]

3. 次のページで、右上にある **[开发者服务 (開発者向けサービス)]** をクリックします。

  	![][7]

4. 次のページで、**[云服务 (クラウド サービス)]** セクションから **[云推送 (クラウド プッシュ)]** をクリックします。

  	![][12]

5. 登録済みの開発者になると、上部メニューに **[管理控制台 (管理コンソール)]** が表示されます。**[开发者服务管理 (開発者サービスの管理)]** をクリックします。

  	![][13]

6. 次のページで **[创建工程 (プロジェクトの作成)]** をクリックします。

  	![][14]

7. アプリケーション名を入力し、**[创建 (作成)]** をクリックします。

  	![][15]

8. 正常に作成されると、**AppID**、**API キー**、**シークレット キー**とともにページが表示されます。**API キー**と**シークレット キー**は後で使用するので書き留めておきます。

  	![][16]

9. 左側のウィンドウで **[云推送 (クラウド プッシュ)]** をクリックしてプッシュ通知用のプロジェクトを構成します。

  	![][31]

10. 次のページで、**[推送设置 (設定をプッシュ)]** ボタンをクリックします。

	![][32]

11. 構成ページで、Android プロジェクトで使用するパッケージ名を **[应用包名 (アプリケーション パッケージ)]** フィールドに入力し、**[保存设置 (保存)]** をクリックします。

	![][33]

[**保存成功！ (正常に保存されました)**] のメッセージが表示されます。

##通知ハブを構成する

1. [Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

3. **[通知ハブ]** の名前を指定し、この通知ハブが作成される **[領域]** および **[名前空間]** を選択して、**[新しい通知ハブの作成]** をクリックします。

  	![][17]

4. 通知ハブを作成した名前空間をクリックし、上部にある **[Notification Hubs]** をクリックします。

  	![][18]

5. 作成した通知ハブを選択し、上部のメニューから **[構成]** をクリックします。

  	![][19]

6. **[Baidu 通知の設定]** セクションまでスクロールし、Baidu クラウド プッシュ プロジェクトのために Baidu コンソールから取得した **API キー**と**シークレット キー**を入力します。これらの値を入力したら **[保存]** をクリックします。

  	![][20]

7. 通知ハブの上部にある **[ダッシュボード]** タブをクリックし、**[接続文字列の表示]** をクリックします。

  	![][21]

8. [接続情報へのアクセス] ウィンドウから **DefaultListenSharedAccessSignature** と **DefaultFullSharedAccessSignature** を書き留めます。

    ![][22]

##通知ハブにアプリケーションを接続する

1. Eclipse ADT で、新しい Android プロジェクトを作成します ([File] -> [New] -> [Android Application])。

    ![][23]

2. **[アプリケーション名]** に名前を入力し、**[最小必須 SDK]** のバージョンが **API 16: Android 4.1** に設定されていることを確認します。

    ![][24]

3. **[次へ]** をクリックし、**[アクティビティの作成]** ウィンドウが表示されるまでウィザードに従います。**[空のアクティビティ]** が選択されていることを確認し、最後に **[完了]** を選択して新しい Android アプリケーションを作成します。

    ![][25]

4. **[プロジェクト ビルド ターゲット]** が正しく設定されていることを確認します。

    ![][26]

5. [Mobile Services Android SDK] をダウンロードし、解凍します。**notificationhubs** フォルダーを開き、**notification-hubs-x.y.jar** ファイルを Eclipse プロジェクトの *libs* フォルダーにコピーした後、*libs* フォルダーを最新の情報に更新します。

6. [Baidu Push Android SDK] をダウンロードし、解凍します。**libs** フォルダーを開き、Android アプリケーションの **libs** フォルダーに *pushservice-x.y.z* jar ファイル と *armeabi* および *mips* フォルダーをコピーします。

7. Android プロジェクトの **AndroidManifest.xml** を開き、Baidu SDK に必要なアクセス許可を追加します。

	    <uses-permission android:name="android.permission.INTERNET" />
	    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
	    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
	    <uses-permission android:name="android.permission.VIBRATE" />
	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
	    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
	    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. *android:name* プロパティを **AndroidManifest.xml** の *application* 要素に追加し、*yourprojectname* (例: **com.example.BaiduTest**) を置き換えます。このプロジェクト名が、Baidu コンソールで構成したプロジェクト名と一致していることを確認します。

		<application android:name="yourprojectname.DemoApplication"

9. application 要素内で、.MainActivity アクティビティ要素の後に次の構成を追加し、*yourprojectname* (例: **com.example.BaiduTest**) を置き換えます。

		<receiver android:name="yourprojectname.MyPushMessageReceiver">
		    <intent-filter>
		        <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
		        <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
		        <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
		    </intent-filter>
		</receiver>

		<receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
		    android:process=":bdservice_v1">
		    <intent-filter>
		        <action android:name="android.intent.action.BOOT_COMPLETED" />
		        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
				<action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
		    </intent-filter>
		</receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. プロジェクトに **ConfigurationSettings.java** という名前の新しいクラスを追加します。

    ![][28]

    ![][29]

10. このクラスに次のコードを追加します。

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}

	*API_KEY* には前もって Baidu クラウド プロジェクトから取得した値、*NotificationHubName* には Azure ポータルの通知ハブ名、*NotificationHubConnectionString* には Azure ポータルの DefaultListenSharedAccessSignature をそれぞれ設定します。

11. **DemoApplication.java** という名前の新しいクラスを追加し、それに次のコードを追加します。

		import com.baidu.frontia.FrontiaApplication;

		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. **MyPushMessageReceiver.java** という名前の別の新しいクラスを追加し、それに次のコードを追加します。これは、Baidu プッシュ サーバーから受信したプッシュ通知を処理するクラスです。

		import java.util.List;
		import android.content.Context;
		import android.os.AsyncTask;
		import android.util.Log;
		import com.baidu.frontia.api.FrontiaPushMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub;

		public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
		    /** TAG to Log */
			public static NotificationHub hub = null;
			public static String mChannelId, mUserId;
		    public static final String TAG = MyPushMessageReceiver.class
		            .getSimpleName();

			@Override
		    public void onBind(Context context, int errorCode, String appid,
		            String userId, String channelId, String requestId) {
		        String responseString = "onBind errorCode=" + errorCode + " appid="
		                + appid + " userId=" + userId + " channelId=" + channelId
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		        mChannelId = channelId;
		        mUserId = userId;

		        try {
		       	 if (hub == null) {
		                hub = new NotificationHub(
		                		ConfigurationSettings.NotificationHubName,
		                		ConfigurationSettings.NotificationHubConnectionString,
		                		context);
		                Log.i(TAG, "Notification hub initialized");
		            }
		        } catch (Exception e) {
		           Log.e(TAG, e.getMessage());
		        }

		        registerWithNotificationHubs();
			}

		    private void registerWithNotificationHubs() {
		       new AsyncTask<Void, Void, Void>() {
		          @Override
		          protected Void doInBackground(Void... params) {
		             try {
		            	 hub.registerBaidu(mUserId, mChannelId);
		            	 Log.i(TAG, "Registered with Notification Hub - '"
		     	    			+ ConfigurationSettings.NotificationHubName + "'"
		     	    			+ " with UserId - '"
		     	    			+ mUserId + "' and Channel Id - '"
		     	    			+ mChannelId + "'");
		             } catch (Exception e) {
		            	 Log.e(TAG, e.getMessage());
		             }
		             return null;
		         }
		       }.execute(null, null, null);
		    }

		    @Override
		    public void onSetTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onSetTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }

		    @Override
		    public void onDelTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onDelTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }

		    @Override
		    public void onListTags(Context context, int errorCode, List<String> tags,
		            String requestId) {
		        String responseString = "onListTags errorCode=" + errorCode + " tags="
		                + tags;
		        Log.d(TAG, responseString);
		    }

		    @Override
		    public void onUnbind(Context context, int errorCode, String requestId) {
		        String responseString = "onUnbind errorCode=" + errorCode
		                + " requestId = " + requestId;
		        Log.d(TAG, responseString);
		    }

		    @Override
		    public void onNotificationClicked(Context context, String title,
		            String description, String customContentString) {
		        String notifyString = "title="" + title + "" description=""
		                + description + "" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }

		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message="" + message + "" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. **MainActivity.java** を開き、**onCreate** メソッドに次のコードを追加します。

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

さらに、import ステートメント (import com.baidu.android.pushservice.PushConstants; import com.baidu.android.pushservice.PushManager;) を先頭に追加します。

##アプリケーションに通知を送信する

Notification Hubs を使用すると、<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を使用するどのバックエンドからでも通知が送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して表示します。

1. Visual C# の新しいコンソール アプリケーションを作成します。

	![][30]

2. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure Service Bus SDK への参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力し、Enter キーを押します。

        Install-Package WindowsAzure.ServiceBus

3. Program.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

4. `Program` クラスで、次のメソッドを追加し、 *DefaultFullSharedAccessSignatureSASConnectionString* と *NotificationHubName* を実際の値に置き換えます。

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{"title":"((Notification title))","description":"Hello from Azure"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. 次に、Main メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

##アプリケーションのテスト

実際の電話でこのアプリケーションをテストする場合は、USB ケーブルでコンピューターに電話を接続するだけです。

エミュレーターを使用してこのアプリケーションをテストするには。

1. 上部の Eclipse ツール バーで、[Run] をクリックし、アプリケーションを選択します。

2. これによって、アプリケーションが接続した電話に読み込まれるか、またはエミュレーターが開始してアプリケーションが読み込まれ、実行されます。

3. アプリケーションが Baidu プッシュ通知サービスから 'userId' と 'channelId' を取得し、通知ハブに登録します。

4.	.Net コンソール アプリケーションの使用時にテスト通知を送信するには、Visual Studio で F5 キーを押してアプリケーションを実行すると、通知が送信され、デバイスまたはエミュレーターの上部の通知領域に表示されます。


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure 管理ポータル]: https://manage.windowsazure.com/
[Baidu ポータル]: http://www.baidu.com/
 

<!---HONumber=July15_HO4-->