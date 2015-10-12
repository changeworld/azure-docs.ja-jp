<properties
	pageTitle="Xamarin.iOS アプリに対して Azure App Service Mobile Apps を使用する | Microsoft Azure"
	description="このチュートリアルに従って、Xamarin.iOS 開発用の Azure Mobile Apps を使用します。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>


#Xamarin.iOS アプリを作成する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##概要

このチュートリアルでは、Azure Mobile Apps バックエンドを使用して Xamarin.iOS モバイル アプリにクラウドベースのバックエンド サービスを追加する方法を示します。新しいモバイル アプリ バックエンドと、アプリのデータを Azure に保存する簡単な _Todo list_ Xaamrin.iOS アプリの両方を作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Xamarin.iOS チュートリアルを実行する前に完了しておく必要があります。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

* [Visual Studio Community 2013] 以降。Visual Studio Community 2013 をインストールする場合は、[Xamarin] を別個にインストールしてください。Visual Studio 2015 をインストールするときに、Xamarin ツールをインストールできます。

* [Xcode] v7.0 以降と [Xamarin Studio] がインストールされた Mac。

     >[AZURE.NOTE]Visual Studio を使用して Windows コンピューターでアプリをビルドする場合でも、それを行うにはネットワーク接続された Mac にアクセスする必要があります。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Azure App Service アプリケーションの作成](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)」にアクセスしてください。有効期間が短いスターター モバイル アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 新しい Azure モバイル アプリ バックエンドを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## サーバー プロジェクトのダウンロード

1. お使いの PC で [Azure ポータル]にアクセスします。**[すべて参照]**、**[Mobile Apps]** の順にクリックし、作成したモバイル アプリ バックエンドをクリックします。

2. [Mobile Apps] ブレードで **[設定]** をクリックし、**[Mobile App]** の下で **[クイック スタート]**、**[Xamarin.iOS]** の順にクリックします。

3. **[サーバー プロジェクトをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。圧縮されたプロジェクト ファイルを PC に抽出し、Visual Studio でソリューションを開きます。

## バックエンドのプロジェクトをローカルでテストします。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Azure へのサーバー プロジェクトの発行

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Xamarin.iOS アプリのダウンロードと実行

1. Mac で、[Azure ポータル]をブラウザー ウィンドウで開きます。

>[AZURE.NOTE]Mac で Xamarin.iOS アプリを実行するほうが簡単です。必要であれば Visual Studio を使用して Windows コンピューター上で Xamarin.iOS を実行できますが、ネットワーク化された Mac に接続する必要があるため、少し複雑になります。この設定に関心がある場合は、「[Installing Xamarin.iOS on Windows (Windows への Xamarin.iOS のインストール)]」をご覧ください。

2. **[Xamarin.iOS プロジェクトをダウンロードして実行する]** の下の **[ダウンロード]** ボタンをクリックします。

  	これにより、モバイル アプリに接続されているクライアント アプリケーションが含まれているプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

3. ダウンロードしたプロジェクトを抽出し、Xamarin Studio または Visual Studio で開きます。

	![][9]

	![][8]

4. F5 キーを押してプロジェクトをビルドし、iPhone エミュレーターでアプリを起動します。

5. アプリで、意味のあるテキスト (「_Xamarin の学習_」など) を入力し、**+** ボタンをクリックします。

	![][10]

	これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

>[AZURE.NOTE]モバイル アプリ バックエンドにアクセスして QSTodoService.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。

##次のステップ

* [アプリへの認証の追加](app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md) <br/>ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。

* [アプリケーションにプッシュ通知を追加する](app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md) <br/>アプリにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-quickstart-startup-ios.png

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
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Installing Xamarin.iOS on Windows (Windows への Xamarin.iOS のインストール)]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/

<!---HONumber=Oct15_HO1-->