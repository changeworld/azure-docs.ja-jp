<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# モバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS" class="current">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!-- <a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET バックエンド" class="current">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、Azure Mobile Services を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple Push Notification Service (APNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [証明書の署名要求を生成する][証明書の署名要求を生成する]
2.  [アプリケーションを登録し、プッシュ通知を有効にする][アプリケーションを登録し、プッシュ通知を有効にする]
3.  [アプリケーションのプロビジョニング プロファイルを作成する][アプリケーションのプロビジョニング プロファイルを作成する]
4.  [サービスをローカルにダウンロードする][サービスをローカルにダウンロードする]
5.  [モバイル サービスをテストする][モバイル サービスをテストする]
6.  [サーバーを更新してプッシュ通知を送信する][サーバーを更新してプッシュ通知を送信する]
7.  [モバイル サービスを Azure に発行する][モバイル サービスを Azure に発行する]
8.  [アプリケーションにプッシュ通知を追加する][アプリケーションにプッシュ通知を追加する]
9.  [プッシュ通知を送信するようにスクリプトを更新する][プッシュ通知を送信するようにスクリプトを更新する]
10. [ローカル テストのためにプッシュ通知を有効にする][ローカル テストのためにプッシュ通知を有効にする]
11. [発行されたモバイル サービスに対してアプリケーションをテストする][発行されたモバイル サービスに対してアプリケーションをテストする]

このチュートリアルには、次のものが必要です。

-   [モバイル サービス iOS SDK][モバイル サービス iOS SDK]
-   [XCode 4.5][XCode 4.5]
-   iOS 6.0 (またはこれ以降のバージョン) に対応したデバイス
-   iOS Developer Program メンバーシップ

> [WACOM.NOTE] プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## プッシュ要求を送信するようにモバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## <a name="download-the-service"></a><span class="short-header">サービスのダウンロード</span>サービスをローカル コンピューターにダウンロードする

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">サービスのテスト</span>モバイル サービスをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <span id="update-server"></span></a> サーバーを更新してプッシュ通知を送信する

1.  Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクト内の **Controllers** フォルダーを右クリックします。TodoItemController.cs を開きます。ファイルの先頭に次の `using` ステートメントを追加します。

        using System;
        using System.Collections.Generic;

2.  次のコードで `PostTodoItem` メソッドの定義を更新します。

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

## <a name="publish-the-service"></a><span class="short-header">サービスの発行</span>モバイル サービスを Azure に発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## アプリケーションにプッシュ通知を追加する

1.  QSAppDelegate.m で、実装内の次のハンドラー メソッドを置き換えます。

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3.  QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4.  QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。

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

5.  QSTodoListViewController.m で、デリゲートを使用してデバイス トークンを取得できるように QSAppDelegate.h ファイルをインポートします。

        #import "QSAppDelegate.h"

6.  QSTodoListViewController.m で、次の行を見つけて **(IBAction)onAdd** アクションを変更します。

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

  これを次のコードに置き換えます。

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : [[NSString alloc] initWithData:delegate.deviceToken encoding:NSUTF8StringEncoding]
        };

   これによって、デバイス トークンを取得するための参照が **QSAppDelegate** に追加され、要求ペイロードがそのデバイス トークンを含めるように変更されます。

   > [WACOM.NOTE] このコードを、**addItem** メソッドへの呼び出しの前に追加する必要があります。

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

## <span id="local-testing"></span></a> ローカル テストのためにプッシュ通知を有効にする

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## アプリケーションでプッシュ通知をテストする

1.  **[Run]** を押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。

    ![][0]

    > [WACOM.NOTE] アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

2.  アプリケーションで、意味のあるテキスト (たとえば、「*新しいモバイル サービス タスク*」) を入力し、プラス (**[+]**) アイコンをクリックします。

    ![][1]

3.  通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

    ![][2]

4.  手順 2. を繰り返してすぐにアプリケーションを閉じ、次のプッシュが表示されることを確認します。

    ![][3]

これで、このチュートリアルは終了です。

## 次のステップ

このチュートリアルでは、iOS アプリケーションで Mobile Services と通知ハブを使用してプッシュ通知を送信できるようにする方法の基本について説明しました。続いて、次のチュートリアル「[認証されたユーザーへのプッシュ通知の送信][認証されたユーザーへのプッシュ通知の送信]」を完了することをお勧めします。このチュートリアルでは、タグを使用して、認証されたユーザーにのみモバイル サービスからプッシュ通知を送信する方法について説明しています。

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Mobile Services と通知ハブについては次のトピックを参照してください。

-   [データの使用][データの使用]<br/>
    モバイル サービスを使用してデータの格納およびクエリの実行の方法について説明します。

-   [認証の使用][認証の使用]<br/>
    モバイル サービスを使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

-   [Windows Azure 通知ハブとは][Windows Azure 通知ハブとは]<br/>
    通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。



  [証明書の署名要求を生成する]: #certificates
  [アプリケーションを登録し、プッシュ通知を有効にする]: #register
  [アプリケーションのプロビジョニング プロファイルを作成する]: #profile
  [サービスをローカルにダウンロードする]: #download-the-service-locally
  [モバイル サービスをテストする]: #test-the-service
  [サーバーを更新してプッシュ通知を送信する]: #update-server
  [モバイル サービスを Azure に発行する]: #publish-mobile-service
  [アプリケーションにプッシュ通知を追加する]: #add-push
  [プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
  [ローカル テストのためにプッシュ通知を有効にする]: #local-testing
  [発行されたモバイル サービスに対してアプリケーションをテストする]: #test-app
  [モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  [0]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [認証されたユーザーへのプッシュ通知の送信]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
  [データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [Windows Azure 通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/
