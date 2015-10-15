<properties 
	pageTitle="Windows ストア クライアントで特定のユーザーに x-plat 通知を送信する"
	description="特定のユーザーのすべてのデバイスにプッシュ通知を送信する方法について説明します。"
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/14/2015"
	ms.author="yuaxu"/>

# 特定のユーザーにクロスプラット フォーム通知を送信する

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../../includes/app-service-mobile-selector-push-users-preview.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、特定のユーザーのすべての登録済みデバイスにモバイル バックエンドから通知を送信する方法について説明します。[テンプレート]の概念が導入されました。これにより、クライアント アプリケーションで、登録時にペイロードの形式と変数プレースホルダーを自由に指定することができます。送信すると、これらのプレースホルダーにより、すべてのプラットフォームにヒットし、クロスプラットフォーム通知が有効になります。

> [AZURE.NOTE]クロスプラットフォーム クライアントでプッシュ操作を有効にするには、有効にする各プラットフォームに対してこのチュートリアルを完了する必要があります。同じモバイル バックエンドを共有しているクライアントに対して、[モバイル バックエンドの更新](#backend)を 1 回だけ実行する必要があります。
 
##前提条件 

このチュートリアルを開始する前に、作業する各クライアント プラットフォームで次のアプリ サービス チュートリアルが既に完了している必要があります。

+ [認証の使用]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用]<br/>プッシュ通知用に TodoList サンプル アプリケーションを構成します。

##<a name="client"></a>クロスプラットフォームのプッシュを処理するためのテンプレートを登録するように、クライアントを更新する

1. 代わりに、**MainPage.cs** で **InitNotificationAsync** を実行してユーザー認証を行います。**App.xmal.cs** の **InitNotificationAsync** メソッド定義と呼び出しを削除し、**MainPage.cs** の **MainPage** クラスに以下を追加します。

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
 
            // building templates for wns
            var toastTemplate = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";
            JObject templateBody = new JObject();
            templateBody["body"] = toastTemplate;
 
            JObject wnsToastHeaders = new JObject();
            wnsToastHeaders["X-WNS-Type"] = "wns/toast";
            templateBody["headers"] = wnsToastHeaders;
 
            JObject templates = new JObject();
            templates["testTemplate"] = templateBody;
 
            await App.MobileService.GetPush().RegisterAsync(channel.Uri, templates);
        }

    また、いくつかの using ステートメントを **MainPage.cs** に移動する必要もあります。

2. このメソッドは、**ButtonLogin\_Click** の **AuthenticateAsync** 呼び出しの直後に使用します。

        await AuthenticateAsync();
        InitNotificationAsync();

これで、ユーザーのログイン情報でユーザー デバイスを登録するようにアプリケーションが設定されました。

##<a name="backend"></a>特定のユーザーに通知を送信するようにサービス バックエンドを更新する

1. Visual Studio で、`PostTodoItem` メソッド定義を次のコードで更新します。  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create tag to identify user
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // build dictionary for template
            var notification = new Dictionary<string, string>{{"message", item.Text}};

            try
            {
            	await Hub.Push.SendTemplateNotificationAsync(notification, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>アプリケーションをテストする

モバイル バックエンド プロジェクトを再発行し、設定してあるクライアント アプリケーションのいずれかを実行します。項目を挿入すると、バックエンドにより、ユーザーがログインしているすべてのクライアント アプリケーションに通知が送信されます。

<!-- URLs. -->
[認証の使用]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[プッシュ通知の使用]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview.md
[テンプレート]: https://msdn.microsoft.com/ja-jp/library/dn530748.aspx
 

<!---HONumber=August15_HO8-->