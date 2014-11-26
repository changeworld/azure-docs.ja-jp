<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/24/2014" ms.author="krisragh" />

# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows ユニバーサル">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Apple Push Notification サービス (APNs) を使用してプッシュ通知を受信する空の iOS アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [証明書の署名要求を生成する][証明書の署名要求を生成する]
2.  [アプリケーションを登録し、プッシュ通知を有効にする][アプリケーションを登録し、プッシュ通知を有効にする]
3.  [アプリケーションのプロビジョニング プロファイルを作成する][アプリケーションのプロビジョニング プロファイルを作成する]
4.  [通知ハブを構成する][通知ハブを構成する]
5.  [通知ハブにアプリケーションを接続する][通知ハブにアプリケーションを接続する]
6.  [バックエンドから通知を送信する][バックエンドから通知を送信する]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。このチュートリアルの前提条件は次のとおりです。

-   [モバイル サービス iOS SDK][モバイル サービス iOS SDK]
-   [XCode 4.5][XCode 4.5]
-   iOS 5.0 (またはこれ以降のバージョン) に対応したデバイス
-   iOS Developer Program メンバーシップ

   <div class="dev-callout"><b>注</b><br /> 
   <p>プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。</p>
   </div>


このチュートリアルを完了することは、iOS アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

[WACOM.INCLUDE [Apple プッシュ通知の有効化](../includes/enable-apple-push-notifications.md)]

## <a name="configure-hub"></a>通知ハブを構成する

1.  Keychain Access で、quickstart アプリの新しい証明書 **[マイ証明書]** を右クリックします。**[エクスポート]** をクリックし、ファイルに名前を付けて、**[.p12]** 形式を選択します。次に、**[保存]** をクリックします。

    ![][0]

エクスポートした証明書のファイル名と場所を書き留めます。

> [WACOM.NOTE] このチュートリアルでは QuickStart.p12 ファイルを作成します。ファイル名と場所は同じである必要はありません。

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2.  **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

    ![][1]

3.  通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

    ![][2]

4.  前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** タブをクリックします。

    ![][3]

5.  上部にある **[通知ハブ]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

    ![][4]

6.  上部にある **[構成]** タブを選択し、Apple 通知設定の **[アップロード]** をクリックします。次に、前にエクスポートした **.p12** 証明書と、証明書のパスワードを選択します。**[運用]** プッシュ サービス (ストアからアプリケーションを購入したユーザーにプッシュ通知を送信する場合) と **[サンドボックス]** (開発時) プッシュ サービスのどちらを使用するかを選択してください。

    ![][5]

7.  上部にある **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

    ![][6]

これで、通知ハブが APN と連動するように構成されました。接続文字列にアプリケーションを登録し、通知を送信できます。

## <a name="connecting-app"></a>通知ハブにアプリケーションを接続する

1.  XCode で、新しい iOS プロジェクトを作成し、**[単一枠ビュー アプリケーション]** テンプレートを選択します。

    ![][7]

2.  **[ターゲット]** でプロジェクト名をクリックし、**[コード署名 ID]** を展開して、**[デバッグ]** でコード署名 ID プロファイルを選択します。さらに、新しいバージョンの XCode を使用する場合は、**[Levels]** を **[Basic]** から **[All]** に切り替え、**[Provisioning Profile]** の明細項目にプロビジョニング プロファイルを設定します。

    ![][8]

3.  Azure Mobile SDK for iOS をダウンロードします。.zip ファイルを開き、WindowsAzureMessaging.framework フォルダーを XCode プロジェクトの Framework フォルダーにドラッグします。**[Copy items in destination group's folder]** を選択し、**[OK]** をクリックします。

    ![][9]

4.  AppDelegate.h ファイルで、次の import ディレクティブを追加します。

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  AppDelegate.m ファイルで、`didFinishLaunchingWithOptions` メソッド内に次のコードを追加します。

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  同じファイルで、次のメソッドを追加します。

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                      @"<connection string>" notificationHubPath:@"mynh"];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

7.  *(省略可能)* もう一度同じファイルで次のメソッドを追加し、アプリケーションがアクティブのときに通知を受信した場合に **UIAlert** を表示します。

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

8.  デバイスでアプリケーションを実行します。

## <a name="send"></a>バックエンドから通知を送信する

通知は、[REST インターフェイス][REST インターフェイス]を使用するどのバックエンドからも Notification Hubs を使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して通知を送信します。Notification Hubs に統合されている Azure Mobile Services バックエンドから通知を送信する方法の例については、「**モバイル サービスでのプッシュ通知の使用**」 ([.NET バックエンド][.NET バックエンド] | [JavaScript バックエンド][.NET バックエンド]) を参照してください。REST API を使用して通知を送信する方法の例については、「**Java/PHP から Notification Hubs を使用する方法**」 ([Java][Java] | [PHP][PHP]) を参照してください。

1.  Visual Studio で、**[ファイル]** メニューから、**[新規作成]**、**[プロジェクト]** をクリックし、**[Visual C#]** で **[Windows]**、**[コンソール アプリケーション]**、**[OK]** の順にクリックします。

    ![][20]

    これで、新しいコンソール アプリケーション プロジェクトが作成されます。

2.  **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

    これで、パッケージ マネージャー コンソールが表示されます。

3.  コンソール ウィンドウで、次のコマンドを実行します。

        Install-Package WindowsAzure.ServiceBus

    これで、[WindowsAzure.ServiceBus NuGet パッケージ][WindowsAzure.ServiceBus NuGet パッケージ]を使用して Azure Service Bus SDK への参照が追加されます。

4.  Program.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

5.  **Program** クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

    "hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。また、接続文字列のプレースホルダーは、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えてください。

    > [WACOM.NOTE]**リッスン** アクセスではなく**フル** アクセスを持つ接続文字列を使用してください。リッスン アクセス文字列に通知を送信するアクセス許可はありません。

6.  次に、**Main** メソッド内に次の行を追加します。

         SendNotificationAsync();
         Console.ReadLine();

7.  F5 キーを押してコンソール アプリケーションを実行します。

    デバイスでアラートを受信します。Wi-fi を使用している場合、接続が機能していることを確認します。

Apple の「[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)][Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]」に、使用できるすべてのペイロードが記載されています。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての iOS デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知][通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信][通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要][Windows Azure 通知ハブの概要]」を参照してください。

 
 


  [証明書の署名要求を生成する]: #certificates
  [アプリケーションを登録し、プッシュ通知を有効にする]: #register
  [アプリケーションのプロビジョニング プロファイルを作成する]: #profile
  [通知ハブを構成する]: #configure-hub
  [通知ハブにアプリケーションを接続する]: #connecting-app
  [バックエンドから通知を送信する]: #send
  [モバイル サービス iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [1]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [6]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [7]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [8]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
  [9]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png
  [REST インターフェイス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Java]: /ja-jp/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ja-jp/documentation/articles/notification-hubs-php-backend-how-to/
  [WindowsAzure.ServiceBus NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
  [通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
  [Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
