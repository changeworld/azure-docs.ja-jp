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

このトピックでは、Azure Mobile Services を使用して iOS アプリケーションに Apple Push Notification サービス (APNS) を通じてプッシュ通知を送信する方法について説明します。このチュートリアルでは、[クイック スタート プロジェクト](http://azure.microsoft.commobile-services-ios-get-started.md/)への Azure Notification Hubs を使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [証明書の署名要求を生成する](#certificates)
2. [アプリケーションを登録し、プッシュ通知を有効にする](#register)
3. [アプリのプロビジョニング プロファイルを作成する](#profile)
4. [Mobile Services を構成する](#configure)
5. [アプリケーションにプッシュ通知を追加する](#add-push)
6. [プッシュ通知を送信するようにスクリプトを更新する](#update-scripts)
7. [データを挿入して通知を受け取る](#test)

このチュートリアルには、次のものが必要です。

+ [Mobile Services iOS SDK]
+ [XCode 4.5][Install Xcode]
+ iOS 6.0 (またはこれ以降のバージョン) に対応したデバイス
+ iOS Developer Program メンバーシップ

   >[AZURE.NOTE]プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[Mobile Services の使用]」または「[既存のアプリケーションへのMobile Services の追加][Get started with data]」のいずれかを完了しておく必要があります。


[AZURE.INCLUDE [Apple プッシュ通知を有効にする](../includes/enable-apple-push-notifications.md)]


## <a id="configure"></a>プッシュ要求を送信するように Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## <a id="update-scripts"></a>管理ポータルで登録されている挿入スクリプトを更新する

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][21]

2. **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

  	![][22]

   	**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

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

   	これで、新しい挿入スクリプトが登録されます。このスクリプトは [apns オブジェクト]を使用して、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。


   	> [AZURE.NOTE]このスクリプトでは、プッシュ通知を受け取るためにアプリケーションを閉じる時間を与えるために通知の送信を遅らせています。

## <a id="add-push"></a>アプリケーションにプッシュ通知を追加する

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
			MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"];

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

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

## <a id="test"></a>アプリケーションでプッシュ通知をテストする

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

## <a id="next-steps"></a>次のステップ

このチュートリアルでは、iOS アプリケーションで Mobile Services と Notification Hubs を使用してプッシュ通知を送信できるようにする方法の基本について説明しました。次に、次のチュートリアルのいずれかを完了します。

+ [認証されたユーザーにプッシュ通知を送信する] <br/>タグを使用して Mobile Services から認証されたユーザーのみにプッシュ通知を送信する方法について説明します。

+ [登録者への通知の送信] <br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。
<!---
+ [登録者へのテンプレートベース通知の送信] <br/>テンプレートを使用し、バックエンドでプラットフォームに固有のペイロードを作成することなしに、Mobile Services からプッシュ通知を送信する方法について説明します。
--> 
Mobile Services および Notification Hubs の詳細については、次のトピックで説明します。

* [データの使用] <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用] <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [通知ハブとは] <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [Notification Hubs アプリケーションのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Notification Hubs ソリューションのトラブルシューティングとデバッグについて説明します。

* [Mobile Services Objective-C の使用方法の概念リファレンス] <br/>Mobile Services を Objective-C および iOS と共に使用する方法について説明します。

* [Mobile Services のサーバー スクリプト リファレンス] <br/>モバイル サービスでビジネス ロジックを実装する方法を説明します。

<!-- Anchors. -->


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

<!-- URLs.   -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Mobile Services の使用]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[データの使用]: mobile-services-ios-get-started-data.md
[認証の使用]: mobile-services-ios-get-started-users.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Mobile Services のサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293

[認証されたユーザーにプッシュ通知を送信する]: mobile-services-javascript-backend-ios-push-notifications-app-users.md

[通知ハブとは]: notification-hubs-overview.md
[登録者への通知の送信]: notification-hubs-ios-send-breaking-news.md
[登録者へのテンプレートベース通知の送信]: notification-hubs-ios-send-localized-breaking-news.md

[Mobile Services Objective-C の使用方法の概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54-->