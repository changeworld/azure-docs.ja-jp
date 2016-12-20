---
title: "Azure Mobile Engagement iOS SDK の概要 | Microsoft Docs"
description: "Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/14/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8bfadc110b8b2e0de470185ec9d84343125c960d


---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK for Azure Mobile Engagement
ここでは、iOS アプリで Azure Mobile Engagement を統合する方法の詳細を確認します。 まず試してみる場合は、「 [15 分間チュートリアル](mobile-engagement-ios-get-started.md)」をご覧ください

 [SDK コンテンツ](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>統合手順
1. ここから開始: [iOS アプリで Mobile Engagement を統合する方法](mobile-engagement-ios-integrate-engagement.md)
2. 通知: [リーチ (通知) を iOS アプリに統合する方法](mobile-engagement-ios-integrate-engagement-reach.md)
3. タグ付けプランの実装: [iOS アプリで高度な Mobile Engagement のタグ付け API を使用する方法](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>リリース ノート
### <a name="400-09122016"></a>4.0.0 (2016 年 9 月 12 日)
* iOS 10 デバイスで処理されない通知を修正しました。
* XCode 7 が非推奨になりました。

以前のバージョンについては、「 [完全リリース ノート](mobile-engagement-ios-release-notes.md)

## <a name="upgrade-procedures"></a>アップグレードの手順
既にアプリケーションに以前のバージョンの Mobile Engagement を統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK のいくつかのバージョンがない場合は、次の手順に従う必要があります。完全な「[アップグレード手順](mobile-engagement-ios-upgrade-procedure.md)」をご覧ください。

まず、新しいバージョンの SDK ごとに、EngagementSDK フォルダーと EngagementReach フォルダーを置き換える (削除し、xcode で再インポートする) 必要があります。

### <a name="from-300-to-400"></a>3.0.0 から 4.0.0 に移行
### <a name="xcode-8"></a>XCode 8
SDK のバージョン 4.0.0 以降では、XCode 8 が必須となります。

> [!NOTE]
> XCode 7 に依存している場合は、 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)を使用できます。 この以前のバージョンの Reach モジュールには、iOS 10 デバイスで実行したときに、システム通知が処理されないという既知のバグがあります。 これを修正するには、次のようにアプリケーション デリゲートで非推奨 API の `application:didReceiveRemoteNotification:` を実装する必要があります。
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **この回避策はお勧めできません。**この iOS API は推奨されていないため、(マイナー アップグレードも含め) iOS バージョンの今後のアップグレードでこの動作が変更される可能性があります。 できるだけ速やかに XCode 8 に切り替えてください。
> 
> 

#### <a name="usernotifications-framework"></a>UserNotifications フレームワーク
`UserNotifications` フレームワークをビルド フェーズに追加する必要があります。

プロジェクト エクスプローラーでプロジェクト ペインを開き、適切なターゲットを選択します。 次に、**[Build phases (ビルド フェーズ)]** タブを開き、**[Link Binary With Libraries (バイナリをライブラリとリンク)]** メニューで `UserNotifications.framework` フレームワークを追加し、リンクを `Optional` として設定します。

#### <a name="application-push-capability"></a>アプリケーションのプッシュ機能
XCode 8 はアプリケーションのプッシュ機能をリセットする場合があります。選択したターゲットの `capability` タブで、この機能を再確認してください。

#### <a name="add-the-new-ios-10-notification-registration-code"></a>新しい iOS 10 通知登録コードの追加
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

#### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>開発者独自の UNUserNotificationCenterDelegate 実装が既にある場合
SDK には UNUserNotificationCenterDelegate プロトコルの独自の実装があります。 これは、iOS 10 以降で実行されているデバイスで Engagement 通知のライフ サイクルを監視するために SDK によって使用されます。 UNUserNotificationCenter デリゲートはアプリケーションごとに 1 つしか使用できないため、SDK は開発者のデリゲートを検出すると、独自の実装を使用しなくなります。 これは、開発者独自のデリゲートに Engagement ロジックを追加する必要があることを意味します。

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

> [!NOTE]
> 通知が Engagement から送信されたものかどうかを確認するには、`userInfo` ディクショナリをエージェントの `isEngagementPushPayload:` クラス メソッドに渡します。
> 
> 




<!--HONumber=Nov16_HO3-->


