<properties pageTitle="プッシュ通知の使用 (Android) | モバイル デベロッパー センター" description="Azure Mobile Services を使用して Android アプリにプッシュ通知を送信する方法について説明します。" services="mobile-services" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="11/21/2014" ms.author="ricksal"/>

# モバイル サービスでのプッシュ通知の使用 (従来のプッシュ)

<div class="dev-center-tutorial-selector sublanding"> <a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a> <a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows Store JavaScript</a> <a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a> <a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a> <a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a> <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript backend" class="current">JavaScript バックエンド</a></div>

このトピックでは、Azure モバイル サービスを使用して Android アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Google Cloud Messaging (GCM) サービスを使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

>[AZURE.NOTE]このトピックは、まだ Notification Hubs 統合を使用するように<em>アップグレードされていない</em>、<em>既存の</em>モバイル サービスを対象とします。<em>新しい</em>モバイル サービスを作成すると、この統合機能は自動的に有効になります。新しいモバイル サービスについては、[プッシュ通知の使用](/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).に関するページを参照してください。
>
>Mobile Services は Azure Notification Hubs に統合され、テンプレート、複数のプラットフォーム、改良されたスケールなど、追加のプッシュ通知機能をサポートするようになりました。<em>可能な場合には、既存のモバイル サービスをアップグレードして Notification Hubs を使用することをお勧めします</em>。アップグレード後は、「[プッシュ通知の使用](/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

* [Google Cloud Messaging を有効にする](#register)
* [Mobile Services を構成する](#configure)
* [アプリケーションにプッシュ通知を追加する](#add-push)
* [プッシュ通知を送信するようにスクリプトを更新する](#update-scripts)
* [データを挿入して通知を受け取る](#test)

このチュートリアルには、次のものが必要です。

+ [Mobile Services Android SDK]
+ アクティブな Google アカウント

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、[Mobile Services の使用]に関するチュートリアルを完了している必要があります。 

##<a id="register"></a>Google Cloud Messaging を有効にする


[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

次に、この API キー値を使用して、モバイル サービスが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。

##<a id="configure"></a>プッシュ要求を送信するように Mobile Services を構成する

1. [Azure 管理ポータル] にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![][18]

2. **[プッシュ]** タブをクリックし、前の手順で GCM から取得した **API キー**値を入力して、**[保存]** をクリックします。

   	![][19]

これで、GCM と連係してプッシュ通知を送信するようにモバイル サービスが構成されました。

##<a id="add-push"></a>アプリケーションにプッシュ通知を追加する


###プロジェクトへの Google Play Services の追加

[AZURE.INCLUDE [Play サービスの追加](../includes/mobile-services-add-Google-play-services.md)]

###コードの追加

1. プロジェクト ファイル **AndroidManifest.xml** を開きます。Google Cloud Messaging には、マニフェストの **minSdkVersion** プロパティが準拠する必要がある、開発およびテストに関する最小 API レベル要件があります。古いデバイスを使用しているためにこの値を 16 より小さい値に設定する必要がある場合は、「[Set Up Google Play Services SDK (Google Play Services SDK のセットアップ)]」を参考に、どれだけ小さな値を設定できるか判断してください。プロパティを適切に設定します。

2.  `uses-sdk` 要素で、**targetSdkVersion** に、手順 1. でインストールした SDK プラットフォームのバージョンが設定されていることを確認します。使用可能な最も新しいバージョンに設定することをお勧めします。 

3. 前の手順の選択内容に応じて、**uses-sdk** タグは次のようになります。

	    <uses-sdk
	        android:minSdkVersion="17"
	        android:targetSdkVersion="19" />
	
4. 続く 2 つの手順で、コード内の _`**my_app_package**`_ を、プロジェクトのアプリケーション パッケージの名前 ( `manifest` タグの  `package` 属性の値) に置き換えます。 

5. 次の新しいアクセス許可を、既存の  `uses-permission` 要素の後に追加します。
     <permission android:name="**my_app_package**.permission.C2D_MESSAGE"          android:protectionLevel="signature" />     <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />      <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />     <uses-permission android:name="android.permission.GET_ACCOUNTS" />     <uses-permission android:name="android.permission.WAKE_LOCK" />

6.  `application` 開始タグの後に次のコードを追加します。 
     <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"         android:permission="com.google.android.c2dm.permission.SEND">         <intent-filter>             <action android:name="com.google.android.c2dm.intent.RECEIVE" />             <category android:name="**my_app_package**" />         </intent-filter>     </receiver>



7. ファイル ToDoItem.java を開き、次のコードを **TodoItem** クラスに追加します。

			@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
		
	このコードは、登録 ID を保持する新しいプロパティを作成します。
 > [AZURE.NOTE] モバイル サービスで動的スキーマが有効になっていると、このプロパティを含む新しい項目が挿入されたときに、新しい **handle** 列が自動的に **TodoItem** テーブルに追加されます。

8. [Mobile Services Android SDK] をダウンロードし、解凍します。**notifications** フォルダーを開き、**notifications-1.0.1.jar** ファイルを Eclipse プロジェクトの  *libs* フォルダーにコピーした後、 *libs* フォルダーを最新の情報に更新します。
 > [AZURE.NOTE] ファイル名の末尾にある数値は、今後の SDK リリースで変更される可能性があります。

9.  ファイル  *ToDoItemActivity.java* を開き、次の import ステートメントを追加します。

		import com.microsoft.windowsazure.notifications.NotificationsManager;

10. 次のプライベート変数をクラスに追加します。ここで、_`<PROJECT_NUMBER>`_ は、最初の手順で Google によってアプリケーションに割り当てられたプロジェクト番号です。

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. **onCreate** メソッドで、MobileServiceClient をインスタンス化する前に、デバイスに対応する通知ハンドラーを登録する次のコードを追加します。

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);



12. 項目がテーブルに追加される前に、次のコード行を **addItem** メソッドに追加します。

		item.setHandle(MyHandler.getHandle());

	このコードは、項目の  `handle` プロパティにデバイスの登録 ID を設定します。

13. Package Explorer で ( `src` ノードの下にある) パッケージを右クリックし、**[New]**、**[Class]** の順にクリックします。

14. **[Name]** に「 `MyHandler`」と入力し、**[Superclass]** に「 `com.microsoft.windowsazure.notifications.NotificationsHandler`」と入力します。**[Finish]** をクリックします。

	![][6]

	これで、新しい MyHandler クラスが作成されます。

15. 次の import ステートメントを追加します。

		import android.content.Context;
		
		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		

16. 次のコードを追加します。

		@com.google.gson.annotations.SerializedName("handle")
		private static String mHandle;
	
		public static String getHandle() {
			return mHandle;
		}
	
		public static final void setHandle(String handle) {
			mHandle = handle;
		}
	

17. 既存の **onRegistered** メソッドのオーバーライドを次のコードに置き換えます。

		@Override
		public void onRegistered(Context context, String gcmRegistrationId) {
			super.onRegistered(context, gcmRegistrationId);
			
			setHandle(gcmRegistrationId);
		}
		

これで、アプリケーションがプッシュ通知をサポートするように更新されました。


##<a id="update-scripts"></a>管理ポータルで登録されている挿入スクリプトを更新する

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。 

   	![][21]

2. **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
  	![][22]

   	**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

		function insert(item, user, request) {
			request.execute({
				success: function() {
					// Write to the response and then send the notification in the background
					request.respond();
					push.gcm.send(item.handle, item.text, {
						success: function(response) {
							console.log('Push notification sent: ', response);
						}, error: function(error) {
							console.log('Error sending push notification: ', error);
						}
					});
				}
			});
		}

   	これで、新しい insert スクリプトが登録されます。このスクリプトは [gcm オブジェクト]を使用して、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。 

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

> [AZURE.NOTE] このアプリケーションをエミュレーターで実行する場合は、Google API をサポートしている Android Virtual Device (AVD) を使用してください。

1. Eclipse を再開し、Package Explorer でプロジェクトを右クリックします。**[Properties]**、**[Android]** の順にクリックし、**[Google APIs]** をオンにしてから **[OK]** をクリックします。

	![][23]

  	これで、プロジェクトの対象が Google API になります。

2. **[Window]** で **[Android Virtual Device Manager]** を選択し、デバイスを選択してから **[Edit]** をクリックします。

	![][24]

3. **[Target]** で **[Google APIs]** を選択し、[OK] をクリックします。

   	![][25]

	これで、AVD が Google API を使用するようになります。

4. **[Run]** メニューの **[Run]** をクリックして、アプリケーションを開始します。

5. アプリケーションで、意味のあるテキスト (たとえば、「新しいモバイル サービス タスク」) を入力し、**[Add]** ボタンをクリックします。

  	![][26]

6. 画面の下部に、一時的に空の通知ボックスが表示されます。 

  	![][28]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.

  ![][27]-->

これで、このチュートリアルは終了です。

## <a name="next-steps"></a>次のステップ

この単純な例では、ユーザーは挿入したばかりのデータのプッシュ通知を受け取ります。GCM によって使用されるデバイス トークンは、要求のクライアントによってモバイル サービスに提供されます。次のチュートリアル、「[Push notifications to app users (アプリケーション ユーザーへのプッシュ通知)]」では、デバイス トークンを格納するための個別の Devices テーブルを作成し、挿入が発生したときに、すべての格納されているチャネルにプッシュ通知を送信します。 


<!-- Images. -->

[1]: ./media/mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[5]: ./media/mobile-services-android-get-started-push/mobile-services-android-sdk-manager.png
[6]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
[7]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-new-project.png
[8]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-sms-verify.png
[9]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-project-created.png
[10]: ./media/mobile-services-android-get-started-push/mobile-google-choose-play-api.png
[18]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
[24]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
[25]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
[26]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
[27]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Google Cloud コンソール]: https://cloud.google.com/console
[Google API]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Android プロビジョニング ポータル]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Google Play Services SDK のセットアップ]: http://go.microsoft.com/fwlink/?LinkId=389801
[ライブラリ プロジェクトの参照]: http://go.microsoft.com/fwlink/?LinkId=389800
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-android
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-android
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-android
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-android

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services Android の概念]: /ja-jp/develop/mobile/how-to-guides/work-with-android-client-library/
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645



<!--HONumber=42-->
