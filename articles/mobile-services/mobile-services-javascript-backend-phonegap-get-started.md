<properties
	pageTitle="PhoneGap/Cordova アプリ向け Azure Mobile Services の使用 | Microsoft Azure"
	description="このチュートリアルでは、iOS、Android、Windows Phone 用に PhoneGap の開発を行う場合に Azure Mobile Services を使用する方法について説明します。"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="multiple"
	ms.topic="get-started-article" 
	ms.date="07/21/2016"
	ms.author="ggailey777"/>

# Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)] &nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

このチュートリアルでは、Azure Mobile Services を使用してアプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な _To do list_ アプリケーションの両方を作成します。

完成したアプリケーションのスクリーンショットは次のようになります。

![][3]

### その他の要件

このチュートリアルを完了するには、次のものが必要です。

+ PhoneGap ツール (Windows Phone 8 プロジェクトの場合は v3.2 以降が必要です)

+ アクティブな Microsoft Azure アカウント。

+ PhoneGap は、複数のプラットフォーム向けの開発をサポートします。PhoneGap ツール自体に加えて、対象としている各プラットフォーム用のツールをインストールする必要があります。

	- Windows Phone: [Visual Studio 2012 Express for Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374) のインストール
	- iOS: [Xcode] \(v4.4 以上が必要) のインストール
	- Android: [Android Developer Tools][Android SDK] のインストール<br/> (Android 向けの Mobile Services SDK は Android 2.2 以降用のアプリケーションをサポートします。Android 4.2 以降がクイック スタート アプリケーションの実行に必要です。)

## 新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 新しい PhoneGap アプリを作成する

ここでは、モバイル サービスに接続された新しい PhoneGap アプリケーションを作成します。

1.  [Azure クラシック ポータル]で、**[Mobile Services]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[PhoneGap]** を選択し、**[新しい PhoneGap アプリを作成する]** を展開します。

   	![][0]

   	これにより、モバイル サービスに接続された PhoneGap アプリケーションを作成するための簡単な 3 つの手順が表示されます。

  	![][1]

3. まだインストールしていない場合は、PhoneGap および、プラットフォーム開発ツールの少なくとも 1 つ (Windows Phone、iOS、または Android) をダウンロードしてインストールします。

4. **[TodoItem テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5. **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

	これにより、モバイル サービスに接続されている _To do list_ サンプル アプリケーションのプロジェクトが、Mobile Services JavaScript SDK と共にダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## 新しい PhoneGap アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1.	圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開します。

2.	各プラットフォームについてここに示す手順に従って、プロジェクトを開いて実行します。

	+ **Windows Phone 8**

		1. Windows Phone 8: Visual Studio 2012 Express for Windows Phone の **platforms\\wp8** フォルダーにある .sln ファイルを開きます。

		2. **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

	  	![][2]

	+ **iOS**

		1. Xcode の **platforms/ios** フォルダーのプロジェクトを開きます。

		2. **[実行]** をクリックしてプロジェクトをビルドし、このプロジェクトの既定である iPhone エミュレーターでアプリケーションを開始します。

	  	![][3]

	+ **Android**

		1. Eclipse で、**[File]**、**[Import]** の順にクリックし、**[Android]** を展開します。**[Existing Android Code into Workspace]** をクリックし、**[Next]** をクリックします。

		2. **[Browse]** をクリックします。展開したプロジェクト ファイルの場所を参照し、**[OK]** をクリックします。TodoActivity プロジェクトのチェック ボックスがオンになっていることを確認し、**[Finish]** をクリックします。<p>これにより、プロジェクト ファイルが現在のワークスペースにインポートされます。</p>

		3. **[Run]** メニューの **[Run]** をクリックして、Android エミュレーター内でプロジェクトを開始します。

			![][4]

		>[AZURE.NOTE]プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。


3. 上で示したモバイル エミュレーターのいずれかでアプリケーションを起動した後、テキスト ボックスにテキストを入力し、**[追加]** をクリックします。

	これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは **TodoItem** テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

	> [AZURE.IMPORTANT] メイン プロジェクトが PhoneGap ツールで再ビルドされる場合、このプラットフォーム プロジェクトへの重要な変更が上書きされます。以下のセクションで説明されているように、代わりの方法として、変更はプロジェクトのルートの www ディレクトリで行います。

4. [Azure クラシック ポータル]に戻り、**[データ]** タブ、**TodoItem** テーブルの順にクリックします。

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)


## アプリケーションを更新して、各プラットフォーム用にプロジェクトを再ビルドする

1. ´www´ ディレクトリ (この場合は ´todolist/www´) のコード ファイルを変更します。

2. すべてのターゲット プラットフォームのツールがシステム パスでアクセスできることを確認します。

2. ルート プロジェクト ディレクトリでコマンド プロンプトを開き、次のプラットフォームに固有のコマンドのいずれかを実行します。

	+ **Windows Phone**

		Visual Studio 開発者用コマンド プロンプトで次のコマンドを実行します。

    		phonegap local build wp8

	+ **iOS**

		ターミナルを開き、次のコマンドを実行します。

    		phonegap local build ios

	+ **Android**

		コマンド プロンプトまたはターミナル ウィンドウを開き、次のコマンドを実行します。

		    phonegap local build android

4. 前のセクションで説明したように、適切な開発環境で各プロジェクトを開きます。

>[AZURE.NOTE]Mobile Services にアクセスして js/index.js ファイルにあるデータを照会および挿入するコードを確認できます。

## 次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックを参照してください。

* **[アプリへの認証の追加]** ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* **[アプリケーションにプッシュ通知を追加する](https://msdn.microsoft.com/magazine/dn879353.aspx)**プッシュ通知を登録し、アプリに送信する方法について説明します。

* **[Mobile Services HTML/JavaScript の使用方法の概念リファレンス](mobile-services-html-how-to-use-client-library.md)** JavaScript クライアント ライブラリを使用してデータにアクセスする方法、カスタム API を呼び出す方法、認証を実行する方法について説明します。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[アプリへの認証の追加]: mobile-services-html-get-started-users.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
 

<!---HONumber=AcomDC_0727_2016-->