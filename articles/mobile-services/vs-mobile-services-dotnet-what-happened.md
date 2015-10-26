<properties 
	pageTitle="Visual Studio 接続済みサービスを使用して Mobile Services を追加したことによる .NET プロジェクトへの影響 | Microsoft Azure" 
	description="接続済みサービスを使用して Azure Mobile Services を追加したことによる Visual Studio .NET プロジェクトへの影響について説明します" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="patshea"/>

# 接続済みサービスを使用して Azure Mobile Services を追加したことによる Visual Studio .NET プロジェクトへの影響

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

## リファレンスの追加

プロジェクトに Azure Mobile Services の Nuget パッケージが追加されました。その結果、次の .NET 参照がプロジェクトに追加されました。

- **Microsoft.WindowsAzure.Mobile**
- **Microsoft.WindowsAzure.Mobile.Ext**
- **Newtonsoft.Json**
- **System.Net.Http.Extensions**
- **System.Net.Http.Primitives** 

## Mobile Services 用の接続文字列の値

選択したモバイル サービスのアプリケーション URL とアプリケーション キーを使用して、App.xaml.cs ファイル内に **MobileServiceClient** オブジェクトが作成されました。

## Mobile Services プロジェクトの追加

Connected Service Provider に .NET モバイル サービスが作成されている場合、モバイル サービス プロジェクトはソリューションに追加されています。


[モバイル サービスの詳細を確認する](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=Oct15_HO3-->