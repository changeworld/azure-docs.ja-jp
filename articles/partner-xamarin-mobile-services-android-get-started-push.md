<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-android" urlDisplayName="プッシュ通知の使用" pageTitle="プッシュ通知の使用 - モバイル サービス" metaKeywords="" description="Azure モバイル サービスにより Xamarin.Android アプリケーションでプッシュ通知を使用する方法について説明します。" metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="モバイル サービスでのプッシュ通知の使用" documentationCenter="Mobile" authors="" />
# モバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

このトピックでは、Azure モバイル サービスを使用して Xamarin Android アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Google Cloud Messaging (GCM) サービスを使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションをプッシュ通知に登録する]
2. [モバイル サービスを構成する]
2. [アプリケーションにプッシュ通知を追加する]
3. [プッシュ通知を送信するようにスクリプトを更新する]
4. [データを挿入して通知を受け取る]

このチュートリアルには、次のものが必要です。

+ アクティブな Google アカウント

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」を完了している必要があります。

<h2><a name="register"></a><span class="short-header">アプリケーションの登録</span>アプリケーションをプッシュ通知に登録する</h2>

<div class="dev-callout"><b>メモ</b>
<p>このトピックの手順を完了するには、電子メール アドレスの確認が完了している Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。</p>
</div> 

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> Web サイトに移動し、Google アカウント資格情報でサインインして、**[Create project...]** をクリックします。

	![][1]   

	> [WACOM.NOTE]
	> 既にプロジェクトがある場合は、ログイン後に **[Dashboard]** ページが表示されます。ダッシュボードで新しいプロジェクトを作成するには、**[API Project]<**, click **Create...** under **Other projects**, then enter a project name and click **Create project**.

2. 左の列にある[概要]ボタンをクリックし、ダッシュボードセクションにあるプロジェクト番号をメモしてください。 

後ほどのチュートリアルで、クライアントにある** PROJECT_ID**変数の値を設定します。

3. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> ページで **[Services]** をクリックし、トグルをクリックして **[Google Cloud Messaging for Android]** をオンにし、サービスの条件に同意します。

4. **[API Access]** をクリックし、**[Create new Server key...]** をクリックします。

	![][2]

5. **[Configure Server Key for API Project]** で、**[Create]** をクリックします。

	![][3]

6. **[API key]** の値をメモしておきます。

	![][4] 

次に、この API キー値を使用して、モバイル サービスが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。

<a name="configure"></a><h2><span class="short-header">サービスの構成</span>プッシュ要求を送信するようにモバイル サービスを構成する</h2>

1. Azure の管理ポータル[にログオンし、][モバイル サービス]** をクリックして、アプリケーションをクリックします。

	![][18]

2. **[プッシュ]** タブをクリックし、前の手順で GCM から取得した **API キー**値を入力して、**[保存]** をクリックします。

	![][19]

	これで、GCM と連係してプッシュ通知を送信するようにモバイル サービスが構成されました。

<a name="add-push"></a><h2><span class="short-header">プッシュ通知の追加</span>アプリケーションにプッシュ通知を追加する</h2>

1. 最初に、**PushSharp** をプロジェクトの参照として追加します。そのためには、最新バージョンの PushSharp をコンパイルし、コンパイルされた DLL を Xamarin.Android プロジェクトの参照として追加する必要があります。

2. [PushSharp Github ページ]にアクセスし、最新リリースをダウンロードします。ファイル一式を展開したら、次のサンプル プロジェクト フォルダーに移動します。

	**/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

	次のプロジェクト ファイルを開きます。
	
	**  PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3. MonoForAndroid PushSharp クライアント サンプルを**リリース** モードでビルドします。

4. **_external** フォルダーを Xamarin.Android プロジェクト フォルダー内に作成します。

5. MonoForAndroid PushSharp クライアント サンプルから、Xamarin.Android プロジェクト フォルダー内の新しく作成した **_external** フォルダーに次のファイルをコピーします。

	 **\bin\Release\PushSharp.Client.MonoForAndroid.dll**

6. Xamarin.Android プロジェクトを Xamarin Studio (または Visual Studio) で開きます。

7. プロジェクトの **References** フォルダーを右クリックし、**[Edit References]** を選択します。

8. **[.Net Assembly]** タブに移動します。プロジェクトの **_external** フォルダーを参照し、前の手順でビルドした **PushSharp.Client.MonoForAndroid.dll** を選択し、**[Add]** をクリックします。[OK] をクリックしてダイアログ ボックスを閉じます。

9. **Constants.cs** を開き、次の行を追加します。このとき、**PROJECT_ID** を、メモに記録しておいた Google Project_ID に置き換えます。

		public const string SenderID = "PROJECT_ID"; // Google API Project Number

10. MonoForAndroid PushSharp クライアント サンプルから **PushService.cs** ファイルを Xamarin.Android プロジェクト フォルダーにコピーして、これをプロジェクトに追加します。

11. **PushService.cs** 内で使用されている名前空間を、プロジェクトの名前空間 (たとえば、XamarinTodoQuickStart など)。

12. 前の手順で作成した定数 **SenderID** を参照するために、**PushService.cs** 内の **SENDER_IDS** 配列を変更します。

		public static string[] SENDER_IDS = new string[] { Constants.SenderID };
		
13. デバイス登録 ID を追跡するために、**PushService.cs** 内の **PushHandlerService** に新しい静的プロパティを追加します。

		public static string RegistrationID { get; private set; }
		
14. 受け取った登録 ID をローカル静的変数に格納するために、**PushService.cs** 内の **OnRegistered** メソッドを更新します。

		protected override void OnRegistered(Context context, string registrationId)
		{
			Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;
		}

15. 受け取ったプッシュ メッセージを通知の一部として表示するために、**PushService.cs** 内の **OnMessage** メソッドを更新します (既存の **createNotification** 呼び出しを置き換えます)。

        string message = intent.Extras.GetString("message");
        createNotification("New todo item!", "Todo item: " + message);
       
16. **OnMessage** メソッドは、最後に受け取ったプッシュ メッセージを格納するための次のコードを既定で持ちます。

        //Store the message
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();

17. **DefaultActivity** の代わりに **TodoActivity** を参照するために、**PushService.cs** 内の **createNotification** メソッドを更新します。

18. **TodoActivity.cs** を開き、次の using ステートメントを追加します。

        using PushSharp.Client;

19. **TodoActivity.cs** 内で、作成された **MobileServiceClient** のすぐ上に次の行を追加します。
	
        // Check to ensure everything's setup right
        PushClient.CheckDevice(this);
        PushClient.CheckManifest(this);

        // Register for push notifications
        System.Diagnostics.Debug.WriteLine("Registering...");
        PushClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);

20. TodoItem を追加したユーザーの登録済みデバイス ID を追跡するために、**TodoItem.cs** を開き、次の新しいフィールドを追加します。

        [DataMember(Name = "channel")]
        public string RegistrationId { get; set; }

21. In **TodoActivity.cs** update the **AddItem** method to set the **RegistrationID** of the newly added **TodoItem** to the device's registration ID receieved during registration:

		// Create a new item
		var item = new TodoItem() {
			Text = textNewTodo.Text,
			Complete = false,
            RegistrationId = PushHandlerService.RegistrationID
		};

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

<h2><a name="update-scripts"></a><span class="short-header">挿入スクリプトの更新</span>管理ポータルで登録されている挿入スクリプトを更新する</h2>

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

	![][21]

2. **[TodoItem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
  	![][22]

	**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

		function insert(item, user, request) {
			request.execute({
				success: function() {
					// Write to the response and then send the notification in the background
					request.respond();
					push.gcm.send(item.channel, item.text, {
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

<h2><a name="test"></a><span class="short-header">アプリケーションのテスト</span>アプリケーションでプッシュ通知をテストする</h2>

1. アプリケーションを実行し、新しい Todo 項目を追加します。新しい Todo 項目の追加に関するプッシュ通知を受け取ることを確認します。

2. Azure 管理ポータルのモバイル アプリケーションの **[ログ]** タブで、前の手順で **TodoItem** テーブルの **Insert** メソッドに追加したログ メッセージが表示されていることを確認します。

3. Azure 管理ポータルの **TodoItem** テーブルで、追加された新しい **channel** 列に一意のデバイス登録識別子が含まれていることを確認します。

これで、このチュートリアルは終了です。

##完成したサンプルの入手
[完成したサンプル プロジェクト]をダウンロードします。**ApplicationURL**、**ApplicationKey**、および **SenderID** の各変数を独自の Azure 設定で更新してください。

## <a name="next-steps"> </a>次のステップ

この単純な例では、ユーザーは挿入したばかりのデータのプッシュ通知を受け取ります。次のチュートリアル、「[Push notifications to app users (アプリケーション ユーザーへのプッシュ通知)]」では、デバイス トークンを格納するための個別の Devices テーブルを作成し、挿入が発生したときに、すべての格納されているチャネルにプッシュ通知を送信します。

<!-- Anchors. -->
[アプリケーションをプッシュ通知に登録する]: #register
[モバイル サービスを構成する]: #configure
[プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
[アプリケーションにプッシュ通知を追加する]: #add-push
[データを挿入して通知を受け取る]: #test
[次のステップ]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[18]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png







<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Google API]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Android プロビジョニング ポータル]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[モバイル サービス Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-android
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Push notifications to app users (アプリケーション ユーザーへのプッシュ通知)]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-android
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービスに関連する Xamarin の概念]: /ja-jp/develop/mobile/how-to-guides/work-with-xamarin-client-library
[gcm オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=282645
[完成したサンプル プロジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[PushSharp Github ページ]: https://github.com/Redth/PushSharp


