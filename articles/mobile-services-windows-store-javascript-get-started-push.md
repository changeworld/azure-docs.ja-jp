<properties urlDisplayName="Get Started with Push (JS)" pageTitle="プッシュ通知の使用 (従来のプッシュ) | モバイル デベロッパー センター" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store JavaScript app (legacy push)." metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# Mobile Services アプリケーションにプッシュ通知を追加する (従来のプッシュ)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows ストア C#</a>
    <a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript" class="current">Windows ストア JavaScript</a>
    <a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/ja-jp/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title=".NET backend">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="JavaScript backend" class="current">JavaScript バックエンド</a></div>		

このトピックでは、Visual Studio 2013 によって可能になる、Azure モバイル サービスを使用した Windows ストア アプリへのプッシュ通知の送信方法について説明します。このチュートリアルでは、Visual Studio から直接、Windows プッシュ通知サービス (WNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

>[WACOM.NOTE]このトピックは、まだ Notification Hubs 統合を使用するように<em>アップグレードされていない</em>、<em>既存</em>のモバイル サービスを対象とします。<em>新しい</em>モバイル サービスを作成すると、この統合機能は自動的に有効になります。新しいモバイル サービスについては、「[Mobile Services アプリへのプッシュ通知の追加](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/)」を参照してください。
>
>モバイル サービスは Azure 通知ハブに統合され、テンプレート、複数のプラットフォーム、改良されたスケールなど、追加のプッシュ通知機能をサポートするようになりました。<em>可能な限り、既存のモバイル サービスをアップグレードして Notification Hubs を使用するようにしてください</em>。アップグレードしたら、「[Mobile Services アプリへのプッシュ通知の追加](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/)」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [プッシュ通知用のアプリケーションを登録して Mobile Services を構成する]
2. [生成されたプッシュ通知コードを更新する]
3. [データを挿入して通知を受け取る]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。 

<h2><a name="register"></a>プッシュ通知を追加し、アプリケーションを構成する</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>生成された push.register.js コード ファイルを開きます。デバイスのインストール ID とチャネルを取得するコードを見つけ、このデータを新しい <strong>channels</strong> テーブルに挿入します。</p> 

	<p>このテーブルは、プッシュ通知の追加ウィザードによってモバイル サービスに作成されています。このコードにより、アプリケーションがアクティブ化されるたびにデバイスの登録が試行されます。</p></li>
<li><p>[サーバー エクスプローラー] で <strong>Azure</strong>、 <strong>Mobile Services</strong>、サービス名、 <strong>channels</strong>の順に展開し、insert.js ファイルを開きます。</p> 

<p>モバイル サービスに格納されるこのファイルには、クライアントが channels テーブルにデータを挿入してデバイスの登録を要求するときに実行される JavaScript コードが含まれます。</p> 

<div class="dev-callout"><b>注</b>
	<p>このファイルの初期バージョンには、デバイスの既存の登録の有無を調べるコードが含まれています。さらに、新しい登録が channels テーブルに追加されたときにプッシュ通知を送信するコードも含まれています。プッシュ通知を送信するコードは、任意の登録済みのスクリプト ファイルに含めることができます。このスクリプトの場所は、通知のトリガー方法によって異なります。スクリプトは、テーブルの挿入、更新、削除、または読み取り操作として、スケジュールされたジョブとして、またはカスタム API として登録できます。詳細については、「 <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">モバイル サービスのサーバー スクリプトの操作</a>」を参照してください。</p>
</div>
</li> 
<li><p>F5 キーを押してアプリケーションを実行し、モバイル サービスから通知を即座に受信することを確認します。</p>
<p>この通知は、新しい channels テーブルに行を挿入することによって生成されています (デバイスの登録)。</p>
</li>
</ol>

生成されたコードを使用するとアプリケーションが実行されたときの通知を簡単に示すことができますが、これは一般的には意味のあるシナリオではありません。次に、channels テーブルから通知コードを削除し、TodoItem テーブル内で変更を加えたコードで置き換えます。 

<h2><a name="update-scripts"></a>生成されたプッシュ通知コードを更新する</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>アプリケーションでプッシュ通知をテストする</h2>

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

   	![][13]

   	挿入が完了すると、アプリケーションは、WNS からプッシュ通知を受け取ります。

   	![][14]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、モバイル サービスに用意されている基本的なプッシュ通知機能を示します。より高度な機能 (たとえば、クロスプラットフォーム通知の送信、サブスクリプション ベースのルーティング、大ボリューム) を必要とする場合は、モバイル サービスで Azure 通知ハブを使用することを検討してください。詳細については、通知ハブに関する次のトピックのいずれかを参照してください。

+ [通知ハブの使用]
  <br/>Windows ストア アプリで通知ハブを活用する方法について説明します。

+ [登録者への通知の送信]
	<br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

+ [ユーザーへの通知の送信]
	<br/>モバイル サービスから任意のデバイスの特定のユーザーにプッシュ通知を送信する方法について説明します。

+ [ユーザーへのクロスプラットフォーム通知の送信]
	<br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。

次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

* [Mobile Services HTML/JavaScript の使用方法の概念リファレンス]
  <br/>HTML および JavaScript でモバイル サービスを使用する方法について説明します。  

<!-- Anchors. -->
[プッシュ通知用のアプリケーションを登録して Mobile Services を構成する]: #register
[生成されたプッシュ通知コードを更新する]: #update-scripts
[データを挿入して通知を受け取る]: #test
[次のステップ]:#next-steps

<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Mobile Services の使用]: /ja-jp/develop/mobile/tutorials/get-started/
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-js/
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-js
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js

[Azure の管理ポータル]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-html-js-client/
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[通知ハブの使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
[Notification Hubs とは]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
[登録者への通知の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet/
[ユーザーへの通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
[ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/

<!--HONumber=35.1-->
