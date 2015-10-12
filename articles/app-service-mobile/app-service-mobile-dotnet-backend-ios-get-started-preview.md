<properties
	pageTitle="Azure App Service Mobile Apps で iOS アプリを作成する | Microsoft Azure"
	description="このチュートリアルに従って、Objective-C または Swift で iOS を開発用するための Azure モバイル アプリ バックエンドの使用を開始します。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/11/2015"
	ms.author="krisragh"/>

#iOS アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##概要

このチュートリアルでは、Azure モバイル アプリ バックエンドを使用してクラウド ベースのバックエンド サービスを iOS モバイル アプリに追加する方法を説明します。新しいモバイル アプリ バックエンドと、アプリのデータを Azure に格納する簡単な _Todo list_ iOS アプリの両方を作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての iOS チュートリアルを実行する前に完了しておく必要があります。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

* [Visual Studio Community 2013] 以降のバージョン。

* Mac with Xcode v7.0 以降のバージョン。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Azure App Service アプリケーションの作成](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)」にアクセスしてください。有効期間が短いスターター モバイル アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 新しい Azure モバイル アプリ バックエンドを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## サーバー プロジェクトのダウンロード

1. お使いの PC で [Azure ポータル]にアクセスします。**[すべて参照]**、**[Mobile Apps]** の順にクリックし、作成したモバイル アプリ バックエンドをクリックします。

2. Mobile App ブレードで **[設定]** をクリックし、**[Mobile App]** の下で **[クイック スタート]**、**[iOS (Objective-C)]** の順にクリックします。Swift を使用する場合は、**[クイック スタート]**、**[iOS (Swift)]** の順にクリックします。

3. **[サーバー プロジェクトをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。圧縮されたプロジェクト ファイルを PC に抽出し、Visual Studio でソリューションを開きます。

## Azure へのサーバー プロジェクトの発行

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## iOS アプリのダウンロードと実行

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Azure ポータル]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Oct15_HO1-->