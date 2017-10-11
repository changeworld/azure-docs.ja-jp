---
title: "Windows Phone で Azure Notification Hubs によるプッシュ通知を送信する |Microsoft ドキュメント"
description: "このチュートリアルでは、Azure Notification Hubs を使用して、Windows Phone 8 または Windows Phone 8.1 Silverlight アプリケーションにプッシュ通知をする方法を学習します。"
services: notification-hubs
documentationcenter: windows
keywords: "プッシュ通知、プッシュ通知、windows phone のプッシュ"
author: ysxu
manager: erikre
editor: erikre
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: f0bfe81f849813d146d644b32490af657b1071b5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Windows Phone で Azure Notification Hubs によるプッシュ通知を送信します。
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントを持っていない場合は、ほんの数分で無料の試用アカウントを作成できます。 詳細については、「 [Azure 無料評価版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F)です。
> 
> 

このチュートリアルでは、Azure Notification Hubs を使用して、Windows Phone 8 または Windows Phone 8.1 Silverlight アプリケーションにプッシュ通知を送信する方法を示します。 Windows Phone 8.1 (Silverlight ではない) を対象とする場合を参照し、 [Windows ユニバーサル](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)バージョン。
このチュートリアルでは、Microsoft プッシュ通知サービス (MPNS) を使用して、プッシュ通知を受信する、空の Windows Phone 8 アプリを作成します。 完了したら、アプリを実行するすべてのデバイスにプッシュ通知をブロードキャストする、通知ハブを使用することができます。

> [!NOTE]
> 通知ハブの Windows Phone SDK では、Windows Phone 8.1 Silverlight アプリを使用して、Windows プッシュ通知サービス (WNS) を使用することはできません。 (MPNS) ではなく WNS を使用して、Windows Phone 8.1 Silverlight アプリを使用して、 [Notification Hubs - Windows Phone Silverlight チュートリアル]、REST Api を使用します。
> 
> 

チュートリアルでは、単純なブロードキャスト シナリオでは、Notification Hubs の使用について説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、次の項目が必要です。

* [Visual Studio 2012 Express for Windows Phone]、以降のバージョン。

Windows Phone 8 アプリの他のすべての Notification Hubs のチュートリアルの前提条件では、このチュートリアルを完了します。

## <a name="create-your-notification-hub"></a>通知ハブを作成します。
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>をクリックして、 <b>Notification Services</b>セクション (内で<i>設定</i>)、をクリックして<b>Windows Phone (MPNS)</b>をクリックし、<b>非認証プッシュを有効にする</b>チェック ボックスをオンします。</p>
</li>
</ol>

&emsp;&emsp;![Azure ポータルで有効にする unathenticated プッシュ通知](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

これで、ハブが作成され、認証されていない Windows Phone 通知を送信するように構成します。

> [!NOTE]
> このチュートリアルでは、認証されていないモードで MPNS を使用します。 MPNS 認証されていないモードは、各チャネルに送信することが通知の制限事項と付属します。 通知ハブをサポートしている[MPNS 認証モード](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx)によって証明書をアップロードすることができます。
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>通知ハブにアプリを接続します。
1. Visual Studio で、新しい Windows Phone 8 アプリケーションを作成します。
   
       ![Visual Studio - New Project - Windows Phone App][13]
   
    Visual Studio 2013 Update 2 以降では、代わりに、Windows Phone Silverlight アプリケーションを作成します。
   
    ![Visual Studio - 新しいプロジェクトでは Windows Phone Silverlight アプリ - を空白][11]
2. Visual Studio で、ソリューションを右クリックし、をクリックして**NuGet パッケージの管理**です。
   
    これが表示されます、 **NuGet パッケージの管理** ダイアログ ボックス。
3. 検索`WindowsAzure.Messaging.Managed` をクリック**インストール**、し、その後、使用条件に同意します。
   
    ![Visual Studio の NuGet パッケージ マネージャー][20]
   
    これは、ダウンロード、インストールすると、しを使用して Windows 用 Azure メッセージング ライブラリへの参照を追加、 <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet パッケージ</a>です。
4. App.xaml.cs ファイルを開き、次の追加`using`ステートメント。
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. 上部にある次のコードを追加**Application_Launching** App.xaml.cs でメソッド。
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > 値**MyPushChannel**の既存のチャンネルの検索に使用されるインデックス、 [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx)コレクション。 ない場合がありますは、その名前を持つ新しいエントリを作成します。
   > 
   > 
   
    ハブおよびと呼ばれる接続文字列の名前を挿入することを確認**DefaultListenSharedAccessSignature**前のセクションで取得します。
    このコードでは、MPNS から、アプリのチャネル URI を取得し、通知ハブをそのチャネル URI を登録します。 ことが保証される URI が、アプリケーションが起動されるたびに、通知ハブの登録されたチャネル。
   
   > [!NOTE]
   > このチュートリアルでは、デバイスにトースト通知を送信します。 タイル通知を送信するときに代わりに呼び出す必要があります、 **BindToShellTile**チャネルでのメソッドです。 両方トーストをサポートし、通知を並べて表示、両方を呼び出す**BindToShellTile**と**BindToShellToast**です。
   > 
   > 
6. ソリューション エクスプ ローラーで、**プロパティ**を開き、`WMAppManifest.xml`ファイルをクリックして、**機能** タブを確認して、 **ID_CAP_PUSH_NOTIFICATION**機能がオンになってです。
   
       ![Visual Studio - Windows Phone App Capabilities][14]
   
       This ensures that your app can receive push notifications. Without it, any attempt to send a push notification to the app will fail.
7. キーを押して、`F5`キーをアプリを実行します。
   
    登録メッセージは、アプリに表示されます。
8. アプリを閉じます。  
   
   > [!NOTE]
   > トースト プッシュ通知を受信するには、フォア グラウンドで、アプリケーションを実行されていない必要があります。
   > 
   > 

## <a name="send-push-notifications-from-your-backend"></a>バックエンドからプッシュ通知を送信します。
プッシュ通知を送信するには、パブリック経由で任意のバックエンドから Notification Hubs を使用して、 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST インターフェイス</a>です。 このチュートリアルでは、.NET コンソール アプリケーションを使用してプッシュ通知を送信します。 

Notification Hubs と統合されている ASP.NET WebAPI バックエンドからプッシュ通知を送信する方法の例は、次を参照してください。 [Azure Notification Hubs に通知を持つユーザー .NET バックエンド](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)です。  

使用してプッシュ通知を送信する方法の例については、 [REST Api](https://msdn.microsoft.com/library/azure/dn223264.aspx)、チェック アウト[Java から Notification Hubs を使用する方法](notification-hubs-java-push-notification-tutorial.md)と[PHP から Notification Hubs を使用する方法](notification-hubs-php-push-notification-tutorial.md)です。

1. ソリューションを右クリックし、選択**追加**と**新しいプロジェクト.**、下にある**Visual c#**、 をクリックして**Windows**と**コンソール アプリケーション**、 をクリック**OK**です。
   
       ![Visual Studio - New Project - Console Application][6]
   
    新しい Visual c# コンソール アプリケーションをソリューションに追加されます。 これは、個別のソリューションでも実行できます。
2. をクリックして**ツール**、 をクリックして**ライブラリ パッケージ マネージャー**、クリックして**パッケージ マネージャー コンソール**です。
   
    これには、パッケージ マネージャー コンソールが表示されます。
3. **パッケージ マネージャー コンソール**ウィンドウで、設定、**既定のプロジェクト**新しいコンソール アプリケーション プロジェクトにし、コンソール ウィンドウには、次のコマンドを実行します。
   
       Install-Package Microsoft.Azure.NotificationHubs
   
   使用して、Azure Notification Hubs の SDK への参照を追加、 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification ハブ NuGet パッケージ</a>です。
4. 開く、`Program.cs`ファイルし、次の追加`using`ステートメント。
   
        using Microsoft.Azure.NotificationHubs;
5. `Program`クラスで、次のメソッドを追加します。
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }
   
    必ず置き換えて、`<hub name>`プレース ホルダーをポータルに表示される通知ハブの名前。 また、接続文字列のプレース ホルダーと呼ばれる接続文字列に置き換える**DefaultFullSharedAccessSignature**ことで取得した、セクション「通知ハブを構成します」。
   
   > [!NOTE]
   > 接続文字列を使用することを確認してください**完全**アクセス、**リッスン**アクセスします。 リッスン アクセス文字列には、プッシュ通知を送信するアクセス許可がありません。
   > 
   > 
6. 次の行を追加、`Main`メソッド。
   
         SendNotificationAsync();
         Console.ReadLine();
7. Windows Phone エミュレーターの実行と、アプリを閉じ、コンソール アプリケーション プロジェクトを既定のスタートアップ プロジェクトとして設定し、キーを押し、`F5`キーをアプリを実行します。
   
    トースト プッシュ通知を受け取ります。 トースト バナーをタップすると、アプリが読み込まれます。

可能なすべてのペイロードを見つけることができます、[トースト カタログ]と[タイル カタログ]MSDN のトピックです。

## <a name="next-steps"></a>次のステップ
この簡単な例では、すべての Windows Phone 8 デバイスにプッシュ通知をブロードキャストします。 

特定のユーザーを対象とするためを参照してください、[ユーザーへのプッシュ通知を使用して Notification Hubs]チュートリアルです。 

対象グループと、ユーザーをセグメント化するかどうか、確認できる[最新ニュースを送信する通知ハブを使用して]です。 

通知ハブを使用する方法の詳細について[Notification Hubs ガイダンス]です。

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Notification Hubs ガイダンス]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[ユーザーへのプッシュ通知を使用して Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[最新ニュースを送信する通知ハブを使用して]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[トースト カタログ]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[タイル カタログ]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs - Windows Phone Silverlight チュートリアル]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

