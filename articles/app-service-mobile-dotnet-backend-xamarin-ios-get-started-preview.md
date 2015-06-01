<properties
	pageTitle="Xamarin iOS でモバイル アプリを使用する"
	description="Xamarin iOS を使用して、Azure App Service で Azure モバイル アプリを構築します。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Xamarin iOS アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

このチュートリアルでは、Azure モバイル アプリを使用して Xamarin iOS アプリケーションにクラウドベースのバックエンド サービスを追加する方法について説明します。このチュートリアルでは、新しい .NET サービスと、.NET バックエンドにアプリケーション データを格納する簡単な _To do list_ アプリケーションの両方を作成します。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)に関するページにアクセスしてください。App Service で、有効期限付きのスターター モバイル アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 新しいモバイル アプリ バックエンドを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 新しい Xamarin iOS アプリケーションを作成する

モバイル アプリ バックエンドを作成すると、Azure ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル アプリ バックエンドに接続することができます。

このセクションでは、モバイル アプリ バックエンド用の新しい Xamarin iOS アプリケーションとサービス プロジェクトをダウンロードします。

1. Azure ポータルで、**[モバイル アプリ]**、作成したモバイル アプリ バックエンドの順にクリックします。

2. ブレードの上部で、**[クライアントの追加]** をクリックし、**[Xamarin iOS]** を展開します。

	![][6]

	これにより、モバイル アプリ バックエンドに接続された Xamarin iOS アプリケーションを作成するための簡単な 3 つの手順が表示されます。

3. まだインストールしていない場合は、<a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> をダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。

4. [Xcode] v4.4 以降および [Xamarin Studio] をダウンロードしてインストールします。また、Xamarin for Visual Studio を使用することもできます。

5. **[サービスのダウンロードとクラウドへの発行]** で **[ダウンロード]** をクリックします。

 これにより、モバイル アプリ バックエンドと、モバイル アプリ バックエンドに接続されている _To do list_ サンプル アプリケーションの両方のプロジェクトを含むソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

6. 発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル アプリ バックエンドをテストする

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## モバイル アプリ バックエンドを発行する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Xamarin iOS アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. Visual Studio または Xamarin Studio で、モバイル アプリ バックエンド ソリューション内のクライアント プロジェクトに移動します。

	![][8]

	![][9]

2. **[実行]** ボタンを押してクライアント プロジェクトをビルドし、iPhone エミュレーターでアプリケーションを開始します。

3. アプリケーションで、意味のあるテキスト (たとえば、「_チュートリアルの完了_」) を入力し、プラス (**+**) アイコンをクリックします。

	![][10]

	これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

>[AZURE.NOTE]モバイル アプリ バックエンドにアクセスして QSTodoService.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。


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
[Get started with authentication]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
[JavaScript backend version]: partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=52-->
