---
title: ユニバーサル Windows 8.1 アプリへのプッシュ通知の追加 | Microsoft Docs
description: Azure Notification Hubs を使用して .NET バックエンド モバイル サービスからユニバーサル Windows 8.1 アプリにプッシュ通知を送信する方法について説明します。
services: mobile-services,notification-hubs
documentationcenter: windows
author: ggailey777
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Mobile Services アプリへのプッシュ通知の追加
[!INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> このトピックの Mobile Apps バージョンについては、「[Add push notifications to your Windows app (Windows アプリにプッシュ通知を追加する)](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)」を参照してください。
> 
> 

## 概要
このトピックでは、Azure Mobile Services を .NET バックエンドで使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトの Azure Notification Hubs を使用したプッシュ通知を有効にします。完了すると、TodoList テーブルにレコードが挿入されるたびに、モバイル サービスは、.NET バックエンドから、登録されたすべての Windows ストア アプリおよび Windows Phone ストア アプリにプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

このチュートリアルを完了するには、以下が必要です。

* アクティブな [Microsoft ストア アカウント](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)。

## <a id="register"></a>アプリケーションをプッシュ通知に登録する
[!INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6.`\Services\MobileServices\your_service_name` プロジェクト フォルダーに移動し、生成された push.register.cs コード ファイルを開いて、デバイスのチャネル URL を通知ハブに登録する **UploadChannel** メソッドを見つけます。

&nbsp;&nbsp;7.共有 App.xaml.cs コード ファイルを開き、新しい **UploadChannel** メソッドの呼び出しが **OnLaunched** イベント ハンドラーに追加されていることを確認します。これにより、アプリケーションが起動されるたびにデバイスの登録が試行されます。

&nbsp;&nbsp;8.前の手順を繰り返して、Windows Phone ストア アプリ プロジェクトへのプッシュ通知を追加し、共有 App.xaml.cs ファイルで、**UploadChannel** の余分な呼び出しと残りの `#if...#endif` 条件付きラッパーを削除します。これで、両方のプロジェクトで **UploadChannel** の 1 回の呼び出しを共有できるようになりました。

> [!NOTE]
> `#if...#endif` でラップされた [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを簡素化することもできます。
> 
> 

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するにはモバイル サービスを更新する必要があります。

## <a id="update-service"></a>サービスを更新してプッシュ通知を送信する
次の手順では、既存の TodoItemController クラスを更新して、新しい項目が挿入されたときにプッシュ通知を登録済みのすべてのデバイスに送信します。同様のコードは、任意のカスタム [[ApiController]](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx)、[[TableController]](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx)、またはバックエンド サービスの任意の場所に実装できます。

[!INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <a id="local-testing"></a>ローカル テストのためにプッシュ通知を有効にする
[!INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

このセクションの残りの手順はオプションです。これらの手順により、ローカル コンピューターで実行されているモバイル サービスに対してアプリをテストできます。Azure で実行されているモバイル サービスを使用してプッシュ通知をテストする場合は、最後のセクションまでスキップしてもかまいません。これは、既にプッシュ通知の追加ウィザードで、Azure で実行されているサービスに接続するようにアプリが構成されているためです。

> [!NOTE]
> 運用環境のモバイル サービスをテストや開発作業を行う目的で使用しないでください。モバイル サービス プロジェクトは必ずテスト用の個別のステージング サービスに発行してください。
> 
> 

&nbsp;&nbsp;5.共有 App.xaml.cs プロジェクト ファイルを開き、[MobileServiceClient] クラスの新しいインスタンスを作成して Azure で実行されているモバイル サービスにアクセスするコード行を見つけます。

&nbsp;&nbsp;6.このコードをコメント アウトし、同じ名前でローカル ホストの URL を使用する、次のような新しい [MobileServiceClient] を作成するコードをコンストラクターに追加します。

    // This MobileServiceClient has been configured to communicate with your local
    // test project for debugging purposes.
    public static MobileServiceClient todolistClient = new MobileServiceClient(
        "http://localhost:4584"
    );

&nbsp;&nbsp;この [MobileServiceClient] を使用して、アプリは Azure でホストされているバージョンではなく、ローカル サービスに接続するようになります。この設定を戻して、Azure でホストされているモバイル サービスに対してアプリを実行する場合は、元の [MobileServiceClient] 定義に戻します。

## <a id="test"></a>アプリケーションでプッシュ通知をテストする
[!INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>次のステップ
このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。次は、タグを使用して、プッシュ通知をモバイル サービスから認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[プッシュ通知を認証ユーザーに送信する]」を行うことをお勧めします。

Mobile Services と Notification Hubs については次のトピックを参照してください。

* [既存のアプリケーションへの Mobile Services の追加][Get started with data] <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。
* [アプリへの認証の追加][Get started with authentication] <br/>Mobile Services を使用して別の種類のアカウントでアプリケーションのユーザーを認証する方法について説明します。
* [Notification Hubs とは] <br/>Notification Hubs がすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。
* [Notification Hubs アプリケーションのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Notification Hubs ソリューションのトラブルシューティングとデバッグについて説明します。
* [Azure Mobile Services 用の .NET クライアントを使用する方法] <br/>Mobile Services を C# Windows アプリから使用する方法について説明します。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md

[プッシュ通知を認証ユーザーに送信する]: mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Notification Hubs とは]: ../notification-hubs-overview.md

[Azure Mobile Services 用の .NET クライアントを使用する方法]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx

<!---HONumber=AcomDC_0727_2016-->