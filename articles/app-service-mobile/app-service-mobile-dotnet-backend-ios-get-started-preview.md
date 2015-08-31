<properties
	pageTitle="Azure Mobile Apps での iOS アプリの作成"
	description="このチュートリアルでは、Objective-C または Swift で iOS 開発用の Azure Mobile App バックエンドを使用する方法を説明します。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/11/2015"
	ms.author="krisragh"/>

#iOS アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##概要

このチュートリアルでは、Azure Mobile App バックエンドを使用してクラウド ベースのバックエンド サービスを iOS モバイル アプリに追加する方法を説明します。新しい Mobile App バックエンドと、アプリのデータを Azure に格納する簡単な _Todo list_ iOS アプリの両方を作成します。

iOS アプリの他のすべての Mobile Apps チュートリアルを行う前に、このチュートリアルを完了する必要があります。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。
 
* [Visual Studio Community 2013] 以降のバージョン。

* Mac with Xcode v7.0 以降のバージョン。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)に関するページにアクセスしてください。App Service で、有効期限付きのスターター Mobile App をすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 新しい Azure Mobile App バックエンドの作成

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## サーバー プロジェクトのダウンロード

1. お使いの PC で [Azure ポータル]にアクセスします。**[すべて参照]**、**[Mobile Apps]** の順にクリックし、作成した Mobile App バックエンドをクリックします。
 
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
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
 
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=August15_HO8-->