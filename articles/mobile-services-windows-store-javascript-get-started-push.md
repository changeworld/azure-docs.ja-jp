<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store JavaScript app (legacy push)." metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# モバイル サービスでのプッシュ通知の使用 (従来のプッシュ)

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows ストア JavaScript" class="current">Windows ストア JavaScript</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
<a href="/ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title=".NET バックエンド">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a></div>

このトピックでは、Visual Studio 2013 によって可能になる、Azure モバイル サービスを使用した Windows ストア アプリへのプッシュ通知の送信方法について説明します。このチュートリアルでは、Visual Studio から直接、Windows プッシュ通知サービス (WNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

> [WACOM.NOTE]このトピックでは、通知ハブの統合を使用するためのアップグレードをまだ実行していない、既存のモバイル サービスについて説明します。新しいモバイル サービスを作成すると、この統合された機能が自動的に有効になります。新しいモバイル サービスについては、「[通知ハブの使用][通知ハブの使用]」を参照してください。
>
> モバイル サービスは Azure 通知ハブに統合され、テンプレート、複数のプラットフォーム、改善されたスケールなど、追加のプッシュ通知機能をサポートします。可能な場合には、既存のモバイル サービスをアップグレードして通知ハブを使用することをお勧めします。アップグレード後は、「[通知ハブの使用][通知ハブの使用]」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [プッシュ通知用のアプリケーションを登録してモバイル サービスを構成する][プッシュ通知用のアプリケーションを登録してモバイル サービスを構成する]
2.  [生成されたプッシュ通知コードを更新する][生成されたプッシュ通知コードを更新する]
3.  [データを挿入して通知を受け取る][データを挿入して通知を受け取る]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」または「[モバイル サービスでのデータの使用][モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。

## <a name="register"></a><span class="short-header">アプリケーションの登録</span>プッシュ通知を追加し、アプリケーションを構成する

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  生成された push.register.js コード ファイルを開きます。デバイスのインストール ID とチャネルを取得するコードを見つけ、このデータを新しい **channels** テーブルに挿入します。

    このテーブルは、プッシュ通知の追加ウィザードによってモバイル サービスに作成されています。このコードにより、アプリケーションがアクティブ化されるたびにデバイスの登録が試行されます。

2.  サーバー エクスプローラーで、**[Azure]**、**[モバイル サービス]**、\<サービス名\>、**[channels]** の順に展開し、insert.js ファイルを開きます。

    モバイル サービスに格納されるこのファイルには、クライアントが channels テーブルにデータを挿入してデバイスの登録を要求するときに実行される JavaScript コードが含まれます。

    <div class="dev-callout">

    <b>注</b>
    <p>このファイルの初期バージョンには、デバイスの既存の登録の有無を調べるコードが含まれています。さらに、新しい登録が channels テーブルに追加されたときにプッシュ通知を送信するコードも含まれています。プッシュ通知を送信するコードは、任意の登録済みのスクリプト ファイルに含めることができます。このスクリプトの場所は、通知のトリガー方法によって異なります。スクリプトは、テーブルの挿入、更新、削除、または読み取り操作として、スケジュールされたジョブとして、またはカスタム API として登録できます。詳細については、<a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">モバイル サービスのサーバー スクリプトの操作に関するページ</a> を参照してください。</p>

    </div>

3.  F5 キーを押してアプリケーションを実行し、モバイル サービスから通知を即座に受信することを確認します。

    この通知は、新しい channels テーブルに行を挿入することによって生成されています (デバイスの登録)。

生成されたコードを使用するとアプリケーションが実行されたときの通知を簡単に示すことができますが、これは一般的には意味のあるシナリオではありません。次に、channels テーブルから通知コードを削除し、TodoItem テーブル内で変更を加えたコードで置き換えます。

## <a name="update-scripts"></a><span class="short-header">コードの更新</span>生成されたプッシュ通知コードを更新する

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

## <a name="test"></a><span class="short-header">アプリケーションのテスト</span>アプリケーションでプッシュ通知をテストする

1.  Visual Studio で、F5 キーを押してアプリケーションを実行します。

2.  アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

    ![][0]

    挿入が完了すると、アプリケーションは、WNS からプッシュ通知を受け取ります。

    ![][1]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、モバイル サービスに用意されている基本的なプッシュ通知機能を示します。より高度な機能 (たとえば、クロスプラットフォーム通知の送信、サブスクリプション ベースのルーティング、大ボリューム) を必要とする場合は、モバイル サービスで Azure 通知ハブを使用することを検討してください。詳細については、通知ハブに関する次のトピックのいずれかを参照してください。

-   [通知ハブの使用][2]
   
    Windows ストア アプリで通知ハブを活用する方法について説明します。

-   [登録者への通知の送信][登録者への通知の送信]
   
    ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

-   [ユーザーへの通知の送信][ユーザーへの通知の送信]
   
    モバイル サービスから任意のデバイスの特定のユーザーにプッシュ通知を送信する方法について説明します。

-   [ユーザーへのクロスプラットフォーム通知の送信][ユーザーへのクロスプラットフォーム通知の送信]
   
    テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。

次のモバイル サービスのトピックの詳細を確認することをお勧めします。

-   [データの使用][モバイル サービスでのデータの使用]
   
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [認証の使用][認証の使用]
   
    Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
   
    サーバー スクリプトの登録および使用について説明します。

-   [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス][モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]
   
    HTML と JavaScript でモバイル サービスを使用する方法について説明します。



  [通知ハブの使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [プッシュ通知用のアプリケーションを登録してモバイル サービスを構成する]: #register
  [生成されたプッシュ通知コードを更新する]: #update-scripts
  [データを挿入して通知を受け取る]: #test
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/
  [モバイル サービスでのデータの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-js/
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-create-new-push-vs2013-2]: ../includes/mobile-services-create-new-push-vs2013-2.md
  [0]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png
  [2]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
  [登録者への通知の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet/
  [ユーザーへの通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
  [ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
  [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-html-js-client/
