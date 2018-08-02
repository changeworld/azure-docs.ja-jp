---
title: Azure クイック スタート - Java Storage SDK V7 を使用してオブジェクト ストレージに BLOB を作成する | Microsoft Docs
description: このクイック スタートでは、ストレージ アカウントとコンテナーをオブジェクト (BLOB) ストレージ内に作成します。 その後、Java 用のストレージ クライアント ライブラリを使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: 7fc5b164792907644a0a41615436806f998a8b1e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398711"
---
# <a name="quickstart-upload-download-and-list-blobs-using-java-sdk-v7"></a>クイック スタート: Java SDK V7 を使用して BLOB をアップロード、ダウンロード、および一覧表示する

このクイックスタートでは、Java を使って、Azure Blob Storage 内のコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* Maven が統合された IDE をインストールする

* または、Maven をインストールして、コマンド ラインから使用するように構成する

このチュートリアルでは、"Eclipse IDE for Java Developers" 構成の [Eclipse](http://www.eclipse.org/downloads/) を使用します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイックスタートで使う[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-java-quickstart)は、基本的なコンソール アプリケーションです。  

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 プロジェクトを開くには、Eclipse を起動し、ウェルカム画面を閉じます。 **[File]\(ファイル\)** を選択し、**[Open Projects from File System]\(ファイル システムからプロジェクトを開く\)** を選択します。**[Detect and configure project natures]\(プロジェクトの特性を検出して構成\)** チェック ボックスがオンになっていることを確認します。 **[Directory]\(ディレクトリ\)** を選択して、複製済みリポジトリの保存先に移動し、その中の **javaBlobsQuickstart** フォルダーを選択します。 Eclipse プロジェクトとして **javaBlobsQuickstarts** プロジェクトが表示されることを確認し、**[完了]** を選択します。

プロジェクトのインポートが完了したら、(**src/main/java** 内の **blobQuickstart.blobAzureApp** に格納されている) **AzureApp.java** を開き、`storageConnectionString` 文字列内の `accountname` と `accountkey` を置き換えます。 そのうえでアプリケーションを実行します。

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]    

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
    
アプリケーションでは、ストレージ アカウントの接続文字列を指定する必要があります。 **AzureApp.Java** ファイルを開きます。 `storageConnectionString` 変数を見つけて、前のセクションでコピーした接続文字列値を貼り付けます。 `storageConnectionString` 変数は次のようになります。

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>サンプルを実行する

このサンプルは、既定のディレクトリ (Windows ユーザーの場合はマイ ドキュメント) にテスト ファイルを作成して Blob Storage にアップロードし、コンテナー内の BLOB の一覧を取得してから、古いファイルと新しいファイルを比較できるように新しい名前でファイルをダウンロードします。 

コマンド ラインで Maven を使用してサンプルを実行します。 シェルを開き、複製されたディレクトリ内の **blobAzureApp** に移動します。 次に「`mvn compile exec:java`」と入力します。 

次に示したのは、アプリケーションを Windows で実行した場合の出力例です。

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

続行する前に、既定のディレクトリ (Windows ユーザーの場合はマイ ドキュメント) で 2 つのファイルをチェックしてください。 それらを開いて、同じであるかどうか確認します。 コンソール ウィンドウから BLOB の URL をコピーしてブラウザーに貼り付け、Blob Storage のファイルの内容を表示します。 Enter キーを押すと、ストレージ コンテナーとファイルが削除されます。 

[Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。

ファイルを確認した後、Enter キーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、**AzureApp.java** ファイルを開いてコードを確認します。 

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを実行して、そのしくみを理解できるようにします。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する

最初に、Blob Storage にアクセスして管理するために使うオブジェクトへの参照を作成します。 これらのオブジェクトは、他のオブジェクトを基にして作成されます。各オブジェクトは、一覧で次にあるオブジェクトによって使われます。

* ストレージ アカウントを指す [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage._storage_account) オブジェクトのインスタンスを作成します。

    **CloudStorageAccount** オブジェクトはストレージ アカウントの表現であり、これを使用してストレージ アカウントのプロパティをプログラムで設定してアクセスできます。 **CloudStorageAccount** オブジェクトを使用して、**CloudBlobClient** インスタンスを作成できます。これは Blob service へのアクセスで必要です。

* ストレージ アカウントの [Blob service](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) を指す **CloudBlobClient** オブジェクトのインスタンスを作成します。

    **CloudBlobClient** は Blob service へのアクセス ポイントを提供し、BLOB ストレージのプロパティをプログラムで設定してアクセスできるようにします。 **CloudBlobClient** オブジェクトを使用して、**CloudBlobContainer** インスタンスを作成できます。これはコンテナーを作成するために必要です。

* アクセスしているコンテナーを表す [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) オブジェクトのインスタンスを作成します。 コンテナーは、コンピューターでフォルダーを使ってファイルを整理するのと同じように、BLOB を整理するために使われます。    

    **CloudBlobContainer** を作成した後は、関心がある特定の BLOB を指す [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) オブジェクトのインスタンスを作成して、アップロード、ダウンロード、コピーなどの操作を実行できます。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[コンテナー、BLOB、メタデータの名前付けと参照](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

### <a name="create-a-container"></a>コンテナーを作成する

このセクションでは、オブジェクトのインスタンスを作成し、新しいコンテナーを作成した後、BLOB をパブリックにして URL のみでアクセスできるように、コンテナーに対するアクセス許可を設定します。 コンテナーの名前は **quickstartblobs** です。 

この例では、サンプルを実行するたびに新しいコンテナーを作成したいので、[CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists) を使います。 アプリケーション全体で同じコンテナーを使用する運用環境では、**CreateIfNotExists** を 1 回だけ呼び出すことがよい方法です。 または、コードから作成する必要がないように、前もってコンテナーを作成しておいてもかまいません。

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage では、ブロック BLOB、追加 BLOB、およびページ BLOB がサポートされています。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。 

ファイルを BLOB にアップロードするには、ターゲット コンテナーの BLOB に対する参照を取得します。 BLOB の参照を取得した後は、[CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long) を使ってそれにデータをアップロードできます。 この操作により、BLOB がまだ存在しない場合は作成され、既に存在する場合は上書きされます。

次のサンプル コードは、アップロードとダウンロードで使用されるローカル ファイルを作成し、 そのファイルを**ソース**としてアップロードされるファイルとして格納し、BLOB 名を **blob** に格納します。 次の例では、ファイルを **quickstartblobs** という名前のコンテナーにアップロードします。

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Blob Storage では、[upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload)、[uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock)、[uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob)、[uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier)、[uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext) など、いくつかのアップロード方法を用いることができます。 たとえば、文字列がある場合は、Upload メソッドではなく、UploadText メソッドを使用できます。 

ブロック BLOB は、あらゆる種類のテキスト ファイルまたはバイナリ ファイルに使うことができます。 ページ BLOB は、主に、IaaS VM のバックアップ用の VHD ファイルに使われます。 追加 BLOB は、ファイルに書き込んでから情報を追加する場合など、ログ記録に使われます。 BLOB ストレージに格納されているほとんどのオブジェクトはブロック BLOB です。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内のファイルの一覧を取得するには、[CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs) を使います。 次のコードは、BLOB の一覧を取得し、ループ処理して、見つかった BLOB の URI を表示します。 コマンド ウィンドウから URI をコピーしてブラウザーに貼り付けることで、ファイルを表示できます。

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>BLOB をダウンロードする

ローカル ディスクに BLOB をダウンロードするには、[CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String) を使います。

次のコードは、前のセクションでアップロードした BLOB をダウンロードし、ローカル ディスクで両方のファイルを見ることができるように、BLOB 名に "_DOWNLOADED" というサフィックスを追加します。 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートでアップロードした BLOB が不要になった場合は、[CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists) を使ってコンテナー全体を削除できます。 これによりコンテナー内のファイルも削除されます。

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Java を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Java の使用方法の詳細については、GitHub のソース コード リポジトリを参照してください。

> [!div class="nextstepaction"]
> [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java) 
> [API リファレンス](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-stable)
> [Java のコード サンプル](../common/storage-samples-java.md)

* Storage Explorer と BLOB について詳しくは、「[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。