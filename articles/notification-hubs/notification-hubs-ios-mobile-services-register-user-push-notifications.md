<properties 
	pageTitle="モバイル サービスを使用した現在のユーザーのプッシュ通知への登録 - 通知ハブ" 
	description="Azure Mobile Services により登録が実行されるときに、iOS アプリケーションで Azure Notification Hubs へのプッシュ通知登録を要求する方法について説明します。" 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="yuaxu"/>

# モバイル サービスを使用した現在のユーザーのプッシュ通知への登録

<div class="dev-center-tutorial-selector sublanding">
    <a href="/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows ストア C#">Windows ストア C#</a><a href="/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS" class="current">iOS</a>
</div>

このトピックでは、Azure のモバイル サービスにより登録が実行されるときに Azure 通知ハブへのプッシュ通知登録を要求する方法について説明します。このトピックは、チュートリアル「[通知ハブによるユーザーへの通知]」を拡張したものです。認証されたモバイル サービスを作成するには、このチュートリアルの必要な手順を既に完了している必要があります。ユーザー通知シナリオの詳細については、「[通知ハブによるユーザーへの通知]」を参照してください。

1. Xcode で、前提条件のチュートリアルである「[認証の使用]」を実行したときに作成したプロジェクトの QSTodoService.h ファイルを開き、次の **deviceToken** プロパティを追加します。

		@property (nonatomic) NSData* deviceToken;

 	このプロパティには、デバイス トークンが格納されます。

2. QSTodoService.m ファイルで、次の **getDeviceTokenInHex** メソッドを追加します。

			- (NSString*)getDeviceTokenInHex {
			    const unsigned *tokenBytes = [[self deviceToken] bytes];
			    NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
			                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
			                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
			                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
			    return hexToken;
			}

	このメソッドは、デバイス トークンを 16 進数の文字列値に変換します。

3. QSAppDelegate.m ファイルで、次のコード行を **didFinishLaunchingWithOptions** メソッドに追加します。

			[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	これにより、アプリケーションでプッシュ通知が有効になります。

4. 	QSAppDelegate.m ファイルで、次のメソッドを追加します。

			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
			    [QSTodoService defaultService].deviceToken = deviceToken;
			}

	これにより、**deviceToken** プロパティが更新されます。

	> [AZURE.NOTE]この時点では、このメソッドに他のコードは存在しません。チュートリアル「[通知ハブの使用](/manage/services/notification-hubs/get-started-notification-hubs-ios/"%20target="_blank")」を完了したときに追加された **registerNativeWithDeviceToken** メソッドへの呼び出しが既にある場合、その呼び出しをコメント解除するか、削除する必要があります。

5.  (省略可能) QSAppDelegate.m ファイルで、次のハンドラー メソッドを追加します。

			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
			    NSLog(@"%@", userInfo);
			    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
			                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
			                          @"OK" otherButtonTitles:nil, nil];
			    [alert show];
			}

 	アプリケーションが実行中に通知を受信すると、このメソッドは UI にアラートを表示します。

6. QSTodoListViewController.m ファイルで、**registerForNotificationsWithBackEnd** メソッドを追加します。

			- (void)registerForNotificationsWithBackEnd {
			    NSString* json = [NSString  stringWithFormat:@"{"platform":"ios", "deviceToken":"%@"}", [self.todoService getDeviceTokenInHex] ];

			    [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
			        if (error != nil) {
			            NSLog(@"Registration failed: %@", error);
			        } else {
			            // display UIAlert with successful login
			            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
			            [alert show];
			        }
			    }];
			}

	このメソッドは、デバイス トークンを含む json ペイロードを作成します。その後、モバイル サービスでカスタム API を呼び出して通知に登録します。このメソッドは、プッシュ通知のデバイス トークンを作成し、通知ハブで登録を作成するカスタム API メソッドに、デバイスの種類と共に送信します。このカスタム API は、「[通知ハブによるユーザーへの通知]」で定義されたものです。

7.	最後に、ユーザーが正常に認証された後、**viewDidAppear** メソッド内の新しい **registerForNotificationsWithBackEnd** メソッドにこのメソッドへの呼び出しを追加します。次に例を示します。

			- (void)viewDidAppear:(BOOL)animated
			{
			    MSClient *client = self.todoService.client;

			    if (client.currentUser != nil) {
			        return;
			    }

			    [client loginWithProvider:@"microsoftaccount" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
			        [self refresh];
			        [self registerForNotificationsWithBackEnd];
			    }];
			}

	> [AZURE.NOTE]これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。
	
これで、クライアント アプリケーションが更新されました。「[通知ハブによるユーザーへの通知]」に戻り、通知ハブを使用することで通知を送信するようにモバイル サービスを更新します。

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[通知ハブによるユーザーへの通知]: /manage/services/notification-hubs/notify-users
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-ios/

[Azure Management Portal]: https://manage.windowsazure.com/
[Get Started with Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
 

<!---HONumber=July15_HO2-->