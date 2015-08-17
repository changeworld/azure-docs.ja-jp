<properties 
	pageTitle="Azure Storage の使用" 
	description="Visual Studio の Azure Web ジョブ プロジェクトで Azure ストレージを作成するときに起きることについて説明します。" 
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
	ms.date="07/13/2015" 
	ms.author="patshea123"/>

# プロジェクトの変更点

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-blobs.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)

###<span id="whathappened">プロジェクトの変更点</span>

##### リファレンスの追加

Visual Studio プロジェクトで Azure Storage の NuGet パッケージが追加または更新されました。このパッケージは、次の .NET 参照を追加します。

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.ConfigurationManager`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####Azure Storage の接続文字列の追加 
選択したストレージ アカウントの接続文字列とキーを使用して、プロジェクトの App.config ファイル内の `AzureWebJobsStorage`と `AzureWebJobsDashboard`が更新されました。

詳細については、[Azure WebJobs の推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)に関するページをご覧ください。

<!---HONumber=August15_HO6-->