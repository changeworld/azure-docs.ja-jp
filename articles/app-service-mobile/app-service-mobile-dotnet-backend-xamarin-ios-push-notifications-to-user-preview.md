<properties 
	pageTitle="Xamarin iOS クライアントで特定のユーザーにプッシュ通知を送信する" 
	description="ユーザーのすべてのデバイスにプッシュ通知を送信する方法について説明します" 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/17/2015"
	ms.author="yuaxu"/>

# 特定のユーザーにクロスプラット フォーム通知を送信する

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../../includes/app-service-mobile-selector-push-users-preview.md)]

このトピックでは、特定のユーザーのすべての登録済みデバイスにモバイル バックエンドから通知を送信する方法について説明します。[テンプレート]の概念が導入されました。これにより、クライアント アプリケーションで、登録時にペイロードの形式と変数プレースホルダーを自由に指定することができます。送信すると、これらのプレースホルダーにより、すべてのプラットフォームにヒットし、クロスプラットフォーム通知が有効になります。

> [AZURE.NOTE]クロスプラットフォーム クライアントでプッシュ操作を有効にするには、有効にする各プラットフォームに対してこのチュートリアルを完了する必要があります。同じモバイル バックエンドを共有しているクライアントに対して、[モバイル バックエンドの更新](#backend)を 1 回だけ実行する必要があります。
 
##前提条件 

このチュートリアルを開始する前に、作業する各クライアント プラットフォームで次のアプリ サービス チュートリアルが既に完了している必要があります。

+ [認証の使用]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用]<br/>プッシュ通知用に TodoList サンプル アプリケーションを構成します。

##<a name="client"></a>クロスプラットフォームのプッシュを処理するためのテンプレートを登録するように、クライアントを更新する

1. APNs 登録スニペットを **AppDelegate.cs** の **FinishedLaunching** から **QSTodoListViewController.cs** の **RefreshAsync** タスクの定義に移動します。認証が完了した後に登録が行われます。

        ...
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound, 
                new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        ...

2. **AppDelegate.cs** で、**RegisteredForRemoteNotifications** の **RegisterAsync** 呼び出しを次のコードに置き換えてテンプレートを使用します。

        // delete await push.RegisterAsync (deviceToken);
        
        var notificationTemplate = "{"aps": {"alert":"$(message)"}}";

        JObject templateBody = new JObject();
        templateBody["body"] = notificationTemplate;

        JObject templates = new JObject();
        templates["testApnsTemplate"] = templateBody;

        // register with templates
        await push.RegisterAsync (deviceToken, templates);

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

            // get the current user id and create given user tag
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

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
[認証の使用]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[プッシュ通知の使用]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[テンプレート]: https://msdn.microsoft.com/ja-jp/library/dn530748.aspx
<!--HONumber=54--> 