<properties pageTitle="Xamarin.Android 用のモバイル サービスの使用" metaKeywords="Azure Xamarin.Android アプリケーション, モバイル サービス XamarinAndroid, Azure Xamarin.Android の使用" writer="craigd" description="Xamarin.Android アプリで Azure モバイル サービスを使用する方法を説明します。" metaCanonical="" 
	documentationCenter="Mobile" title="モバイル サービスの使用" />

# <a name="getting-started"></a>モバイル サービスの使用

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ja-jp/documentation/articles/mobile-services-windows-store-get-started" title="Windows ストア">Windows ストア</a>
	<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/ja-jp/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/ja-jp/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
	<a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android" class="current">Xamarin.Android</a>
	<a href="/ja-jp/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<!--<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/" title=".NET バックエンド">.NET バックエンド</a> |
	<a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>-->

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Azure のモバイル サービスを使用して Xamarin.Android アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な <em>To do list</em> アプリケーションの両方を作成します。</p>
<p>完成したアプリケーションのスクリーンショットは次のようになります。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">10:05</span></div>
</div>

![][0]

このチュートリアルを完了するには、(Windows 上に) Xamarin Studio と Visual Studio プラグインをインストールする [Xamarin.Android] と、最新の Android プラットフォームが必要です。このほか、Android SDK のバージョン 4.2 以降が必要になります。

ダウンロードしたクイック スタート プロジェクトには、Xamarin.Android 向けのモバイル サービス SDK が含まれています。このプロジェクトではバージョン 4.2 以降の Android をターゲットにしていますが、モバイル サービス SDK ではバージョン 2.2 以降があれば問題ありません。

<div class="dev-callout"><strong>注</strong><p> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2><span class="short-header">新しいアプリの作成</span>新しい Xamarin.Android アプリを作成する</h2>

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい Xamarin.Android アプリを作成します。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で [**Xamarin.Android**] を選択し、**[新しい iOS アプリを作成する]** を展開します。

	![][6]

	これにより、モバイル サービスに接続された Xamarin.Android アプリケーションを作成するための簡単な 3 つの手順が表示されます。

	![][7]

3. **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

4. **[アプリをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

	これにより、モバイル サービスに接続されている _To do list_ サンプル アプリケーションのプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Android アプリを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開します。

2. Xamarin Studio または Visual Studio で、**[File]**、**[Open]** の順にクリックし、サンプル ファイルを展開した場所を参照します。**XamarinTodoQuickStart.Android.sln** を選択して開きます。

 	![][8]

	![][9]

3. **[Run]** ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。エミュレーターまたは接続されている USB デバイスを選択するよう求めるメッセージが表示されます。

	<div class="dev-callout"><strong>注</strong><p>プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。</p></div>

4. アプリケーションで、意味のあるテキスト (たとえば、チュートリアルの完了) を入力し、**[Add]** をクリックします。

	![][10]

	これにより、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

	> [WACOM.NOTE] 
   	> モバイル サービスにアクセスして ToDoActivity.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。

6. 管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

	![][11]

	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

	![][12]

## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[モバイル サービスの使用]:#getting-started
[新しいモバイル サービスを作成する]:#create-new-service
[モバイル サービス インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[モバイル サービス Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[WindowsAzure.com]: http://www.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/

