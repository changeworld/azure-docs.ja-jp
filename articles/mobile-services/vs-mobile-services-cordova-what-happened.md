<properties 
	pageTitle="Cordova プロジェクトへの影響 (Visual Studio 接続済みサービス) | Microsoft Azure" 
	description="Visual Studio 接続済みサービスを使用して Azure Mobile Services を追加したことによる Azure Cordova プロジェクトへの影響について説明します" 
	services="mobile-services" 
	documentationCenter="na" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="patshea"/>

# Visual Studio 接続済みサービスを使用して Azure Mobile Services を追加したことによる Azure Cordova プロジェクトへの影響

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

##リファレンスの追加

すべてのマルチデバイス ハイブリッド アプリケーションに含まれる Azure Mobile Service のクライアント プラグインが有効になりました。
  
##Mobile Services 用の接続文字列の値

`services\mobileServices\settings` に新しい JavaScript (.js) ファイルが生成されました。このファイルには、選択したモバイル サービスのアプリケーション URL とアプリケーション キーを格納した **MobileServiceClient** が含まれています。このファイルには、次のようなモバイル サービス クライアント オブジェクトの初期化コードが含まれています。

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[モバイル サービスの詳細を確認する](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=Sept15_HO4-->