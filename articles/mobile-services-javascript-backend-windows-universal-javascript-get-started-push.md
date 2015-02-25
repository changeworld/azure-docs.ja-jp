<properties pageTitle="JavaScript バックエンド モバイル サービスを使用したプッシュ通知の使用" description="Azure Mobile Services と Notification Hubs を使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。" services="mobile-services, notification-hubs" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga"/>


# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

このトピックでは、Azure Mobile Services を JavaScript バックエンドで使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、TodoList テーブルにレコードが挿入されるたびに、モバイル サービスは、JavaScript バックエンドから、登録されたすべての Windows ストア アプリおよび Windows Phone ストア アプリにプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[AZURE.NOTE]このトピックでは、Visual Studio 2013 with Update 3 でツーリングを使用して、Mobile Services からユニバーサル Windows アプリへのプッシュ通知のサポートを追加する方法について説明します。同じ手順を使用して、モバイル サービスから Windows ストアまたは Windows Phone ストア 8.1 アプリへのプッシュ通知を追加することができます。Visual Studio 2013 Update 3 にアップグレードできない場合や、モバイル サービス プロジェクトを手動で Windows ストア アプリ ソリューションに追加する場合は、このトピックの[このバージョン] (/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションをプッシュ通知に登録する](#register)
2. [サービスを更新してプッシュ通知を送信する](#update-service)
3. [アプリケーションでプッシュ通知をテストする](#test)

このチュートリアルを完了するには、以下が必要です。

* アクティブな [Microsoft ストア アカウント](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* [Visual Studio 2013 Express for Windows](http://go.microsoft.com/fwlink/?LinkId=257546) Update 3 以降のバージョン 

##<a id="register"></a>アプリケーションをプッシュ通知に登録する

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p><code>\services\mobileServices\scripts</code> プロジェクト フォルダーに移動し、生成された &lt;<em>your_service_name</em>&gt;.push.register.js スクリプト ファイルを共有 <code>\js</code> フォルダーにコピーしてから、Windows アプリ プロジェクトと WindowsPhone アプリ プロジェクトの両方からこのファイルを削除します。</p></li> 
<li><p>このスクリプト ファイルを共有 <code>\js</code> プロジェクト フォルダーで開き、デバイスのチャネル URL を通知ハブに登録する <em>activated</em> イベント リスナーのコードを見つけ、<strong>done</strong> promise 関数を削除します。</p>
<p>このチュートリアルでは、カスタム API が呼び出されたときではなく、新しい項目が挿入されたときに通知を送信します。</p></li>
<li><p>Windows アプリ プロジェクトで、default.html ファイルを開き、スクリプト ファイル参照のパスを次のように共有 <code>\js</code> プロジェクト フォルダーに変更します。</p><pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>
<li><p>WindowsPhone アプリ プロジェクトでこの手順を繰り返します。</p>
<p>これで、両方のプロジェクトがプッシュ登録スクリプトの共有バージョンを使用することになります。</p></li>
</ol>

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するにはモバイル サービスを更新する必要があります。 

##<a id="update-service"></a>サービスを更新してプッシュ通知を送信する

次の手順では、TodoItem テーブルに登録されている挿入スクリプトを更新します。同様のコードを任意のサーバー スクリプトまたはバックエンド サービスの任意の場所に実装することができます。 

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

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

* [Azure Mobile Services 向け HTML/JavaScript クライアントを使用する方法]
  <br/>HTML および JavaScript のアプリから Mobile Services を使用する方法について説明します。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users

[認証されたユーザーへのプッシュ通知の送信]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/

[Notification Hubs とは]: /ja-jp/documentation/articles/notification-hubs-overview/

[Azure Mobile Services 向け HTML/JavaScript クライアントを使用する方法]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library


<!--HONumber=42-->
