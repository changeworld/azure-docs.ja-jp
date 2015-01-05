<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs によるユーザーへの通知" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="yuaxu" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="11/22/2014" ms.author="yuaxu" />

#Azure Notification Hubs によるユーザーへの通知

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/" title="Windows Universal">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
		<a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。このチュートリアルでは、Azure Notification Hubs を使用して特定のデバイスで特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。ガイダンス トピック「[アプリ バックエンドからの登録]」に示すように、ASP.NET WebAPI バックエンドを使用してクライアントを認証し、通知を生成します(http://msdn.microsoft.com/ja-jp/library/dn743807.aspx)。

> [AZURE.NOTE] このチュートリアルでは、「[通知ハブの使用 (iOS)](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-ios-get-started/)」の説明に従って通知ハブが作成され、構成されていることを前提にしています。また、このチュートリアルは、「[Azure Notification Hubs の安全なプッシュ (iOS)](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/) 」の前提条件でもあります。
> バックエンド サービスとして Mobile Services を使用している場合は、このチュートリアルの [Mobile Services バージョン](/ja-jp/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/) を参照してください。


## 通知ハブを作成し構成する

「[通知ハブの使用 (iOS)](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-ios-get-started/)」のセクション 1 から 5 の手順に従います。iOS デバイスのプロビジョニングの詳細については、「[Big Nerd Ranch](http://www.bignerdranch.com/we-teach/how-to-prepare/ios-device-provisioning.html)」にあるガイドを参照してください。

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## iOS アプリを変更する

1. 「[通知ハブの使用 (iOS)](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-ios-get-started/)」のセクション 1 から 5 の手順に従って、単一枠ビュー アプリケーションを開きます。

> [AZURE.NOTE] このセクションでは、プロジェクトの組織名は空で構成されていると想定しています。そうでない場合は、すべてのクラス名の前にその組織名を付けてください。

2. Main.storyboard で、オブジェクト ライブラリから次のコンポーネントを追加します。
	+ プレースホルダー テキスト **Username** を含む UITextField
	+ プレースホルダー テキスト **Password** を含む UITextField。[Attribute Inspector] の [Textfield Return Key] の下の **[Secure]** オプションのチェック ボックスをオンにします。
	+ 「**1. Log in**」ラベルの付いた UIButton。[Attributes Inspector] の [Control] 内の [Content] にある **[Enabled]** オプションのチェック ボックスをオフにします。
	+ 「**2. Send Push**」ラベルの付いた UIButton。**[Enabled]** オプションのチェック ボックスをオフにします。

	ストーリーボードは次のようになります。

    ![][IOS1]

3. ViewController.m のインターフェイス部分で UITextFields と UIButtons の両方にアウトレットを作成します。

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

		- (IBAction)LogInAction:(id)sender;
		- (IBAction)SendPushAction:(id)sender;

4. 最初に、バックエンドとインターフェイスする RegisterClient を作成します。NSObject から継承する RegisterClient という Objective-C クラスを作成します。次に、RegisterClient.h インターフェイス セクションに次のコードを追加します。

		@property (strong, nonatomic) NSString* authenticationHeader;
		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5. RegisterClient.m で次のインターフェイス セクションを追加します。

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

		@end

6. その後、RegisterClient.m 実装セクションに次のコードを追加して、*{backend endpoint}* プレースホルダーを、前のセクションでアプリケーション バックエンドをデプロイするために使用した宛先 URL と置き換えます。

		NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
		NSString *const BackEndEndpoint = @"{backend endpoint}/api/register";

		- (instancetype)init
		{
		    self = [super init];
		    if (self) {
		        NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
		        _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
		    }
		    return self;
		}

		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;
		{
		    [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
		}

		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		{
		    NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

		    NSString *deviceTokenString = [[token description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
		    deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""] uppercaseString];

		    [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString completion:^(NSString* registrationId, NSError *error) {
		        NSLog(@"regId: %@", registrationId);
		        if (error) {
		            completion(error);
		            return;
		        }

		        [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
		            if (error) {
		                completion(error);
		                return;
		            }

		            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
		            if (httpResponse.statusCode == 200) {
		                completion(nil);
		            } else if (httpResponse.statusCode == 410 && retry) {
		                [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
		            } else {
		                NSLog(@"Registration error with response status: %ld", (long) httpResponse.statusCode);

		                completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
		            }

		        }];
		    }];
		}

		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSData*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;
		{
		    NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token, @"Tags": [tags allObjects]};
		    NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration options:NSJSONWritingPrettyPrinted error:nil];

		    NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding]);

		    NSString* endpoint = [NSString stringWithFormat:@"%@/%@", BackEndEndpoint, registrationId];
		    NSURL* requestURL = [NSURL URLWithString:endpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"PUT"];
		    [request setHTTPBody:jsonData];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
		    [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        if (!error)
		        {
		            completion(response, error);
		        }
		        else
		        {
		            NSLog(@"Error request: %@", error);
		            completion(nil, error);
		        }
		    }];
		    [dataTask resume];
		}

		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		{
		    NSString* registrationId = [[NSUserDefaults standardUserDefaults] objectForKey:RegistrationIdLocalStorageKey];

		    if (registrationId)
		    {
		        completion(registrationId, nil);
		        return;
		    }

		    // request new one & save
		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@?handle=%@", BackEndEndpoint, token]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSString* registrationId = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

		            // remove quotes
		            registrationId = [registrationId substringWithRange: NSMakeRange(1, [registrationId length]-2)];

		            [[NSUserDefaults standardUserDefaults] setObject:registrationId forKey:RegistrationIdLocalStorageKey];
		            [[NSUserDefaults standardUserDefaults] synchronize];

		            completion(registrationId, nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}

	上記のコードは、ガイダンス記事「[アプリ バックエンドからの登録](http://msdn.microsoft.com/ja-jp/library/dn743807.aspx) 」で説明したロジックを実装します。この記事では、アプリケーション バックエンドへの REST 呼び出しを実行するための NSURLSession、および通知ハブによって返される registrationId をローカルに格納するための NSUserDefaults の使用について解説しています。

	このクラスが適切に機能するためには、プロパティ **authorizationHeader** を設定する必要があります。このプロパティは、ログイン後に **ViewController** クラスによって設定されます。

7. ViewController.h で、デバイス トークンおよび RegisterClient インスタンスへの参照を含む次の宣言を追加します。

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. ViewController.m で、ViewController クラスを UITextFieldDelegate にします。その後、プライベート メソッド宣言を追加します。

		@interface ViewController () <UITextFieldDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;

		@end

> [AZURE.NOTE] 次のスニペットは安全な認証スキームではないため、**createAndSetAuthenticationHeaderWithUsername:AndPassword:** の実装を、登録クライアント クラス (OAuth、Active Directory など) によって読み取られる認証トークンを生成する特定の認証メカニズムで置き換えます。

9. その後で、ViewController.m の実装セクションに次のコードを追加します。

		-(void) setDeviceToken: (NSData*) deviceToken
		{
		    _deviceToken = deviceToken;
		    self.LogInButton.enabled = YES;
		}

		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
		{
		    NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

		    NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

		    self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData encoding:NSUTF8StringEncoding];
		}

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
		    [textField resignFirstResponder];
		    return YES;
		}

	ログイン ボタンを有効にするデバイス トークンの設定方法に注意してください。これは、ログイン アクションの一部として、View Controller がアプリケーション バックエンドでプッシュ通知を登録するためです。そのため、デバイス トークンが適切に設定される前にユーザーがログイン ボタンを押すのを防ぐ必要があります。プッシュ登録の前にログインが発生する場合には、プッシュ登録からログインを切り離す必要があります。

10. ViewController.m でバックエンド エンドポイントの定数を追加し、次のスニペットを使用して UIButtons のアクション メソッドを実装します。プレースホルダーのバックエンド エンドポイントを、前のセクションで使用した宛先 URL で置き換えます。

		- (IBAction)LogInAction:(id)sender {
		    // create authentication header and set it in register client
		    NSString* username = self.UsernameField.text;
		    NSString* password = self.PasswordField.text;

		    [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

		    __weak ViewController* selfie = self;
		    [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil andCompletion: ^(NSError* error) {
		        if (!error) {
		            dispatch_async(dispatch_get_main_queue(), ^{
		                selfie.SendPushButton.enabled = YES;

		                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Success" message:
		                                      @"Registered successfully!" delegate:nil cancelButtonTitle:
		                                      @"OK" otherButtonTitles:nil, nil];
		                [alert show];
		            });
		        }
		    }];
		}

		NSString *const SendNotificationEndpoint = @"{backend endpoint}/api/notifications";

		- (IBAction)SendPushAction:(id)sender {
		    NSURLSession* session = [NSURLSession
		                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
		                             delegate:nil
		                             delegateQueue:nil];


		    NSURL* requestURL = [NSURL URLWithString:SendNotificationEndpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.registerClient.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSLog(@"Error status: %d, request: %@", httpResponse.statusCode, error);
		        }
		    }];
		    [dataTask resume];
		}

11. **ViewDidLoad** 関数で次のコードを追加して、RegisterClient インスタンスをインスタンス化し、テキスト フィールドに対するデリゲートを設定します。

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.registerClient = [[RegisterClient alloc] init];

12. **AppDelegate.m** 内の **application:didRegisterForPushNotificationWithDeviceToken:** メソッドの内容をすべて削除して以下で置き換え、View Controller に APN から取得した最新のデバイス トークンが含まれることを確認します。

	    ViewController* rvc = (ViewController*) self.window.rootViewController;
	    rvc.deviceToken = deviceToken;

13. 最後に、**AppDelegate.m** に次のメソッドが含まれることを確認してください。

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

## アプリケーションの実行

1. XCode を使用して、物理 iOS デバイスでアプリケーションを実行します (プッシュ通知はシミュレーターでは機能しません）。

2. iOS アプリケーションの UI で、ユーザー名とパスワードを入力します。文字列は任意ですが、値は同じである必要があります。その後、**[Log In]** をクリックします。

3. 登録の成功を通知するポップアップが表示されます。**[OK]** をクリックします。

4. **[Send push]** をクリックし、ホーム ボタンを押します。すぐにプッシュ通知が表示されます。


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png

<!--HONumber=35.1-->
