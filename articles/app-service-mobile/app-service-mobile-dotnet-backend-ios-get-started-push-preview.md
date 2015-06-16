<properties 
	pageTitle="Azure App Service を使用して iOS アプリにプッシュ通知を追加する" 
	description="Azure App Service を使用して iOS アプリにプッシュ通知を送信する方法について説明します。" 
	services="app-service\mobile" 
	documentationCenter="ios" 
	manager="dwrede"
	editor="" 
	authors="ysxu"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="yuaxu"/>


# iOS アプリにプッシュ通知を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

このトピックでは、Azure App Service を使用して、Apple Push Notification Service (APNs) で iOS アプリにプッシュ通知を送信する方法について説明します。完了すると、.NET バックエンドは、レコードが挿入されるたびにクイック スタート ToDo アプリにプッシュ通知を送信します。このアプリは、iOS 8 以前のバージョンと互換性があります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [iOS 証明書の署名要求を生成する]
2. [アプリケーションを登録し、プッシュ通知を有効にする]
3. [アプリケーションのプロビジョニング プロファイルを作成する]
4. [プッシュ要求を送信するようにモバイル バックエンドを構成する]
5. [プッシュ通知を送信するようにサーバーを更新する](#update-server)
6. [モバイル バックエンドを Azure に発行する]
7. [アプリケーションにプッシュ通知を追加する]
8. [アプリケーションをテストする]

このチュートリアルには、次のものが必要です。

+ [Azure モバイル アプリ iOS SDK]
+ [Azure Notification Hubs Nuget]
+ [XCode 6.0][Install Xcode]
+ iOS 6.0 (またはこれ以降のバージョン) に対応したデバイス
+ iOS Developer Program メンバーシップ

   >[AZURE.NOTE]プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルは、App Service モバイル アプリのクイック スタートに基づいています。このチュートリアルを開始する前に、[App Service Mobile Apps の使用]に関するチュートリアルを完了している必要があります。

[AZURE.INCLUDE [Apple プッシュ通知を有効にする](../../includes/enable-apple-push-notifications.md)]

## プッシュ要求を送信するようにモバイル アプリを構成する

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>プッシュ通知を送信するようにサーバーを更新する

1. Visual Studio でソリューションを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. **Microsoft.Azure.NotificationHubs** を検索し、ソリューション内のすべてのプロジェクトに対して **[インストール]** をクリックします。

3. Visual Studio のソリューション エクスプローラーで、モバイル バックエンド プロジェクト内の **Controllers** フォルダーを展開します。TodoItemController.cs を開きます。ファイルの先頭に、次の `using` ステートメントを追加します。

		using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. **InsertAsync** の呼び出しの後の `PostTodoItem` メソッドに次のスニペットを追加します。

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    このコードは、このモバイル アプリに関連付けられている通知ハブに、todo 項目の挿入後にプッシュ通知を送信するよう指示します。


## <a name="publish-the-service"></a>モバイル バックエンドを Azure に発行する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## アプリケーションにプッシュ通知を追加する
1. App Service モバイル アプリ クライアント SDK をダウンロードし、xcode でその SDK への参照を追加します。

2. Apple Push Notification Service にクライアントを登録するために、**QSAppDelegate.m** で、以下を **application:didFinishLaunchingWithOptions** に追加します。

        // register iOS8 or previous devices for notifications
        if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && 	
        	[[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
        } else {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }

3. 同じファイル内で、次のハンドラー メソッドを **QSAppDelegate** 実装内に追加します。

        // registration with APNs is successful
        - (void)application:(UIApplication *)application 
            didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            // make sure you have imported "QSTodoService.h"
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. 次に、実装内にエラー ハンドラー メソッドを追加します。

        // handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。

        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application 
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            
            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];
    
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" 
                                                            message:alertString 
                                                           delegate:nil 
                                                  cancelButtonTitle:@"OK" 
                                                  otherButtonTitles:nil];
            [alert show];
        }

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

## アプリケーションでプッシュ通知をテストする

1. **[Run]** を押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。

  	![][23]

    > [AZURE.NOTE]アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

2. アプリケーションで、意味のあるテキスト (たとえば、「_新しい Mobile Services タスク_」) を入力し、プラス (**+**) アイコンをクリックします。

  	![][24]

3. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

  	![][25]

4. 手順 2 を繰り返してすぐにアプリケーションを閉じ、次のプッシュが表示されることを確認します。

  	![][26]

これで、このチュートリアルは終了です。

<!-- Anchors.  -->
[iOS 証明書の署名要求を生成する]: #certificates
[アプリケーションを登録し、プッシュ通知を有効にする]: #register
[アプリケーションのプロビジョニング プロファイルを作成する]: #profile
[アプリケーションにプッシュ通知を追加する]: #add-push
[プッシュ要求を送信するようにモバイル バックエンドを構成する]: #configure
[Update the server to send push notifications]: #update-server
[モバイル バックエンドを Azure に発行する]: #publish-mobile-service
[アプリケーションをテストする]: #test-the-service

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push1-ios.png
[24]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push2-ios.png
[25]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push3-ios.png
[26]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Azure モバイル アプリ iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=529823
[Azure Notification Hubs Nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: ../mobile-services-dotnet-backend-ios-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!--HONumber=54--> 