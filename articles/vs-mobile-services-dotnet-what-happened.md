<properties 
	pageTitle="" 
	description="Visual Studio の Azure Mobile Services .NET プロジェクトの変更点について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# プロジェクトの変更点

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

### <span id="whathappened">プロジェクトの変更点</span>

##### リファレンスの追加

プロジェクトに Azure Mobile Services の Nuget パッケージが追加されました。その結果、次の .NET 参照がプロジェクトに追加されました。

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

##### Mobile Services 用の接続文字列の値

選択したモバイル サービスのアプリケーション URL とアプリケーション キーを使用して、App.xaml.cs ファイル内に **MobileServiceClient** オブジェクトが作成されました。

[モバイル サービスの詳細を確認する](http://azure.microsoft.com/documentation/services/mobile-services/)
<!--HONumber=54-->