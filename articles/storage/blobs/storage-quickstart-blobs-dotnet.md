---
title: "Azure クイック スタート - .NET を使用して Azure Storage の BLOB をアップロード、ダウンロード、および一覧表示する | Microsoft Docs"
description: "このクイック スタートでは、ストレージ アカウントおよびコンテナーを作成します。 その後、.NET 用のストレージ クライアント ライブラリを使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。"
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: tamram
ms.openlocfilehash: 265691ff189c628156f234083645a4b2ca4b637b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>クイック スタート: .NET を使用して BLOB をアップロード、ダウンロード、および一覧表示する

このクイックスタートでは、Azure Storage 用の .NET クライアント ライブラリを使用して、コンテナー内のブロック BLOB のアップロード、ダウンロード、および一覧取得を行う方法を説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) または [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x) 向けの .NET core 2.0 をインストールします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイックスタートで使うサンプル アプリケーションは、基本的なコンソール アプリケーションです。 

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 Visual Studio ソリューションを開くには、storage-blobs-dotnet-quickstart フォルダーを開き、storage-blobs-dotnet-quickstart.sln をダブルクリックします。 

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

アプリケーションでは、ストレージ アカウントの接続文字列を指定する必要があります。 アプリケーションを実行しているローカル コンピューター上の環境変数内にこの接続文字列を格納することをお勧めします。 環境変数を作成するオペレーティング システムに応じて、以下の例のいずれかに従います。  \<yourconnectionstring\> をお使いの実際の接続文字列に置換します。

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>サンプルを実行する

このサンプルは、[マイ ドキュメント] にテスト ファイルを作成し、それを Blob Storage にアップロードし、コンテナー内の BLOB の一覧を取得してから、古いファイルと新しいファイルを比較できるように新しい名前でファイルをダウンロードします。 

お使いのアプリケーションのディレクトリに移動し、`dotnet run` コマンドを使ってアプリケーションを実行します。

```
dotnet run
```

表示される出力は、次の例のようになります。

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

いずれかのキーを押して続行すると、ストレージ コンテナーとファイルが削除されます。 続行する前に、[マイ ドキュメント] で 2 つのファイルをチェックします。 それらを開いて、同じであるかどうか確認します。 コンソール ウィンドウから BLOB の URL をコピーしてブラウザーに貼り付け、Blob Storage のファイルの内容を表示します。

[Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 

ファイルを確認した後、任意のキーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、Program.cs ファイルを開いてコードを確認します。 

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを実行して、そのしくみを理解できるようにします。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する

最初に、Blob Storage にアクセスして管理するために使うオブジェクトへの参照を作成します。 これらのオブジェクトはお互いを基にして作成され、各オブジェクトは、以下の一覧の直後のオブジェクトによって使用されます。

* ストレージ アカウントを指す [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet) オブジェクトのインスタンスを作成します。

* ストレージ アカウントの Blob service を指す [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet) オブジェクトのインスタンスを作成します。

* アクセスしているコンテナーを表す [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) オブジェクトのインスタンスを作成します。 コンテナーは、コンピューターでフォルダーを使ってファイルを整理するのと同じように、BLOB を整理するために使われます。

[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) を作成した後は、関心がある特定の BLOB を指す [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet) オブジェクトのインスタンスを作成して、アップロード、ダウンロード、コピーなどの操作を実行できます。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[コンテナー、BLOB、メタデータの名前付けと参照](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

このセクションでは、オブジェクトのインスタンスを作成し、新しいコンテナーを作成した後、BLOB をパブリックにして URL のみでアクセスできるように、コンテナーに対するアクセス許可を設定します。 コンテナーの名前は **quickstartblobs** です。

この例では、サンプルを実行するたびに新しいコンテナーを作成したいので、[CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet) を使います。 アプリケーション全体で同じコンテナーを使う運用環境では、**CreateIfNotExists** を 1 回だけ呼び出すことがよい方法です。 または、コードから作成する必要がないように、前もってコンテナーを作成しておいてもかまいません。

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage では、ブロック BLOB、追加 BLOB、およびページ BLOB がサポートされています。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。

ファイルを BLOB にアップロードするには、ターゲット コンテナーの BLOB に対する参照を取得します。 BLOB の参照を取得した後は、[CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) を使ってそれにデータをアップロードできます。 この操作により、BLOB がまだ存在しない場合は作成され、既に存在する場合は上書きされます。

サンプル コードは、アップロードとダウンロードに使うローカル ファイルを作成し、アップロードするファイルを **fileAndPath** として、BLOB の名前を **localFileName** に格納します。 次の例では、ファイルを **quickstartblobs** という名前のコンテナーにアップロードします。

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

Blob Storage では複数のアップロード方法を使うことができます。 たとえば、メモリ ストリームがある場合、UploadFromFileAsync ではなく UploadFromStreamAsync メソッドを使うことができます。

ブロック BLOB は、あらゆる種類のテキスト ファイルまたはバイナリ ファイルに使うことができます。 ページ BLOB は、主に、IaaS VM のバックアップ用の VHD ファイルに使われます。 追加 BLOB は、ファイルに書き込んでから情報を追加する場合など、ログ記録に使われます。 BLOB ストレージに格納されているほとんどのオブジェクトはブロック BLOB です。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内のファイルの一覧を取得するには、[CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) を使用できます。 次のコードは、BLOB の一覧を取得し、ループ処理して、見つかった BLOB の URI を表示します。 コマンド ウィンドウから URI をコピーしてブラウザーに貼り付けることで、ファイルを表示できます。

コンテナー内の BLOB が 5,000 個以下の場合、ListBlobsSegmentedAsync を 1 回呼び出すと、すべての BLOB 名が取得されます。 コンテナー内の BLOB が 5000 個より多い場合は、すべての BLOB 名が取得されるまで、5,000 個単位で一覧が取得されます。 この API を初めて呼び出すと、最初の 5,000 個の BLOB 名と継続トークンが返されます。 2 回目以降の呼び出しでは、このトークンを渡して、次の BLOB 名のセットを取得します。これを、継続トークンが null になるまで続けます。null は、すべての BLOB 名が取得されたことを示します。

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null);

```

### <a name="download-blobs"></a>BLOB をダウンロードする

ローカル ディスクに BLOB をダウンロードするには、[CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) を使います。

次のコードは、前のセクションでアップロードした BLOB をダウンロードし、ローカル ディスクで両方のファイルを見ることができるように、BLOB 名に "_DOWNLOADED" というサフィックスを追加します。

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートでアップロードした BLOB が不要になった場合は、[CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) を使ってコンテナー全体を削除できます。 また、不要になった場合は、作成されたファイルも削除します。

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、.NET を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Blob Storage の操作についてさらに学習するには、Blob Storage の操作方法に関するトピックに進んでください。

> [!div class="nextstepaction"]
> [Blob Storage の操作方法](storage-dotnet-how-to-use-blobs.md)

ダウンロードして実行できる他の Azure Storage コード サンプルについては、「[.NET を使用した Azure Storage サンプル](../common/storage-samples-dotnet.md)」の一覧を参照してください。

Storage Explorer と BLOB について詳しくは、「[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。
