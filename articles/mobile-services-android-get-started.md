<properties 
	pageTitle="Android アプリ用 Azure Mobile Services の使用" 
	description="次のチュートリアルに従って、Android 開発用の Azure Mobile Services を使用します。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="hero-article" 
	ms.date="02/03/2015" 
	ms.author="ricksal,/glenga"/>

# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Azure モバイル サービスを使用して Android アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な <em>To do list</em> アプリケーションの両方を作成します。</p>
<p>完成したアプリケーションのスクリーンショットは次のようになります。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a><span class="time">7:26</span></div>
</div>

![][0]

このチュートリアルを完了するには、Android Studio 統合開発環境と最新の Android プラットフォームを含む [Android Developer Tools][Android Studio] が必要です。このほか、Android のバージョン 4.2 以降が必要になります。 

ダウンロードしたクイック スタート プロジェクトには、Android 向けの Azure のMobile Services SDK が含まれています。

> [AZURE.IMPORTANT] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、[Azure の無料評価版サイト](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank)をご覧ください。

<!-- -->

> [AZURE.NOTE] アプリケーション全体のソース コードを参照するには、 <a href="https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStarted/AndroidStudio" target="_blank">ここ</a>をクリックしてください。

<!-- -->

> [AZURE.NOTE] このチュートリアルの Eclipse バージョンを確認する場合は、「[Eclipse の使用](Eclipse の使用)」をご覧ください。


## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>新しい Android アプリを作成する</h2>

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続できます。 

ここでは、モバイル サービスに接続された新しい Android アプリを作成します。

1.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Android]** を選択し、**[新しい Android アプリを作成する]** を展開します。

   	![][6]

   	これにより、モバイル サービスに接続された Android アプリを作成するための簡単な 3 つの手順が表示されます。

  	![][7]

3. まだインストールしていない場合は、[Android Developer Tools][Android SDK]をダウンロードしてローカル コンピューターまたは仮想マシンにインストールします。

4. **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。


5. アプリケーションをダウンロードします。
	- アプリケーションの最新バージョンでは、Mobile Services Android SDK 2.0 を使用します。このバージョンは、 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">ここ</a>をクリックしてください。**[Download Zip]** をクリックしてファイルをダウンロードしたら解凍します。プロジェクトは、GettingStarted の Android フォルダーに含まれています。
	 
	- 以前のバージョンでは、以前のバージョンの SDK を使用しています。これを使用するには、**[アプリケーションをダウンロードして実行する]** の下の **ダウンロード]** をクリックします。これにより、モバイル サービスに接続されている _To do list_ サンプル アプリケーションのプロジェクトがダウンロードされます。プロジェクト ファイルは圧縮されています。コンピューター上のファイルをダウンロードした場所を参照し、ファイルを展開してください。


## Android アプリを実行する

[WACOM.INCLUDE [mobile-services-run-your-app](../includes/mobile-services-android-get-started.md)]


## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。モバイル サービスで重要になるこれ以外の作業については、以下のトピックをご覧ください。 

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納とクエリを実行する方法について説明します。

* [認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] 
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。



<!-- Anchors. -->
[Mobile Services の使用]:#getting-started
[新しいモバイル サービスを作成する]:#create-new-service
[モバイル サービス インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Eclipse の使用]: /documentation/articles/mobile-services-android-get-started-EC/
[データの使用]: /documentation/articles/mobile-services-android-get-started-data/
[認証の使用]: /documentation/articles/mobile-services-android-get-started-users/
[プッシュ通知の使用]: /documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理ポータル]: https://manage.windowsazure.com/

<!--HONumber=47-->
