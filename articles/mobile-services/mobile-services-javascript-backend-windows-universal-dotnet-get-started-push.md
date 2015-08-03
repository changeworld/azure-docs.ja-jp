<properties 
	pageTitle="JavaScript バックエンド モバイル サービスを使用したプッシュ通知の使用" 
	description="Azure Mobile Services と Notification Hubs を使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="glenga"/>


# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

このトピックでは、Azure のモバイル サービスを JavaScript バックエンドで使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、TodoList テーブルにレコードが挿入されるたびに、モバイル サービスは、JavaScript バックエンドから、登録されたすべての Windows ストア アプリおよび Windows Phone ストア アプリにプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[AZURE.NOTE]このトピックでは、Visual Studio 2013 with Update 3 でツーリングを使用して、Mobile Services からユニバーサル Windows アプリへのプッシュ通知のサポートを追加する方法について説明します。同じ手順を使用して、モバイル サービスから Windows ストアまたは Windows Phone ストア 8.1 アプリへのプッシュ通知を追加することができます。プッシュ通知を Windows Phone 8 または Windows Phone Silverlight 8.1 アプリに追加するには、このバージョンの「[モバイル サービスでのプッシュ通知の使用](mobile-services-javascript-backend-windows-phone-get-started-push.md)」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリをプッシュ通知に登録する](#register)
2. [サービスを更新してプッシュ通知を送信する](#update-service)
3. [アプリケーションでプッシュ通知をテストする](#test)

このチュートリアルを完了するには、以下が必要です。

* アクティブな [Microsoft ストア アカウント](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* [Visual Studio 2013 Express for Windows](http://go.microsoft.com/fwlink/?LinkId=257546) Update 3 以降のバージョン

##<a id="register"></a>アプリをプッシュ通知に登録する

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p><code>\Services\MobileServices\your_service_name</code> プロジェクト フォルダーに移動し、生成された push.register.cs コード ファイルを開き、デバイスのチャネル URL を通知ハブに登録する <strong>UploadChannel</strong> メソッドを見つけます。</p></li> 
<li><p>共有 App.xaml.cs コード ファイルを開き、新しい <strong>UploadChannel</strong> メソッドの呼び出しが <strong>OnLaunched</strong> イベント ハンドラーに追加されていることを確認します。</p> <p>これにより、アプリケーションが起動されるたびにデバイスの登録が試行されます。</p></li>
<li><p>直前の手順を繰り返して、Windows Phone ストア アプリ プロジェクトへのプッシュ通知を追加し、続いて共有 App.xaml.cs ファイルで、<strong>UploadChannel</strong> の余分の呼び出しと残りの <code>#if...#endif</code> 条件付きラッパーを削除します。</p> <p>これで、両方のプロジェクトで <strong>UploadChannel</strong> の 1 回の呼び出しを共有できるようになりました。</p>
<p><code>#if...#endif</code> でラップされた <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することもできます。</p></li>
</ol>

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するにはモバイル サービスを更新する必要があります。

##<a id="update-service"></a>サービスを更新してプッシュ通知を送信する

次の手順では、TodoItem テーブルに登録されている挿入スクリプトを更新します。同様のコードを任意のサーバー スクリプトまたはバックエンド サービスの任意の場所に実装することができます。

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]


##<a id="test"></a>アプリケーションでプッシュ通知をテストする

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。次に、次のチュートリアルのいずれかを完了します。

+ [認証されたユーザーにプッシュ通知を送信する](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) <br/>タグを使用して Mobile Services から認証されたユーザーのみにプッシュ通知を送信する方法について説明します。

+ [登録者へのブロードキャスト通知の送信](../notification-hubs-windows-store-dotnet-send-breaking-news.md) <br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

+ [登録者へのプラットフォーム固有の通知の送信](../notification-hubs-aspnet-cross-platform-notify-users.md) <br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。

Mobile Services と Notification Hubs については次のトピックを参照してください。

* [Azure Notification Hubs - 診断ガイドライン](../notification-hubs-diagnosing.md) <br/>プッシュ通知の問題をトラブルシューティングする方法について説明します。

* [認証の使用] <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [Notification Hubs とは] <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [Azure Mobile Services 用の .NET クライアントを使用する方法] <br/>Mobile Services を C# Windows アプリから使用する方法について説明します。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md
[認証の使用]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md

[Send push notifications to authenticated users]: mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md

[Notification Hubs とは]: ../notification-hubs-overview.md

[Azure Mobile Services 用の .NET クライアントを使用する方法]: mobile-services-windows-dotnet-how-to-use-client-library.md
 

<!---HONumber=July15_HO4-->