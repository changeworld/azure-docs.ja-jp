---
title: Azure クイック スタート - Go を使用してオブジェクト ストレージに BLOB を作成する | Microsoft Docs
description: このクイック スタートでは、ストレージ アカウントとコンテナーをオブジェクト (BLOB) ストレージ内に作成します。 その後、Go 用のストレージ クライアント ライブラリを使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: seguler
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: seguler
ms.openlocfilehash: 90858501cbf20af032c98ca8703f9e74b475e9c1
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398578"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>クイック スタート: Go を使用して BLOB をアップロード、ダウンロード、および一覧表示する

このクイックスタートでは、Go プログラミング言語を使って、Azure Blob Storage 内のコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。 

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。 
* [Go 1.8 以上](https://golang.org/dl/)をインストールする
* `go get -u github.com/azure/azure-storage-blob-go/2016-05-31/azblob` を使用して [Go 用の Azure Storage Blob SDK](https://github.com/azure/azure-storage-blob-go/) をダウンロードしてインストールする。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード
このクイックスタートで使う[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git)は、基本的な Go アプリケーションです。  

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 BLOB ストレージ用の Go サンプルを開くために、storage-quickstart.go ファイルを探します。  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
このソリューションでは、サンプルを実行するマシンに対してローカルな環境変数に、ストレージ アカウントの名前とキーが安全に格納される必要があります。 環境変数を作成するオペレーティング システムに応じて、以下のいずれかの例に従います。

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>サンプルを実行する
このサンプルでは、現在のフォルダーにテスト ファイルを作成してそれを BLOB ストレージにアップロードし、コンテナー内の BLOB の一覧を取得してから、そのファイルをバッファーにダウンロードします。 

サンプルを実行するには、次のコマンドを発行します。 

```go run storage-quickstart.go```

次の出力は、アプリケーションを実行するときに返される出力の例です。
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
キーを押して続行すると、サンプル プログラムによってストレージ コンテナーとファイルが削除されます。 

> [!TIP]
> [Azure Storage Explorer](http://storageexplorer.com) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 
>

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを実行して、そのしくみを理解できるようにします。

### <a name="create-containerurl-and-bloburl-objects"></a>ContainerURL オブジェクトと BlobURL オブジェクトを作成する
最初に、BLOB ストレージにアクセスして管理するために使用される ContainerURL オブジェクトと BlobURL オブジェクトへの参照を作成します。 これらのオブジェクトは、REST API を発行する低レベルの API (Create、PutBlob、GetBlob など) を提供します。

* **SharedKeyCredential** 構造体を使用して資格情報を格納します。 

* 資格情報とオプションを使用して**パイプライン**を作成します。 パイプラインでは、再試行ポリシー、ログの記録、HTTP 応答ペイロードの逆シリアル化などを指定します。  

* 新しい ContainerURL オブジェクトと、コンテナーに対する操作 (Create) と BLOB に対する操作 (PutBlob および GetBlob) を実行する新しい BlobURL オブジェクトをインスタンス化します。


ContainerURL を作成したら、BLOB をポイントする **BlobURL** オブジェクトをインスタンス化して、アップロード、ダウンロード、コピーなどの操作を実行できます。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[コンテナー、BLOB、メタデータの名前付けと参照](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

このセクションでは、新しいコンテナーを作成します。 コンテナーの名前は **quickstartblobs-[random string]** です。 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential := azblob.NewSharedKeyCredential(accountName, accountKey)
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err := containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。  

ファイルを BLOB にアップロードするには、**os.Open** を使用してファイルを開きます。 PutBlob、PutBlock、PutBlockList のいずれかの REST API を使用して、指定されたパスにファイルをアップロードできます。 

別の方法として、低レベルの REST API の上に構築された[高レベルの API](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go) が SDK に用意されています。 たとえば ***UploadFileToBlockBlob*** 関数では、スループットを最適化するために、PutBlock 操作を使用してチャンクにしたファイルを同時にアップロードします。 ファイルが 256 MB 未満の場合、代わりに PutBlob を使用して単一のトランザクションで転送を完了します。

次の例では、ファイルを **quickstartblobs-[randomstring]** という名前のコンテナーにアップロードします。

```go
// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level PutBlob API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that PutBlob can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.PutBlob(ctx, file, azblob.BlobHTTPHeaders{}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls PutBlock/PutBlockList for files larger 256 MBs, and calls PutBlob for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

**ContainerURL** で **ListBlobs** メソッドを使用して、コンテナー内のファイルの一覧を取得します。 ListBlobs は、指定された**マーカー**から開始して単一セグメントの BLOB (最大 5,000) を返します。 空のマーカーを使用して最初から列挙を開始します。 BLOB 名は辞書式順序で返されます。 セグメントの取得後、それを処理し、前に返されたマーカーを渡す ListBlobs を再度呼び出します。  

```go
// List the blobs in the container
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobs(ctx, marker, azblob.ListBlobsOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Blobs.Blob {
        fmt.Print("Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>BLOB をダウンロードする

BlobURL で **GetBlob** 低レベル メソッドを使用して、BLOB をダウンロードします。 または、[highlevel.go](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go) で提供される **NewDownloadStream** 高レベル API を使用して、ストリームを作成してそこから範囲を読み取ることができます。 NewDownloadStream 関数は接続エラーの際に再試行されますが、GetBlob API は、503 (サーバー ビジー) などの HTTP 状態コードの場合にのみ再試行されます。 次のコードでは、**NewDownloadStream** 関数を使用して BLOB をダウンロードします。 BLOB の内容がバッファーに書き込まれ、コンソールに表示されます。

```go
// Here's how to download the blob. NOTE: This method automatically retries if the connection fails
// during download (the low-level GetBlob function does NOT retry errors when reading from its stream).
stream := azblob.NewDownloadStream(ctx, blobURL.GetBlob, azblob.DownloadStreamOptions{})
downloadedData := &bytes.Buffer{}
_, err = downloadedData.ReadFrom(stream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイックスタートでアップロードした BLOB が不要になった場合は、**Delete** メソッドを使ってコンテナー全体を削除できます。 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press the enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>BLOB を使用する Go アプリケーションを開発するためのリソース

Blob Storage を使用する Go 開発については、以下の追加リソースを参照してください。

- GitHub で Azure Storage 用の [Go クライアント ライブラリ ソース コード](https://github.com/Azure/azure-storage-blob-go)を確認し、インストールします。
- Go クライアント ライブラリを使用して記述された [Blob Storage のサンプル](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob#pkg-examples)を確認します。

## <a name="next-steps"></a>次の手順
 
このクイックスタートでは、Go を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Azure Storage Blob SDK の詳細については、[ソース コード](https://github.com/Azure/azure-storage-blob-go/)と [API リファレンス](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob)をご覧ください。
