<properties linkid="dev-net-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# How to use Blob Storage from .NET (.NET から BLOB ストレージを使用する方法)

このガイドでは、Azure BLOB ストレージ サービスを使用して
一般的なシナリオを実行する方法のデモンストレーションを行います。例は C# で記述され、Azure .NET 用
ストレージ クライアント ライブラリを利用しています。紹介するシナリオは、BLOB の
**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。BLOB の
詳細については、「[次のステップ][]」のセクションを参照してください。

> [WACOM.NOTE] このガイドは、Azure .NET 用ストレージ クライアント ライブラリ 2.x 以上を対象としています。推奨されるバージョンはストレージ クライアント ライブラリ 4.x です。これは、[NuGet][] から、または [Azure SDK for .NET][] の一部として提供されています。ストレージ クライアント ライブラリの入手方法の詳細については、「[方法: プログラムで BLOB ストレージにアクセスする][]」を参照してください。

## 目次

-   [BLOB ストレージとは][]
-   [概念][]
-   [Azure のストレージ アカウントの作成][]
-   [ストレージ接続文字列の設定][]
-   [方法: プログラムで BLOB ストレージにアクセスする][]
-   [方法: コンテナーを作成する][]
-   [方法: コンテナーに BLOB をアップロードする][]
-   [方法: コンテナー内の BLOB を一覧表示する][]
-   [方法: BLOB をダウンロードする][]
-   [方法: BLOB を削除する][]
-   [次のステップ][]

[WACOM.INCLUDE [howto-blob-storage][]]

## <a name="create-account"></a><span class="short-header">アカウントの作成</span>Azure の Storage アカウントの作成

[WACOM.INCLUDE [create-storage-account][]]

## <a name="setup-connection-string"></a><span class="short-header">接続文字列の設定</span>ストレージ接続文字列の設定

[WACOM.INCLUDE [storage-configure-connection-string][]]

## <a name="configure-access"> </a><span class="short-header">プログラムでのアクセス</span>方法: プログラムで BLOB ストレージにアクセスする

### アセンブリの取得

NuGet を使用して `Microsoft.WindowsAzure.Storage.dll` アセンブリを取得できます。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして Azure Storage のパッケージと依存関係をインストールします。

`Microsoft.WindowsAzure.Storage.dll` は、[.NET デベロッパー センター][]からダウンロードできる Azure SDK for .NET にも含まれています。このアセンブリは `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code> ディレクトリにインストールされます。

### 名前空間宣言

プログラムを使用して Azure ストレージにアクセスするすべての C#
ファイルの冒頭部分に、次の名前空間宣言を追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

必ず `Microsoft.WindowsAzure.Storage.dll` アセンブリを参照してください。

### 接続文字列の取得

**CloudStorageAccount** 型を使用してストレージ アカウント
情報を表すことができます。Azure プロジェクト
テンプレートを使用している場合や、
Microsoft.WindowsAzure.CloudConfigurationManager
への参照がある場合は、**CloudConfigurationManager** 型
を使用して Azure サービス構成からストレージ接続文字列
とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、接続文字列が前に示したように `web.config` または `app.config` に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

**CloudBlobClient** 型では、BLOB ストレージ サービス内に格納されている
コンテナーと BLOB を表すオブジェクトを取得できます。次の
コードは、前に取得したストレージ アカウント オブジェクトを使用して、
**CloudBlobClient** オブジェクトを作成します。

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### ODataLib 依存

.NET 用ストレージ クライアント ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet から入手できる ODataLib (バージョン 5.0.2) パッケージで解決されます。ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。具体的な ODataLib パッケージは、[OData][]、[Edm][]、および [Spatial][] です。

## <a name="create-container"> </a><span class="short-header">コンテナーの作成</span>方法: コンテナーを作成する

ストレージの BLOB はすべてコンテナー内に格納されます。**CloudBlobClient**
オブジェクトを使用すると、使用するコンテナーへの参照を取得
できます。コンテナーが存在しない場合は、次のようにして作成できます。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

既定では、新しいコンテナーはプライベートなので、
このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを
指定する必要があります。コンテナー内のファイルをだれでも利用できるようにする場合は、
次のコードを使ってコンテナーをパブリックに設定
できます。

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob }); 

パブリック コンテナー内の BLOB は、インターネットに接続している
すべてのユーザーが表示できますが、変更または削除できるのは、適切なアクセス キーを持っているユーザーだけです。

## <a name="upload-blob"> </a><span class="short-header">コンテナーへのアップロード</span>方法: コンテナーに BLOB をアップロードする

Azure BLOB Storage では、ブロック BLOB とページ BLOB がサポートされています。ほとんどの場合は、ブロック BLOB を使用することをお勧めします。

ファイルをブロック BLOB にアップロードするには、コンテナーの参照を取得し、
それを使用してブロック BLOB の参照を取得します。BLOB の参照を取得したら、**UploadFromStream** メソッドを
呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。この操作により、BLOB がまだ存在しない場合は作成され、
存在する場合は上書きされます。次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## <a name="list-blob"> </a><span class="short-header">コンテナー内の BLOB の一覧表示</span>方法: コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。次に、
コンテナーの **ListBlobs** メソッドを使って、その中の BLOB やディレクトリを取得
します。返される **IListBlobItem** のプロパティ
やメソッドにアクセスするには、**CloudBlockBlob**、
**CloudPageBlob**、**CloudBlobDirectory** のいずれかのオブジェクトにキャストする必要があります。型がわからない場合は、型チェック
を使うとどれにキャストすればよいかがわかります。次のコードは、
`photos` コンテナー内の各アイテムの URI を取得して出力する方法を示しています。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client. 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;
            
            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

この例からわかるように、BLOB サービスにはコンテナー内のディレクトリの概念も
あります。これは、BLOB をよりフォルダーに近い構造で整理できるように
するためです。たとえば、`photos` というコンテナーに次のブロック BLOB があったとします。

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

"photos" コンテナーの **ListBlobs** を呼び出すと (上の例を参照)、返されるコレクションには
最上位レベルにあるディレクトリおよび BLOB を表す **CloudBlobDirectory** および
**CloudBlockBlob** オブジェクトが含まれています。結果の出力は次のようになります。

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

必要に応じて、**ListBlobs** メソッドの **UseFlatBlobListing** パラメーターを
**true** に設定することもできます。そうすると、ディレクトリに関係なく、すべての BLOB が **CloudBlockBlob**
 として返されるようになります。この場合、**ListBlobs** の呼び出しは次のようになります。

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

結果は次のようになります。

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

詳細については、[CloudBlobContainer.ListBlobs に関するページ][]を参照してください。

## <a name="download-blobs"> </a><span class="short-header">BLOB のダウンロード</span>方法: BLOB をダウンロードする

BLOB をダウンロードするには、まず BLOB の参照を取得し、次に **DownloadToStream** メソッドを呼び出します。次の例は、
**DownloadToStream** メソッドを使用して、ローカル ファイルに
保存できるストリーム オブジェクトに BLOB の内容を転送します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

**DownloadToStream** メソッドを使用して BLOB の内容をテキスト文字列としてダウンロードすることもできます。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"> </a><span class="short-header">BLOB の削除</span>方法: BLOB を削除する

BLOB を削除するには、まず BLOB の参照を取得し、次にその
**Delete** メソッドを呼び出します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 

## <a name="next-steps"></a><span class="short-header">次の手順</span>次の手順

これで、BLOB ストレージの基本を学習できました。さらに複雑な
ストレージ タスクを実行する方法については、
次のリンク先を参照してください。

-   利用可能な API の詳細については、BLOB サービスのリファレンス ドキュメントを参照してください。
    -   [.NET 用ストレージ クライアント ライブラリ リファレンス][]
    -   [REST API リファレンス][]

-   Azure Storage を使用して実行できるさらに高度なタスクについては、「[Azure のデータの格納とアクセス][]」を参照してください。
-   Azure Websites のバックエンド プロセスで Azure Storage を使用する方法については、「[Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)][]」を参照してください。
-   Azure でデータを格納するための追加のオプションについては、他の機能ガイドも
    参照してください。
    -   構造化データの格納には、[テーブル ストレージ][]を使用します。
    -   非構造化データの格納には、[キュー ストレージ][]を使用します。
    -   リレーショナル データの格納には、[SQL データベース][]を使用します。

</p>

  [次のステップ]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Azure SDK for .NET]: /en-us/downloads/
  [方法: プログラムで BLOB ストレージにアクセスする]: #configure-access
  [BLOB ストレージとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [ストレージ接続文字列の設定]: #setup-connection-string
  [方法: コンテナーを作成する]: #create-container
  [方法: コンテナーに BLOB をアップロードする]: #upload-blob
  [方法: コンテナー内の BLOB を一覧表示する]: #list-blob
  [方法: BLOB をダウンロードする]: #download-blobs
  [方法: BLOB を削除する]: #delete-blobs
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [storage-configure-connection-string]: ../includes/storage-configure-connection-string.md
  [.NET デベロッパー センター]: http://www.windowsazure.com/en-us/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [.NET 用ストレージ クライアント ライブラリ リファレンス]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API リファレンス]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)]: /ja-jp/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [テーブル ストレージ]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-tables/
  [キュー ストレージ]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-queues/
  [SQL データベース]: /ja-jp/documentation/articles/sql-database-dotnet-how-to-use/
