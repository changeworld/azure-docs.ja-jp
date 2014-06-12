<properties pageTitle="Windows ストア アプリケーション向け Mobile Services の使用 | モバイル デベロッパー センター" metaKeywords="" description="このチュートリアルでは、C# または JavaScript で Windows ストア用の開発を行う場合に Azure Mobile Services を使用する方法を示します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスの使用" authors="glenga" solutions="" manager="" editor="" />


# <a name="getting-started"> </a>モバイル サービスの使用

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
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">10:08</span></div>
</div>

このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な _To do list_ アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために JavaScript を使用します。Visual Studio を使用してサポートされる .NET 言語でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「.NET バックエンド バージョン」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了することは、Windows ストア アプリの他のすべてのモバイル サービス チュートリアルの前提条件です。

>[WACOM.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F)を参照してください。

>このチュートリアルには、Visual Studio 2013 が必要です。Visual Studio 2012 を使用して Windows ストア アプリケーションに接続するには、「[Visual Studio 2012 を使用した Mobile Services でのデータの使用](/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/)」の手順に従ってください。

## 新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## 新しい Windows ストア アプリを作成する

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい Windows ストア アプリを作成します。

1. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

   
2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows]** を選択し、**[新しい Windows ストア アプリを作成する]** を展開します。

   	![][6]

   	これにより、モバイル サービスに接続された Windows ストア アプリを作成するための簡単な 3 つの手順が表示されます。

  	![][7]

3. まだインストールしていない場合は、[Visual Studio 2013 Express for Windows] をダウンロードしてローカル コンピューターまたは仮想マシンにインストールします。

4. **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5. **[アプリケーションをダウンロードして実行する]** で、アプリケーションの言語を選択し、**[ダウンロード]** をクリックします。

  	これにより、モバイル サービスに接続する、_To do list_ サンプル アプリケーションのプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Windows アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、Visual Studio 2013 Express for Windows でソリューション ファイルを開きます。

2. **F5** キーを押してプロジェクトを再ビルドし、アプリケーションを開始します。

3. アプリケーションで、**[Insert a TodoItem]** ボックスに意味のあるテキスト (たとえば、「_チュートリアルの完了_」) を入力し、**[Save]** をクリックします。

   	![][10]

   	これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目はモバイル サービスによって返され、データはアプリケーションの 2 番目の列に表示されます。

	>[WACOM.NOTE]モバイル サービスにアクセスして MainPage.xaml.cs ファイル (C#/XAML プロジェクト) または default.js ファイル (C#/XAML プロジェクト) にあるデータを照会および挿入するコードを確認できます。

4. 管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

   	![][11]

   	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

   	![][12]

## 次のステップ
クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。

* **データの使用** ( [C#][Get started with data] / [JavaScript][Get started with data JS] )
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* **認証の使用** ( [C#][Get started with authentication] / [JavaScript][Get started with authentication JS] )
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* **プッシュ通知の使用** ( [C#][Get started with push notifications] / [JavaScript][Get started with push notifications JS] )
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[モバイル サービスの使用]:#getting-started
[新しいモバイル サービスを作成する]:#create-new-service
[モバイル サービス インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-windows-store-get-started/mobile-vs2013-project.png

[10]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png


<!-- URLs. -->
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet
[データの使用 (JS)]: /ja-jp/develop/mobile/tutorials/get-started-with-data-js
[認証の使用 (JS)]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
[プッシュ通知の使用 (JS)]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript と HTML]: mobile-services-win8-javascript/
[管理ポータル]: https://manage.windowsazure.com/
[.NET バックエンド バージョン]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Visual Studio 2012 を使用したモバイル サービスでのデータの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012

