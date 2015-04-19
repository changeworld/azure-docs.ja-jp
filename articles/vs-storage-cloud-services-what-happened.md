<properties 
	pageTitle="Azure Storage の使用" 
	description="Visual Studio のクラウド サービス プロジェクトで Azure ストレージを作成するときに起きることについて説明します。" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Getting Started (概要)](vs-storage-cloud-services-getting-started-blobs.md)
> - [変更内容](vs-storage-cloud-services-what-happened.md)

###<span id="whathappened">プロジェクトの変更点</span>

###### リファレンスの追加

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

######Azure Storage の接続文字列の追加 
選択されたストレージ アカウントの接続文字列とキーを使用して要素が作成されました。次のファイルが修正されました。

- `ServiceDefinition.csdef`
- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`


<!--HONumber=46--> 
