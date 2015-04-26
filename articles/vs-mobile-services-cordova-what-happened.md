<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [作業の開始](vs-mobile-services-cordova-getting-started.md)
> - [変更内容](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">プロジェクトの変更点</span>

#####リファレンスの追加

すべてのマルチデバイス ハイブリッド アプリケーションに含まれる Azure Mobile Service のクライアント プラグインが有効になりました。
  
#####Mobile Services 用の接続文字列の値

 `services\mobileServices\settings` に、**MobileServiceClient** を利用する新しい JavaScript (.js) ファイルが生成されました。選択したモバイル サービスのアプリケーション URL とアプリケーション キーが含まれています。このファイルには、次のようなモバイル サービス クライアント オブジェクトの初期化コードが含まれています。

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[モバイル サービスの詳細を確認する](http://azure.microsoft.com/documentation/services/mobile-services/)


<!--HONumber=42-->
