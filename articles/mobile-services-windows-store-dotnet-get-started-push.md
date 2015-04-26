<properties 
	pageTitle="プッシュ通知の使用 (Windows ストア) | モバイル デベロッパー センター" 
	description="Azure Mobile Services を使用して Windows ストア アプリにプッシュ通知を送信する方法について説明します (従来のプッシュ)。" 
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
	ms.date="09/11/2014" 
	ms.author="glenga"/>

# Mobile Services アプリケーションにプッシュ通知を追加する (従来のプッシュ)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" class="current">Windows ストア C#</a>
    <a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows ストア JavaScript</a>
    <a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/ja-jp/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title=".NET backend">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="JavaScript backend" class="current">JavaScript バックエンド</a></div>	

このトピックでは、Visual Studio 2013 によって可能になる、Azure Mobile Services を使用した Windows Store アプリへのプッシュ通知の送信方法について説明します。このチュートリアルでは、Visual Studio から直接、Windows プッシュ通知サービス (WNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

>[AZURE.NOTE]このトピックは、まだ Notification Hubs 統合を使用するように<em>アップグレードされていない</em>、<em>既存の</em>モバイル サービスを対象とします。<em>新しい</em>モバイル サービスを作成すると、この統合機能は自動的に有効になります。新しいモバイル サービスについては、[プッシュ通知の使用](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)に関するページ を参照してください。
>
>Mobile Services は Azure Notification Hubs に統合され、テンプレート、複数のプラットフォーム、改良されたスケールなど、追加のプッシュ通知機能をサポートするようになりました。<em>可能な場合には、既存のモバイル サービスをアップグレードして Notification Hubs を使用することをお勧めします</em>。アップグレード後は、「[プッシュ通知の使用](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)」を参照してください。.

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [プッシュ通知用のアプリケーションを登録して Mobile Services を構成する]
2. [生成されたプッシュ通知コードを更新する]
3. [データを挿入して通知を受け取る]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。 

<h2><a name="register"></a>アプリケーションにプッシュ通知を追加して構成する</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p><strong>[サービス]</strong>、<strong>[モバイル サービス]</strong>、サービス名の順に展開し、生成されたコード ファイルを開きます。デバイスのインストール ID とチャネルを取得する <strong>UploadChannel</strong> メソッドを見つけ、このデータを新しい channels テーブルに挿入します。</p> 

<p>ウィザードによって、このメソッドの呼び出しも App.xaml.cs コード ファイルの <strong>OnLaunched</strong> イベント ハンドラーに追加されています。これにより、アプリケーションが起動されるたびにデバイスの登録が試行されます。</p></li> 
<li><p>サーバー エクスプローラーで、<strong>[Azure]</strong>、<strong>[モバイル サービス]</strong>、サービス名、<strong>[チャネル]</strong> の順に展開し、insert.js ファイルを開きます。</p> 

<p>モバイル サービスに格納されるこのファイルには、クライアントが channels テーブルにデータを挿入してデバイスの登録を要求するときに実行される JavaScript コードが含まれます。</p> 

> [AZURE.NOTE] このファイルの初期バージョンには、デバイスの既存の登録の有無を調べるコードが含まれています。さらに、新しい登録が channels テーブルに追加されたときにプッシュ通知を送信するコードも含まれています。プッシュ通知を送信するコードは、任意の登録済みのスクリプト ファイルに含めることができます。このスクリプトの場所は、通知のトリガー方法によって異なります。スクリプトは、テーブルの挿入、更新、削除、または読み取り操作として、スケジュールされたジョブとして、またはカスタム API として登録できます。詳細については、[Mobile Services でのサーバー スクリプトの使用](http://go.microsoft.com/fwlink/p/?LinkID=287178)に関するページを参照してください。

</li> 
<li><p>F5 キーを押してアプリケーションを実行し、モバイル サービスから通知を即座に受信することを確認します。</p>
<p>この通知は、新しい channels テーブルに行を挿入することによって生成されています (デバイスの登録)。</p>
</li>
</ol>
生成されたコードを使用するとアプリケーションが実行されたときの通知を簡単に示すことができますが、これは一般的には意味のあるシナリオではありません。次に、channels テーブルから通知コードを削除し、TodoItem テーブル内で変更を加えたコードで置き換えます。 

<h2><a name="update-scripts"></a>生成されたプッシュ通知コードを更新する</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>アプリケーションでプッシュ通知をテストする</h2>

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

   	![][13]

   	挿入が完了すると、アプリケーションは、WNS からプッシュ通知を受け取ります。

   	![][14]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

+ [Notification Hubs の使用]
  <br/>Windows ストア アプリで Notification Hubs を活用する方法について説明します。

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

* [Mobile Services .NET の使用方法の概念リファレンス]
  <br/>.NET で Mobile Services を使用する方法について説明します。

<!-- Anchors. -->
[プッシュ通知用のアプリケーションを登録して Mobile Services を構成する]: #register
[生成されたプッシュ通知コードを更新する]: #update-scripts
[データを挿入して通知を受け取る]: #test
[次のステップ]:#next-steps

<!-- Images. -->











[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet/
[モバイル サービスでのデータの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet/
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-dotnet
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js

[Azure の管理ポータル]: https://manage.windowsazure.com/
[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library/
[スクリプトを使用したデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
[Notification Hubs の使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
[Notification Hubs とは]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
[登録者への通知の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet/
[ユーザーへの通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
[ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293



<!--HONumber=42-->
