<properties pageTitle=".NET バックエンド モバイル サービスを使用したプッシュ通知の使用" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />


# Mobile Services アプリへのプッシュ通知の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

このトピックでは、Azure Mobile Services を .NET バックエンドで使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、TodoList テーブルにレコードが挿入されるたびに、モバイル サービスは、.NET バックエンドから、登録されたすべての Windows ストア アプリおよび Windows Phone ストア アプリにプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[WACOM.NOTE]このトピックでは、Visual Studio Professional 2013 with Update 3 でツーリングを使用して、Mobile Services からユニバーサル Windows アプリへのプッシュ通知のサポートを追加する方法について説明します。同じ手順を使用して、モバイル サービスから Windows ストアまたは Windows Phone ストア 8.1 アプリへのプッシュ通知を追加することができます。Visual Studio Professional 2013 Update 3 にアップグレードできない場合や、モバイル サービス プロジェクトを手動で Windows ストア アプリ ソリューションに追加する場合は、トピックの「[このバージョン](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push)」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションをプッシュ通知に登録する](#register)
2. [サービスを更新してプッシュ通知を送信する](#update-service)
3. [ローカル テストのためにプッシュ通知を有効にする](#local-testing)
4. [アプリケーションでプッシュ通知をテストする](#test)

このチュートリアルを完了するには、以下が必要です。

* アクティブな [Microsoft ストア アカウント](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 以降のバージョン)。<br/>無料評価版が利用できます。 

##<a id="register"></a>アプリケーションをプッシュ通知に登録する

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p><code>\services\mobileServices\scripts</code> プロジェクト フォルダーに移動し、生成された <<em>your_service_name</em>>.push.register.js スクリプト ファイルを共有 <code>\js</code> フォルダーにコピーしてから、Windows アプリ プロジェクトと WindowsPhone アプリ プロジェクトの両方からこのファイルを削除します。</p></li> 
<li><p>このスクリプト ファイルを共有 <code>\js</code> プロジェクト フォルダーで開き、デバイスのチャネル URL を通知ハブに登録する <em>activated</em> イベント リスナーのコードを見つけ、<strong>done</strong> promise 関数を削除します。</p>
<p>このチュートリアルでは、カスタム API が呼び出されたときではなく、新しい項目が挿入されたときに通知を送信します。</p></li>
<li><p>Windows アプリ プロジェクトで、default.html ファイルを開き、スクリプト ファイル参照のパスを次のように共有 <code>\js</code> プロジェクト フォルダーに変更します。</p><pre><code><script src="/js/your_service_name.push.register.js"></script></code></pre></li>
<li><p>WindowsPhone アプリ プロジェクトでこの手順を繰り返します。</p>
<p>これで、両方のプロジェクトがプッシュ登録スクリプトの共有バージョンを使用することになります。</p></li>
</ol>

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するにはモバイル サービスを更新する必要があります。 

##<a id="update-service"></a>サービスを更新してプッシュ通知を送信する

次の手順では、既存の TodoItemController クラスを更新して、新しい項目が挿入されたときにプッシュ通知を登録済みのすべてのデバイスに送信します。同様のコードは、任意のカスタム [[ApiController]]、[[TableController]]、またはバックエンド サービスの任意の場所に実装できます。 

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> ローカル テストのためにプッシュ通知を有効にする

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

このセクションの残りの手順はオプションです。これらの手順により、ローカル コンピューターで実行されているモバイル サービスに対してアプリをテストできます。Azure で実行されているモバイル サービスを使用してプッシュ通知をテストする場合は、最後のセクションまでスキップしてもかまいません。これは、既にプッシュ通知の追加ウィザードで、Azure で実行されているサービスに接続するようにアプリが構成されているためです。  

>[WACOM.NOTE]運用環境のモバイル サービスをテストや開発作業を行う目的で使用しないでください。モバイル サービス プロジェクトは必ずテスト用の個別のステージング サービスに発行してください。

<ol start="5">
<li><p><code>\services\mobileServices\settings</code> プロジェクト フォルダーに移動し、生成された <<em>your_service_name</em>>.js スクリプト ファイルを共有 <code>\js</code> フォルダーにコピーしてから、Windows アプリ プロジェクトと WindowsPhone アプリ プロジェクトの両方からこのファイルを削除します。さらに、このファイルが各アプリ プロジェクトの <code>\services\mobileServices\scripts</code> フォルダーにも存在する場合は、そこからも削除します。</p></li> 
<li><p>このスクリプト ファイルを共有 <code>\js</code> プロジェクト フォルダーで開き、Azure で実行されているモバイル サービスへのアクセスに使用する <a href="http://msdn.microsoft.com/ja-jp/library/azure/jj554219.aspx">MobileServiceClient オブジェクト</a>を定義する既存のコードをコメント アウトします。</p></li>
<li><p>同じ名前で、ローカル ホストの URL を使用する新しい <strong>MobileServiceClient</strong> オブジェクト定義を次のようにコンストラクターに追加します。</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
var todolistClient = new WindowsAzure.MobileServiceClient(
	"http://localhost:4584");
</code></pre><p>この <strong>MobileServiceClient</strong> オブジェクトを使用して、アプリは Azure でホストされているバージョンではなく、ローカル サービスに接続するようになります。この設定を戻して、Azure でホストされているモバイル サービスに対してアプリを実行する場合は、元の <strong>MobileServiceClient</strong> オブジェクト定義に戻します。</p></li>
</ol>

##<a id="test"></a> アプリケーションでプッシュ通知をテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。次は、タグを使用して、プッシュ通知をモバイル サービスから認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[Send push notifications to authenticated users (認証されたユーザーへのプッシュ通知の送信)]」を行うことをお勧めします。

Mobile Services と通知ハブについては次のトピックを参照してください。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [Windows Azure 通知ハブとは]
  <br/>Notification Hubs がすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [通知ハブのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Notification Hubs ソリューションのトラブルシューティングとデバッグについて説明します。 

* [Azure モバイル サービス向け HTML/JavaScript クライアントを使用する方法]
  <br/>HTML および JavaScript のアプリから Mobile Services を使用する方法について説明します。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services (Mobile Services の使用)]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users

[Send push notifications to authenticated users (認証されたユーザーへのプッシュ通知の送信)]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[What are Notification Hubs? (Notification Hubs とは)]: /ja-jp/documentation/articles/notification-hubs-overview/

[How to use an HTML/JavaScript client for Azure Mobile Services (Azure Mobile Services 向け HTML/JavaScript クライアントを使用する方法)]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library
