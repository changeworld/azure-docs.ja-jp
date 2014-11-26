<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# モバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS" class="current">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a><!---<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET バックエンド" >.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="JavaScript バックエンド" class="current">JavaScript バックエンド</a></div>

このトピックでは、Azure Mobile Services を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple Push Notification Service (APNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

> [WACOM.NOTE] このチュートリアルでは、Mobile Services と通知ハブとの統合について示します。それによって、モバイル サービスからプッシュ通知を送信できるようになります。従来のプッシュを使用する古いモバイル サービスを使用していて、まだ通知ハブにアップグレードしていない場合は、このチュートリアルの中で*アップグレードすることをお勧めします*。またアップグレードしない場合は、チュートリアル「[Get started with push notifications (legacy) (プッシュ通知の使用 (従来))][Get started with push notifications (legacy) (プッシュ通知の使用 (従来))]」に従ってください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [証明書の署名要求を生成する][証明書の署名要求を生成する]
2.  [アプリケーションを登録し、プッシュ通知を有効にする][アプリケーションを登録し、プッシュ通知を有効にする]
3.  [アプリケーションのプロビジョニング プロファイルを作成する][アプリケーションのプロビジョニング プロファイルを作成する]
4.  [モバイル サービスを構成する][モバイル サービスを構成する]
5.  [アプリケーションにプッシュ通知を追加する][アプリケーションにプッシュ通知を追加する]
6.  [プッシュ通知を送信するようにスクリプトを更新する][プッシュ通知を送信するようにスクリプトを更新する]
7.  [データを挿入して通知を受け取る][データを挿入して通知を受け取る]

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

  これによって、デバイス トークンを取得するための参照が **QSAppDelegate** に追加され、要求ペイロードがそのデバイス トークンを含むように変更されます。

  > [WACOM.NOTE] このコードを、**addItem** メソッドへの呼び出しの前に追加する必要があります。

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

## 管理ポータルで登録されている挿入スクリプトを更新する

1.  管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][0]

2.  **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

    ![][1]

    **TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3.  insert 関数を次のコードに置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    これで、新しい挿入スクリプトが登録されます。このスクリプトは [apns オブジェクト][apns オブジェクト]を使用して、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。

    > [WACOM.NOTE] このスクリプトでは、プッシュ通知を受け取るためにアプリケーションを閉じる時間が確保されるように、通知の送信を遅らせています。

## アプリケーションでプッシュ通知をテストする

1.  **[Run]** を押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。

    ![][2]

    > [WACOM.NOTE] アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

2.  アプリケーションで、意味のあるテキスト (たとえば、「*新しいモバイル サービス タスク*」) を入力し、プラス (**[+]**) アイコンをクリックします。

    ![][3]

3.  通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

    ![][4]

4.  手順 2. を繰り返してすぐにアプリケーションを閉じ、次のプッシュが表示されることを確認します。

    ![][5]

これで、このチュートリアルは終了です。

## 次のステップ

このチュートリアルでは、iOS アプリケーションで Mobile Services と Notification Hubs を使用してプッシュ通知を送信できるようにする方法の基本について説明しました。この後は、次のチュートリアルのいずれかを完了することをお勧めします。

-   [認証されたユーザーへのプッシュ通知の送信][認証されたユーザーへのプッシュ通知の送信]<br/>
    タグを使用して、モバイル サービスから認証されたユーザーに対してだけプッシュ通知を送信する方法について説明します。

-   [Send broadcast notifications to subscribers (登録者へのブロードキャスト通知の送信)][Send broadcast notifications to subscribers (登録者へのブロードキャスト通知の送信)]<br/>
    ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。
    <!--- + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->
    Mobile Services と Notification Hubs については次のトピックを参照してください。

-   [データの使用][データの使用]<br/>
    モバイル サービスを使用してデータの格納およびクエリの実行の方法について説明します。

-   [認証の使用][認証の使用]<br/>
    モバイル サービスを使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

-   [Windows Azure 通知ハブとは][Windows Azure 通知ハブとは]<br/>
    通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

-   [Mobile Services Objective-C how-to conceptual reference (Mobile Services Objective-C の使用方法の概念リファレンス)][Mobile Services Objective-C how-to conceptual reference (Mobile Services Objective-C の使用方法の概念リファレンス)]<br/>
    Mobile Services を Objective-C および iOS と共に使用する方法について説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]<br/>
    モバイル サービスにビジネス ロジックを実装する方法について説明します。



  [証明書の署名要求を生成する]: #certificates
  [アプリケーションを登録し、プッシュ通知を有効にする]: #register
  [アプリケーションのプロビジョニング プロファイルを作成する]: #profile
  [モバイル サービスを構成する]: #configure
  [アプリケーションにプッシュ通知を追加する]: #add-push
  [プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
  [データを挿入して通知を受け取る]: #test
  [モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [apns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [認証されたユーザーへのプッシュ通知の送信]: /ja-jp/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/
  [データの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started-data
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started-users
  [Windows Azure 通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
