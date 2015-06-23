<properties 
	pageTitle=".NET バックエンド モバイル サービスを使用したプッシュ通知の使用" 
	description="Azure Mobile Services と Notification Hubs を使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]
#概要
このトピックでは、Azure のモバイル サービスを .NET バックエンドで使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、TodoList テーブルにレコードが挿入されるたびに、モバイル サービスは、.NET バックエンドから、登録されたすべての Windows ストア アプリおよび Windows Phone ストア アプリにプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[AZURE.NOTE]このトピックでは、Visual Studio Professional 2013 with Update 3 でツーリングを使用して、Mobile Services からユニバーサル Windows アプリへのプッシュ通知のサポートを追加する方法について説明します。同じ手順を使用して、モバイル サービスから Windows ストアまたは Windows Phone ストア 8.1 アプリへのプッシュ通知を追加することができます。プッシュ通知を Windows Phone 8 または Windows Phone Silverlight 8.1 アプリに追加するには、このバージョンの「[モバイル サービスでのプッシュ通知の使用](mobile-services-dotnet-backend-windows-phone-get-started-push.md)」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリをプッシュ通知に登録する](#register)
2. [サービスを更新してプッシュ通知を送信する](#update-service)
3. [ローカル テストのためにプッシュ通知を有効にする](#local-testing)
4. [アプリケーションでプッシュ通知をテストする](#test)

このチュートリアルを完了するには、以下が必要です。

* アクティブな [Microsoft ストア アカウント](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>。 

##<a id="register"></a>アプリをプッシュ通知に登録する

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p><code>\Services\MobileServices\your_service_name</code> プロジェクト フォルダーに移動し、生成された push.register.cs コード ファイルを開き、デバイスのチャネル URL を通知ハブに登録する <strong>UploadChannel</strong> メソッドを見つけます。</p></li> 
<li><p>共有 App.xaml.cs コード ファイルを開き、新しい <strong>UploadChannel</strong> メソッドの呼び出しが <strong>OnLaunched</strong> イベント ハンドラーに追加されていることを確認します。</p> <p>これにより、アプリケーションが起動されるたびにデバイスの登録が試行されます。</p></li>
<li><p>直前の手順を繰り返して、Windows Phone ストア アプリ プロジェクトへのプッシュ通知を追加し、続いて共有 App.xaml.cs ファイルで、<strong>UploadChannel</strong> の余分の呼び出しと残りの <code>#if...#endif</code> 条件付きラッパーを削除します。</p> <p>これで、両方のプロジェクトで <strong>UploadChannel</strong> の 1 回の呼び出しを共有できるようになりました。</p>
</li>
</ol>

> [AZURE.NOTE]<code>#if...#endif</code> でラップされた [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することもできます。

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するにはモバイル サービスを更新する必要があります。

##<a id="update-service"></a>サービスを更新してプッシュ通知を送信する

次の手順では、既存の TodoItemController クラスを更新して、新しい項目が挿入されたときにプッシュ通知を登録済みのすべてのデバイスに送信します。同様のコードは、任意のカスタム [[ApiController]](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx)、[[TableController]](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx)、またはバックエンド サービスの任意の場所に実装できます。

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a>ローカル テストのためにプッシュ通知を有効にする

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

このセクションの残りの手順はオプションです。これらの手順により、ローカル コンピューターで実行されているモバイル サービスに対してアプリをテストできます。Azure で実行されているモバイル サービスを使用してプッシュ通知をテストする場合は、最後のセクションまでスキップしてもかまいません。これは、既にプッシュ通知の追加ウィザードで、Azure で実行されているサービスに接続するようにアプリが構成されているためです。

>[AZURE.NOTE]運用環境のモバイル サービスをテストや開発作業を行う目的で使用しないでください。モバイル サービス プロジェクトは必ずテスト用の個別のステージング サービスに発行してください。

<ol start="5">
<li><p>共有 App.xaml.cs プロジェクト ファイルを開き、<a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> クラスの新しいインスタンスを作成して Azure で実行されているモバイル サービスにアクセスするコード行を見つけます。</p></li>
<li><p>このコードをコメント アウトし、同じ名前でローカル ホストの URL を使用する、次のような新しい <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> を作成するコードをコンストラクターに追加します。</p>
<pre><code>// この MobileServiceClient は、デバッグ用のローカル テスト プロジェクト通信するように
// 構成されています。
public static MobileServiceClient todolistClient = new MobileServiceClient(
	"http://localhost:4584"
);
</code></pre><p>この <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> を使用して、アプリは Azure でホストされているバージョンではなく、ローカル サービスに接続するようになります。この設定を戻して、Azure でホストされているモバイル サービスに対してアプリを実行する場合は、元の <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> 定義に戻します。</p></li>
</ol>

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスおよび通知ハブを使用してプッシュ通知を送信できるようにするための基本について説明しました。次は、タグを使用して、プッシュ通知をモバイル サービスから認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[プッシュ通知を認証ユーザーに送信する]」を行うことをお勧めします。

Mobile Services と通知ハブについては次のトピックを参照してください。

* [既存のアプリケーションへの Mobile Services の追加][Get started with data] <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [アプリへの認証の追加][Get started with authentication] <br/>Mobile Services を使用して別の種類のアカウントでアプリケーションのユーザーを認証する方法について説明します。

* [通知ハブとは] <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

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

[通知ハブとは]: ../notification-hubs-overview.md

[Azure Mobile Services 用の .NET クライアントを使用する方法]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54--> 