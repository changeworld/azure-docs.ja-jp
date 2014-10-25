<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Azure Notification Hubs によるユーザーへの通知

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ja-jp/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows ユニバーサル">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
<a href="/ja-jp/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。このチュートリアルでは、Azure Notification Hubs を使用して特定のデバイスで特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。ガイダンス トピック「[アプリ バックエンドからの登録][アプリ バックエンドからの登録]」に示すように、ASP.NET WebAPI バックエンドを使用してクライアントを認証し、通知を生成します。このチュートリアルは、「**Notification Hubs の使用**」チュートリアルで作成した通知ハブが基になっています。

また、「**安全なプッシュ**」チュートリアルの前提条件でもあります。この「**ユーザーへの通知**」チュートリアルの手順を完了した後は、**ユーザーへの通知**のコードを変更してプッシュ通知を安全に送信する方法を示した「**安全なプッシュ**」チュートリアルに進むことができます。

> [AZURE.NOTE] このチュートリアルでは、「[Getting Started with Notification Hubs (iOS) (Notification Hubs (iOS) の使用)][Getting Started with Notification Hubs (iOS) (Notification Hubs (iOS) の使用)]」での説明に従って通知が作成され、構成されていると想定しています。

## 通知ハブを作成し構成する

このチュートリアルを開始する前に、iOS プロビジョニング プロファイルと開発プッシュ証明書を作成してから、Azure の通知ハブを作成しアプリケーションに接続する必要があります。「[通知ハブの使用][Getting Started with Notification Hubs (iOS) (Notification Hubs (iOS) の使用)]」の手順に従ってください。特にセクション 1 から 5 が重要です。

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][notification-hubs-aspnet-backend-notifyusers]]

## iOS アプリを変更する

1.  「[通知ハブの使用][Getting Started with Notification Hubs (iOS) (Notification Hubs (iOS) の使用)]」のセクション 1 から 5 の手順に従って、単一ページの表示アプリケーションが通知ハブからプッシュ通知を受信できるようにします。

> [AZURE.NOTE] このセクションでは、プロジェクトの組織名は空で構成されていると想定しています。そうでない場合は、以下のコードのすべてのクラス名の前にその組織名を付けてください。

1.  Main.storyboard で、オブジェクト ライブラリから次のコンポーネントを追加します。

    -   プレースホルダー テキスト **Username** を含む UITextField
    -   プレースホルダー テキスト **Password** を含む UITextField。右側のウィンドウの TextField プロパティ グループの **[安全なテキスト エントリ]** チェックボックスをオンにします。
    -   **"1. ログイン"** ラベルの付いた UIButton。右側のウィンドウの Accessibility プロパティ グループの **[有効]** チェックボックスをオフにします。
    -   **"2. プッシュの送信"** ラベルの付いた UIButton。右側のウィンドウの Accessibility プロパティ グループの **[有効]** チェックボックスをオフにします。

    ストーリーボードは次のようになります。

    ![][]

2.  ViewController.m のインターフェイス部分で UITextFields と UIButtons の両方にアウトレットを作成します。

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

3.  ViewController.m 実装の両方のボタンに対してアクションを作成します。
		- (IBAction)LogInAction:(id)sender { }
		- (IBAction)SendPushAction:(id)sender { }

4.  最初に、バックエンドとインターフェイスする RegisterClient を作成します。NSObject から継承する RegisterClient という Objective-C クラスを作成します。次に、RegisterClient.h インターフェイス セクションに次のコードを追加します。

        @property (strong, nonatomic) NSString* authenticationHeader;
        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5.  RegisterClient.m で次のインターフェイス セクションを追加します。

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

        @end

6.  その後、実装セクションに次のコードを追加して、*{backend endpoint}* プレースホルダーを、前のセクションでアプリケーション バックエンドをデプロイするために使用したエンドポイントと置き換えます。

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

    上記のコードは、アプリケーション バックエンドへの REST 呼び出しを実行するための NSURLSession、および通信ハブによって返される registrationId をローカルに格納するための NSUserDefaults の使用についてのガイダンス記事「[アプリ バックエンドからの登録][アプリ バックエンドからの登録]」で説明したロジックを実装します。

    このクラスが適切に機能するためには、プロパティ **authorizationHeader** を設定する必要があります。このプロパティは、ログイン後に **ViewController** クラスによって設定されます。

7.  ViewController.h でデバイス トークンを含む次のプロパティを追加します。

        @property (strong, nonatomic) NSData* deviceToken;

8.  ViewController.m では、ViewController クラスを UITextFieldDelegate にし、RegisterClient インスタンスを参照するプロパティを追加して、プライベート メソッド宣言を追加します。インターフェイス セクションは次のようになります。

        @interface ViewController () <UITextFieldDelegate>
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

        @property (strong, nonatomic) RegisterClient* registerClient;

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        @end

    プライベート メソッドを使用して、アプリケーション バックエンドで基本認証を実行する認証ヘッダーを作成します。

> [AZURE.NOTE] これは安全な認証スキームではないため、**createAndSetAuthenticationHeaderWithUsername:AndPassword:** の実装を、登録クライアント クラス (OAuth、Active Directory など) によって読み取られる認証トークンを生成する特定の認証メカニズムで置き換えます。

1.  その後で、ViewController.m の実装セクションに次のコードを追加します。

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

    ログイン ボタンを有効にするデバイス トークンの設定方法に注意してください。これは、ログイン アクションの一部として、View Controller がアプリケーション バックエンドでプッシュ通知を登録するためです。そのため、デバイス トークンが適切に設定される前にユーザーがログイン ボタンを押すのを防ぐ必要があります。アプリケーションによっては、プッシュ登録の前にログインが発生する場合には、プッシュ登録からログインを切り離す必要があります。

2.  ViewController.m でバックエンド エンドポイントの定数を追加し、UIButtons のアクション メソッドの実装を入力します。プレースホルダーを前のセクションで使用したバックエンド エンドポイントで置き換えるのを忘れないでください。

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

3.  最後に、**ViewDidLoad** で次のコードを追加して、RegisterClient インスタンスをインスタンス化し、テキスト フィールドに対するデリゲートを設定します。

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.registerClient = [[RegisterClient alloc] init];

4.  これで、**AppDelegate.m** はメソッド **application:didRegisterForPushNotificationWithDeviceToken:** の内容をすべて削除し、以下で置き換えます。

        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;

    これにより、View Controller には APN から取得した最新のデバイス トークンが含まれるようになります。

5.  **AppDelegate.m** に次のメソッドが含まれることを確認してください。

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

## アプリケーションの実行

アプリケーションを実行するには、以下の手順に従います。

1.  **AppBackend** が Azure にデプロイされていることを確認します。Visual Studio を使用している場合は、**AppBackend** Web API アプリケーションを実行します。ASP.NET Web ページが表示されます。

2.  XCode を使用して、物理 iOS デバイスでアプリケーションを実行します (プッシュ通知はシミュレーターでは機能しません）。

3.  iOS アプリケーションの UI で、ユーザー名とパスワードを入力します。文字列は任意ですが、値は同じである必要があります。

4.  iOS アプリケーションの UI で、**[ログイン]** をクリックします。次に、**[プッシュを送信する]** をクリックします。

  [Windows ユニバーサル]: /ja-jp/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows ユニバーサル"
  [iOS]: /ja-jp/documentation/articles/notification-hubs-/ "iOS"
  [Android]: /ja-jp/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [アプリ バックエンドからの登録]: http://msdn.microsoft.com/en-us/library/dn743807.aspx
  [Getting Started with Notification Hubs (iOS) (Notification Hubs (iOS) の使用)]: http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-ios-get-started/
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
