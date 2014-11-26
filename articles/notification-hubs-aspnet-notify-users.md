<properties linkid="notification-hubs-how-to-guides-howto-notify-users-aspnet" urlDisplayName="ユーザーへの通知" pageTitle="通知ハブによる ASP.NET サービスのユーザーへの通知" metaKeywords="" description="このチュートリアルでは、通知ハブを使用して ASP.NET サービスからの通知を受け取るように登録を行う方法を示します。" metaCanonical="" services="notification-hubs" documentationCenter="" title="通知ハブによるユーザーへの通知" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# 通知ハブによるユーザーへの通知 

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ja-jp/manage/services/notification-hubs/notify-users" title="モバイル サービス">モバイル サービス</a><a href="/ja-jp/manage/services/notification-hubs/notify-users-aspnet" title="ASP.NET" class="current">ASP.NET</a>
</div> 

このチュートリアルでは、Azure 通知ハブを使用して特定のデバイスの特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。ASP.NET Web API バックエンドを使用して、クライアントを認証し、通知を生成します。このチュートリアルは、1 つ前のチュートリアル「**通知ハブの使用**」で作成した通知ハブが基になっています。通知登録コードは、クライアントからバックエンド サービスに移動します。これにより、クライアントがサービスによって正しく認証された後のみ登録が完了するようになります。さらに、通知ハブの資格情報がクライアント アプリケーションに配布されることもありません。登録時に要求されたタグも、このサービスによって制御されます。

このチュートリアルでは、次の基本的な手順について説明します。

+ [認証付きの ASP.NET アプリケーションを作成する]
+ [通知に登録できるように ASP.NET アプリケーションを更新する]
+ [ログインして登録を要求できるようにアプリケーションを更新する]

## 前提条件

+ Visual Studio 2012。Visual Studio Express 2012 for Web と Visual Studio Express 2012 for Windows 8 を使用すると、それぞれ ASP.NET アプリケーションと Windows ストア アプリケーションを作成できます。
+ このチュートリアルは、「**通知ハブの使用**」で作成したアプリケーションと通知ハブが基になっています。このチュートリアルを始める前に、まず「**通知ハブの使用**」([Windows ストア C##][Get started Windows Store]/[iOS][Get started iOS]/[Android][Get started Android]) のチュートリアルを完了する必要があります。

<div class="dev-callout"><b>メモ</b>
	<p>このチュートリアルで作成する ASP.NET Web API プロジェクトは、ローカル コンピューターで実装されます。ASP.NET Web API プロジェクトを Azure に発行することもできます。詳細については、「<a href="/ja-jp/develop/net/tutorials/rest-service-using-web-api/" target="_blank">ASP.NET Web API と SQL データベースを使用してモバイル対応の REST サービスを作成する</a>」を参照してください。</p>
</div>

<h2><a name="create-application"></a><span class="short-header">ASP.NET アプリケーションの作成</span>認証を使用する ASP.NET アプリケーションを作成する</h2>

まず、ASP.NET Web API アプリケーションを作成します。このバックエンド サービスは、クライアントを認証し、認証されたユーザーの代わりにプッシュ通知に登録して、通知を送信します。

1. Visual Studio または Visual Studio Express 2012 for Web で、**[ファイル]** をクリックして [ファイル] メニューの **[新しいプロジェクト] をクリックし、**[テンプレート]**、[**Visual C#**] の順に展開します。次に、**[Web]** と **[ASP.NET MVC 4 Web アプリケーション]** をクリックし、「_NotificationService_」という名前を入力して **[OK]** をクリックします。

	![][0]

2. **[新しい ASP.NET MVC プロジェクト]** ダイアログ ボックスで **[空]**、**[OK]** の順にクリックします。

	これにより、ASP.NET MVC プロジェクトが作成されます。

3. ソリューション エクスプローラーで、プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックしてから「`AuthenticationTestHandler`」と入力して **[追加]** をクリックします。

	![][1] 

	これにより、**AuthenticationTestHandler** クラスのコード ファイルがプロジェクトに追加されます。

4. 新しい AuthenticationTestHandler.cs プロジェクト ファイルを開き、クラス定義を次のコードに置き換えます。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Security.Principal;
		using System.Text;
		using System.Web;
		
		namespace NotificationService
		{
		    public class AuthenticationTestHandler : DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(
		        HttpRequestMessage request, CancellationToken cancellationToken)
		        {
		            var authorizationHeader = request.Headers.GetValues("Authorization").First();
		
		            if (authorizationHeader != null && authorizationHeader
		                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
		            {
		                string authorizationUserAndPwdBase64 = 
		                    authorizationHeader.Substring("Basic ".Length);
		                string authorizationUserAndPwd = Encoding.Default
		                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
		                string user = authorizationUserAndPwd.Split(':')[0];
		                string password = authorizationUserAndPwd.Split(':')[1];
		
		                if (verifyUserAndPwd(user, password))
		                {
		                    // Attach the new principal object to the current HttpContext object
		                    HttpContext.Current.User = 
		                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
		                    System.Threading.Thread.CurrentPrincipal = 
		                        System.Web.HttpContext.Current.User;
		                }
		                else return Unauthorised();
		            } else return Unauthorised();
		
		            return base.SendAsync(request, cancellationToken);
		        }
		
		        private bool verifyUserAndPwd(string user, string password)
		        {
		            // This is not a real authentication scheme.
		            return user == password;
		        }
		
		        private Task<HttpResponseMessage> Unauthorised()
		        {
		            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
		            var tsc = new TaskCompletionSource<HttpResponseMessage>();
		            tsc.SetResult(response);
		            return tsc.Task;
		        }
		    }
		} 

	<div class="dev-callout"><b>セキュリティに関する注意</b>
		<p><strong>AuthenticationTestHandler</strong> クラスは、本当の認証を提供するわけではありません。基本認証を模倣して、原理を返すためだけに使用されます。通知ハブ登録を作成するには、ユーザー名が必要です。上の実装はセキュリティで保護されていません。実稼働のアプリケーションとサービスでは、セキュリティで保護された認証メカニズムを実装する必要があります。</p>
	</div>

5. **App_Start** フォルダーを展開して WebApiConfig.cs ファイルを開き、**Register** メソッドの末尾に次のコードを追加します。

		config.MessageHandlers.Add(new AuthenticationTestHandler());

	これには、ASP.NET アプリケーションへの要求に Authorization ヘッダーが含まれている必要があります。

これで、ユーザー名を提供する疑似的な認証スキームを備えた基本アプリケーションが作成されました。

<h2><a name="register-notification"></a><span class="short-header">通知の登録</span>通知に登録できるように ASP.NET アプリケーションを更新する</h2>

次の手順では、新しい **Registration** コントローラーを作成することで、通知ハブへの登録ロジックを ASP.NET アプリケーションに追加します。

1. [Azure の管理ポータル][Management Portal]にログインして、**[Service Bus]**、名前空間、**[通知ハブ]** の順にクリックし、通知ハブを選択して **[接続情報]** をクリックします。

	![][6]

2. 通知ハブの名前をメモし、**DefaultFullSharedAccessSignature** の接続文字列をコピーします。

	![][7]

	この接続文字列は、通知への登録の通知の送信の両方で、通知ハブ名と共に使用します。

3. **ソリューション エクスプローラー**でプロジェクト名を右クリックし、**[NuGet パッケージの管理]** を選択します。

4. 左側のウィンドウで、**[オンライン]** カテゴリを選択し、`WindowsAzure.ServiceBus` を探します。**Azure の Service Bus** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

  	![][2]

  	これにより、Microsoft.ServiceBus.dll アセンブリがプロジェクトに追加されます。

5. ソリューション エクスプローラーで、**Controllers** フォルダーを右クリックして **[追加]**、**[コントローラー...]** の順にクリックし、**[コントローラー名]** に「`RegisterController`」と入力した後、**[空の API コントローラー]** を選択して **[追加]** をクリックします。

	![][3]

	これにより、コントローラー クラスがプロジェクトに追加されます。このコントローラーは、呼び出されると、通知ハブへのデバイスの登録処理を行います。

6. 新しい RegisterController.cs プロジェクト ファイルを開き、次の **using** ステートメントを追加します。

		using Microsoft.ServiceBus.Notifications;
		using Newtonsoft.Json.Linq;
		using System.Threading.Tasks;
		using System.Web;

7. 次のコードを新しい RegisterController クラスに追加します。

		// Define the Notification Hubs client.
		private NotificationHubClient hubClient;

		// Create the client in the constructor.
        public RegisterController()
        {
            var cn = "<FULL_SAS_CONNECTION_STRING>";
            hubClient = NotificationHubClient
				.CreateClientFromConnectionString(cn, "<NOTIFICATION_HUB_NAME>");
        }

8. コンストラクターでコードを更新して _`<NOTIFICATION_HUB_NAME>`_ と _`<FULL_SAS_CONNECTION_STRING>`_ を通知ハブ用の値に置き換え、**[保存]** をクリックします。

	<div class="dev-callout"><b>メモ</b>
		<p><em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em> には必ず <strong>DefaultFullSharedAccessSignature</strong> を使用してください。この要求により、Web API が登録を作成および更新できるようになります。</p>
	</div>

9. 次の Post メソッド コードを RegisterController クラスに追加します。

        public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
        {
            // Get the registration info that we need from the request. 
            var platform = registrationCall["platform"].ToString();
            var installationId = registrationCall["instId"].ToString();
            var channelUri = registrationCall["channelUri"] != null ? 
                registrationCall["channelUri"].ToString() : null;
            var deviceToken = registrationCall["deviceToken"] != null ? 
                registrationCall["deviceToken"].ToString() : null;       
            var userName = HttpContext.Current.User.Identity.Name;

            // Get registrations for the current installation ID.
            var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

            bool updated = false;
            bool firstRegistration = true;
            RegistrationDescription registration = null;

            // Check for existing registrations.
            foreach (var registrationDescription in regsForInstId)
            {
                if (firstRegistration)
                {
 					// Update the tags.
                    registrationDescription.Tags = new HashSet<string>() { installationId, userName };

                    // We need to handle each platform separately.
                    switch (platform)
                    {
                        case "windows":
                            var winReg = registrationDescription as WindowsRegistrationDescription;
                            winReg.ChannelUri = new Uri(channelUri);
                            registration = await hubClient.UpdateRegistrationAsync(winReg);                            
                            break;
                        case "ios":
                            var iosReg = registrationDescription as AppleRegistrationDescription;
                            iosReg.DeviceToken = deviceToken;
                            registration = await hubClient.UpdateRegistrationAsync(iosReg);
                            break;
                    }
                    updated = true;
                    firstRegistration = false;
                }
                else
                {
                    // We shouldn't have any extra registrations; delete if we do.
                    await hubClient.DeleteRegistrationAsync(registrationDescription);
                }
            }

            // Create a new registration.
            if (!updated)
            {
                switch (platform)
                {
                    case "windows":
                        registration = await hubClient.CreateWindowsNativeRegistrationAsync(channelUri, 
                            new string[] { installationId, userName });
                        break;
                    case "ios":
                        registration = await hubClient.CreateAppleNativeRegistrationAsync(deviceToken, 
                            new string[] { installationId, userName });
                        break;
                }
            }

            // Send out a test notification.
            sendNotification(string.Format("Test notification for {0}", userName), userName);

            return registration;
        }

	このコードは、POST 要求により呼び出され、メッセージ本文からプラットフォームおよび deviceID 情報を取得します。このデータは、要求ヘッダーから取得されたインストール ID およびログインしているユーザーのユーザー ID と共に、登録の更新に使用されます。登録が存在しない場合、新しい登録が作成されます。この登録には、ユーザー ID とインストール ID がタグ付けされます。

10. 次の sendNotification メソッドを追加します。

        // Basic implementation that sends a not ification to Windows Store and iOS app clients.
        private async Task sendNotification(string notificationText, string tag)
        {
            try
            {
                // Create notifications for both Windows Store and iOS platforms.
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                    notificationText + "</text></binding></visual></toast>";
                var alert = "{\"aps\":{\"alert\":\"" + notificationText + 
                    "\"}, \"inAppMessage\":\"" + notificationText + "\"}";

                // Send a notification to the logged-in user on both platforms.
                await hubClient.SendWindowsNativeNotificationAsync(toast, tag);
                await hubClient.SendAppleNativeNotificationAsync(alert, tag);
            }
            catch(ArgumentException ex)
            {
                // This is expected when an APNS registration doesn't exist.
                Console.WriteLine(ex.Message);
            }
        }

	このメソッドは、登録が完了したらすぐに通知を送信するために呼び出されます。

次に、「**通知ハブの使用**」のチュートリアルを実行したときに作成したクライアント アプリケーションを更新します。

<h2><a name="update-app"></a><span class="short-header">アプリケーションの更新</span>ログインして登録を要求できるようにアプリケーションを更新する</h2>

「**通知ハブの使用**」のチュートリアルを実行したときに作成したアプリケーションは、通知ハブから直接登録を要求します。クライアント アプリケーションからこの登録を削除し、ASP.NET Web API アプリケーションにある新しい登録 API への呼び出しに置き換えます。

1. F5 キーを押して ASP.NET アプリケーションを起動し、既定のページの読み込みを試みます。

	ブラウザーが表示されたら、要求されたサイトのホスト名をメモします。このルート URL は、クライアント アプリケーションを更新するときに必要になります。

	<div class="dev-callout"><b>メモ</b>
		<p>ローカルの IIS Web サーバーまたは Visual Studio 開発サーバーを使用している場合、ポート番号も指定する必要があります。このアプリケーションでは既定のページを実装していないため、404 エラーが返される点に注意してください。</p>
	</div>

2. 使用しているクライアント プラットフォームに応じて、「**ASP.NET を使用した現在のユーザーのプッシュ通知への登録**」で説明されている次のいずれかのバージョンの手順に従います。

	+ [Windows ストア C## バージョン][Client topic Windows Store C# version]
	+ [iOS バージョン][Client topic iOS version]

3. 更新されたアプリケーションを実行し、ユーザー名とパスワードに同じ文字列を使用してサービスにログインした後、通知に割り当てられた登録 ID が表示されることを確認します。

	プッシュ通知も受信します。

	<div class="dev-callout"><b>メモ</b>
		<p>要求された通信の送信先プラットフォームに登録がない場合、バックエンドでエラーが発生します。その場合、このエラーは無視できます。テンプレートを使用してこの状況を回避する方法については、「<a href="/ja-jp/manage/services/notification-hubs/notify-users-xplat-aspnet" target="_blank">通知ハブによるユーザーへのクロスプラットフォーム通知の送信</a>」を参照してください。</p>
	</div>

4. (省略可能) クライアント アプリケーションを 2 つ目のデバイスにデプロイし、アプリケーションを実行してテキストを挿入します。

	各デバイスに通知が表示されます。

## <a name="next-steps"> </a>次のステップ
このチュートリアルが完了したら、次のチュートリアルを実行することをお勧めします。

+ **通知ハブを使用したニュース速報の送信 ([Windows ストア C#][Breaking news .NET] / [iOS][Breaking news iOS])** <br/>このプラットフォーム固有のチュートリアルでは、タグを使用して、ユーザーが興味を持つ種類の通知を購読できるようにする方法について説明します。

+ **[通知ハブによるユーザーへのクロスプラットフォーム通知の送信]**<br/>このチュートリアルでは、この「**通知ハブによるユーザーへの通知**」のチュートリアルを拡張し、プラットフォーム固有のテンプレートを使用して通知に登録します。これにより、サーバー側コードの 1 つのメソッドから通知を送信できるようになります。

通知ハブの詳細については、「[Azure 通知ハブ]」を参照してください。


[認証付きの ASP.NET アプリケーションを作成する]: #create-application
[通知に登録できるように ASP.NET アプリケーションを更新する]: #register-notification
[ログインして登録を要求できるようにアプリケーションを更新する]: #update-app
[通知を送信できるように ASP.NET アプリケーションを更新する]: #send-notifications


[0]: ./media/notification-hubs-aspnet-notify-users/notification-hub-create-mvc-app.png
[1]: ./media/notification-hubs-aspnet-notify-users/notification-hub-create-aspnet-class.png
[2]: ./media/notification-hubs-aspnet-notify-users/notification-hub-add-nuget-package.png
[3]: ./media/notification-hubs-aspnet-notify-users/notification-hub-add-register-controller2.png
[6]: ./media/notification-hubs-aspnet-notify-users/notification-hub-select-hub-connection.png
[7]: ./media/notification-hubs-aspnet-notify-users/notification-hub-connection-strings.png



[Windows ストア アプリの使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet
[iOS の使用]: /ja-jp/manage/services/notification-hubs/get-started-notification-hubs-ios
[Android の使用]: /ja-jp/manage/services/notification-hubs/get-started-notification-hubs-android
[認証を使用する Windows ストアの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
[認証を使用する iOS の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios/
[認証を使用する Android の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android/
[クライアント トピックの Windows ストア C## バージョン]: /ja-jp/manage/services/notification-hubs/register-users-aspnet-dotnet 
[クライアント トピックの iOS バージョン]: /ja-jp/manage/services/notification-hubs/howto-register-user-with-aspnet-ios 
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[管理ポータル]: https://manage.windowsazure.com/

[ASP.NET Web API と SQL データベースを使用してモバイル対応の REST サービスを作成する]: /ja-jp/develop/net/tutorials/rest-service-using-web-api/
[通知ハブによるユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-aspnet
[ニュース速報 .NET]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
[ニュース速報 iOS]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
[Azure 通知ハブ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj927170.aspx

