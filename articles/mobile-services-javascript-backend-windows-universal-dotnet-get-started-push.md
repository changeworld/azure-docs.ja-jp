<properties pageTitle="Get started with push notification using a JavaScript backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga"></tags>

# モバイル サービスでのプッシュ通知の使用

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

このトピックでは、Azure のモバイル サービスを JavaScript バックエンドで使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、TodoList テーブルにレコードが挿入されるたびに、モバイル サービスは、JavaScript バックエンドから、登録されたすべての Windows ストア アプリおよび Windows Phone ストア アプリにプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

> [WACOM.NOTE] このトピックでは、Visual Studio 2013 with Update 3 でツーリングを使用して、モバイル サービスからユニバーサル Windows アプリへのプッシュ通知のサポートを追加する方法について説明します。同じ手順を使用して、モバイル サービスから Windows ストアまたは Windows Phone ストア 8.1 アプリへのプッシュ通知を追加することができます。プッシュ通知を Windows Phone 8 または Windows Phone Silverlight 8.1 アプリに追加するには、このバージョンの「[モバイル サービスでのプッシュ通知の使用][モバイル サービスでのプッシュ通知の使用]」を参照してください。

> Visual Studio 2013 Update 3 にアップグレードできない場合や、モバイル サービス プロジェクトを手動で Windows ストア アプリ ソリューションに追加する場合は、トピックの「[このバージョン][このバージョン]」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションをプッシュ通知に登録する][アプリケーションをプッシュ通知に登録する]
2.  [サービスを更新してプッシュ通知を送信する][サービスを更新してプッシュ通知を送信する]
3.  [アプリケーションでプッシュ通知をテストする][アプリケーションでプッシュ通知をテストする]

このチュートリアルを完了するには、以下が必要です。

-   アクティブな [Microsoft ストア アカウント][Microsoft ストア アカウント]。
-   [Visual Studio 2013 Express for Windows][Visual Studio 2013 Express for Windows] Update 3 以降のバージョン

## <span id="register"></span></a> アプリケーションをプッシュ通知に登録する

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  `\Services\MobileServices\your_service_name` プロジェクト フォルダーに移動し、生成された push.register.cs コード ファイルを開き、デバイスのチャネル URL を通知ハブに登録する **UploadChannel** メソッドを見つけます。

2.  共有 App.xaml.cs コード ファイルを開き、新しい **UploadChannel** メソッドの呼び出しが **OnLaunched** イベント ハンドラーに追加されていることを確認します。

    これにより、アプリケーションが起動されるたびにデバイスの登録が試行されます。

3.  直前の手順を繰り返して、Windows Phone ストア アプリ プロジェクトへのプッシュ通知を追加し、続いて共有 App.xaml.cs ファイルで、**UploadChannel** の余分の呼び出しと残りの `#if...#endif` 条件付きラッパーを削除します。

    これで、両方のプロジェクトで **UploadChannel** の 1 回の呼び出しを共有できるようになりました。

    <div class="dev-callout"><strong>注</strong> <p><code data-inline="1">#if...#endif</code> でラップされた <a href="http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することもできます。</p></div>

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するにはモバイル サービスを更新する必要があります。

## <span id="update-service"></span></a>サービスを更新してプッシュ通知を送信する

次の手順では、TodoItem テーブルに登録されている挿入スクリプトを更新します。同様のコードを任意のサーバー スクリプトまたはバックエンド サービスの任意の場所に実装することができます。

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>アプリケーションでプッシュ通知をテストする

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスおよび通知ハブを使用してプッシュ通知を送信できるようにするための基本について説明しました。次は、タグを使用して、プッシュ通知をモバイル サービスから認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[プッシュ通知を認証ユーザーに送信する][プッシュ通知を認証ユーザーに送信する]」を行うことをお勧めします。

Mobile Services と通知ハブについては次のトピックを参照してください。

-   [データの使用][データの使用]
    モバイル サービスを使用してデータの格納およびクエリの実行の方法について説明します。

-   [認証の使用][認証の使用]
    モバイル サービスを使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

-   [Windows Azure 通知ハブとは][Windows Azure 通知ハブとは]
    通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

-   [Azure モバイル サービス用の .NET クライアントを使用する方法][Azure モバイル サービス用の .NET クライアントを使用する方法]
    モバイル サービスを C# Windows アプリから使用する方法について説明します。


  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [モバイル サービスでのプッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [このバージョン]: /ja-jp/documentation/articles/mobile-services-javscript-backend-windows-store-dotnet-get-started-push
  [アプリケーションをプッシュ通知に登録する]: #register
  [サービスを更新してプッシュ通知を送信する]: #update-service
  [アプリケーションでプッシュ通知をテストする]: #test
  [Microsoft ストア アカウント]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [MobileServiceClient]: http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-javascript-backend-windows-universal-test-push]: ../includes/mobile-services-javascript-backend-windows-universal-test-push.md
  [プッシュ通知を認証ユーザーに送信する]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [データの使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users
  [Windows Azure 通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/
  [Azure モバイル サービス用の .NET クライアントを使用する方法]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
