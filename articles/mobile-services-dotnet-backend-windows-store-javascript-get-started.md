<properties linkid="develop-mobile-tutorials-get-started" urlDisplayName="作業の開始" pageTitle="Windows ストア アプリ向けモバイル サービスの使用 | モバイル デベロッパー センター" metaKeywords="" description="このチュートリアルでは、C#、VB、または JavaScript で Windows ストア用の開発を行う場合に Azure のモバイル サービスを使用する方法を示します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスの使用" authors="glenga" solutions="" manager="" editor="" />


# <a name="getting-started"> </a>モバイル サービスの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="Windows ストア JavaScript" class="current">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS">iOS</a>	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android">Android</a><!--<a href="/ja-jp/documentation/articles/get-started-html" title="HTML">HTML</a><a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a><a href="/ja-jp/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap" class="current">PhoneGap</a>--></div>

<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/" title=".NET バックエンド" class="current">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-windows-store-get-started/"  title="JavaScript バックエンド" >JavaScript バックエンド</a>
</div>

このチュートリアルでは、Azure のモバイル サービスを使用して Windows ストア アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する HTML および JavaSript の簡単な _To do list_ アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、モバイル サービスを管理します。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バージョン]」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了することは、Windows ストア アプリの他のすべてのモバイル サービス チュートリアルの前提条件です。

>[WACOM.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started%2F" target="_blank">無料評価版のサイト</a>を参照してください。<br />このチュートリアルには <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> が必要です。無料評価版が利用できます。

## 新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 新しい Windows ストア アプリを作成する

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービスに接続された新しい Windows ストア アプリを作成します。

1. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。
   
2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows]** を選択し、**[新しい Windows ストア アプリを作成する]** を展開します。

   	![][6]

   	これにより、モバイル サービスに接続された Windows ストア アプリを作成するための簡単な 3 つの手順が表示されます。

  	![][7]

3. まだインストールしていない場合は、<a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> をダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。

4. **[アプリケーションとサービスをダウンロードしてローカルに実行する]** で、Windows ストア アプリの言語を選択し、**[ダウンロード]** をクリックします。

  	これにより、モバイル サービスとモバイル サービスに接続されている _To do list_ サンプル アプリケーションの両方のプロジェクトを含むソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

5.  **[クラウドへのサービスの発行]** で、発行プロファイルをクリックしてダウンロードします。ダウンロードされたファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

	<div class="dev-callout"><strong>セキュリティ上の注意</strong><p>ダウンロードしたファイルに含まれている情報を使用して他のユーザーがお客様のサービスにアクセスすることも可能であるため、発行プロファイルをインポートした後、ダウンロードしたファイルを削除することを検討してください。</p></div>

## ローカル モバイル サービスに対してアプリケーションをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[WACOM.NOTE]モバイル サービスにアクセスして default.js ファイルにあるデータを照会および挿入するコードを確認できます。

## モバイル サービスを発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>Windows ストア アプリ プロジェクトで、App.xaml.cs ファイルを開き、<a href="http://msdn.microsoft.com/ja-jp/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a> インスタンスを作成するコードを見つけて、<em>localhost</em> を使用してこのクライアントを作成するコードをコメント アウトし、リモート モバイル サービス URL を使用してクライアントを作成するコードのコメントを解除すると、次のようになります。</p>

        <pre><code>var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXX-APPLICATION-KEY-XXXXXX"
        );</code></pre>

	<p>クライアントは Azure に発行されたモバイル サービスにアクセスするようになります。</p></li>

<li><p><strong>F5</strong> キーを押してプロジェクトを再ビルドし、アプリケーションを開始します。</p></li>

<li><p>アプリケーションで、<strong>[Insert a TodoItem]</strong> ボックスに意味のあるテキスト (たとえば、「<em>チュートリアルの完了</em>」) を入力し、<strong>[Save]</strong> をクリックします。</p>

<p>これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。</p>
</li>
</ol>

<!--This should be in the list HTML, but there is a bug-->
![][10]

## 次のステップ
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
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript と HTML]: mobile-services-win8-javascript/
[管理ポータル]: https://manage.windowsazure.com/
[JavaScript バージョン]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started
[Visual Studio 2012 を使用したモバイル サービスでのデータの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012

