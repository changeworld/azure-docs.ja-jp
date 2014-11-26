<properties title="Azure Notification Hubs Secure Push" pageTitle="Azure Notification Hubs Secure Push" metaKeywords="Azure push notifications, Azure notification hubs, Azure messaging, secure push" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Azure Notification Hubs の安全なプッシュ

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ja-jp/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows ユニバーサル" class="current">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android">Android</a>
</div>

Microsoft Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。

規制やセキュリティの制約により、アプリケーションでは、標準のプッシュ通知インフラストラクチャからは転送できないものを通知に含める必要がある場合があります。このチュートリアルでは、クライアントのデバイスとアプリケーションのバックエンドとの間の安全で認証された接続を通して機密情報を送信することによって、同じエクスペリエンスを実現する方法について説明します。

大まかには、フローは次のようになります。

1.  アプリケーションのバックエンド:

    -   バックエンド データベースに安全なペイロードを格納します。
    -   この通知の ID をデバイスに送信します (安全でない情報は送信しません)。

2.  通知を受け取ったときのデバイスのアプリケーション:

    -   デバイスは、安全なペイロードを要求するバックエンドにアクセスします。
    -   アプリケーションはデバイスに通知としてペイロードを表示できます。

前のフロー (およびこのチュートリアル) で重要なことは、ユーザーのログイン後、デバイスはローカル ストレージに認証トークンを格納すると想定していることです。これにより、デバイスはこのトークンを使用して通知の安全なペイロードを取得できるため、完全にシームレスなエクスペリエンスが保証されます。アプリケーションがデバイスに認証トークンを格納しない、またはそれらのトークンが期限切れの場合、デバイスのアプリケーションは、通知を受け取ったときにアプリケーションの起動を促す一般的な通知を表示する必要があります。その後、アプリケーションはユーザーを認証し、通知ペイロードを表示します。

この安全なプッシュのチュートリアルでは、プッシュ通知を安全に送信する方法を説明します。このチュートリアルは「**ユーザーへの通知**」チュートリアルに基づいて記述されているため、先にそのチュートリアルでの手順を完了してください。

> [AZURE.NOTE] このチュートリアルでは、「[Getting Started with Notification Hubs (Windows Store) (Notification Hubs (Windows ストア) の使用)][Getting Started with Notification Hubs (Windows Store) (Notification Hubs (Windows ストア) の使用)]」の説明に従って、通知ハブが作成され、構成されていると想定しています。
> また、Windows Phone 8.1 には Windows (Windows Phone ではなく) の資格情報が必要で、バックグラウンド タスクは Windows Phone 8.0 または Silverlight 8.1 では機能しないことに注意してください。Windows ストア アプリケーションの場合は、アプリケーションでロック画面が有効 (Appmanifest でチェック ボックスをオンにする) な場合にだけバックグラウンド タスクから通知を受信できます。

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Windows Phone プロジェクトを変更する

1.  **NotifyUserWindowsPhone** プロジェクトで、次のコードを App.xaml.cs に追加して、プッシュ バックグラウンド タスクを登録します。`OnLaunched()` メソッドの最後に次のコード行を追加します。

        RegisterBackgroundTask();

2.  引き続き App.xaml.cs で、`OnLaunched()` の直後に次のコードを追加します。

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3.  App.xaml.cs ファイルの先頭に次の `using` ステートメントを追加します。

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4.  Visual Studio の **[ファイル]** メニューで **[すべて保存]** をクリックします。

## プッシュ バックグラウンド コンポーネントを作成する

次の手順では、プッシュ バックグラウンド コンポーネントを作成します。

1.  ソリューション エクスプローラーで、ソリューションの最上位ノード (この場合は、**Solution SecurePush**) を右クリックし、**[追加]**、**[新しいプロジェクト]** の順にクリックします。

2.  **[ストア アプリ]** を展開し、**[Windows Phone アプリ]**、**[Windows ランタイム コンポーネント (Windows Phone)]** の順にクリックします。プロジェクトの名前として「**PushBackgroundComponent**」と入力し、**[OK]** をクリックしてプロジェクトを作成します。

    ![][0]

3.  ソリューション エクスプローラーで、**PushBackgroundComponent (Windows Phone 8.1)** プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。新しいクラスに「**PushBackgroundTask.cs**」という名前を付けます。**[追加]** をクリックしてクラスを生成します。

4.  **PushBackgroundComponent** 名前空間定義の内容全体を次のコードで置き換えます。プレースホルダー `{back-end endpoint}` をバックエンドのデプロイ時に取得したバックエンド エンドポイントで置き換えます。

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5.  ソリューション エクスプローラーで **PushBackgroundComponent (Windows Phone 8.1)** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

6.  左側で、**[オンライン]** をクリックします。

7.  **[検索]** ボックスに、「**Http Client**」と入力します。

8.  結果の一覧で、**[Microsoft HTTP Client Libraries]**、**[インストール]** の順にクリックします。インストールを完了します。

9.  NuGet **[検索]** ボックスに戻り、「**Json.net**」と入力します。**Json.NET** パッケージをインストールし、NuGet パッケージ マネージャーのウィンドウを閉じます。

10. **PushBackgroundTask.cs** ファイルの先頭に次の `using` ステートメントを追加します。

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. ソリューション エクスプローラーで、**NotifyUserWindowsPhone (Windows Phone 8.1)** プロジェクトの **[参照]** を右クリックし、**[参照の追加]** をクリックします。参照マネージャー ダイアログで、**PushBackgroundComponent** のチェック ボックスをオンにして、**[OK]** をクリックします。

12. ソリューション エクスプローラーで、**NotifyUserWindowsPhone (Windows Phone 8.1)** プロジェクトの **[Package.appxmanifest]** をダブルクリックします。**[通知]** で、**[トースト対応]** を **[はい]** に設定します。

    ![][1]

13. 引き続き **Package.appxmanifest** で、上部の **[宣言]** メニューをクリックします。**[使用可能な宣言]** ボックスで、**[バックグラウンド タスク]**、**[追加]** の順にクリックします。

14. **Package.appxmanifest** で、**[プロパティ]** の **[プッシュ通知]** チェック ボックスをオンにします。

15. **Package.appxmanifest** で、**[アプリ設定]** の **[エントリ ポイント]** フィールドに「**PushBackgroundComponent.PushBackgroundTask**」と入力します。

    ![][2]

16. **[ファイル]** メニューの **[すべて保存]** をクリックします。

## アプリケーションの実行

アプリケーションを実行するには、以下の手順に従います。

1.  Visual Studio で、**AppBackend** Web API アプリケーションを実行します。ASP.NET Web ページが表示されます。

2.  Visual Studio で、**NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone アプリケーションを実行します。Windows Phone エミュレーターが自動的に起動し、アプリケーションを読み込みます。

3.  **NotifyUserWindowsPhone** アプリケーションの UI で、ユーザー名とパスワードを入力します。文字列は任意ですが、値は同じである必要があります。

4.  **NotifyUserWindowsPhone** アプリケーションの UI で、**[ログインして登録]** をクリックします。次に、**[プッシュを送信する]** をクリックします。

  [0]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
  [1]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
  [2]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
