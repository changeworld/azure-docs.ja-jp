<properties title="Azure Storage の使用" pageTitle="Azure Storage の使用" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Getting Started (概要)][Getting Started (概要)]
> -   [変更内容][変更内容]

## Azure Storage の使用 (ASP.NET プロジェクト)

> [AZURE.SELECTOR]
>
> -   [BLOB][BLOB]
> -   [キュー][キュー]
> -   [テーブル][Getting Started (概要)]

Azure テーブル ストレージ サービスを使用すると、大量の構造化データを格納できるようになります。このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。詳細については、「[How to use Table Storage from .NET (.NET からテーブル ストレージを使用する方法)][How to use Table Storage from .NET (.NET からテーブル ストレージを使用する方法)]」を参照してください。

プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

##### ストレージ接続文字列を取得する

テーブルを使用した操作を行うには、テーブルを使用するストレージ アカウントの接続文字列を取得する必要があります。ストレージ アカウント情報を表すには、**CloudStorageAccount** 型を使用します。ASP.NET プロジェクトの場合は、次のコードで示すように、**ConfigurationManager** 型を使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.AppSettings["<storageAccountName>_AzureStorageConnectionString"]);

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

詳細については、「[ASP.NET][ASP.NET]」を参照してください。

  [Getting Started (概要)]: /documentation/articles/vs-storage-aspnet-getting-started-tables/
  [変更内容]: /documentation/articles/vs-storage-aspnet-what-happened/
  [BLOB]: /documentation/articles/vs-storage-aspnet-getting-started-blobs/
  [キュー]: /documentation/articles/vs-storage-aspnet-getting-started-queues/
  [How to use Table Storage from .NET (.NET からテーブル ストレージを使用する方法)]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET"
  [ASP.NET]: http://www.asp.net
