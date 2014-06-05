<properties pageTitle="Android アプリ向け Azure モバイル サービスの使用" metaKeywords="Azure Android アプリケーション, モバイル サービス Android, Azure Android の使用, Azure Droid, Droid Windows の使用" description="Android 向けの開発を行うために Azure モバイル サービスを使用するには、このチュートリアルに従います。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスの使用" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>モバイル サービスの使用

<div class="dev-center-tutorial-selector sublanding">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started" title="Windows ストア C#">Windows ストア C#</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started" title="iOS">iOS</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started/" title=".NET バックエンド" class="current">.NET バックエンド</a> |
	<a href="/ja-jp/documentation/articles/mobile-services-android-get-started/"  title="JavaScript バックエンド">JavaScript バックエンド</a>
</div>

このチュートリアルでは、Azure モバイル サービスを使用して Android アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な _To do list_ アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックを想定した Visual Studio でサポートされる .NET 言語を使用し、モバイル サービスを管理します。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成するには、このトピックの「[JavaScript バックエンド バージョン](/ja-jp/documentation/articles/mobile-services-android-get-started/)」 を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了するには、Eclipse 統合開発環境 (IDE)、Android Developer Tools (ADT) プラグイン、および最新の Android プラットフォームを含む [Android Developer Tools][Android SDK] が必要です。このほか、Android のバージョン 4.2 以降が必要になります。

ダウンロードしたクイック スタート プロジェクトには、Android 向けのモバイル サービス SDK が含まれています。このプロジェクトではバージョン 4.2 以降の Android が必要であるものの、モバイル サービス SDK では Android バージョン 2.2 以降があれば問題ありません。

<div class="dev-callout"><strong>注</strong><p> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## ローカル コンピューターにモバイル サービスをダウンロードする

モバイル サービスを作成したので、個人用のモバイル サービス プロジェクトをダウンロードして、ローカル コンピューターまたは仮想マシンで動かすことができます。

1. 作成したモバイル サービスをクリックし、[クイック スタート] タブの **[プラットフォームの選択]** で **[Android]** をクリックして、**[新しい Android アプリを作成する]** を展開します。

2. [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) 以降をダウンロードしてインストールします (まだインストールしていない場合)。

3. **[サービスのダウンロードとクラウドへの発行]** の下の **[ダウンロード]** をクリックします。

	これにより、モバイル サービスを実装する Visual Studio プロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

4. また、発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル サービスをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## モバイル サービスを発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 新しい Android アプリを作成する

ここでは、モバイル サービスに接続された新しい Android アプリを作成します。

1. [管理ポータル]で、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Android]** を選択し、**[新しい Android アプリを作成する]** を展開します。

3. まだインストールしていない場合は、[Android Developer Tools][Android SDK] をダウンロードしてローカル コンピューターまたは仮想マシンにインストールします。

4. **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5. **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

  	これにより、モバイル サービスに接続する、_To do list_ サンプル アプリケーションのプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Android アプリを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1.  圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開します。

2. Eclipse で、**[File]**、**[Import]** の順にクリックし、**[Android]** を展開します。**[Existing Android Code into Workspace]** をクリックし、**[Next]** をクリックします。

 	![][14]

3. **[Browse]** をクリックします。展開したプロジェクト ファイルの場所を参照し、**[OK]** をクリックします。TodoActivity プロジェクトのチェック ボックスがオンになっていることを確認し、**[Finish]** をクリックします。

 	![][15]

	これにより、プロジェクト ファイルが現在のワークスペースにインポートされます。

   	![][8]

4. **[Run]** メニューの **[Run]** をクリックして、Android エミュレーター内でプロジェクトを開始します。

	<div class="dev-callout"><strong></strong>注<p>プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。</p></div>

5. アプリケーションで、わかりやすいテキスト (たとえば、「_チュートリアルの完了_」) を入力し、**[Add]** をクリックします。

   	![][10]

   	これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

	<div class="dev-callout"><strong>注</strong>
   	<p>モバイル サービスにアクセスして ToDoActivity.java ファイルにあるデータを照会および挿入するコードを確認できます。</p>
 	</div>

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/ja-jp/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

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
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png




[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[モバイル サービス Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理ポータル]: https://manage.windowsazure.com/

