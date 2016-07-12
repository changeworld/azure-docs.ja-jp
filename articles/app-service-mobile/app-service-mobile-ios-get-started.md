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
	ms.date="06/30/2016"
	ms.author="krisragh"/>

#iOS アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## 概要

このチュートリアルでは、[Azure Mobile Apps](app-service-mobile-value-prop.md) (クラウドのバックエンド サービス) を iOS アプリに追加する方法について説明します。まず、新しいモバイル バックエンドを作成します。その後 _Todo list_ という単純な iOS アプリを使用して Azure にデータを格納します。

## 前提条件

このチュートリアルを完了するには、以下が必要です。

* [アクティブな Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
* [Visual Studio Community 2013] 以降がインストールされている PC
* Xcode 7.3 以降がインストールされている Mac

## 手順 I: 新しい Azure Mobile App バックエンドの作成

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## 手順 II: バックエンド プロジェクトの構成

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## 手順 III: iOS アプリのダウンロードと実行

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0706_2016-->