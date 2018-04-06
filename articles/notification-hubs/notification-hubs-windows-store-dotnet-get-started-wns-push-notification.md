---
title: ユニバーサル Windows プラットフォーム アプリでの Azure Notification Hubs の使用 | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して Windows ユニバーサル プラットフォーム アプリケーションにプッシュ通知を送信する方法について学習します。
services: notification-hubs
documentationcenter: windows
author: jwhitedev
manager: kpiteira
editor: ''
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 8464a7e48a02be39fb624322fac6c26f4c6c6806
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-notification-hubs-for-universal-windows-platform-apps"></a>ユニバーサル Windows プラットフォーム アプリでの Notification Hubs の使用

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
この記事では、Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を送信する方法について説明します。

この記事では、Windows プッシュ通知サービス (WNS) を使用してプッシュ通知を受信する、空の Windows ストア アプリを作成します。 完了すると、通知ハブを使用して、アプリが実行されているすべてのデバイスにプッシュ通知をブロードキャストできます。

## <a name="before-you-begin"></a>開始する前に
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

このチュートリアルの完成したコードについては、[GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal) を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルには、次のものが必要です。

* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) 以降
* [UWP アプリ開発ツールがインストールされている](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
* アクティブな Azure アカウント  
    アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F)に関するページを参照してください。
* アクティブな Windows ストア アカウント

このチュートリアルを完了することは、UWP アプリに関する他のすべての Notification Hubs チュートリアルを行うための前提条件になっています。

## <a name="register-your-app-for-the-windows-store"></a>アプリケーションを Windows ストアに登録する
UWP アプリにプッシュ通知を送信するには、アプリを Windows ストアに関連付けます。 次に、WNS に統合するために通知ハブを構成します。

1. アプリをまだ登録していない場合は、[Windows デベロッパー センター](https://dev.windows.com/overview)に移動し、Microsoft アカウントでサインインしてから、**[新しいアプリの作成]** を選択します。

2. アプリの名前を入力し、**[アプリ名を予約]** を選択します。 これでアプリの新しい Windows ストア登録が作成されます。

3. Visual Studio で、UWP の **[空のアプリケーション]** テンプレートを使用して新しい Visual C# ストア アプリ プロジェクトを作成し、**[OK]** を選択します。

4. ターゲットとプラットフォームの最小バージョンの既定値をそのまま使用します。

5. ソリューション エクスプローラーで Windows ストア アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順に選択します。  
    **アプリケーションを Windows ストアと関連付ける** ウィザードが表示されます。

6. ウィザードで、自分の Microsoft アカウントでサインインします。

7. 手順 2. で登録したアプリを選択し、**[次へ]**、**[関連付け]** の順に選択します。 この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

8. [Windows デベロッパー センター](http://dev.windows.com/overview)で新しいアプリのページに戻り、**[サービス]**、**[プッシュ通知]**、**[WNS/MPNS]** の順に選択します。

9. **[新しい通知]** を選択します。

10. **[Blank (Toast)]\(空白 (トースト)\)** テンプレートを選択して、**[OK]** を選択します。

11. 通知の**名前**とビジュアル **コンテキスト** メッセージを入力してから、**[下書きとして保存]** を選択します。

12. [アプリケーション登録ポータル](http://apps.dev.microsoft.com)に移動して、サインインします。

13. アプリケーション名を選択します。 **Windows ストア** プラットフォームの設定で、**アプリケーション シークレット** パスワードと**パッケージ セキュリティ ID (SID)** をメモします。

    >[!WARNING]
    >アプリケーション シークレットおよびパッケージ SID は、重要なセキュリティ資格情報です。 これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。

## <a name="configure-your-notification-hub"></a>通知ハブを構成する
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p><b>[Notification Services]</b> の <b>[Windows (WNS)]</b> を選択し、アプリケーション シークレット パスワードを <b>[セキュリティ キー]</b> ボックスに入力します。 前のセクションで WNS から取得した値を <b>[パッケージ SID]</b> ボックスに入力し、<b>[保存]</b> を選択します。</p>
</li>
</ol>

![[パッケージ SID] ボックスと [セキュリティ キー] ボックス](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

これで、通知ハブが WNS と連携するように構成されました。 接続文字列を使用してアプリを登録し、通知を送信できます。

## <a name="connect-your-app-to-the-notification-hub"></a>通知ハブにアプリケーションを接続する
1. Visual Studio でソリューションを右クリックし、**[NuGet パッケージの管理]** を選択します。  
    **[NuGet パッケージの管理]** ウィンドウが開きます。

2. 検索ボックスに「**Microsoft.Azure.NotificationHubs**」と入力し、**[インストール]** を選択して、使用条件に同意します。
   
    ![[NuGet パッケージの管理] ウィンドウ][20]
   
    この操作によって、[Microsoft.Azure.NotificationHubs NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)を使用して Windows 用 Azure Notification Hubs ライブラリがダウンロード、インストールされ、ライブラリへの参照が追加されます。

3. App.xaml.cs プロジェクト ファイルを開き、次の `using` ステートメントを追加します。 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. さらに App.xaml.cs で、次の **InitNotificationsAsync** メソッドの定義を **App** クラスに追加します。
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    このコードにより、WNS からアプリケーションのチャネル URI が取得され、そのチャネル URI が通知ハブに登録されます。
   
    >[!NOTE]
    >* **ハブ名**プレースホルダーは、Azure Portal に表示される通知ハブの名前に置き換えてください。 
    >* さらに、接続文字列プレースホルダーを、前のセクションで通知ハブの **[アクセス ポリシー]** ページから取得した **DefaultListenSharedAccessSignature** 接続文字列に置き換えてください。
   > 
   > 
5. App.xaml.cs 内で、**OnLaunched** イベント ハンドラーの先頭に、次に示す新しい **InitNotificationsAsync** メソッドへの呼び出しを追加します。
   
        InitNotificationsAsync();
   
    これにより、アプリケーションが起動するたびに必ずチャネル URI が通知ハブに登録されます。

6. アプリを実行するには、**F5** キーを押します。 登録キーを示すダイアログ ボックスが表示されます。

これで、アプリケーションがトースト通知を受信する準備が整いました。

## <a name="send-notifications"></a>通知を送信する
[Azure Portal](https://portal.azure.com/) で通知を送信することで、通知の受信をすばやくテストできます。 次の画像に示すように、通知ハブの **[テスト送信]** ボタンを使用します。

![[テスト送信] ウィンドウ](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Services などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドで利用できない場合、REST API を直接使用して通知メッセージを送信することもできます。 

このチュートリアルでは、バックエンド サービスを使用せず、単にコンソール アプリケーションの通知ハブに .NET SDK を使用した通知を送信することで、クライアント アプリをテストする方法を説明します。 ASP.NET バックエンドから通知を送信するには、次のステップとして [Notification Hubs を使用したユーザーへのプッシュ通知]に関するチュートリアルを参照することをお勧めします。 ただし、通知の送信には次の方法も使用できます。

* **REST インターフェイス**: [REST インターフェイス](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)を使用すると、任意のバックエンド プラットフォームの通知をサポートできます。

* **Microsoft Azure Notification Hubs .NET SDK**: Visual Studio 用の NuGet パッケージ マネージャーで、[Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) を実行します。

* **Node.js**: [Node.js から Notification Hubs を使用する方法](notification-hubs-nodejs-push-notification-tutorial.md)についてのページを参照してください。
* **Azure Mobile Apps**: Notification Hubs に統合されている Azure モバイル アプリから通知を送信する方法の例については、[Mobile Apps へのプッシュ通知の追加](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)に関するページを参照してください。

* **Java または PHP**: REST API を使用して通知を送信する方法の例については、以下を参照してください。
    * [Java](notification-hubs-java-push-notification-tutorial.md)
    * [PHP](notification-hubs-php-push-notification-tutorial.md)

## <a name="next-steps"></a>次の手順
この簡単な例では、ポータルまたはコンソール アプリを使用して、ブロードキャスト通知をすべての Windows デバイスに送信しました。 次のステップとして、[Notification Hubs を使用したユーザーへのプッシュ通知]に関するチュートリアルをお勧めします。 このチュートリアルでは、特定のユーザーに対してタグを使用して ASP.NET バックエンドから通知を送信する方法について説明しています。

対象グループごとにユーザーを区分する場合は、「 [Notification Hubs を使用したニュース速報の送信]」を参照してください。 

Notification Hubs の全般的な情報については、[Notification Hubs の概要](notification-hubs-push-notification-overview.md)に関するページを参照してください。

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Notification Hubs を使用したユーザーへのプッシュ通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Notification Hubs を使用したニュース速報の送信]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
