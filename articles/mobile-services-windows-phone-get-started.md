<properties pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Mobile Services の使用

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Azure のモバイル サービスを使用して Windows Phone 8 アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な <em>To do list</em> アプリケーションの両方を作成します。</p>
<p>右側のクリップを見ると、このチュートリアルと同じ手順をビデオで確認できます。このビデオでは Nick Harris がモバイル サービスを紹介し、初めてのモバイル サービスを作成して Windows ストア アプリから接続する方法を説明しています。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">13:18:00</span></div>

</div>

このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために JavaScript を使用します。Visual Studio を使用してサポートされる .NET 言語でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[.NET バックエンド バージョン][.NET バックエンド バージョン]」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。新しい Windows Phone 8.1 アプリケーションを作成するには、Visual Studio 2013 Update 2 以降が必要です。

## <a name="create-new-service"> </a> 新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 

## <span class="short-header">新しいアプリケーションを作成する</span>新しい Windows Phone アプリケーションを作成する

</h2>
モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい Windows Phone 8 アプリを作成します。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2.  [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows Phone 8]** を選択し、**[新しい Windows Phone 8 アプリを作成する]** を展開します。

    ![][1]

    これにより、モバイル サービスに接続された Windows Phone アプリを作成するための簡単な 3 つの手順が表示されます。

    ![][2]

3.  まだインストールしていない場合は、[Visual Studio 2012 Express for Windows Phone][Visual Studio 2012 Express for Windows Phone] をダウンロードしてローカル コンピューターにインストールします。

    > [WACOM.NOTE] Windows Phone 8.1 アプリを作成するには、Visual Studio 2013 Update 2 をインストールする必要があります。

4.  **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5.  **[アプリをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

    これにより、モバイル サービスに接続する、*To do list* サンプル アプリケーションのプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Windows Phone アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1.  圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、Visual Studio でソリューション ファイルを開きます。

2.  (省略可能) Windows Phone 8.1 アプリケーションを作成する場合、ソリューション エクスプローラーでプロジェクトを右クリックし、**[プロパティ]** をクリックして **[Windows Phone OS バージョンの対象]** を **[Windows Phone 8.1]** に設定し、**[はい]** をクリックしてプロジェクトをアップグレードします。

3.  **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4.  アプリケーションで、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、**[保存]** をクリックします。

    ![][3]

    これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

    > [WACOM.NOTE] モバイル サービスにアクセスして MainPage.xaml.cs ファイルにあるデータを照会および挿入するコードを確認できます。

5.  管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

    ![][4]

    これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

    ![][5]

## <a name="next-steps"> </a> 次のステップ

クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。

-   [データの使用][データの使用]
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [オフライン データの同期の使用][オフライン データの同期の使用]
    オフライン データの同期を使用してアプリケーションの反応と信頼性を高める方法について説明します。

-   [認証の使用][認証の使用]
    ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [.NET バックエンド バージョン]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2FFja-jp%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [3]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png
  [データの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data/
  [オフライン データの同期の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-users/
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
