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
	ms.date="02/13/2015" 
	ms.author="ricksal;glenga"/>

# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Azure Mobile Services を使用して Android アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しい Mobile Services と、新しい Mobile Services にアプリケーション データを保存する簡単な <em>To do list</em> アプリケーションの両方を作成します。</p>
<p>完成したアプリケーションのスクリーンショットは次のようになります。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a><span class="time">7:26</span></div>
</div>

![][0]

このチュートリアルを完了するには、Eclipse 統合開発環境 (IDE)、Android Developer Tools (ADT) プラグイン、および最新の Android プラットフォームを含む [Android Developer Tools][Android SDK] が必要です。このほか、Android のバージョン 4.2 以降が必要になります。 

ダウンロードしたクイック スタート プロジェクトには、Android 向けの Mobile Services SDK が含まれています。このプロジェクトではバージョン 4.2 以降の Android が必要であるものの、Mobile Services  SDK では Android バージョン 2.2 以降があれば問題ありません。

> [AZURE.IMPORTANT] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank)をご覧ください。

<!-- -->

>[AZURE.NOTE] 完成したアプリケーションのソース コードは、<a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">ここ</a>で確認できます。

## <a name="create-new-service"> </a>新しい Mobile Services を作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## <h2>新しい Android アプリを作成する</h2>

Mobile Services を作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更して Mobile Services に接続できます。 

ここでは、Mobile Services に接続された新しい Android アプリを作成します。

1.  管理ポータルで、**[Mobile Services]** をクリックし、先ほど作成した Mobile Services をクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Android]** を選択し、**[新しい Android アプリを作成する]** を展開します。

   	![][6]

   	これにより、Mobile Services に接続された Android アプリを作成するための簡単な 3 つの手順が表示されます。

  	![][7]

3. まだインストールしていない場合は、[Android Developer Tools][Android SDK]をダウンロードしてローカル コンピューターまたは仮想マシンにインストールします。

4. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。


5. アプリケーションをダウンロードします。
	- アプリケーションの最新バージョンでは、Mobile Services Android SDK 2.0 を使用します。このバージョンは、<a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">ここ</a>からダウンロードできます。**[Download Zip]** をクリックしてファイルをダウンロードしたら解凍します。プロジェクトは、GettingStarted の Android フォルダーに含まれています。
	 
	- 以前のバージョンでは、以前のバージョンの SDK を使用しています。これを使用するには、**[アプリケーションをダウンロードして実行する]** の下の **ダウンロード]** をクリックします。これにより、Mobile Services に接続されている _To do list_ サンプル アプリケーションのプロジェクトがダウンロードされます。プロジェクト ファイルは圧縮されています。コンピューター上のファイルをダウンロードした場所を参照し、ファイルを展開してください。


## Android アプリを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

2. Eclipse で、**[File]**、**[Import]** の順にクリックし、**[Android]** を展開します。**[Existing Android Code into Workspace]** をクリックし、**[Next]** をクリックします。 

 	![][14]

3. **[Browse]** をクリックします。展開したプロジェクト ファイルの場所を参照し、**[OK]** をクリックします。TodoActivity プロジェクトのチェック ボックスがオンになっていることを確認し、**[Finish]** をクリックします。 

 	![][15]

	これにより、プロジェクト ファイルが現在のワークスペースにインポートされます。

   	![][8]

4. SDK のバージョン 2.0 をダウンロードした場合は、Mobile Services の URL とキーを含むようにコードを更新する必要があります。
	- 	**TodoActivity.java** の **OnCreate** メソッド内で、Mobile Services クライアントをインスタンス化するコードを見つけます。前の図にこのコードが示されています。
	- 	"MobileServiceUrl" を、Mobile Services の実際の URL に置き換えます。
	- 	"AppKey" を、Mobile Services のキーに置き換えます。
	- 	詳細については、チュートリアル「<a href="http://azure.microsoft.com/documentation/articles/mobile-services-android-get-started-data/">既存のアプリケーションへの  Mobile Services の追加</a>」をご覧ください。 



4. **[Run]** メニューの **[Run]** をクリックして、Android エミュレーター内でプロジェクトを開始します。

	> [AZURE.IMPORTANT]プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。

5. アプリケーションで、意味のあるテキスト (たとえば、チュートリアルの完了) を入力し、**[Add]** をクリックします。

   	![][10]

   	これで、Azure でホストされている新しい Mobile Services に POST 要求が送信されます。要求のデータは TodoItems テーブルに挿入されます。テーブルに格納された項目が Mobile Services によって返され、データが一覧に表示されます。

	> [AZURE.NOTE] Mobile Services にアクセスして ToDoActivity.java ファイルにあるデータを照会および挿入するコードを確認できます。

6. 管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

   	![][11]

   	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

   	![][12]

## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックをご覧ください。 

* [データの使用]
  <br/>Mobile Services を使用してデータの格納とクエリを実行する方法について説明します。

* [認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[Mobile Services の使用]:#getting-started
[新しい Mobile Services を作成する]:#create-new-service
[Mobile Services インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started-EC/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started-EC/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started-EC/mobile-quickstart-steps-android-EC.png
[8]: ./media/mobile-services-android-get-started-EC/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started-EC/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started-EC/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started-EC/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started-EC/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-EC/mobile-services-import-android-project.png

<!-- URLs. -->
[データの使用]: mobile-services-android-get-started-data.md
[認証の使用]: mobile-services-android-get-started-users.md
[プッシュ通知の使用]: mobile-services-javascript-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理ポータル]: https://manage.windowsazure.com/


<!--HONumber=52--> 