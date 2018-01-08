---
title: "Azure Notification Hubs の使用 (iOS アプリ) | Microsoft Docs"
description: "このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について学習します。"
services: notification-hubs
documentationcenter: ios
keywords: "プッシュ通知,プッシュ通知,iOS プッシュ通知"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 0e9e7ab196eef790b74074be319cd8122cf3ff5c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-ios-apps"></a>Azure Notification Hubs の使用 (iOS アプリ)
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)を参照してください。
> 
> 

このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。 [Apple Push Notification サービス (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)を使用してプッシュ通知を受信する空の iOS アプリケーションを作成します。 

完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

## <a name="before-you-begin"></a>開始する前に
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

このチュートリアルの完成したコードについては、 [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted)を参照してください。 

## <a name="prerequisites"></a>前提条件
このチュートリアルには、次のものが必要です。

* [Windows Azure Messaging Framework]
* [Xcode]
* iOS 10 (またはこれ以降のバージョン) に対応したデバイス
* [Apple Developer Program](https://developer.apple.com/programs/) メンバーシップ
  
  > [!NOTE]
  > プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、iOS シミュレーターではなく物理 iOS デバイス (iPhone または iPad) で行う必要があります。
  > 
  > 

このチュートリアルを完了することは、iOS アプリケーションの他のすべての Notification Hubs チュートリアルの前提条件です。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>iOS プッシュ通知向けに通知ハブを構成する
このセクションでは、以前に作成した **.p12** プッシュ証明書を使用して、新しい通知ハブを作成し、APNS での認証を構成する手順について説明します。 既に作成した通知ハブを使用する場合は、手順 5. に進んでください。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p><b>[Notification Services]</b> で <b>[Apple (APNS)]</b> を選択します。 <b>[証明書]</b> を選択するには、ファイル アイコンをクリックし、以前にエクスポートした <b>.p12</b> ファイルを選択します。 必ず正しいパスワードも入力してください。</p>

<p>これは開発用であるため、<b>[サンドボックス]</b> モードを選択してください。 <b>[実稼働]</b> は、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。</p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![Azure Portal で APNS を構成する][6]

&emsp;&emsp;&emsp;&emsp;![Azure Portal で APNS 証明書を構成する][7]

通知ハブと APNS の構成が完了したので、接続文字列にアプリケーションを登録し、プッシュ通知を送信できます。

## <a name="connect-your-ios-app-to-notification-hubs"></a>Notification Hubs に iOS アプリケーションを接続する
1. Xcode で、新しい iOS プロジェクトを作成し、 **[Single View Application]** テンプレートを選択します。
   
    ![Xcode - Single View Application][8]
    
2. 新しいプロジェクトのオプションを設定する際には、Apple Developer ポータルでバンドル ID を設定したときと同じ**製品名**と**組織識別子**を使用してください。
   
    ![Xcode - project options][11]
    
3. プロジェクト ナビゲーターでプロジェクト名をクリックし、**[全般]** タブをクリックし、**[署名]** を探します。 Apple Developer アカウントに適したチームを選択します。 XCode を選択すると、バンドル識別子に基づいて以前に作成したプロビジョニング プロファイルが自動的に表示されます。
   
    Xcode で作成した新しいプロビジョニング プロファイルが表示されない場合は、署名 ID のプロファイルを更新してみてください。 メニュー バーの **Xcode** をクリックし、**[Preference (ユーザー設定)]**、**[Account (アカウント)]** タブ、**[View Details (詳細の表示)]** ボタンの順にクリックします。次に、署名 ID をクリックし、右下隅にある更新ボタンをクリックします。

    ![Xcode - provisioning profile][9]

4. **[機能]** タブを選択し、プッシュ通知を有効にします。

    ![Xcode - プッシュ機能][12]
   
5. [Windows Azure Messaging Framework] をダウンロードし、ファイルを展開します。 Xcode でプロジェクトを右クリックして **[Add Files to (ファイルの追加先)]** オプションをクリックし、Xcode プロジェクトに **WindowsAzureMessaging.framework** フォルダーを追加します。 **[オプション]** を選択し、**[Copy items if needed]\(必要に応じてアイテムをコピーする\)** をオンにして **[追加]** をクリックします。

    ![Unzip Azure SDK][10]

6. **HubInfo.h** という名前の新しいヘッダー ファイルをプロジェクトに追加します。 このファイルに通知ハブの定数が保存されます。 次の定義を追加し、文字列リテラルのプレースホルダーを*ハブ名*とメモしておいた *DefaultListenSharedAccessSignature* に置き換えます。

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. **AppDelegate.h** ファイルを開き、次の import ディレクティブを追加します。

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. **AppDelegate.m ファイル**に、使用している iOS バージョンに基づいて **didFinishLaunchingWithOptions** メソッドに次のコードを追加します。 このコードにより、APNs にデバイス ハンドルが登録されます。

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. 同じファイルで、次のメソッドを追加します。

    ```obj-c
         - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
           SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
               if (error != nil) {
                   NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                   [self MessageBox:@"Registration Status" message:@"Registered"];
              }
          }];
         }
   
        -(void)MessageBox:(NSString *) title message:(NSString *)messageText
        {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    このコードは、HubInfo.h に指定した接続情報を使用して通知ハブに接続します。 その後、通知ハブが通知を送信できるように、通知ハブにデバイス トークンを指定します。

10. 同じファイルで次のメソッドを追加し、アプリケーションがアクティブのときに通知を受信した場合に **UIAlert** が表示されるようにします。

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. エラーがないことを確認するために、デバイスでアプリをビルドして実行します。

## <a name="send-test-push-notifications"></a>テスト プッシュ通知を送信する
アプリの通知の受信をテストするには、[Azure Portal] の *[テスト送信]* オプションを使用します。 テスト プッシュ通知がデバイスに送信されます。

![Azure Portal - テスト送信][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="checking-if-your-app-can-receive-push-notifications"></a>アプリでプッシュ通知を受信できるかどうかを確認する
iOS でプッシュ通知をテストするには、物理 iOS デバイスにアプリをデプロイする必要があります。 iOS シミュレーターを使用して Apple のプッシュ通知を送信することはできません。

1. アプリケーションを実行して登録が成功したことを確認したら、 **[OK]**を押します。
   
    ![iOS App Push Notification Registration Test][33]
2. 次に、前述の手順で [Azure Portal] からテスト プッシュ通知を送信します。 

3. 特定の通知ハブから通知を受信するように登録されているすべてのデバイスにプッシュ通知が送信されます。
   
    ![iOS App Push Notification Receive Test][35]

## <a name="next-steps"></a>次の手順
この簡単な例では、すべての登録済み iOS デバイスにプッシュ通知をブロードキャストしました。 学習の順序として、次に「[Azure Notification Hubs と .NET バックエンドによる iOS ユーザーへの通知]」チュートリアルを始めることをお勧めします。 このガイドでは、タグを使用してプッシュ通知を送信するバックエンドを作成する手順が説明されています。 

対象グループごとにユーザーを区分する場合は、チュートリアル「 [Notification Hubs を使用したニュース速報の送信] 」もご覧ください。 

Notification Hubs の全般的な情報については、「 [Notification Hubs の概要]」を参照してください。

<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs と .NET バックエンドによる iOS ユーザーへの通知]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notification Hubs を使用したニュース速報の送信]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com
