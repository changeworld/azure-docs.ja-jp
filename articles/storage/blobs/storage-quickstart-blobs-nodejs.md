---
title: "Azure クイックスタート - Node.js を使用して Azure Blob Storage との間でオブジェクトを転送する | Microsoft Docs"
description: "Node.js を使って Azure Blob Storage との間で双方向にオブジェクトを転送する方法を説明します"
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/06/2018
ms.author: tamram
ms.openlocfilehash: 07845d0e1917c00dbd6098ef2bfbd9dcbbf2f97b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>Node.js を使用して Azure Blob Storage との間でオブジェクトを転送する

このクイックスタートでは、Node.js を使って、Azure Blob Storage 内のコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* [Node.js](https://nodejs.org/en/)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイックスタートで使用する[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)は、基本的なコンソール アプリケーションです。 

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 アプリケーションを開くには、storage-blobs-node-quickstart フォルダーを開き、index.js をダブルクリックします。

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

アプリケーションでは、ストレージ アカウントの接続文字列を指定する必要があります。 `index.js` ファイルを開き、`connectionString` 変数を探します。 その値と接続文字列の値全体を、Azure Portal から保存した値に置き換えます。 お使いのストレージの接続文字列は次のように表示されます。

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>必要なパッケージをインストールする

アプリケーション ディレクトリで `npm install` を実行し、`package.json` ファイルの一覧にあるすべての必要なパッケージをインストールします。

```javascript
npm install
```

## <a name="run-the-sample"></a>サンプルを実行する

このサンプルは、[マイ ドキュメント] にテスト ファイルを作成し、それを Blob Storage にアップロードし、コンテナー内の BLOB の一覧を取得してから、古いファイルと新しいファイルを比較できるように新しい名前でファイルをダウンロードします。

`node index.js` を入力して、サンプルを実行します。 次の出力は、Windows システムからのものです。  Linux を使用すると、適切なファイル パスと同様の出力にできます。

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

続行する前に、[マイ ドキュメント] で 2 つのファイルをチェックします。 それらを開いて、同じであるかどうか確認します。

[Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。

ファイルを確認した後、任意のキーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、index.js ファイルを開いてコードを確認します。 

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを実行して、そのしくみを理解できるようにします。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する

最初に、Blob Storage にアクセスして管理するために使う **BlobService** オブジェクトへの参照を作成します。 これらのオブジェクトは、他のオブジェクトを基にして作成されます。各オブジェクトは、一覧で次にあるオブジェクトによって使われます。

* ストレージ アカウントの Blob service を指す [BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor) オブジェクトのインスタンスを作成します。

* 新しいコンテナーを作成してから、BLOB をパブリックにして URL のみでアクセスできるように、コンテナーに対するアクセス許可を設定します。 **quickstartcontainer-** で始まるコンテナーです。

この例では、サンプルを実行するたびに新しいコンテナーを作成したいので、[createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) を使います。 アプリケーション全体で同じコンテナーを使用する運用環境では、CreateIfNotExists を 1 回だけ呼び出すことがよい方法です。 または、コードから作成する必要がないように、前もってコンテナーを作成しておいてもかまいません。

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 ブロック BLOB は最もよく使用されます。 ブロック BLOB は、テキストまたはバイナリ ファイルを格納するのに最適であるため、クイックスタートで使用されます。

BLOB にファイルをアップロードするには、[createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) メソッドを使用します。 この操作により、BLOB がまだ存在しない場合は作成され、既に存在する場合は上書きされます。

サンプル コードは、アップロードとダウンロードに使用するローカル ファイルを作成し、アップロードするファイルを **localPath** として、BLOB の名前を **localFileToUpload** に格納します。 次の例では、ファイルを **quickstartcontainer-** から始まる名前のコンテナーにアップロードします。

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Blob Storage では複数のアップロード方法を使うことができます。 たとえば、メモリ ストリームがある場合、[createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) ではなく [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) メソッドを使用できます。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

次に、[listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented) を使用して、アプリケーションがコンテナー内のファイルの一覧を取得します。 次のコードは、BLOB の一覧を取得し、ループ処理して、見つかった BLOB の URI を表示します。 コマンド ウィンドウから URI をコピーしてブラウザーに貼り付けることで、ファイルを表示できます。

コンテナー内の BLOB が 5,000 個以下の場合、[listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented) を 1 回呼び出すと、すべての BLOB 名が取得されます。 コンテナー内の BLOB が 5000 個より多い場合は、すべての BLOB 名が取得されるまで、5,000 個単位で一覧が取得されます。 この API を初めて呼び出すと、最初の 5,000 個の BLOB 名と継続トークンが返されます。 2 回目以降の呼び出しでは、このトークンを渡して、次の BLOB 名のセットを取得します。これを、継続トークンが null になるまで続けます。null は、すべての BLOB 名が取得されたことを示します。

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

### <a name="download-blobs"></a>BLOB をダウンロードする

[getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile)を使用して、ローカル ディスクに BLOB をダウンロードします。

次のコードは、前のセクションでアップロードした BLOB をダウンロードし、ローカル ディスクで両方のファイルを見ることができるように、BLOB 名に "_DOWNLOADED" というサフィックスを追加します。 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートでアップロードした BLOB が不要になった場合は、[deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) と [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists) を使ってコンテナー全体を削除できます。 また、不要になった場合は、作成されたファイルも削除します。 これは、ユーザーが [Enter] キーを押してアプリケーションを終了するときに、アプリケーション内で処理されます。

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Node.js を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Blob Storage の操作の詳細を学習するには、Blob Storage の操作方法に進みます。

> [!div class="nextstepaction"]
> [Blob Storage の操作方法](storage-nodejs-how-to-use-blob-storage.md)

Azure Storage に関する Node.js のリファレンスについては、「[azure-storage package (azure-storage パッケージ)](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest)」を参照してください。