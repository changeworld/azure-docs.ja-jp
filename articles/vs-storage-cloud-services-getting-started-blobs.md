<properties title="Azure Storage の使用" pageTitle="Azure Storage の使用" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Getting Started (概要)][Getting Started (概要)]
> -   [変更内容][変更内容]

## Azure Storage の使用 (クラウド サービス プロジェクト)

> [AZURE.SELECTOR]
>
> -   [BLOB][Getting Started (概要)]
> -   [キュー][キュー]
> -   [テーブル][テーブル]

Azure BLOB ストレージは、大量の非構造化データを格納して HTTP または HTTPS を介して世界中のどこからでもアクセスできるようにするサービスです。1 つの BLOB は任意のサイズにできます。BLOB として扱えるのは、画像、オーディオ ファイル、ビデオ ファイル、生データ、およびドキュメント ファイルのようなデータです。

使用を開始するには、Azure Storage アカウントを作成し、このストレージ内に 1 つ以上のコンテナーを作成する必要があります。たとえば、"Scrapbook" という名前のストレージを作成し、このストレージに、写真を格納するための "images" という名前のコンテナーと、音声ファイルを格納するための "audio" という名前のコンテナーを作成します。コンテナーを作成すると、個々の BLOB ファイルをコンテナーにアップロードできるようになります。プログラムによる BLOB の操作の詳細については、「[How to use Blob Storage from .NET (.NET から BLOB ストレージを使用する方法)][How to use Blob Storage from .NET (.NET から BLOB ストレージを使用する方法)]」を参照してください。

プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

##### ストレージ接続文字列を取得する

BLOB を使用した操作を行うには、BLOB を使用するストレージ アカウントの接続文字列を取得する必要があります。ストレージ アカウント情報を表すには、**CloudStorageAccount** 型を使用します。クラウド サービス プロジェクトの場合、**CloudConfigurationManager** 型を使用すると、次のコード例に示すように、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

  [Getting Started (概要)]: /documentation/articles/vs-storage-cloud-services-getting-started-blobs/
  [変更内容]: /documentation/articles/vs-storage-cloud-services-what-happened/
  [キュー]: /documentation/articles/vs-storage-cloud-services-getting-started-queues/
  [テーブル]: /documentation/articles/vs-storage-cloud-services-getting-started-tables/
  [How to use Blob Storage from .NET (.NET から BLOB ストレージを使用する方法)]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
