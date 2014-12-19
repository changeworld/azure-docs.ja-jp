<properties title="Getting Started with Azure Storage" pageTitle="Azure Storage の使用" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Getting Started (概要)](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [変更内容](/documentation/articles/vs-storage-cloud-services-what-happened/)

## Azure Storage の使用 (クラウド サービス プロジェクト)

> [AZURE.SELECTOR]
> - [BLOB](/documentation/articles/vs-storage-cloud-services-getting-started-blobs)
> - [Queues](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [テーブル](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。キューの 1 つのメッセージの最大サイズは 64 KB で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。詳細については、「[NET からキュー ストレージを使用する方法](http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET")」を参照してください。

プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

#####ストレージ接続文字列を取得する
キューを使用した操作を行うには、キューを使用するストレージ アカウントの接続文字列を取得する必要があります。ストレージ アカウント情報を表すには、**CloudStorageAccount** 型を使用します。クラウド サービス プロジェクトの場合、**CloudConfigurationManager** 型を使用すると、次のコード例に示すように、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

