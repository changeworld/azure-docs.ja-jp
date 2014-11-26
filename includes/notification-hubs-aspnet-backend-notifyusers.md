## Web API プロジェクトを作成する

最初に、ASP.NET Web API プロジェクトを作成します。これは、クライアントを認証し、通知を生成するために使用するバックエンドです。

> [AZURE.NOTE] **重要**: このチュートリアルを始める前に、最新の NuGet パッケージ マネージャーがインストールされていることを確認してください。確認するには、Visual Studio を起動します。**[ツール]** メニューの **[拡張機能と更新プログラム]** をクリックします。**NuGet Package Manager for Visual Studio 2013** を探し、バージョンが 2.8.50313.46 以降であることを確認します。違う場合は、アンインストールしてから、NuGet パッケージ マネージャーをもう一度インストールしてください。
>
> ![][0]

1.  昇格された特権を使用して Visual Studio を起動します (管理者として実行します)。
2.  Visual Studio または Visual Studio Express で、**[ファイル]** をクリックして **[新規]**、**[プロジェクト]** の順にクリックし、**[テンプレート]**、**[Visual C\#]** の順に展開します。次に、**[Web]** と **[ASP.NET Web アプリケーション]** をクリックし、「**AppBackend**」という名前を入力して **[OK]** をクリックします。

    ![][1]

3.  **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで **[Web API]**、**[OK]** の順にクリックします。

    ![][2]

4.  **[Azure サイトの構成]** ダイアログ ボックスで、このプロジェクトで使用するサブスクリプション、リージョン、データベースを選択します。**[OK]** をクリックしてプロジェクトを作成します。

    ![][3]

5.  ソリューション エクスプローラーで **AppBackend** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

6.  左側で、**[オンライン]** をクリックします。

7.  **[検索]** ボックスに、「**servicebus**」と入力します。

8.  結果の一覧で、**[Windows Azure Service Bus]** をクリックし、**[インストール]** をクリックします。インストールが完了したら、NuGet パッケージ マネージャーのウィンドウを閉じます。

    ![][4]

9.  ソリューション エクスプローラーで、**Models** フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。新しいクラスに「**Notifications.cs**」という名前を付けます。**[追加]** をクリックしてクラスを生成します。このモジュールは、送信される、セキュリティで保護された別の通知を表します。完全な実装では、通知はデータベースに格納されます。ここでは、操作を簡単にするために、メモリに格納します。

    ![][5]

10. コードを Notifications.cs に追加し、`Notifications` クラス定義を次のコードで置き換えます。

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. ファイルの先頭に次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

12. `Notifications()` メソッドで、次のコード行の 2 つのプレースホルダーを通知ハブの (フル アクセスを持つ) 接続文字列とハブの名前に置き換えます。これらの値は [Azure の管理ポータル][Azure の管理ポータル]から取得できます。

        Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");

13. ソリューション エクスプローラーで、**AppBackend** プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。新しいクラスに「**AuthenticationTestHandler.cs**」という名前を付けます。**[追加]** をクリックしてクラスを生成します。このクラスは、*基本認証*でユーザーを認証するために使用します。実際のアプリでは、任意の認証スキームを使用できます。

14. コードを AuthenticationTestHandler.cs に追加し、`AuthenticationTestHandler` クラス定義を次のコードで置き換えます。

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
                }
                else return Unauthorised();

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

    > [AZURE.NOTE] **セキュリティに関する注意**: `AuthenticationTestHandler` クラスは、本当の認証を提供するわけではありません。基本認証を模倣して、原理を返すためだけに使用されます。通知ハブ登録を作成するには、ユーザー名が必要です。上の実装はセキュリティで保護されていません。実稼働のアプリケーションとサービスでは、セキュリティで保護された認証メカニズムを実装する必要があります。

15. AuthenticationTestHandler.cs ファイルの先頭に次の `using` ステートメントを追加します。

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;               

16. **App\_Start/WebApiConfig.cs** クラスの `Register` メソッドの末尾に、次のコードを追加します。

        config.MessageHandlers.Add(new AuthenticationTestHandler());

17. ソリューション エクスプローラーで、**Controllers** フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。**[Web API 2 コントローラー -- 空]** 項目をクリックし、**[追加]** をクリックします。

    ![][6]

18. 新しいクラスに「**RegisterController**」という名前を付け、**[追加]** をもう一度クリックして、コントローラーを生成します。

    ![][7]

19. `RegisterController` クラス定義内で、次のコードを追加します。

        private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;

            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

20. RegisterController.cs ファイルの先頭に次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;
        using SecurePush.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

21. このコードでは、ハンドラーによって認証されたユーザーのユーザー タグを追加します。また、要求されたタグを登録する権限をユーザーが持っていることを確認するコードをオプションで追加することもできます。

22. ソリューション エクスプローラーで、**Controllers** フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。**[Web API 2 コントローラー -- 空]** 項目をクリックし、**[追加]** をクリックします。新しいクラスに「**NotificationsController**」という名前を付け、**[追加]** をもう一度クリックして、コントローラーを生成します。このコンポーネントは、デバイスが安全に通知を取得する方法を公開します。また、(このチュートリアルでは) ユーザーが自分のデバイスへの安全なプッシュをトリガーする方法も提供します。通知ハブに通知を送信するときに、通知の ID のみを含む (実際のメッセージは含まない) 直接通知を送信することに注意してください。

23. **NotificationsController** クラス定義内で、次のコードを追加します。

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);

        }

24. NotificationsController.cs ファイルの先頭に次の `using` ステートメントを追加します。

        using SecurePush.Models;
        using System.Threading.Tasks;
        using System.Web;

25. **F5** キーを押して、アプリケーションを実行し、ここまでの作業に問題がないことを確認します。アプリにより、Web ブラウザーが起動し、ASP.NET ホーム ページが表示される必要があります。

26. 次に、このアプリを Azure の Web サイトにデプロイして、すべてのデバイスからアクセスできるようにします。**AppBackend** プロジェクトを右クリックして **[発行]** を選択します。

27. 発行先として Azure の Web サイトを選択します。

    ![][8]

28. Azure アカウントでログインし、既存または新規の Web サイトを選択します。

    ![][9]

29. **[接続]** タブの **[宛先 URL]** プロパティをメモしておきます。後で、この URL を*バックエンド エンドポイント*として参照します。**[発行]** をクリックします。

    ![][10]

  [0]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
  [1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
  [2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
  [3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
  [4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
  [5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
  [7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
  [8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
  [9]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
  [10]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
