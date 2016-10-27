<properties
    pageTitle="Azure Mobile Engagement iOS SDK のアップグレード手順 | Microsoft Azure"
    description="Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />


#<a name="upgrade-procedures"></a>アップグレードの手順

既にアプリケーションに以前のバージョンの Mobile Engagement を統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

まず、新しいバージョンの SDK ごとに、EngagementSDK フォルダーと EngagementReach フォルダーを置き換える (削除し、xcode で再インポートする) 必要があります。

##<a name="from-3.0.0-to-4.0.0"></a>3.0.0 から 4.0.0 に移行

### <a name="xcode-8"></a>XCode 8
SDK のバージョン 4.0.0 以降では、XCode 8 が必須となります。

> [AZURE.NOTE] XCode 7 に依存している場合は、 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)を使用できます。 この以前のバージョンの Reach モジュールには、iOS 10 デバイスで実行したときに、システム通知が処理されないという既知のバグがあります。 これを修正するには、次のようにアプリケーション デリゲートで非推奨 API の `application:didReceiveRemoteNotification:` を実装する必要があります。

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **この回避策はお勧めできません。**この iOS API は推奨されていないため、(マイナー アップグレードも含め) iOS バージョンの今後のアップグレードでこの動作が変更される可能性があります。 できるだけ速やかに XCode 8 に切り替えてください。

### <a name="usernotifications-framework"></a>UserNotifications フレームワーク
`UserNotifications` フレームワークをビルド フェーズに追加する必要があります。

プロジェクト エクスプローラーでプロジェクト ペインを開き、適切なターゲットを選択します。 次に、**[Build phases (ビルド フェーズ)]** タブを開き、**[Link Binary With Libraries (バイナリをライブラリとリンク)]** メニューで `UserNotifications.framework` フレームワークを追加し、リンクを `Optional` として設定します。

### <a name="application-push-capability"></a>アプリケーションのプッシュ機能
XCode 8 はアプリケーションのプッシュ機能をリセットする場合があります。選択したターゲットの `capability` タブで、この機能を再確認してください。

### <a name="add-the-new-ios-10-notification-registration-code"></a>新しい iOS 10 通知登録コードの追加
アプリケーションを通知に登録するための古いコード スニペットは引き続き機能しますが、iOS 10 での実行時に非推奨 API を使用します。

`User Notification` フレームワークをインポートします。

        #import <UserNotifications/UserNotifications.h> 

アプリケーション デリゲートの `application:didFinishLaunchingWithOptions` メソッド内の

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

を以下で置き換えます。

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>開発者独自の UNUserNotificationCenterDelegate 実装が既にある場合

SDK にも UNUserNotificationCenterDelegate プロトコルの独自の実装があります。 これは、iOS 10 以降で実行されているデバイスで Engagement 通知のライフ サイクルを監視するために SDK によって使用されます。 UNUserNotificationCenter デリゲートはアプリケーションごとに 1 つしか使用できないため、SDK は開発者のデリゲートを検出すると、独自の実装を使用しなくなります。 これは、開発者独自のデリゲートに Engagement ロジックを追加する必要があることを意味します。

これを実現する方法は 2 つあります。

デリゲート呼び出しを SDK に転送します。

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

または、`AEUserNotificationHandler` クラスから継承します。

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] 通知が Engagement から送信されたものかどうかを確認するには、`userInfo` ディクショナリをエージェントの `isEngagementPushPayload:` クラス メソッドに渡します。

##<a name="from-2.0.0-to-3.0.0"></a>2.0.0 から 3.0.0 に移行
iOS 4.X のサポートが終了。 このバージョンから、アプリケーションのデプロイ ターゲットは iOS 6 以降である必要があります。

アプリケーションでリーチを使用している場合は、リモート通知を受け取れるように、`remote-notification` 値を Info.plist ファイル内の `UIBackgroundModes` 配列に追加する必要があります。

アプリケーション デリゲートのメソッド `application:didReceiveRemoteNotification:` は、`application:didReceiveRemoteNotification:fetchCompletionHandler:` に置き換える必要があります。

AEPushDelegate.h インターフェイスは廃止されるため、すべての参照を削除する必要があります。 これには、 `[[EngagementAgent shared] setPushDelegate:self]` の削除や、アプリケーション デリゲートからのデリゲート メソッドの削除も含まれます。

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##<a name="from-1.16.0-to-2.0.0"></a>1.16.0 から 2.0.0 に移行
Azure Mobile Engagement を使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。
以前のバージョンから移行する場合は、Capptain web サイトをご覧のうえ、まず 1.16 に移行し、次の手順を適用してください。

>[AZURE.IMPORTANT] Capptain と Mobile Engagement は、同じサービスではありません。次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。 アプリで SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。

### <a name="agent"></a>エージェント

メソッド `registerApp:` は、新しいメソッド `init:` で置き換えられました。 その結果、アプリケーション デリゲートは更新され、接続文字列を使用する必要があります。

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

SmartAd の追跡が SDK から削除されました。`AETrackModule` クラスのすべてのインスタンスを削除する必要があります。

### <a name="class-name-changes"></a>クラス名の変更

ブランド変更の一部として、いくつかの変更が必要なクラス名とファイル名があります。

「CP」で始まるクラスは、「AE」で始まる名前に変更されました。

例:

-   `CPModule.h` は `AEModule.h` に変更されました。

「Capptain」で始まるクラスは、「Engagement」で始まる名前に変更されました。

次に例を示します。

-   クラス `CapptainAgent` は `EngagementAgent` に変更されました。
-   クラス `CapptainTableViewController` は `EngagementTableViewController` に変更されました。
-   クラス `CapptainUtils` は `EngagementUtils` に変更されました。
-   クラス `CapptainViewController` は `EngagementViewController` に変更されました。



<!--HONumber=Oct16_HO2-->


