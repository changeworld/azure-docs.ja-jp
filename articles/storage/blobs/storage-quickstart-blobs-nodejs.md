---
title: クイック スタート:Azure Blob Storage ライブラリ v12 - JavaScript
description: このクイックスタートでは、JavaScript 用 Azure Blob Storage クライアント ライブラリ バージョン 12 を使用して、BLOB (オブジェクト) ストレージ内にコンテナーと BLOB を作成する方法について説明します。 次に、ローカル コンピューターに BLOB をダウンロードする方法と、コンテナー内のすべての BLOB を一覧表示する方法について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 28a75158b161e680f857b986bcb754f1f99e8fab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825316"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>クイック スタート:JavaScript 用 Azure Blob Storage クライアント ライブラリ v12

JavaScript 用 Azure Blob Storage クライアント ライブラリ v12 を使用してみましょう。 Azure Blob Storage は、Microsoft のクラウド用オブジェクト ストレージ ソリューションです。 手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。 Blob Storage は、テキスト データやバイナリ データなどの大量の非構造化データを格納するために最適化されています。

> [!NOTE]
> 以前の SDK バージョンを使ってみるには、「[クイックスタート: JavaScript 用 Azure Blob Storage クライアント ライブラリ](storage-quickstart-blobs-nodejs-v10.md)」を参照してください。

JavaScript 用 Azure Blob Storage クライアント ライブラリ v12 を使用すると、以下のことができます。

* コンテナーを作成する
* Azure Storage への BLOB のアップロード
* コンテナー内のすべての BLOB を一覧表示する
* ローカル コンピューターに BLOB をダウンロードする
* コンテナーを削除する

[API のリファレンスのドキュメント](/javascript/api/@azure/storage-blob) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [パッケージ (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
* Azure Storage アカウント - [ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 使用するオペレーティング システム用の最新の [Node.js](https://nodejs.org/en/download/)。

## <a name="setting-up"></a>設定

このセクションでは、JavaScript 用 Azure Blob Storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。

### <a name="create-the-project"></a>プロジェクトを作成する

*blob-quickstart-v12* という名前の JavaScript アプリケーションを作成します。

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、プロジェクト用に新しいディレクトリを作成します。

    ```console
    mkdir blob-quickstart-v12
    ```

1. 新しく作成された *blob-quickstart-v12* ディレクトリに切り替えます。

    ```console
    cd blob-quickstart-v12
    ```

1. *package.json* という名前の新しいテキスト ファイルを作成します。 このファイルには、Node.js プロジェクトが定義されます。 このファイルを *blob-quickstart-v12* ディレクトリに保存します。 ファイルのコンテンツを次に示します。

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    必要に応じて、`author` フィールドにご自身の名前を入力できます。
   
### <a name="install-the-package"></a>パッケージをインストールする

まだ *blob-quickstart-v12* ディレクトリにいる間に、`npm install` コマンドを使用して、JavaScript パッケージ用 Azure Blob Storage クライアント ライブラリをインストールします。 このコマンドでは、*package.json* ファイルを読み取り、JavaScript パッケージ用 Azure Blob Storage クライアント ライブラリ v12 と、依存しているすべてのライブラリをインストールします。

```console
npm install
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. コード エディターで別の新しいテキスト ファイルを開く
1. `require` の呼び出しを追加して Azure および Node.js モジュールを読み込む
1. 非常に基本的な例外処理を含め、プログラムの構造を作成する

    コードは次のとおりです。

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - Javascript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. *blob-quickstart-v12* ディレクトリに新しいファイルを *blob-quickstart-v12.js* として保存します。

### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure Portal で資格情報をコピーする

サンプル アプリケーションから Azure Storage に対して要求を実行するときは、承認されている必要があります。 要求を承認するには、ストレージ アカウントの資格情報を接続文字列としてアプリケーションに追加します。 次の手順に従って、ストレージ アカウントの資格情報を表示します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 自分のストレージ アカウントを探します。
3. ストレージ アカウントの概要の **[設定]** セクションで、 **[アクセス キー]** を選択します。 ここでは、アカウント アクセス キーと各キーの完全な接続文字列を表示できます。
4. **[Key1]** の **[接続文字列]** の値を見つけ、 **[コピー]** ボタンを選択して接続文字列をコピーします。 すぐ後の手順で、接続文字列の値を環境変数に追加します。

    ![Azure portal から接続文字列をコピーする方法を示すスクリーンショット](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

接続文字列をコピーした後、アプリケーションを実行しているローカル マシンの新しい環境変数にそれを書き込みます。 環境変数を設定するには、コンソール ウィンドウを開いて、お使いのオペレーティング システムの手順に従います。 `<yourconnectionstring>` は、実際の接続文字列に置き換えてください。

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Windows で環境変数を追加した後、コマンド ウィンドウの新しいインスタンスを開始する必要があります。

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>プログラムの再起動

環境変数を追加した後、環境変数の読み取りを必要とする実行中のプログラムをすべて再起動します。 たとえば、続行する前に、ご使用の開発環境またはエディターを再起動します。

## <a name="object-model"></a>オブジェクト モデル

Azure Blob Storage は、大量の非構造化データを格納するために最適化されています。 非構造化データとは、特定のデータ モデルや定義に従っていないデータであり、テキスト データやバイナリ データなどがあります。 Blob Storage には、3 種類のリソースがあります。

* ストレージ アカウント
* ストレージ アカウント内のコンテナー
* コンテナー内の BLOB

次の図に、これらのリソースの関係を示します。

![Blob Storage のアーキテクチャ図](./media/storage-blob-introduction/blob1.png)

これらのリソースとやり取りするには、以下の JavaScript クラスを使用します。

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient):`BlobServiceClient` クラスを使用して、Azure Storage リソースと BLOB コンテナーを操作できます。
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient):`ContainerClient` クラスを使用して、Azure Storage コンテナーとその BLOB を操作できます。
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient):`BlobClient` クラスを使用して、Azure Storage BLOB を操作できます。

## <a name="code-examples"></a>コード例

これらのコード例のスニペットでは、JavaScript 用 Azure Blob Storage クライアント ライブラリを使用して以下を実行する方法を示します。

* [接続文字列を取得する](#get-the-connection-string)
* [コンテナーの作成](#create-a-container)
* [コンテナーに BLOB をアップロードする](#upload-blobs-to-a-container)
* [コンテナー内の BLOB を一覧表示する](#list-the-blobs-in-a-container)
* [BLOB をダウンロードする](#download-blobs)
* [コンテナーの削除](#delete-a-container)

### <a name="get-the-connection-string"></a>接続文字列を取得する

以下のコードでは、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」セクションで作成した環境変数から、ストレージ アカウントに対する接続文字列を取得します。

次のコードを `main` 関数内に追加します。

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>コンテナーを作成する

新しいコンテナーの名前を決定します。 次のコードでは、確実に一意になるように、コンテナー名に UUID 値を追加します。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前付けの詳細については、「[Naming and Referencing Containers, Blobs, and Metadata (コンテナー、BLOB、メタデータの名前付けと参照)](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

[fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--newpipelineoptions-) メソッドを呼び出して、[BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) クラスのインスタンスを作成します。 次に、[getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) メソッドを呼び出して、コンテナーへの参照を取得します。 最後に、[create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) を呼び出して、ストレージ アカウントにコンテナーを実際に作成します。

`main` 関数の末尾に次のコードを追加します。

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await new BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
await containerClient.create();
```

### <a name="upload-blobs-to-a-container"></a>コンテナーに BLOB をアップロードする

次のコード スニペット:

1. BLOB にアップロードするテキスト文字列を作成します。
1. 「[コンテナーを作成する](#create-a-container)」セクションからの [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) 上で [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) メソッドを呼び出すことで、[BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) オブジェクトへの参照を取得します。
1. [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) メソッドを呼び出して、テキスト文字列データを BLOB にアップロードします。

`main` 関数の末尾に次のコードを追加します。

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure Storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
await blockBlobClient.upload(data, data.length);
```

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) メソッドを呼び出して、コンテナー内の BLOB を一覧表示します。 この場合、コンテナーに BLOB が 1 つだけ追加されているので、一覧表示操作ではその 1 つの BLOB だけが返されます。

`main` 関数の末尾に次のコードを追加します。

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>BLOB をダウンロードする

[download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) メソッドを呼び出して、以前に作成した BLOB をダウンロードします。 コード例には、Node.js の読み取り可能なストリームを 1 つの文字列に読み取るために使用される `streamToString` というヘルパー関数が含まれています。

`main` 関数の末尾に次のコードを追加します。

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

`main` 関数の "*後*" に、このヘルパー関数を追加します。

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>コンテナーを削除する

次のコードでは、[delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) メソッドを使用して、コンテナー全体を削除することで、アプリによって作成されたリソースをクリーンアップします。 また、必要に応じてローカル ファイルを削除することもできます。

`main` 関数の末尾に次のコードを追加します。

```javascript
console.log('\nDeleting container...');

// Delete container
await containerClient.delete();
```

## <a name="run-the-code"></a>コードの実行

このアプリでは、テキスト文字列が作成され、BLOB ストレージにアップロードされます。 例では、コンテナー内の BLOB を一覧表示し、BLOB をダウンロードして、ダウンロードしたデータを表示します。

コンソール プロンプトから、*blob-quickstart-v12.py* ファイルが格納されているディレクトリに移動し、次の `node` コマンドを実行してアプリを実行します。

```console
node blob-quickstart-v12.js
```

アプリの出力は、次の例のようになります。

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

デバッガーでコードをステップ実行し、プロセス全体について Azure portal 上でチェックします。 コンテナーが作成されていることを確認するために、チェックを行います。 コンテナー内で BLOB を開いて、コンテンツを表示することができます。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、JavaScript を使用して BLOB をアップロード、ダウンロード、および一覧表示する方法について説明しました。

BLOB ストレージのサンプル アプリを確認するには、以下に進んでください。

> [!div class="nextstepaction"]
> [Azure Blob ストレージ SDK v12 の JavaScript サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/azure-storage-blob/samples)

* 詳細については、[JavaScript 用の Azure SDK](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md) に関するページを参照してください。
* チュートリアル、サンプル、クイックスタートなどのドキュメントについては、「[Azure SDK for JavaScript のドキュメント](/azure/javascript/)」を参照してください。
