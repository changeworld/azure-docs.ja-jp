<properties pageTitle="HTML 5 アプリケーション向けの Azure のモバイル サービスの使用" metaKeywords="" description="このチュートリアルでは、HTML 用の開発を行う場合に Azure のモバイル サービスを使用する方法を示します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスの使用" authors="" solutions="" writer="glenga" manager="" editor=""  />


# <a name="getting-started"> </a>モバイル サービスの使用

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ja-jp/documentation/articles/mobile-services-windows-store-get-started" title="Windows ストア">Windows ストア</a>
	<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/ja-jp/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/ja-jp/documentation/articles/mobile-services-html-get-started" title="HTML" class="current">HTML</a>
	<a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/ja-jp/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<!--<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-html-get-started/" title=".NET バックエンド">.NET バックエンド</a> |
	<a href="/ja-jp/documentation/articles/mobile-services-html-get-started/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>-->

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Azure のモバイル サービスを使用して HTML アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な <em>To do list</em> アプリケーションの両方を作成します。このチュートリアルのビデオ バージョンを表示するには、右側のクリップをクリックします。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">3:51</span></div>
</div>
 
完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了することは、HTML アプリの他のすべてのモバイル サービス チュートリアルの前提条件です。

<div class="dev-callout"><strong></strong>注<p> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

###その他の要件

+ このチュートリアルでは、ローカル コンピューター上で次のいずれかの Web サーバーが実行されている必要があります。

	+  **Windows**: IIS ExpressIIS Express は、[Microsoft Web プラットフォーム インストーラー]によってインストールされます。
	+  **MacOS X**: Python。既にインストールされています。
	+  **Linux**: Python。[最新バージョンの Python] をインストールする必要があります。
	
	このアプリケーションは任意の Web サーバーを使用してホストすることができますが、これらの Web サーバーは、ダウンロードしたスクリプトでサポートされています。

+ HTML5 をサポートする Web ブラウザー。


## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>新しいアプリケーションを作成する<span class="short-header">新しい HTML アプリケーションを作成する</span></h2>

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい HTML アプリを作成します。

1. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

   
2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows]** を選択し、**[新しい HTML アプリを作成する]** を展開します。

   	![][6]

   	これにより、モバイル サービスに接続された HTML アプリを作成し、ホストするための簡単な 3 つの手順が表示されます。

  	![][7]

3. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

4. **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

  	これにより、モバイル サービスに接続されている To do list サンプル アプリケーションの Web サイト ファイルがダウンロードされます。圧縮されたファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

5. **[構成]** タブで、**[クロス オリジン リソース共有 (CORS)]** の **[ホスト名からの要求を許可する]** に  `localhost` が既に表示されていることを確認します。表示されていない場合は、**[ホスト名]** フィールドに「`localhost`」と入力し、**[保存]** をクリックします。

  	![][9]

	<div class="dev-callout"><b>注:</b>
		<p>localhost 以外の Web サーバーにクイック スタート アプリケーションを展開する場合は、<strong>[ホスト名からの要求を許可する]</strong> の一覧に Web サーバーのホスト名を追加する必要があります。詳細については、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn155871.aspx" target="_blank">クロス オリジン リソース共有</a>」を参照してください。</p>
	</div>

## HTML アプリケーションのホストと実行

このチュートリアルの最後に、ローカル コンピューターの新しいアプリケーションをホストして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、**server** サブフォルダーから次のコマンド ファイルの 1 つを開始します。

	+ **launch-windows** (Windows コンピューター)
	+ **launch-mac.command** (Mac OS X コンピューター)
	+ **launch-linux.sh** (Linux コンピューター)

	<div class="dev-callout"><b>注:</b>
		<p>Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、`R`と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。</p>
	</div>

	これにより、新しいアプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。

2. Web ブラウザーで URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> を開いて、アプリケーションを開始します。

3. アプリケーションで、意味のあるテキスト (たとえば、チュートリアルの完了) を **[Enter new task]** に入力し、**[Add]** をクリックします。

   	![][10]

   	これにより、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目はモバイル サービスによって返され、データはアプリケーションの 2 番目の列に表示されます。

	<div class="dev-callout"> 
	<b>注:</b>
   	<p>モバイル サービスにアクセスして app.js ファイルにあるデータを照会および挿入するコードを確認できます。</p>
 	</div>

4. 管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

   	![][11]

   	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

   	![][12]

## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。

* **[データの使用]**
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* **[認証の使用]**
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* **[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]**
  <br/>HTML/JavaScript でモバイル サービスを使用する方法について説明します。

<!-- Anchors. -->
[モバイル サービスの使用]:#getting-started
[新しいモバイル サービスを作成する]:#create-new-service
[モバイル サービス インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-html
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-html

[管理ポータル]: https://manage.windowsazure.com/
[Microsoft Web プラットフォーム インストーラー]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[最新バージョンの Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-html-js-client
[クロス オリジン リソース共有]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn155871.aspx

