<properties 
	pageTitle="Azure Storage の使用" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Getting Started (概要)](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [変更内容](/documentation/articles/vs-storage-aspnet-what-happened/)

### <span id="whathappened">プロジェクトの変更点</span>

##### リファレンスの追加

Visual Studio プロジェクトに Azure Storage の NuGet パッケージが追加されました。  
このパッケージは、次の .NET 参照を追加します。

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

##### Azure Storage の接続文字列の追加 
選択されたストレージ アカウントの接続文字列とキーを使用して、プロジェクトの web.config ファイル内に要素が作成されました。

詳細については、「[ASP.NET](http://www.asp.net)」を参照してください。
<!--HONumber=42-->
