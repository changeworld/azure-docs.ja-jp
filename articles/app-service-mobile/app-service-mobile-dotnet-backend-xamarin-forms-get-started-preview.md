<properties
	pageTitle="Xamarin.Forms を使用して Mobile Apps を使用する"
	description="次のチュートリアルに従って、Xamarin.Forms 開発用の Azure Mobile Apps を使用します"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="normesta"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>

#Xamarin.Forms アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##概要

このチュートリアルでは、Azure Mobile App バックエンドを使用して Xamarin.Forms モバイル アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。新しい Mobile App バックエンドと、アプリのデータを Azure に格納する簡単な _Todo list_ Xamarin.Forms アプリの両方を作成します。

Xamarin.Forms の他のすべての Azure Mobile Apps のチュートリアルを行う前に、このチュートリアルを完了してください。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。
 
* [Visual Studio Community 2013] 以降。Visual Studio Community 2013 をインストールする場合は、[Xamarin] を別個にインストールしてください。Visual Studio 2015 をインストールするときに、Xamarin ツールをインストールできます。

* [Xcode] v7.0 以降と [Xamarin Studio] がインストールされた Mac。
 
     >[AZURE.NOTE]Visual Studio を使用して Windows コンピューターにアプリをビルドする予定の場合でも、ネットワーク接続された Mac にアクセスしてそれを行う必要があります。
 
>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)に関するページにアクセスしてください。App Service で、有効期限付きのスターター Mobile App をすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 新しい Azure Mobile App バックエンドの作成

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## サーバー プロジェクトのダウンロード

1. お使いの PC で [Azure ポータル]にアクセスします。**[すべて参照]**、**[Mobile Apps]** の順にクリックし、作成した Mobile App バックエンドをクリックします。

2. Mobile App ブレードで **[設定]** をクリックし、**[Mobile App]** の下で **[クイック スタート]**、**[Xamarin.Forms]** の順にクリックします。
 
3. **[サーバー プロジェクトをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。圧縮されたプロジェクト ファイルを PC に抽出し、Visual Studio でソリューションを開きます。
 
## バックエンドのプロジェクトをローカルでテストします。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Azure へのサーバー プロジェクトの発行

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##Xamarin.Forms ソリューションのダウンロードと実行

いくつかのオプションがあります。Mac にソリューションをダウンロードして Xamarin Studio で開くことも、ソリューションを Windows コンピューターにダウンロードして Visual Studio で開くこともできます。両方の環境を使用して、それらを切り替えることもできます。次の点を考慮してください。

* Mac でソリューションの iOS プロジェクトを実行する方が簡単です。必要であれば Visual Studio を Windows コンピューター上で使用できますが、ネットワーク化された Mac に接続する必要があるため、少し複雑になります。この設定に関心がある場合は、「[Installing Xamarin.iOS on Windows (Windows への Xamarin.iOS のインストール)]」をご覧ください。
* Android プロジェクトは、Mac または Windows コンピューターのいずれかで実行できます。
* Windows プロジェクトは、Windows コンピューターで Visual Studio を使用することによってのみ実行できます。

これをすべて考慮して、先に進みましょう。

 1. Mac または Windows コンピューターで、[Azure ポータル]をブラウザー ウィンドウで開きます。
 2. **[Xamarin.Forms プロジェクトをダウンロードして実行する]** の下の **[ダウンロード]** ボタンをクリックします。

    これにより、モバイル アプリに接続されているクライアント アプリケーションが含まれているプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

 3. ダウンロードしたプロジェクトを抽出し、Xamarin Studio または Visual Studio でそれを開きます。

	![][9]

	![][8]

###iOS プロジェクトの実行

####Xamarin Studio で使用する

1. iOS プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
2. **[実行]** メニューで **[デバッグ開始]** をクリックし、プロジェクトをビルドして、アプリを iPhone エミュレーターで起動します。

####Visual Studio で使用する
1. iOS プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
2. **[ビルド]** メニューの **[構成マネージャー]** をクリックします。 
3. **[構成マネージャー]** ダイアログ ボックスで、iOS プロジェクトの **[ビルド]** と **[デプロイ]** チェックボックスをオンにします。
4. **F5** キーを押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを起動します。

アプリケーションで、意味のあるテキスト (_Xamarin の学習_など) を入力し、**+** ボタンをクリックします。

![][10]

これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

> [AZURE.NOTE]ソリューションのポータブル クラス ライブラリー プロジェクトの ToDoActivity.cs C# ファイルに、モバイル アプリ バックエンドにアクセスするコードが表示されます。

###Android プロジェクトの実行

####Xamarin Studio で使用する

1. Android プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
2. **[実行]** メニューで **[デバッグ開始]** をクリックし、プロジェクトをビルドして、アプリを Android エミュレーターで起動します。

####Visual Studio で使用する
1. Android プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
4. **[ビルド]** メニューの **[構成マネージャー]** をクリックします。 
5. **[構成マネージャー]** ダイアログ ボックスで、Android プロジェクトの **[ビルド]** と **[デプロイ]** チェックボックスを選択します。
6. **F5** キーを押してプロジェクトをビルドし、Android エミュレーターでアプリケーションを起動します。

アプリケーションで、意味のあるテキスト (_Xamarin の学習_など) を入力し、**+** ボタンをクリックします。

![][11]

これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

> [AZURE.NOTE]ソリューションのポータブル クラス ライブラリー プロジェクトの ToDoActivity.cs C# ファイルに、モバイル アプリ バックエンドにアクセスするコードが表示されます。


###Windows プロジェクトの実行

####Visual Studio で使用する
1. いずれかの Windows プロジェクトを右クリックし、**[スタート アップ プロジェクトに設定]** をクリックします。
4. **[ビルド]** メニューの **[構成マネージャー]** をクリックします。 
5. **[構成マネージャー]** ダイアログ ボックスで、選択した Windows プロジェクトの **[ビルド]** と **[デプロイ]** チェックボックスを選択します。
6. **F5** キーを押してプロジェクトをビルドし、 Windows エミュレーターでアプリケーションを起動します。

アプリケーションで、意味のあるテキスト (_Xamarin の学習_など) を入力し、**+** ボタンをクリックします。
	
これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

![][12]
	
> [AZURE.NOTE]ソリューションのポータブル クラス ライブラリー プロジェクトの ToDoActivity.cs C# ファイルに、モバイル アプリ バックエンドにアクセスするコードが表示されます。

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure ポータル]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installing Xamarin.iOS on Windows (Windows への Xamarin.iOS のインストール)]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
 

<!---HONumber=August15_HO8-->