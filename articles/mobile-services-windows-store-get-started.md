<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Mobile Services の使用

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-get-started" title="Windows ストア" class="current">Windows ストア</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
<a href="/ja-jp/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
<a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
<a href="/ja-jp/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
<a href="/ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title=".NET バックエンド">.NET バックエンド</a> | 
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-get-started/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Azure のモバイル サービスを使用して Windows ストア アプリにクラウドベースのバックエンド サービスを追加する方法を示します。</p>
<p>右側のクリップを見ると、このチュートリアルと同じ手順をビデオで確認できます。このビデオでは Scott Guthrie がモバイル サービスを紹介し、初めてのモバイル サービスを作成して Windows ストア アプリから接続する方法を説明しています。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">10:08:00</span></div>

</div>

このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な *To do list* アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために JavaScript を使用します。Visual Studio を使用してサポートされる .NET 言語でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「.NET バックエンド バージョン」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了することは、Windows ストア アプリの他のすべてのモバイル サービス チュートリアルの前提条件です。

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

> このチュートリアルには、Visual Studio 2013 が必要です。Visual Studio 2012 を使用して Windows ストア アプリに接続するには、「[Visual Studio 2012 を使用したモバイル サービスでのデータの使用][Visual Studio 2012 を使用したモバイル サービスでのデータの使用]」の手順に従ってください。

## 新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 新しい Windows ストア アプリを作成する

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい Windows ストア アプリを作成します。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2.  [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows]** を選択し、**[新しい Windows ストア アプリを作成する]** を展開します。

    ![][1]

    これにより、モバイル サービスに接続された Windows ストア アプリを作成するための簡単な 3 つの手順が表示されます。

    ![][2]

3.  まだインストールしていない場合は、[Visual Studio 2013 Express for Windows][Visual Studio 2013 Express for Windows] をダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。

4.  **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5.  **[アプリケーションをダウンロードして実行する]** で、アプリケーションの言語を選択し、**[ダウンロード]** をクリックします。

    これにより、モバイル サービスに接続する、*To do list* サンプル アプリケーションのプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Windows アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1.  圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、Visual Studio 2013 Express for Windows でソリューション ファイルを開きます。

2.  **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

3.  アプリケーションで、**[Insert a TodoItem]** ボックスに意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、**[Save]** をクリックします。

    ![][3]

    これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目はモバイル サービスによって返され、データはアプリケーションの 2 番目の列に表示されます。

    > [WACOM.NOTE]モバイル サービスにアクセスして MainPage.xaml.cs ファイル (C#/XAML プロジェクト) または default.js ファイル (JavaScript/HTML プロジェクト) にあるデータを照会および挿入するコードを確認できます。

4.  管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

    ![][4]

    これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

    ![][5]

## 次のステップ

クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。

-   **データの使用** ([C#][C#]/[JavaScript][JavaScript])

    モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

-   **認証の使用** ([C#][6]/[JavaScript][7])

    ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

-   **プッシュ通知の使用** ([C#][8]/[JavaScript][9])

    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。



  [0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Visual Studio 2012 を使用したモバイル サービスでのデータの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
  [2]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
  [Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [3]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
  [4]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png
  [C#]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet
  [JavaScript]: /ja-jp/develop/mobile/tutorials/get-started-with-data-js
  [6]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
  [7]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
  [8]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet
  [9]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
