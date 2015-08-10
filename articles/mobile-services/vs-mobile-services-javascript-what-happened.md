<properties 
	pageTitle="" 
	description="Visual Studio の Azure Mobile Services プロジェクトの変更点について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="patshea123"/>

# プロジェクトの変更点

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

###プロジェクトの変更点</id>

#####追加された NuGet パッケージ

**WindowsAzure.MobileServices.WinJS** `js\MobileServices.js` ファイル内の Azure Mobile Service ライブラリを含む NuGet パッケージがインストールされました。
  
#####Mobile Services 用の接続文字列の値 

`services\mobileServices\settings` フォルダー内に新しい JavaScript (.js) ファイルが生成されました。このファイルには、選択したモバイル サービスのアプリケーション URL とアプリケーション キーを格納した **MobileServiceClient** が含まれています。


#####Default.html に追加された参照

`MobileServices.js` への参照と設定ファイルが `default.html` に追加されました。


#####追加された接続されたサービス ファイル

サービス フォルダー内に、接続されたサービスの構成ファイルが追加されました。



 

<!---HONumber=July15_HO5-->