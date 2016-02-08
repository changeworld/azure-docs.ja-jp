<properties
    pageTitle="Azure App Service Mobile Apps で Android アプリを作成する | Microsoft Azure"
    description="Android 開発向けの Azure モバイル アプリ バックエンドの使用を開始するには、このチュートリアルに従ってください。"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="yuaxu"/>

#Android アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概要

このチュートリアルでは、Azure モバイル アプリ バックエンドを使用してクラウド ベースのバックエンド サービスを Android モバイル アプリに追加する方法を説明します。新しいモバイル アプリ バックエンドと、アプリのデータを Azure に格納する簡単な _Todo list_ Android アプリの両方を作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Android チュートリアルを実行する前に完了しておく必要があります。

## 前提条件

このチュートリアルを完了するには、以下が必要です。

* Android Studio 統合開発環境と最新の Android プラットフォームを含む [Android Developer Tools](https://developer.android.com/sdk/index.html)
* Azure Mobile Android SDK。ダウンロードしたクイックスタート プロジェクトの一部として自動的に参照されます。
* [Visual Studio Community 2013] 以降のバージョンがインストールされた PC (Node.js バックエンドでは不要)
* [アクティブな Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)

## 新しい Azure モバイル アプリ バックエンドを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## サーバー プロジェクトの構成

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Android アプリのダウンロードと実行

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0128_2016-->