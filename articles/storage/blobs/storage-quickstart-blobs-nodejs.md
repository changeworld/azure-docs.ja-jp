---
title: Azure クイック スタート - Node.js を使用してオブジェクト ストレージに BLOB を作成する | Microsoft Docs
description: このクイック スタートでは、ストレージ アカウントとコンテナーをオブジェクト (BLOB) ストレージ内に作成します。 その後、Node.js 用のストレージ クライアント ライブラリを使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: craigshoemaker
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: cshoe
ms.openlocfilehash: acf332209ca0588ab7722ddcfdcfe7b6715d672c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397934"
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>クイック スタート: Node.js を使用して BLOB をアップロード、ダウンロード、および一覧表示する

このクイック スタートでは、Azure Blob Storage を使用してコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行うための Node.js の使用方法を説明します。

このクイック スタートを完了するには、[Azure サブスクリプション](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)が必要です。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイック スタートで使用する[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)は、シンプルな Node.js コンソール アプリケーションです。 開始するには、次のコマンドを使用して、お使いのマシンにリポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

アプリケーションを開くには、*storage-blobs-node-quickstart* フォルダーを探して、任意のコード編集環境で開きます。

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

アプリケーションを実行する前に、ストレージ アカウントの接続文字列を指定する必要があります。 サンプル リポジトリには、*.env.example* という名前のファイルが含まれています。 このファイル名から *.example* 拡張子を削除して、*.env* というファイル名に変更できます。 *.env* ファイル内で、*AZURE_STORAGE_CONNECTION_STRING* キーの後ろに接続文字列の値を追加します。

## <a name="install-required-packages"></a>必要なパッケージをインストールする

アプリケーション ディレクトリで *npm install* を実行して、アプリケーションに必要なパッケージをインストールします。

```bash
npm install
```

## <a name="run-the-sample"></a>サンプルを実行する
これで依存関係がインストールされたので、コマンドをスクリプトに渡すことにより、サンプルを実行することができます。 たとえば、BLOB コンテナーを作成するには、次のコマンドを実行します。

```bash
node index.js --command createContainer
```

次のコマンドを使用できます。

| コマンド | 説明 |
|---------|---------|
|*createContainer* | *test-container* という名前のコンテナーを作成する (コンテナーが既に存在する場合でも成功します) |
|*upload*          | *example.txt* ファイルを *test-container* コンテナーにアップロードする |
|*download*        | *example* BLOB の内容を *example.downloaded.txt* にダウンロードする |
|*delete*          | *example* BLOB を削除する |
|*list*            | *test-container* コンテナーの内容をコンソールに一覧表示する |


## <a name="understanding-the-sample-code"></a>サンプル コードについて
このコード サンプルは、いくつかのモジュールを使用して、ファイル システムおよびコマンド ラインとのインターフェイスをとります。 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

各モジュールの目的は次のとおりです。 

- *dotenv*: *.env* という名前のファイルで定義された環境変数を現在の実行コンテキストに読み込む
- *path*: Blob Storage にアップロードするファイルの絶対ファイル パスを決定するのに必要
- *yargs*: コマンド ライン引数にアクセスするシンプルなインターフェイスを公開
- *azure-storage*: Node.js 用の [Azure Storage SDK](https://docs.microsoft.com/javascript/api/azure-storage) モジュール

次に、一連の変数が初期化されます。

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

変数は、次の値に設定されます。

- *blobService*: Azure Blob service の新しいインスタンス
- *containerName*: コンテナー名
- *sourceFilePath*: アップロードするファイルの絶対パス
- *blobName*: ファイル名を取得してファイル拡張子を削除することにより作成

次の実装では、*blobService* の各関数は *Promise* にラップされます。これにより、JavaScript の *async* 関数と *await* 演算子にアクセスして [Azure Storage API](/nodejs/api/azure-storage/blobservice) のコールバックの性質を合理化できます。 各関数に対して正常な応答が返されると、アクションに固有のメッセージと関連データを使用して Promise が解決されます。

### <a name="create-a-blob-container"></a>BLOB コンテナーを作成する

*createContainer* 関数は [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) を呼び出し、BLOB に適切なアクセス レベルを設定します。

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

**createContainerIfNotExists** の 2 番目のパラメーター ("*オプション*") は、[publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) の値を受け取ります。 *publicAccessLevel* の値 *blob* は、特定の BLOB データがパブリックに公開されることを示します。 この設定は、コンテナーの内容を一覧表示する権限を付与する *container* レベル アクセスとは対照的です。

**createContainerIfNotExists** を使用すると、コンテナーが既に存在する場合でも、アプリケーションはエラーを返すことなく *createContainer* コマンドを複数回実行できます。 運用環境では、アプリケーション全体で同じコンテナーが使用されるため、多くの場合 **createContainerIfNotExists** は 1 回だけ呼び出されます。 このような場合は、ポータルや Azure CLI を使用して、事前にコンテナーを作成できます。

### <a name="upload-a-blob-to-the-container"></a>コンテナーに BLOB をアップロードする

*upload* 関数は、[createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) 関数を使用して、ファイル システムから Blob Storage にファイルをアップロードし、書き込みまたは上書きを行います。 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
このサンプル アプリケーションのコンテキストでは、*test-container* コンテナー内の *example* という名前の BLOB に *example.txt* というファイルがアップロードされます。 BLOB へのコンテンツのアップロードに使用できる他のアプローチとしては、[text](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) や [streams](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream) の操作があります。

ファイルが Blob Storage にアップロードされたことを確認するには、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、自分のアカウントでデータを表示します。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

*list* 関数は [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) メソッドを呼び出して、コンテナー内の BLOB メタデータの一覧を返します。 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

*listBlobsSegmented* を呼び出すと、[BlobResult](/nodejs/api/azure-storage/blobresult) インスタンスの配列として BLOB メタデータが返されます。 結果は 5,000 の増分バッチ (セグメント) で返されます。 コンテナー内に 5,000 を超える BLOB がある場合は、結果に **continuationToken** の値が含まれます。 BLOB コンテナーから後続のセグメントを一覧表示するには、この継続トークンを 2 番目の引数として **listBlobSegmented** に戻します。

### <a name="download-a-blob-from-the-container"></a>コンテナーから BLOB をダウンロードする

*download* 関数は、[getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) を使用して、BLOB の内容を指定された絶対ファイル パスにダウンロードします。

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
次に示す実装では、ソース ファイルのパスを変更して、ファイル名に *.downloaded.txt* を追加しています。 実際のコンテキストでは、ダウンロード先の選択時に、ファイル名だけでなく、場所も変更できます。

### <a name="delete-blobs-in-the-container"></a>コンテナー内の BLOB を削除する

*deleteBlock* 関数 (別名 *delete* コンソール コマンド) は、[deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) 関数を呼び出します。 名前が示すとおり、この関数は BLOB が既に削除されていてもエラーを返しません。

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>アップロードと一覧表示

Promise を使用する利点の 1 つは、コマンドを連結できることです。 **uploadAndList** 関数により、ファイルをアップロードした後、BLOB の内容を直接、簡単に一覧表示できます。

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>コードの呼び出し

コマンド ラインに実装されている関数を公開するため、関数はそれぞれオブジェクト リテラルにマップされています。

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

*_module* が導入されたことにより、各コマンドがコマンド ラインから使用できるようになりました。

```javascript
const commandExists = () => exists = !!_module[args.command];
```

指定されたコマンドが存在しない場合は、ユーザーへのヘルプ テキストとして *_module* のプロパティがコンソールにレンダリングされます。 

*executeCommand* 関数は *async* 関数の 1 つで、指定されたコマンドを *await* 演算子を使用して呼び出し、データに対するすべてのメッセージをコンソールにログ出力します。

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

最後に、実行コードがまず *commandExists* を呼び出して、既知のコマンドがスクリプトに渡されることを確認します。 既存のコマンドが選択されている場合は、そのコマンドが実行され、エラーが発生した場合はコンソールにログ出力されます。

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
この記事で作成したデータやアカウントを使用する予定がない場合は、不要な請求が発生しないように削除してください。 BLOB とコンテナーを削除するには、[deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) メソッドと [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_) メソッドを使用します。 [ポータルを使用して](../common/storage-create-storage-account.md)、ストレージ アカウントを削除することもできます。

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>BLOB を使用する Node.js アプリケーションを開発するためのリソース

Blob Storage を使用する Node.js 開発については、以下の追加リソースを参照してください。

### <a name="binaries-and-source-code"></a>バイナリとソース コード

- GitHub で Azure Storage 用の [Node.js クライアント ライブラリ ソース コード](https://github.com/Azure/azure-storage-node)を確認し、インストールします。

### <a name="client-library-reference-and-samples"></a>クライアント ライブラリ リファレンスとサンプル

- Node.js クライアント ライブラリの詳細については、[Node.js API リファレンス](https://docs.microsoft.com/javascript/api/overview/azure/storage)を参照してください。
- Node.js クライアント ライブラリを使用して記述された [Blob Storage のサンプル](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob)を確認します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Node.js を使ってローカル ディスクと Azure Blob Storage の間でファイルをアップロードする方法について説明しました。 Blob Storage の操作の詳細を学習するには、Blob Storage の操作方法に進みます。

> [!div class="nextstepaction"]
> [Blob Storage の操作方法](storage-nodejs-how-to-use-blob-storage.md)

Azure Storage に関する Node.js のリファレンスについては、「[azure-storage package (azure-storage パッケージ)](https://docs.microsoft.com/javascript/api/azure-storage)」を参照してください。
