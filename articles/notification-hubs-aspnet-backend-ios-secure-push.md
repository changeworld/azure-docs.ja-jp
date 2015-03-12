<properties 
	pageTitle="Azure Notification Hubs の安全なプッシュ" 
	description="セキュリティで保護されたプッシュ通知を Azure から iOS アプリに送信する方法について説明します。コード サンプルは Objective-C と C# で記述されています。" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor="" 
	services="notification-hubs"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="yuaxu"/>

# Azure Notification Hubs の安全なプッシュ

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-secure-push/" title="Windows Universal">Windows Universal</a><a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS" class="current">iOS</a>
		<a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android">Android</a>
</div>

Microsoft Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。

規制やセキュリティの制約により、アプリケーションでは、標準のプッシュ通知インフラストラクチャからは転送できないものを通知に含める必要がある場合があります。このチュートリアルでは、クライアントのデバイスとアプリケーションのバックエンドとの間の安全で認証された接続を通して機密情報を送信することによって、同じエクスペリエンスを実現する方法について説明します。

大まかには、フローは次のようになります。

1. アプリケーションのバックエンド:
	- バックエンド データベースに安全なペイロードを格納します。
	- この通知の ID をデバイスに送信します (安全でない情報は送信しません)。
2. 通知を受け取ったときのデバイスのアプリケーション:
	- デバイスは、安全なペイロードを要求するバックエンドにアクセスします。
	- アプリケーションはデバイスに通知としてペイロードを表示できます。

前のフロー (およびこのチュートリアル) で重要なことは、ユーザーのログイン後、デバイスはローカル ストレージに認証トークンを格納すると想定していることです。これにより、デバイスはこのトークンを使用して通知の安全なペイロードを取得できるため、完全にシームレスなエクスペリエンスが保証されます。アプリケーションがデバイスに認証トークンを格納しない、またはそれらのトークンが期限切れの場合、デバイスのアプリケーションは、通知を受け取ったときにアプリケーションの起動を促す一般的な通知を表示する必要があります。その後、アプリケーションはユーザーを認証し、通知ペイロードを表示します。

この安全なプッシュのチュートリアルでは、プッシュ通知を安全に送信する方法を説明します。このチュートリアルは **ユーザーへの通知** チュートリアルに基づいて記述されているため、先にそのチュートリアルでの手順を完了してください。

> [AZURE.NOTE] このチュートリアルでは、[通知ハブの使用 (iOS)](http://azure.microsoft.com/ documentation/articles/notification-hubs-ios-get-started/) での説明に従って通知が作成され、構成されていると想定しています。

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## iOS プロジェクトを変更する

通知の  *id* だけを送信するようにアプリケーション バックエンドを変更したため、iOS アプリケーションがその通知を処理し、バックエンドをコールバックしてから安全なメッセージを取得して表示するように変更する必要があります。

そのためには、アプリケーション バックエンドから安全なコンテンツを取得するロジックを作成する必要があります。

1. **AppDelegate.m** で、アプリケーションがサイレント通知に登録されていることを確認し、バックエンドから送信された通知 id を処理できるようにします。didFinishLaunchingWithOption に **UIRemoteNotificationTypeNewsstandContentAvailability** オプションを追加します。

		[[UIApplication sharedApplication] registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. **AppDelegate.m** で、次の宣言を使用して実装セクションを先頭に追加します。

		@interface AppDelegate ()
		- (void) retrieveSecurePayloadWithId:(int)payloadId completion:(void(^)(NSString*, NSError*)) completion;
		@end

3. 次に、実装セクションに次のコードを追加します。これにより、プレースホルダー  `{back-end endpoint}` を前に取得したバックエンドのエンドポイントで置き換えます。

		NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

		- (void) retrieveSecurePayloadWithId:(int)payloadId completion:(void(^)(NSString*, NSError*)) completion;
		{
		    // check if authenticated
		    ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
		    NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
		    if (!authenticationHeader) return;


		    NSURLSession* session = [NSURLSession
		                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
		                             delegate:nil
		                             delegateQueue:nil];


		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"GET"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSLog(@"Received secure payload:%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

		            NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options:NSJSONReadingMutableContainers error:&error];

		            completion([json objectForKey:@"Payload"], nil);
		        }
		        else
		        {
		            NSLog(@"Error status:%ld, request:%@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}

	このメソッドは、共有設定に格納された資格情報によってアプリケーション バックエンドを呼び出して通知コンテンツを取得します。

4. ここでは、受信通知を処理し、上記のメソッドを使用して表示するコンテンツを取得する必要があります。最初に、プッシュ通知を受信するときに iOS アプリケーションがバックグラウンドで実行されるようにします。**XCode** で、左側のパネルのアプリケーション プロジェクトを選択し、中央のウィンドウの **[ターゲット]** セクションでメイン アプリケーション ターゲットをクリックします。

5. 次に、中央ウィンドウの上部で **[機能]** タブをクリックし、**[リモート通知]** チェック ボックスをオンにします。

	![][IOS1]


6. **AppDelegate.m** で、次のメソッドを追加してプッシュ通知を処理します。

		-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
		{
		    NSLog(@"%@", userInfo);

		    [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
		        if (!error) {
		            // show local notification
		            UILocalNotification* localNotification = [[UILocalNotification alloc] init];
		            localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
		            localNotification.alertBody = payload;
		            localNotification.timeZone = [NSTimeZone defaultTimeZone];
		            [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

		            completionHandler(UIBackgroundFetchResultNewData);
		        } else {
		            completionHandler(UIBackgroundFetchResultFailed);
		        }
		    }];

		}

	認証ヘッダー プロパティが不明な場合やバックエンドによって拒否された場合などに対応できるようにすることをお勧めします。こうしたケースに対する特定の処理は、ターゲット ユーザーのエクスペリエンスによって異なります。1 つのオプションとしては、ユーザーに通知と共に認証を求める一般的なプロンプトを表示して、実際の通知を取得する方法があります。

## アプリケーションの実行

アプリケーションを実行するには、以下の手順に従います。

1. XCode を使用して、物理 iOS デバイスでアプリケーションを実行します (プッシュ通知はシミュレーターでは機能しません）。

2. iOS アプリケーションの UI で、ユーザー名とパスワードを入力します。文字列は任意ですが、値は同じである必要があります。

3. iOS アプリケーションの UI で、**[ログイン]** をクリックします。次に、**[プッシュを送信する]** をクリックします。通知センターに安全な通知が表示されます。

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png

<!--HONumber=45--> 
