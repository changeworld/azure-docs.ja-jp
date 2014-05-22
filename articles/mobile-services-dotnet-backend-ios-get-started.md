<properties pageTitle="iOS アプリ向け Azure モバイル サービスの使用" metaKeywords="Azure iOS アプリケーション, モバイル サービス iOS, 概要 Azure iOS" description="このチュートリアルでは、iOS 用の開発を行う場合に Azure モバイル サービスを使用する方法を示します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスの使用" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>モバイル サービスの使用

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="Windows ストア C#">Windows ストア C#</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS" class="current">iOS</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android">Android</a>
	<!--<a href="/ja-jp/documentation/articles/get-started-html" title="HTML">HTML</a>
	<a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/ja-jp/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>-->
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started/" title=".NET バックエンド" class="current">.NET バックエンド</a> |
	<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started/"  title="JavaScript バックエンド" >JavaScript バックエンド</a>
</div>

このチュートリアルでは、Azure モバイル サービスを使用して iOS アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な _To do list_ アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、モバイル サービスを管理します。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン]」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了するには、XCode 4.5 および iOS 5.0 以降のバージョンが必要です。

<div class="dev-callout"><strong>注</strong><p> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## ローカル コンピューターにモバイル サービスをダウンロードする

モバイル サービスを作成したので、個人用のモバイル サービス プロジェクトをダウンロードして、ローカル コンピューターまたは仮想マシンで動かすことができます。

1. 作成したモバイル サービスをクリックし、[クイック スタート] タブの **[プラットフォームの選択]** で **[iOS]** をクリックし、**[新しい iOS アプリを作成する]** を展開します。

	![][1]

2. Visual Studio Professional 2013 以降をダウンロードしてインストールします (まだインストールしていない場合)。

3. **[サービスのダウンロードとクラウドへの発行]** の下の **[ダウンロード]** をクリックします。

	これにより、モバイル サービスを実装する Visual Studio プロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

4. また、発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル サービスをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## モバイル サービスを発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 新しい iOS アプリを作成する

ここでは、モバイル サービスに接続される新しい iOS アプリを作成します。

1. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[iOS]** をクリックし、**[新しい iOS アプリを作成する]** を展開します。

3. [Xcode] v4.4 以降をダウンロードしてインストールします (まだインストールしていない場合)。	

4. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5. **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

  	これにより、モバイル サービスに接続されている _To do list_ サンプル アプリケーションのプロジェクトが、モバイル サービス iOS SDK と共にダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## 新しい iOS アプリを実行する

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

これで、Azure で実行されているモバイル サービスに対して新しいクライアント アプリケーションを実行する方法が示されます。iOS アプリをローカル コンピューターで実行しているモバイル サービスでテストする前に、iOS 開発コンピューターからのアクセスを許可するように Web サーバーおよびファイアウォールを構成する必要があります。詳細については、[ローカル モバイル サービスへの接続を可能にするローカル Web サーバーの構成](/ja-jp/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express) を参照してください。

<!--

## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックを参照してください。

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-->

<!-- Anchors. -->
[モバイル サービスの使用]:#getting-started
[新しいモバイル サービスを作成する]:#create-new-service
[モバイル サービス インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理ポータル]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[JavaScript バックエンド バージョン]: /ja-jp/documentation/articles/mobile-services-ios-get-started 

