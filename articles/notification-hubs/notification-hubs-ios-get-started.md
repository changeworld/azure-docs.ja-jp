<properties 
	pageTitle="Azure Notification Hubs の使用" 
	description="Azure Notification Hubs を使用してプッシュ通知を行う方法について説明します。" 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>

# 通知ハブの使用

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## 概要

このトピックでは、Azure 通知ハブを使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple プッシュ通知サービス (APN) を使用してプッシュ通知を受信する空の iOS アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。

## 前提条件

このチュートリアルの前提条件は次のとおりです。

+ [モバイル サービス iOS SDK]
+ [XCode 4.5][Install Xcode]
+ iOS 5.0 (またはこれ以降のバージョン) に対応したデバイス
+ iOS Developer Program メンバーシップ

   >[AZURE.NOTE]プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルを完了することは、iOS アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

> [AZURE.NOTE]このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)を参照してください。

[AZURE.INCLUDE [Apple プッシュ通知を有効にする](../../includes/enable-apple-push-notifications.md)]

## 通知ハブを構成する

1. Keychain Access で、quickstart アプリの新しい証明書 **[マイ証明書]** を右クリックします。**[エクスポート]** をクリックし、ファイルに名前を付けて、**[.p12]** 形式を選択します。次に、**[保存]** をクリックします。

    ![][26]

  エクスポートした証明書のファイル名と場所を書き留めます。

>[AZURE.NOTE]このチュートリアルでは QuickStart.p12 ファイルを作成します。ファイル名と場所は同じである必要はありません。

2. [Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

3. **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

   ![][27]

4. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   ![][28]

5. 前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** タブをクリックします。

   ![][29]

6. 上部にある **[通知ハブ]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

   ![][210]

7. 上部にある **[構成]** タブを選択し、Apple 通知設定の **[アップロード]** をクリックします。次に、前にエクスポートした **.p12** 証明書と、証明書のパスワードを選択します。**[運用]** プッシュ サービス (ストアからアプリケーションを購入したユーザーにプッシュ通知を送信する場合) と **[サンドボックス]** (開発時) プッシュ サービスのどちらを使用するかを選択してください。

   ![][211]

8. 上部にある **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

   ![][212]

これで、通知ハブが APN と連動するように構成されました。接続文字列にアプリケーションを登録し、通知を送信できます。

## 通知ハブにアプリケーションを接続する

1. XCode で、新しい iOS プロジェクトを作成し、**[単一枠ビュー アプリケーション]** テンプレートを選択します。

   ![][31]

2. **[ターゲット]** でプロジェクト名をクリックし、**[コード署名 ID]** を展開して、**[デバッグ]** でコード署名 ID プロファイルを選択します。さらに、新しいバージョンの XCode を使用する場合は、**[Levels]** を **[Basic]** から **[All]** に切り替え、**[Provisioning Profile]** の明細項目にプロビジョニング プロファイルを設定します。

   ![][32]

3. Azure Mobile SDK for iOS をダウンロードします。.zip ファイルを開き、WindowsAzureMessaging.framework フォルダーを XCode プロジェクトの Framework フォルダーにドラッグします。**[Copy items in destination group's folder]** を選択し、**[OK]** をクリックします。

   ![][33]

4. AppDelegate.h ファイルで、次の import ディレクティブを追加します。

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. AppDelegate.m ファイルで、`didFinishLaunchingWithOptions` メソッド内に次のコードを追加します。

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	iOS 8 の場合、
   
	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                            UIUserNotificationTypeAlert |
                                            UIUserNotificationTypeBadge
					    					categories:nil];
 
    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

6. 同じファイルで、次のメソッドを追加します。

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *(省略可能)* もう一度同じファイルで次のメソッドを追加し、アプリケーションがアクティブのときに通知を受信した場合に **UIAlert** を表示します。


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

8. デバイスでアプリケーションを実行します。

## バックエンドから通知を送信する

通知は、[REST インターフェイス](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)を使用するどのバックエンドからも Notification Hubs を使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して通知を送信します。通知ハブと統合した Azure モバイル サービス バックエンドからの通知の送信例については、「**モバイル サービスでのプッシュ通知の使用**」([.NET バックエンド](../mobile-services-javascript-backend-ios-get-started-push.md) | [JavaScript バックエンド](../mobile-services-javascript-backend-ios-get-started-push.md)) を参照してください。REST API を使用した通知の送信方法の例については、「**How to use Notification Hubs from Java/PHP (Java/PHP から Notification Hubs を使用する方法)**」([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)) を参照してください。

1. Visual Studio で、**[ファイル]** メニューから、**[新規作成]**、**[プロジェクト]** をクリックし、**[Visual C#]** で **[Windows]**、**[コンソール アプリケーション]**、**[OK]** の順にクリックします。  

   ![][20]

	これで、新しいコンソール アプリケーション プロジェクトが作成されます。

2. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

	これで、パッケージ マネージャー コンソールが表示されます。

6. コンソール ウィンドウで、次のコマンドを実行します。

        Install-Package WindowsAzure.ServiceBus

	これにより、<a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure のサービス バス SDK に参照を追加します。

5. Program.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

6. **Program** クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

	"hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。また、接続文字列プレースホルダーを、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えます。

	>[AZURE.NOTE]**リッスン** アクセスではなく**フル** アクセスを持つ接続文字列を使用してください。リッスン アクセス文字列には通知を送信するアクセス許可はありません。

5. 次に、**Main** メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

5. F5 キーを押してコンソール アプリケーションを実行します。

	デバイスでアラートを受信します。Wi-fi を使用している場合、接続が機能していることを確認します。

Apple の「[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]」に、使用できるすべてのペイロードが記載されています。

## 次のステップ

この簡単な例では、すべての iOS デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要]」を参照してください。



<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[26]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[モバイル サービス iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure 管理ポータル]: https://manage.windowsazure.com/
[Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[通知ハブを使用したユーザーへのプッシュ通知]: notification-hubs-ios-mobile-services-register-user-push-notifications.md
[通知ハブを使用したニュース速報の送信]: notification-hubs-ios-send-breaking-news.md

[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1


<!--HONumber=52-->
 