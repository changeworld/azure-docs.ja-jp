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
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Getting Started (概要)](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [変更内容](/documentation/articles/vs-storage-aspnet-what-happened/)

## Azure Storage の使用 (ASP.NET プロジェクト)

> [AZURE.SELECTOR]
> - [BLOB](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [キュー](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [テーブル](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。キューの 1 つのメッセージの最大サイズは 64 KB で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。詳細については、「[.NET からキュー ストレージを使用する方法](http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-queues/)」を参照してください。

プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

##### ストレージ接続文字列を取得する
キューを使用した操作を行うには、キューを使用するストレージ アカウントの接続文字列を取得する必要があります。ストレージ アカウント情報を表すには、**CloudStorageAccount** 型を使用します。ASP.NET プロジェクトの場合は、次のコードで示すように、**ConfigurationManager** 型を使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

詳細については、「[ASP.NET](http://www.asp.net)」を参照してください。
<!--HONumber=42-->
