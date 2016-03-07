<properties
    pageTitle="クラウド サービス プロジェクトの変更点 | Microsoft Azure | Visual Studio 接続済みサービス"
	description="Visual Studio 接続済みサービスを使用して Azure ストレージ アカウントに接続した後の、クラウド サービス プロジェクトの変更点について説明します。"
    services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="tarcher"/>

# クラウド サービス プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス)

## リファレンスの追加

Visual Studio プロジェクトに Azure Storage の NuGet パッケージが追加されました。このパッケージは、次の .NET 参照を追加します。

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Azure Storage の接続文字列の追加
選択されたストレージ アカウントの接続文字列とキーを使用して要素が作成されました。次のファイルが修正されました。

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**

<!---HONumber=AcomDC_0224_2016-->