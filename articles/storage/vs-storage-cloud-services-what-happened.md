<properties 
	pageTitle="Azure Storage の使用" 
	description="Visual Studio のクラウド サービス プロジェクトで Azure ストレージを作成するときに起きることについて説明します。" 
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
	ms.author="patshea123"/>

# プロジェクトの変更点

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-cloud-services-getting-started-blobs.md)
> - [What Happened](vs-storage-cloud-services-what-happened.md)

###プロジェクトの変更点</span>

###### リファレンスの追加

Visual Studio プロジェクトに Azure Storage の NuGet パッケージが追加されました。このパッケージは、次の .NET 参照を追加します。

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

######Azure Storage の接続文字列の追加 
選択されたストレージ アカウントの接続文字列とキーを使用して要素が作成されました。次のファイルが修正されました。

- `ServiceDefinition.csdef`
- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

 

<!---HONumber=July15_HO5-->