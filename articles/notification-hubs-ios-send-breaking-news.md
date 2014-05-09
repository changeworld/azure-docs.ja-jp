<properties linkid="develop-notificationhubs-tutorials-send-breaking-news-ios" urlDisplayName="ニュース速報" pageTitle="通知ハブ ニュース速報チュートリアル - iOS" metaKeywords="" description="Azure のサービス バス通知ハブを使用してニュース速報通知を iOS デバイスに送信する方法について説明します。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="通知ハブを使用したニュース速報の送信" authors="ricksal" solutions="" manager="" editor="" />

# 通知ハブを使用したニュース速報の送信
<div class="dev-center-tutorial-selector sublanding">	
	<a href="/ja-jp/manage/services/notification-hubs/breaking-news-dotnet" title="Windows ストア C#" >Windows ストア C#</a><a href="/ja-jp/manage/services/notification-hubs/breaking-news-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/manage/services/notification-hubs/breaking-news-ios" title="iOS" class="current">iOS</a>
</div>

このトピックでは、Azure 通知ハブを使用してニュース速報通知を iOS アプリケーションにブロードキャストする方法について説明します。完了すると、興味のあるニュース速報カテゴリに登録し、それらのカテゴリのプッシュ通知だけを受信できるようになります。このシナリオは、既に興味があると宣言しているユーザーのグループに通知を送信する必要がある多くのアプリケーション (RSS リーダー、音楽ファン向けアプリケーションなど) で一般的なパターンです。

ブロードキャスト シナリオは、通知ハブでの登録の作成時に 1 つ以上の _tags_ を追加することで有効にします。通知がタグに送信されると、タグに登録されたすべてのデバイスが通知を受信します。タグは文字列にすぎないため、事前にプロビジョニングする必要はありません。タグの詳細については、「[通知ハブの概要]」を参照してください。

このチュートリアルでは、このシナリオを有効にするための、次の基本的な手順について説明します。

1. [アプリケーションにカテゴリ選択を追加する]
2. [通知を登録する]
3. [バックエンドから通知を送信する]
4. [アプリケーションを実行して通知を生成する]

このトピックは、「[通知ハブの使用][get-started]」で作成したアプリケーションが基になります。このチュートリアルを開始する前に、「[通知ハブの使用][get-started]」を完了している必要があります。

##<a name="adding-categories"></a>アプリケーションにカテゴリ選択を追加する

最初の手順として、既存のストーリーボードに UI 要素を追加して、ユーザーが登録するカテゴリを選択できるようにします。ユーザーにより選択されるカテゴリは、デバイスに格納されます。アプリが起動すると、通知ハブにデバイス登録が作成され、選択されたカテゴリがタグとして追加されます。

2. MainStoryboard_iPhone.storyboard で、オブジェクト ライブラリから次のコンポーネントを追加します。
	+ "Breaking News" というテキストが付いたラベル
	+ "World"、"Politics"、"Business"、"Technology"、"Science"、"Sports" というカテゴリ テキストが付いたラベル
	+ 6 つのスイッチ (カテゴリごとに 1 つずつ)
	+ "Subscribe" というラベルが付いたオン ボタン
	
	ストーリーボードは次のようになります。
	
	![][3]
    
3. アシスタント エディターで、すべてのスイッチのアウトレットを作成し、"WorldSwitch"、"PoliticsSwitch"、"BusinessSwitch"、"TechnologySwitch"、"ScienceSwitch"、"SportsSwitch" という名前にします。

	![][4]

4. "subscribe" というボタンのアクションを作成します。BreakingNewsViewController.h は次のような内容にします。
			
		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

5. `Notifications` という新しいクラスを作成します。Notifications.h の interface セクションに次のコードをコピーします。

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
		- (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

6. 次の import ディレクティブを Notifications.m に追加します。

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

7. Notifications.m の implementation セクションに次のコードをコピーします。
		
		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    
		    [self subscribeWithCategories:categories completion:completion];
		}

		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];
		    
		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}
 
	このクラスは、このデバイスが受信するニュースのカテゴリを格納するためにローカル ストレージを使用します。ローカル ストレージには、これらのカテゴリを登録するメソッドも格納されます。

4. 上のコードで、`<hub name>` と `<connection string with listen access>` のプレースホルダーを、通知ハブの名前と既に取得してある *DefaultListenSharedAccessSignature* の接続文字列に置き換えます。

	<div class="dev-callout"><strong>注</strong>
		<p>クライアント アプリケーションを使用して配布される資格情報は一般にセキュリティで保護されないため、クライアント アプリケーションではリッスン アクセス用のキーだけを配布してください。リッスン アクセスにより、アプリケーションが通知を登録できるようになりますが、既存の登録を変更することはできないため、通知を送信できません。通知を送信して既存の登録を変更するセキュリティで保護されたバックエンド サービスでは、フル アクセス キーが使用されます。</p>
	</div> 

8. BreakingNewsAppDelegate.h ファイルで、次のプロパティを追加します。

		@property (nonatomic) Notifications* notifications;

	これにより、AppDelegate で Notification クラスのシングルトン インスタンスが作成されます。
	
9. BreakingNewsAppDelegate.m の **didFinishLaunchingWithOptions** メソッドで、**registerForRemoteNotificationTypes** の前に次のコードを追加します。
	
		self.notifications = [[Notifications alloc] init];
					   
	Notification シングルトンが初期化されます。

10. BreakingNewsAppDelegate.m の **didRegisterForRemoteNotificationsWithDeviceToken** メソッドで、**registerNativeWithDeviceToken** への呼び出しを削除し、次のコードを追加します。
	
		self.notifications.deviceToken = deviceToken;

	この時点では、**didRegisterForRemoteNotificationsWithDeviceToken** メソッドに他のコードが存在しない点に注目してください。

11.	BreakingNewsAppDelegate.m に次のメソッドを追加します。

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
		    [alert show];
	    }

	このメソッドにより、簡単な **UIAlert** を表示することでアプリケーションの実行中に受信した通知が処理されます。

9. BreakingNewsViewController.m で、次のコードを XCode-generated **subscribe** メソッドにコピーします。

		NSMutableArray* categories = [[NSMutableArray alloc] init];
	    
	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];
	    
	    Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
	    
	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];
	
	このメソッドは、カテゴリの **NSMutableArray** を作成し、**Notifications** クラスを使用してそのリストをローカル ストレージに格納し、対応するタグを通知ハブに登録します。カテゴリが変更されると、新しいカテゴリで登録が再作成されます。

これで、アプリケーションがデバイス上のローカル ストレージに一連のカテゴリを格納したり、ユーザーがカテゴリの選択を変更したときに通知ハブに登録できるようになりました。

##<a name="register"></a>通知を登録する

この手順では、ローカル ストレージに格納されたカテゴリを使用して、起動時に通知ハブに通知します。

<div class="dev-callout"><strong>注</strong>
	<p>Apple Push Notification Service (APNS) によって割り当てられたデバイス トークンはいつでも変更される可能性があるので、通知エラーを回避するために通知を頻繁に登録してください。この例では、アプリケーションが起動するたびに通知を登録します。頻繁に実行されるアプリケーションの場合 (1 日に複数回など)、帯域幅を節約するため、前回の登録から 1 日経過していない場合は登録をスキップできます。</p>
</div>  

1. Notifications.h の interface セクションに次のメソッドを追加します。

		- (NSSet*)retrieveCategories;

	このコードは、Notifications クラス内のカテゴリを取得します。

2. Notifications.m ファイルに対応する実装を追加します。
	
		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    
		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];
		    
		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}

2. **didRegisterForRemoteNotificationsWithDeviceToken** メソッドに次のコードを追加します。

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
    
	    NSSet* categories = [notifications retrieveCategories];
	    [notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

	これにより、アプリケーションが起動するたびに、ローカル ストレージからカテゴリを取得し、これらのカテゴリの登録を要求するようになります。

3. BreakingNewsViewController.h で、**viewDidLoad** メソッドに次のコードを追加します。

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
    
	    NSSet* categories = [notifications retrieveCategories];
	    
	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

	これにより、以前に保存されたカテゴリの状態に基づいて起動時に UI が更新されます。

これで、アプリケーションが完成し、デバイスのローカル ストレージに一連のカテゴリを格納できるようになりました。ローカル ストレージは、ユーザーがカテゴリの選択を変更したときに通知ハブに登録するために使用されます。次に、このアプリケーションにカテゴリ通知を送信できるバックエンドを定義します。

<h2><a name="send"></a><span class="short-header">通知を送信する</span>バックエンドから通知を送信する</h2>

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>アプリケーションを実行して通知を生成する

1. [Run] ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。

	![][2] 

	アプリケーションの UI には、購読するカテゴリを選択できる一連の切り替えボタンが表示されている点に注目してください。

2. 1 つ以上のカテゴリ切り替えボタンを有効にし、**[購読]** をクリックします。

	**[購読]** を選択すると、アプリケーションにより、選択されたカテゴリがタグに変換され、選択されたタグの新しいデバイス登録が通知ハブから要求されます。

4. 新しい通知は、次のいずれかの方法でバックエンドから送信します。

	+ **コンソール アプリケーション:** コンソール アプリケーションを開始します。

	+ **モバイル サービス:** **[スケジューラ]** タブをクリックしてジョブをクリックし、**[一度だけ実行する]** をクリックします。

5. 選択されたカテゴリの通知がトースト通知として表示されます。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、ニュース速報をカテゴリごとにブロードキャストする方法について説明しました。他の高度な通知ハブ シナリオを取り上げている、次のいずれかのチュートリアルを行うことをお勧めします。

+ **[通知ハブを使用したローカライズ ニュース速報のブロードキャスト]**

	ニュース速報アプリケーションを拡張して、ローカライズした通知を送信できるようにする方法について説明します。

+ **[通知ハブによるユーザーへの通知]**

	認証された特定のユーザーにプッシュ通知する方法について説明します。これは、特定のユーザーにのみ通知を送信する場合に適したソリューションです。

<!-- Anchors. -->
[アプリケーションにカテゴリ選択を追加する]: #adding-categories
[通知を登録する]: #register
[バックエンドから通知を送信する]: #send
[アプリケーションを実行して通知を生成する]: #test-app
[次のステップ]: #next-steps

<!-- Images. -->
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
[4]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png







<!-- URLs. -->
[方法: サービス バス通知ハブ (iOS アプリケーション)]: http://msdn.microsoft.com/ja-jp/library/jj927168.aspx
[通知ハブを使用したローカライズ ニュース速報のブロードキャスト]: /ja-jp/manage/services/notification-hubs/breaking-news-localized-dotnet/
[モバイル サービス]: /ja-jp/develop/mobile/tutorials/get-started
[通知ハブによるユーザーへの通知]: /ja-jp/manage/services/notification-hubs/notify-users/

[Azure の管理ポータル]: https://manage.windowsazure.com/
[通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
[方法: Azure 通知ハブ (iOS アプリケーション)]: http://msdn.microsoft.com/ja-jp/library/jj927168.aspx
[作業を開始する]: /ja-jp/manage/services/notification-hubs/get-started-notification-hubs-ios/


