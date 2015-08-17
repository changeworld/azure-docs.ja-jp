<properties 
	pageTitle="Azure Storage の使用" 
	description="Visual Studio の ASP.NET 5 プロジェクトで Azure ストレージを作成するときに起きることについて説明します。" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="patshea"/>

# プロジェクトの変更点

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

###プロジェクトの変更点

##### リファレンスの追加

Visual Studio プロジェクトに Azure Storage の NuGet パッケージが追加されました。このパッケージは、次の .NET 参照を追加します。

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

また、NuGet パッケージ **Microsoft.Framework.Configuration.Json** が追加されました。

#####Azure Storage の接続文字列の追加 
選択されたストレージ アカウントの接続文字列とキーを使用して、プロジェクトの config.json ファイル内に要素が作成されました。

詳細については、「[￼￼￼￼￼￼￼ASP.NET 5￼￼￼￼￼￼￼￼￼￼￼￼](http://www.asp.net/vnext)」を参照してください。
 

<!---HONumber=August15_HO6-->