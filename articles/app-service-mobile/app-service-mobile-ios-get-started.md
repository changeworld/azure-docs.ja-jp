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
	ms.date="10/14/2015"
	ms.author="krisragh"/>

#iOS アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概要

このチュートリアルでは、Azure モバイル アプリ バックエンドを使用してクラウド ベースのバックエンド サービスを iOS モバイル アプリに追加する方法を説明します。新しいモバイル アプリ バックエンドと、アプリのデータを Azure に格納する簡単な _Todo list_ iOS アプリの両方を作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての iOS チュートリアルを実行する前に完了しておく必要があります。

## 前提条件

このチュートリアルを完了するには、以下が必要です。

* [アクティブな Azure アカウント](http://azure.microsoft.com/pricing/free-trial/)

* [Visual Studio Community 2013] 以降のバージョンがインストールされた PC

* Xcode v7.0 以降がインストールされた Mac

* [Azure Mobile iOS フレームワーク](https://go.microsoft.com/fwLink/?LinkID=529823)。ダウンロードされるクイックスタート プロジェクトの一部として自動的に追加されます。

## 新しい Azure Mobile App バックエンドの作成

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## サーバー プロジェクトのダウンロード

1. お使いの PC で [Azure ポータル]にアクセスします。**[すべて参照]**、**[Mobile Apps]** の順にクリックし、作成したモバイル アプリ バックエンドをクリックします。

2. [モバイル アプリ] ブレードで **[設定]** をクリックし、**[モバイル アプリ]** の下で **[クイック スタート]**、**[iOS (Objective-C)]** の順にクリックします。Swift を使用する場合は、**[クイック スタート]**、**[iOS (Swift)]** の順にクリックします。

3. **[サーバー プロジェクトをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。圧縮されたプロジェクト ファイルを PC に抽出し、Visual Studio でソリューションを開きます。

## Azure へのサーバー プロジェクトの発行

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## iOS アプリのダウンロードと実行

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure ポータル]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Nov15_HO1-->