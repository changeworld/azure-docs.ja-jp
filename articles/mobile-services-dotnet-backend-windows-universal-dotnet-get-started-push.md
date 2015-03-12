<properties 
	pageTitle=".NET バックエンド モバイル サービスを使用したプッシュ通知の使用" 
	description="Azure Mobile Services と Notification Hubs を使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services, notification-hubs" 
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
	ms.date="09/27/2014" 
	ms.author="glenga"/>

# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

このトピックでは、Azure Mobile Services を .NET バックエンドで使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、TodoList テーブルにレコードが挿入されるたびに、モバイル サービスは、.NET バックエンドから、登録されたすべての Windows ストア アプリおよび Windows Phone ストア アプリにプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[AZURE.NOTE]このトピックでは、Visual Studio Professional 2013 with Update 3 でツーリングを使用して、Mobile Services からユニバーサル Windows アプリへのプッシュ通知のサポートを追加する方法について説明します。同じ手順を使用して、モバイル サービスから Windows ストアまたは Windows Phone ストア 8.1 アプリへのプッシュ通知を追加することができます。プッシュ通知を Windows Phone 8 アプリまたは Windows Phone Silverlight 8.1 アプリに追加するには、こちらのバージョンの「[Mobile Services アプリへのプッシュ通知の追加](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)」を参照してください。

> Visual Studio Professional 2013 Update 3 にアップグレードできない場合や、モバイル サービス プロジェクトを手動で Windows ストア アプリ ソリューションに追加する場合は、トピックの[こちらのバージョン](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push)を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションをプッシュ通知に登録する](#register)
2. [サービスを更新してプッシュ通知を送信する](#update-service)
3. [ローカル テストのためにプッシュ通知を有効にする](#local-testing)
4. [アプリケーションでプッシュ通知をテストする](#test)

このチュートリアルを完了するには、以下が必要です。

* アクティブな [Microsoft ストア アカウント](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 以降のバージョン)。 <br/>無料評価版が利用できます。 

##<a id="register"></a>アプリケーションをプッシュ通知に登録する

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>  <code>\Services\MobileServices\your_service_name</code>  プロジェクト フォルダーに移動し、生成された push.register.cs コード ファイルを開き、デバイスのチャネル URL を通知ハブに登録する <strong>UploadChannel</strong> メソッドを見つけます。</p></li> 
<li><p>共有 App.xaml.cs コード ファイルを開き、新しい <strong>UploadChannel</strong> メソッドの呼び出しが <strong>OnLaunched</strong> イベント ハンドラーに追加されていることを確認します。</p> <p>これにより、アプリケーションが起動されるたびにデバイスの登録が試行されます。</p></li>
<li><p>直前の手順を繰り返して、Windows Phone ストア アプリ プロジェクトへのプッシュ通知を追加し、続いて共有 App.xaml.cs ファイルで、<strong>UploadChannel</strong> の余分の呼び出しと残りの  <code>#if...#endif</code>  条件付きラッパーを削除します。</p> <p>これで、両方のプロジェクトで <strong>UploadChannel</strong> の 1 回の呼び出しを共有できるようになりました。</p>

> [AZURE.NOTE]   <code>#if...#endif</code>  でラップされた [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することもできます。

</li>
</ol>

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するにはモバイル サービスを更新する必要があります。 

##<a id="update-service"></a>サービスを更新してプッシュ通知を送信する

次の手順では、既存の TodoItemController クラスを更新して、新しい項目が挿入されたときにプッシュ通知を登録済みのすべてのデバイスに送信します。同様のコードは、任意のカスタム [ApiController]、[TableController]、またはバックエンド サービスの任意の場所に実装できます。 

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a>ローカル テストのためにプッシュ通知を有効にする

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

このセクションの残りの手順はオプションです。これらの手順により、ローカル コンピューターで実行されているモバイル サービスに対してアプリをテストできます。Azure で実行されているモバイル サービスを使用してプッシュ通知をテストする場合は、最後のセクションまでスキップしてもかまいません。これは、既にプッシュ通知の追加ウィザードで、Azure で実行されているサービスに接続するようにアプリが構成されているためです。 

>[AZURE.NOTE]運用環境のモバイル サービスをテストや開発作業を行う目的で使用しないでください。モバイル サービス プロジェクトは必ずテスト用の個別のステージング サービスに発行してください。

<ol start="5">
<li><p>共有 App.xaml.cs プロジェクト ファイルを開き、<a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> クラスの新しいインスタンスを作成して Azure で実行されているモバイル サービスにアクセスするコード行を見つけます。</p></li>
<li><p>このコードをコメント アウトし、同じ名前でローカル ホストの URL を使用する、次のような新しい <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> を作成するコードをコンストラクターに追加します。</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
public static MobileServiceClient todolistClient = new MobileServiceClient(
	"http://localhost:4584"
);
</code></pre><p>この <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> を使用して、アプリは Azure でホストされているバージョンではなく、ローカル サービスに接続するようになります。この設定を戻して、Azure でホストされているモバイル サービスに対してアプリを実行する場合は、元の <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> 定義に戻します。</p></li>
</ol>

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。次は、タグを使用して、プッシュ通知をモバイル サービスから認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[認証されたユーザーへのプッシュ通知の送信]」を行うことをお勧めします。

Mobile Services と通知ハブについては次のトピックを参照してください。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Mobile Services を使用して、別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [Notification Hubs とは]
  <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [Notification Hubs のデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Notification Hubs ソリューションのトラブルシューティングおよびデバッグのガイダンスについて説明します。 

* [Azure Mobile Services 向け .NET クライアントを使用する方法]
  <br/>Mobile Services を C# Windows アプリから使用する方法について説明します。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users

[認証されたユーザーへのプッシュ通知の送信]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Notification Hubs とは]: /ja-jp/documentation/articles/notification-hubs-overview/

[Azure Mobile Services 向け .NET クライアントを使用する方法]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/


<!--HONumber=42-->
