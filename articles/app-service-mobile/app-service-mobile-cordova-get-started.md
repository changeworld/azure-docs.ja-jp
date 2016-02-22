<properties
    pageTitle="Azure App Service Mobile Apps で Cordova アプリを作成する | Microsoft Azure"
    description="Apache Cordova 開発向けの Azure モバイル アプリ バックエンドの使用を開始するには、このチュートリアルに従ってください。"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="glenga"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-javascript"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="adrianha"/>

#Apache Cordova アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## 概要

このチュートリアルでは、Azure モバイル アプリ バックエンドを使用してクラウドベースのバックエンド サービスを Apache Cordova モバイル アプリに追加する方法を説明します。新しいモバイル アプリ バックエンドと、アプリのデータを Azure に格納する簡単な _Todo list_ Apache Cordova アプリの両方を作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Apache Cordova チュートリアルを実行する前に完了しておく必要があります。

## 前提条件

このチュートリアルを完了するには、以下が必要です。

* [Visual Studio Community 2015] 以降のバージョンがインストールされた PC
* [Apache Cordova の Visual Studio ツール]
* [アクティブな Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)

Visual Studio をバイパスし、Apache Cordova のコマンド ラインを直接使用することもできます。これは、Mac コンピューターでチュートリアルを完成させるときに便利です。コマンドラインを使用する Apache Cordova クライアント アプリケーションのコンパイルは、このチュートリアルでは説明しません。

## 新しい Azure モバイル アプリ バックエンドを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## サーバー プロジェクトの構成

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Apache Cordova アプリのダウンロードと実行

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Apache Cordova の Visual Studio ツール]: https://www.visualstudio.com/ja-JP/features/cordova-vs.aspx

<!---HONumber=AcomDC_0211_2016-->