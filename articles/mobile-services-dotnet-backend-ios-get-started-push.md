<properties 
	pageTitle="Mobile Services アプリケーション (iOS) | モバイル デベロッパー センターへのプッシュ通知の追加" 
	description="Azure Mobile Services を使用して iOS アプリにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="ios" 
	manager="dwrede" 
	editor="" 
	authors="krisragh"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="krisragh"/>


# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

このトピックでは、Azure Mobile Services を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple Push Notification Service (APNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。


このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [証明書の署名要求を生成する]
2. [アプリケーションを登録し、プッシュ通知を有効にする]
3. [アプリのプロビジョニング プロファイルを作成する]
4. [サーバーを更新してプッシュ通知を送信する](#update-server)
5. [モバイル サービスを Azure に発行する]
6. [アプリケーションにプッシュ通知を追加する]
7. [ローカル テストのためにプッシュ通知を有効にする](#local-testing)
8. [発行されたモバイル サービスに対してアプリケーションをテストする]

このチュートリアルには、次のものが必要です。

+ [モバイル サービス iOS SDK]
+ [XCode 4.5][Install Xcode]
+ iOS 6.0 (またはこれ以降のバージョン) に対応したデバイス
+ iOS Developer Program メンバーシップ

   >[AZURE.NOTE]プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[Mobile Services の使用]」または「[アプリケーションへのMobile Services の追加][Get started with data]」のいずれかを完了しておく必要があります。


[AZURE.INCLUDE [Apple プッシュ通知を有効にする](../includes/enable-apple-push-notifications.md)]


## プッシュ要求を送信するようにモバイル サービスを構成する

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]


##<a id="update-server"></a>サーバーを更新してプッシュ通知を送信する

1. Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクト内の **Controllers** フォルダーを右クリックします。TodoItemController.cs を開きます。ファイルの先頭に、次の `using` ステートメントを追加します。


		using System;
		using System.Collections.Generic;

2. 次のコードで `PostTodoItem` メソッドの定義を更新します。

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Todo 項目を挿入した後、このコードは (挿入された項目のテキストを使用して) プッシュ通知を送信します。エラー イベントが発生した場合は、コードはエラー ログ エントリを追加します。そのエントリは、管理ポータル内でモバイル サービスに対応する **[ログ]** タブに表示されます。


##<a name="publish-the-service"></a>モバイル サービスを Azure に発行する

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## アプリケーションにプッシュ通知を追加する

1. QSAppDelegate.m で、Mobile Services iOS SDK をインポートするための次のスニペットを挿入します。

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. QSAppDelegate.m で、実装内の次のハンドラー メソッドを置き換えます。

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。必ず、Mobile Service URL とアプリケーション キーの値をプレースホルダーにコピーして、値を置き換えます。

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
            MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

   >[AZURE.NOTE]このコードを、<strong>addItem</strong> メソッドの呼び出しの前に追加する必要があります。

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

##<a id="local-testing"></a>ローカル テストのためにプッシュ通知を有効にする

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## アプリケーションでプッシュ通知をテストする

1. **[Run]** を押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。

  	![][23]

    > [AZURE.NOTE]アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

2. アプリケーションで、意味のあるテキスト (たとえば、「_新しい Mobile Services タスク_」) を入力し、プラス記号 (**+**) のアイコンをクリックします。

  	![][24]

3. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

  	![][25]

4. 手順 2 を繰り返してすぐにアプリケーションを閉じ、次のプッシュが表示されることを確認します。

  	![][26]

これで、このチュートリアルは終了です。

## 次のステップ

このチュートリアルでは、iOS アプリケーションで Mobile Services と通知ハブを使用してプッシュ通知を送信できるようにする方法の基本について説明しました。次は、タグを使用して、プッシュ通知をモバイル サービスから認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[プッシュ通知を認証ユーザーに送信する]」を行うことをお勧めします。

<!--+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->
Mobile Services と通知ハブについては次のトピックを参照してください。

* [データの使用] <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用] <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [通知ハブとは] <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [Notification Hubs アプリケーションのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Notification Hubs ソリューションのトラブルシューティングとデバッグについて説明します。

<!-- Anchors.  -->
[証明書の署名要求を生成する]: #certificates
[アプリケーションを登録し、プッシュ通知を有効にする]: #register
[アプリのプロビジョニング プロファイルを作成する]: #profile
[Configure Mobile Services]: #configure
[Update scripts to send push notifications]: #update-scripts
[アプリケーションにプッシュ通知を追加する]: #add-push
[Insert data to receive notifications]: #test
[発行されたモバイル サービスに対してアプリケーションをテストする]: #test-app
[Next Steps]: #next-steps
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[モバイル サービスを Azure に発行する]: #publish-mobile-service

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
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
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
[モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Mobile Services の使用]: mobile-services-dotnet-backend-ios-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[データの使用]: mobile-services-dotnet-backend-ios-get-started-data.md
[認証の使用]: mobile-services-dotnet-backend-ios-get-started-users.md

[プッシュ通知を認証ユーザーに送信する]: mobile-services-dotnet-backend-ios-push-notifications-app-users.md

[通知ハブとは]: notification-hubs/notification-hubs-overview.md
[Send broadcast notifications to subscribers]: notification-hubs/notification-hubs-ios-send-breaking-news.md
[Send template-based notifications to subscribers]: notification-hubs/notification-hubs-ios-send-localized-breaking-news.md

<!--HONumber=54-->