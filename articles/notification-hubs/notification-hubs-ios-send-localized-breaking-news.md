<properties 
	pageTitle="Notification Hubs ローカライズ ニュース速報のチュートリアル (iOS 向け)" 
	description="Azure Service Bus Notification Hubs を使用して、ローカライズしたニュース速報通知を送信する方法を説明します (iOS)。" 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="wesmc"/>
# Notification Hubs を使用した iOS デバイスへのローカライズ ニュース速報の送信

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#">Windows Store C#</a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS" class="current">iOS</a>
</div>

##概要

このトピックでは、Azure Notification Hubs の**テンプレート**機能を使用して、言語およびデバイスごとにローカライズしたニュース速報通知をブロードキャストする方法について説明します。このチュートリアルでは、「[Notification Hubs を使用したニュース速報の送信]」で作成した Windows ストア アプリケーションを使用します。完了すると、興味のあるニュース速報カテゴリに登録して受信する通知の言語を指定し、選択したカテゴリのその言語のプッシュ通知だけを受信できるようになります。


このシナリオは次の 2 つに分けられます。

- iOS アプリケーションで、クライアント デバイスが言語を指定し、さまざまなニュース速報カテゴリを購読できるようになります。

- Azure Notification Hubs の**タグ**機能と**テンプレート**機能を使用して、バックエンドから通知がブロードキャストされます。



##前提条件

「[Notification Hubs を使用したニュース速報の送信]」のチュートリアルを完了し、コードが使用可能な状態になっている必要があります。このチュートリアルでは、そのコードに基づいているためです。

Visual Studio 2012 も必要です。



##テンプレートの概念

「[Notification Hubs を使用したニュース速報の送信]」では、**タグ**を使用してさまざまなニュース カテゴリの通知を購読するアプリケーションを構築しました。
しかし、多くのアプリケーションは複数の市場をターゲットとしており、ローカライズが必要です。これは、通知自体の内容をローカライズし、適切なデバイス セットに配信する必要があることを意味します。
このトピックでは、Notification Hubs の**テンプレート**機能を使用して、ローカライズしたニュース速報通知を簡単に配信する方法について説明します。

注: ローカライズした通知を送信する 1 つの方法として、各タグの複数のバージョンを作成する方法があります。たとえば、ワールド ニュースで英語、フランス語、標準中国語をサポートするには、3 つのタグ("world_en"、"world_fr"、"world_ch") が必要です。その後、ワールド ニュースのローカライズ バージョンをこれらの各タグに送信する必要があります。このトピックでは、テンプレートを使用することでタグの増加を抑制し、複数のメッセージを送信しなくてもよいようにします。

大まかに言えば、テンプレートとは、特定のデバイスが通知をどのように受信するかを特定するための手段です。テンプレートは、アプリケーション バックエンドにより送信されるメッセージの一部となっているプロパティを参照することで、正確なペイロードを指定します。このケースでは、サポートされるすべての言語を含む、ロケールにとらわれないメッセージを送信します。

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

次に、デバイスが、適切なプロパティを参照するテンプレートを登録するようにします。たとえば、フランス語ニュースを登録する iOS アプリケーションは、次のテンプレートを登録します。

	{
		aps:{
			alert: "$(News_French)"
		}
	}

テンプレートは有用な機能です。詳細については、「[Notification Hubs の概要]」の記事を参照してください。テンプレート式言語のリファレンスは、「[方法:Service Bus Notification Hubs (iOS Apps)]」にあります。

##アプリケーションのユーザー インターフェイス

ここでは、「[Notification Hubs を使用したニュース速報の送信]」で作成したニュース速報アプリケーションを変更し、テンプレートを使用してローカライズしたニュース速報を送信します。


MainStoryboard_iPhone.storyboard で、サポートする 3 つの言語(英語、フランス語、標準中国語) を備えた Segmented Control を追加します。

![][13]

次に、以下に示すように ViewController.h に IBOutlet を追加します。

![][14]

##iOS アプリケーションを構築する

クライアント アプリケーションがローカライズしたメッセージを受信できるようにするには、 *ネイティブ*登録 (つまり、テンプレートを指定する登録) をテンプレート登録に置き換える必要があります。

1. Notification.h で、 *retrieveLocale* メソッドを追加し、以下に示すように store メソッドと subscribe メソッドを変更します。

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	Notification.m で、ロケール パラメーターを追加してユーザーの既定の設定に格納することで、 *storeCategoriesAndSubscribe* メソッドを変更します。

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	次に、 *subscribe* メソッドを変更してロケールを追加します。

		- (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

		    NSString* localeString;
		    switch (locale) {
		        case 0:
		            localeString = @"English";
		            break;
		        case 1:
		            localeString = @"French";
		            break;
		        case 2:
		            localeString = @"Mandarin";
		            break;
		    }

		    NSString* template = [NSString stringWithFormat:@"{"aps":{"alert":"$(News_%@)"},"inAppMessage":"$(News_%@)"}", localeString, localeString];

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	ここで、*registerNativeWithDeviceToken* の代わりに  *registerTemplateWithDeviceToken* メソッドをどのように使用しているかに注目してください。テンプレートを登録するときは、json テンプレートに加えて、テンプレートの名前も指定する必要があります (アプリケーションにより複数のテンプレートが登録されることがあるため)。それらのニュースの通知を確実に受信できるようにするため、カテゴリをタグとして登録してください。

	最後に、ユーザーの既定の設定からロケールを取得するメソッドを追加します。

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

3. ここまでで、Notifications クラスを変更したので、ViewController が新しい UISegmentControl を使用することを確認する必要があります。 *viewDidLoad* メソッドに次の行を追加し、現在選択されているロケールが表示されるようにします。

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	次に、 *subscribe* メソッドで、 *storeCategoriesAndSubscribe* の呼び出しを次のように変更します。

		[notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

4. 最後に、AppDelegate.m の *didRegisterForRemoteNotificationsWithDeviceToken* メソッドを更新して、アプリケーションの起動時に登録を正しく更新できるようにします。通知の  *subscribe* メソッドの呼び出しを、次のように変更します。

		NSSet* categories = [notifications retrieveCategories];
	    int locale = [notifications retrieveLocale];
	    [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

##バックエンドからローカライズした通知を送信する

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]


## 次のステップ

テンプレートの使用方法の詳細については、以下を参照してください。

- [Notification Hubs によるユーザーへの通知:ASP.NET]
- [Notification Hubs によるユーザーへの通知:Mobile Services]
- [Notification Hubs の概要]

テンプレート式言語のリファレンスは、「[方法: Windows Azure 通知ハブ (iOS アプリ)]」にあります。






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[方法:Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs を使用したニュース速報の送信]: /manage/services/notification-hubs/breaking-news-ios
[モバイル サービス]: /develop/mobile/tutorials/get-started
[Notification Hubs によるユーザーへの通知:ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs によるユーザーへの通知:Mobile Services]: /manage/services/notification-hubs/notify-users
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Mobile Services の使用]: /develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /develop/mobile/tutorials/get-started-with-data-ios
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-ios
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-ios
[アプリケーション ユーザーへのプッシュ通知]: /develop/mobile/tutorials/push-notifications-to-users-ios
[スクリプトを使用したユーザーの承認]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript と HTML]: ../get-started-with-push-js.md

[Azure 管理ポータル]: https://manage.windowsazure.com/
[Windows Developer Preview registration steps for Mobile Services (Mobile Services 向け Windows Developer Preview の登録手順)]: ../mobile-services-windows-developer-preview-registration.md
[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[方法: Windows Azure 通知ハブ (iOS アプリ)]: http://msdn.microsoft.com/library/jj927168.aspx

<!--HONumber=49--> 