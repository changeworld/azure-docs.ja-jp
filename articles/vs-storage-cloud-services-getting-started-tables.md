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
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Getting Started (概要)](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)
> - [変更内容](/documentation/articles/vs-storage-cloud-services-what-happened/)

##Azure Storage の使用 (クラウド サービス プロジェクト)

> [AZURE.SELECTOR]
> - [BLOB](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [キュー](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [テーブル](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

Azure テーブル ストレージ サービスを使用すると、大量の構造化データを格納できるようになります。このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。  詳細については、「[.NET からテーブル ストレージを使用する方法](http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET")」を参照してください。

プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

##### ストレージ接続文字列を取得する
テーブルを使用した操作を行うには、テーブルを使用するストレージ アカウントの接続文字列を取得する必要があります。ストレージ アカウント情報を表すには、**CloudStorageAccount** 型を使用します。クラウド サービス プロジェクトの場合、**CloudConfigurationManager** 型を使用すると、次のコード例に示すように、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]
<!--HONumber=42-->
