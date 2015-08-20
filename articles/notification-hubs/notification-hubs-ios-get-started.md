<properties
	pageTitle="Azure Notification Hubs の使用"
	description="Azure Notification Hubs を使用してプッシュ通知を行う方法について説明します。"
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article" 
	ms.date="05/27/2015"
	ms.author="wesmc"/>

# 通知ハブの使用

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概要

このトピックでは、Azure 通知ハブを使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple プッシュ通知サービス (APN) を使用してプッシュ通知を受信する空の iOS アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。

##前提条件

このチュートリアルの前提条件は次のとおりです。

+ [モバイル サービス iOS SDK]
+ [XCode 6][Install Xcode]
+ iOS 8 (またはこれ以降のバージョン) に対応したデバイス
+ iOS Developer Program メンバーシップ

   >[AZURE.NOTE]プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、シミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルを完了することは、iOS アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

> [AZURE.NOTE]このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)を参照してください。

[AZURE.INCLUDE [通知ハブ による Apple プッシュ通知の有効化](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##通知ハブを構成する

このセクションは、作成したプッシュ証明書を使用して、新しい通知ハブを作成して構成する方法について説明します。既に作成した通知ハブを使用する場合は、2 ～ 5 の手順を省略できます。


1. キーチェーン アクセス で、**[証明書]** カテゴリで作成した新しいプッシュ証明書を右クリックします。**[エクスポート]** をクリックし、ファイルに名前を付けて、**[.p12]** 形式を選択します。次に、**[保存]** をクリックします。

    ![][1]

	エクスポートした証明書のファイル名と場所を書き留めます。

	>[AZURE.NOTE]このチュートリアルでは QuickStart.p12 ファイルを作成します。ファイル名と場所は同じである必要はありません。

2. [Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

3. **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

   	![][2]

4. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][3]

5. 前の手順で作成した名前空間 (通常は ***通知ハブ名*-ns**)をクリックしてダッシュボードを開きます。

   	![][4]

6. 上部にある **[通知ハブ]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

   	![][5]

7. 上部にある **[構成]** タブをクリックし、Apple 通知設定の **[アップロード]** ボタンをクリックして、証明書の拇印をアップロードします。次に、前にエクスポートした **.p12** 証明書と、証明書のパスワードを選択します。**[運用]** プッシュ サービス (ストアからアプリケーションを購入したユーザーにプッシュ通知を送信する場合) と **[サンドボックス]** (開発時) プッシュ サービスのどちらを使用するかを選択してください。

   	![][6]

8. 上部にある **[ダッシュボード]** タブをクリックし、**[接続文字列の表示]** をクリックします。2 つの接続文字列をメモします。

   	![][7]

これで、通知ハブが APNS と連動するように構成されました。接続文字列にアプリケーションを登録し、通知を送信できます。

##通知ハブにアプリケーションを接続する

1. XCode で、新しい iOS プロジェクトを作成し、**[単一枠ビュー アプリケーション]** テンプレートを選択します。

   	![][8]

2. 新しいプロジェクトにオプションを設定する際には、以前に Apple Development ポータルで設定したバンドル ID で使用した同じ**製品名**と**組織 ID** を使用してください。

	![][11]

3. **[Target]** でプロジェクト名をクリックし、**[Build Settings]** タブをクリックして **[Code Signing Identity]** を展開し、**[Debug]** で**コード署名 ID** を選択します。**[Levels]** を **[Basic]** から **[All]** に切り替え、**[Provisioning Profile]** を以前に作成したプロビジョニング プロファイルにします。

	XCode で作成した新しいプロビジョニング プロファイルが表示されない場合は、メニュー バーで **[XCode]** をクリックして **[Preference]**、**[Account]** タブ、**[View Details]** ボタンの順にクリックし、署名 ID をクリックして右下隅にある更新ボタンをクリックし、署名 ID のプロファイルを更新してみてください。

   	![][9]

4. [Mobile Services iOS SDK] の**バージョン 1.2.4** をダウンロードしてファイルを解凍します。XCode でプロジェクトを右クリックして **[Add Files to]** オプションをクリックし、XCode プロジェクトに **WindowsAzureMessaging.framework** フォルダーを追加します。**[Copy items if needed]** を選択し、**[Add]** をクリックします。

   	![][10]

5. AppDelegate.h ファイルを開き、次の import ディレクティブを追加します。

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. AppDelegate.m ファイルで、iOS のバージョンに基づいて `didFinishLaunchingWithOptions` メソッド内に次のコードを追加します。このコードにより、APNS にデバイス ハンドルが登録されます。

	iOS 8 の場合:

	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
												UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

	iOS 8 より前のバージョンの場合:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


7. 同じファイルに次のメソッドを追加し、文字列リテラルのプレース ホルダーを *ハブ名* と前に記載した *DefaultListenSharedAccessSignature* で置き換えます。このコードは通知ハブにデバイス トークンを指定し、通知ハブが通知を送信できるようにします。

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<Enter your listen connection string>"
										notificationHubPath:@"<Enter your hub name>"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
				else {
				    [self MessageBox:@"Registration Status" message:@"Registered"];
				}
	    	}];
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}


8. 同じファイルで次のメソッドを追加し、アプリケーションがアクティブのときに通知を受信した場合に **UIAlert** を表示するようにします。


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

8. お使いのデバイスにアプリケーションを構築して実行し、エラーがないことを確認します。

## 通知の送信方法


以下の画面に示すように、通知ハブの [デバッグ] タブを使用して、Azure ポータルで通知を送信し、アプリケーションで通知の受信テストを行うことができます。

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. XCode で Main.storyboard を開き、次の UI コンポーネントをオブジェクト ライブラリから追加してユーザーがアプリでプッシュ通知を送信できるようにします。

	- ラベル テキストなしの [Label]。これは、通知の送信エラーのレポートに使用されます。**[Lines]** プロパティを **0** に設定して、左右の余白とビューの上部の制約を受けてサイズが自動で設定されるようにします。
	- **[Placeholder]** が **[Enter Notification Message]** に設定された [Text] フィールド。次に示すように、ラベルのすぐ下のフィールドを制限します。ビュー コントローラーをアウトレット デリゲートに設定します。
	- **[Send Notification]** というタイトルのボタンをテキスト フィールドのすぐ下と水平方向の中央に制限します。

	ビューは次のようになります。

	![][32]


2. ViewController.h ファイルを開き、次の `#import` ステートメントと `#define` ステートメントを追加します。プレースホルダーの文字列リテラルを実際の *DefaultFullSharedAccessSignature* 接続文字列と*ハブの名前*で置き換えます。


		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"


3. ビューに接続されたラベルとテキストのフィールドにアウトレットを追加し、`interface` の定義を更新して `UITextFieldDelegate` と `NSXMLParserDelegate` をサポートします。以下に示す 3 つのプロパティ宣言を追加し、REST API の呼び出しと応答の解析をサポートします。

	ViewController.h ファイルは次のようになります。

		#import <UIKit/UIKit.h>
		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"

		@interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
		{
			NSXMLParser *xmlParser;
		}

		// Make sure these outlets are connected to your UI by ctrl+dragging.
		@property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
		@property (weak, nonatomic) IBOutlet UILabel *sendResults;

		@property (copy, nonatomic) NSString *statusResult;
		@property (copy, nonatomic) NSString *currentElement;

		@end


4. ViewController.m を開き、次のコードを追加して *DefaultFullSharedAccessSignature* 接続文字列を解析します。[REST API リファレンス](http://msdn.microsoft.com/library/azure/dn495627.aspx) で説明したように、解析されたこの情報は*認証*要求ヘッダーの SAS トークンの生成に使用されます。

		NSString *HubEndpoint;
		NSString *HubSasKeyName;
		NSString *HubSasKeyValue;

		-(void)ParseConnectionString
		{
			NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
			NSString *part;

			if ([parts count] != 3)
			{
				NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
					reason:@"Invalid full shared access connection string" userInfo:nil];

				@throw parseException;
			}

			for (part in parts)
			{
				if ([part hasPrefix:@"Endpoint"])
				{
					HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
				}
				else if ([part hasPrefix:@"SharedAccessKeyName"])
				{
					HubSasKeyName = [part substringFromIndex:20];
				}
				else if ([part hasPrefix:@"SharedAccessKey"])
				{
					HubSasKeyValue = [part substringFromIndex:16];
				}
			}
		}

5. ViewController.m で、`viewDidLoad` メソッドを更新してビューの読み込み時に接続文字列を解析します。また、次に示すユーティリティ メソッドを追加します。


		- (void)viewDidLoad
		{
			[super viewDidLoad];
			[self ParseConnectionString];
		}

		-(NSString *)CF_URLEncodedString:(NSString *)inputString
		{
		   return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
				NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}





6. ViewController.m で、[REST API リファレンス](http://msdn.microsoft.com/library/azure/dn495627.aspx)で説明したように次のコードを追加して、*認証*ヘッダーに指定する SAS 認証トークンを生成します。

		-(NSString*) generateSasToken:(NSString*)uri
		{
			NSString *targetUri;
			NSString* utf8LowercasedUri = NULL;
			NSString *signature = NULL;
			NSString *token = NULL;

			@try
			{
				// Add expiration
				uri = [uri lowercaseString];
				utf8LowercasedUri = [self CF_URLEncodedString:uri];
				targetUri = [utf8LowercasedUri lowercaseString];
				NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
				int expiresInMins = 60; // 1 hour
				expiresOnDate += expiresInMins * 60;
				UInt64 expires = trunc(expiresOnDate);
				NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

				// Get an hmac_sha1 Mac instance and initialize with the signing key
				const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
				const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
				unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
				CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
				NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
				signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

				// construct authorization token string
				token = [NSString stringWithFormat:@"SharedAccessSignature sr=%@&sig=%@&se=%qu&skn=%@",
					targetUri, signature, expires, HubSasKeyName];
			}
			@catch (NSException *exception)
			{
				[self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
			}
			@finally
			{
				if (utf8LowercasedUri != NULL)
					CFRelease((CFStringRef)utf8LowercasedUri);
				if (signature != NULL)
				CFRelease((CFStringRef)signature);
			}

			return token;
		}


7. **[Send Notification]** ボタンから ViewController.m に **Ctrl キーを押しながらドラッグ**し、次のコードを使用して REST API 呼び出しを実行する**タッチ ダウン** イベントのアクションを追加します。

		- (IBAction)SendNotificationMessage:(id)sender
		{
			self.sendResults.text = @"";
			[self SendNotificationRESTAPI];
		}

		- (void)SendNotificationRESTAPI
		{
		    NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

			// Apple Notification format of the notification message
		    NSString *json = [NSString stringWithFormat:@"{"aps":{"alert":"%@"}}",
								self.notificationMessage.text];

			// Construct the messages REST endpoint
			NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
												HUBNAME, API_VERSION]];

			// Generated the token to be used in the authorization header.
			NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

			//Create the request to add the APNS notification message to the hub
			NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
			[request setHTTPMethod:@"POST"];
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

			// Signify apple notification format
			[request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

			//Authenticate the notification message POST request with the SaS token
			[request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

			//Add the notification message body
			[request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

			// Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
		        }
				if (data != NULL)
				{
		        	xmlParser = [[NSXMLParser alloc] initWithData:data];
		        	[xmlParser setDelegate:self];
		       		[xmlParser parse];
		    	}
		    }];
		    [dataTask resume];
		}


8. ViewController.m で、テキスト フィールドのキーボードを閉じる動作をサポートする次のデリゲート メソッドを追加します。テキスト フィールドからインターフェイス デザイナーのビュー コントローラー アイコンに **Ctrl キーを押しながらドラッグ**して、ビュー コントローラーをアウトレット デリゲートに設定します。

		//===[ Implement UITextFieldDelegate methods ]===

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
			[textField resignFirstResponder];
			return YES;
		}


9. ViewController.m で、`NSXMLParser` を使用して応答の解析をサポートする次のデリゲート メソッドを追加します。

		//===[ Implement NSXMLParserDelegate methods ]===

		-(void)parserDidStartDocument:(NSXMLParser *)parser
		{
		    self.statusResult = @"";
		}

		-(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
			namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
			attributes:(NSDictionary *)attributeDict
		{
		    NSString * element = [elementName lowercaseString];
		    NSLog(@"*** New element parsed : %@ ***",element);

		    if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
		    {
		        self.currentElement = element;
		    }
		}

		-(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
		{
		    self.statusResult = [self.statusResult stringByAppendingString:
		        [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
		}

		-(void)parserDidEndDocument:(NSXMLParser *)parser
		{
			// Set the status label text on the UI thread
			dispatch_async(dispatch_get_main_queue(),
			^{
				[self.sendResults setText:self.statusResult];
			});
		}



10. プロジェクトをビルドし、エラーがないことを確認します。



Apple の「[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]」に、使用できるすべての通知ペイロードが記載されています。



##アプリケーションのテスト

iOS でのプッシュ通知をテストするには、デバイスにアプリケーションをデプロイする必要があります。iOS シミュレーターを使用して Apple のプッシュ通知を送信することはできません。

1. アプリケーションを実行して登録が成功したことを確認したら、**[OK]** を押します。

	![][33]

2. テキスト フィールド内をタッチして通知メッセージを入力します。入力後、キーボードの **Send** キーまたはビュー内の **[Send Notification]** ボタンを押して、通知メッセージを送信します。

	![][34]

3. 通知を受け取るように登録しているすべてのデバイスに通知が送信されます。

	![][35]

このチュートリアルを向上させるために問題を指摘または推奨事項を提案するには、以下のディスカッション セクションにコメントを残してください。


##次のステップ

この簡単な例では、すべての iOS デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要]」を参照してください。



<!-- Images. -->

[1]: ./media/notification-hubs-ios-get-started/notification-hubs-export-cert-p12.png
[2]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal.png
[3]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal2.png
[4]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal.png
[5]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal2.png
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-connection-strings.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[モバイル サービス iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure 管理ポータル]: https://manage.windowsazure.com/
[Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Mobile Services でのプッシュ通知の使用]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Notification Hubs を使用したユーザーへのプッシュ通知]: notification-hubs-aspnet-backend-ios-notify-users.md
[通知ハブを使用したニュース速報の送信]: notification-hubs-ios-send-breaking-news.md

[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
 

<!----HONumber=August15_HO6-->