---
title: Microsoft Azure Data Box BLOB ストレージの要件 | Microsoft Docs
description: Azure Data Box BLOB ストレージ向けにサポートされている API、SDK、クライアント ライブラリのバージョンについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436496"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box BLOB ストレージの要件

この記事では、Data Box BLOB ストレージでサポートされている Azure の API、Azure クライアント ライブラリ、およびツールのバージョンの一覧を示します。 Data Box BLOB ストレージでは、Azure と整合性のあるセマンティクスによって BLOB 管理機能が提供されます。 この記事では、Azure Storage サービスと Azure Data Box BLOB ストレージとの間で確認されている相違点もまとめています。

Data Box BLOB ストレージに接続する前にこの情報をよく確認し、必要に応じて再度参照することをお勧めします。


## <a name="storage-differences"></a>ストレージの相違点

|     機能                                             |     Azure Storage                                     |     Data Box BLOB ストレージ |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File ストレージ                                   |    クラウド ベースの SMB ファイル共有のサポート              |    サポートされていません      |
|    サービスによる保存データの暗号化                  |    256 ビット AES 暗号化                             |    256 ビット AES 暗号化 |
|    ストレージ アカウントの種類                                 |    汎用アカウントと Azure Blob Storage アカウント    |    汎用 v1 のみ|
|    BLOB 名                                            |    1,024 文字 (2,048 バイト)                     |    880 文字 (1,760 バイト)|
|    ブロック BLOB の最大サイズ                              |    4.75 TB (100 MB X 50,000 ブロック)                   |    Azure Data Box v 1.8 以降の場合に 4.75 TB (100 MB x 50,000 ブロック)。|
|    ページ BLOB の最大サイズ                               |    8 TB                                               |    1 TB (テラバイト)                   |
|    ページ BLOB のページ サイズ                                  |    512 バイト                                          |    4 KB                   |

## <a name="supported-api-versions"></a>サポートされる API バージョン

Azure Storage サービス API シリーズの次のバージョンが Data Box BLOB ストレージでサポートされています。

Azure Data Box 1.8 以降

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>サポートされている Azure クライアント ライブラリ

Data Box BLOB ストレージでは、クライアント ライブラリとエンドポイント サフィックスに特定の要件があります。 Data Box BLOB ストレージのエンドポイントは、最新バージョンの Azure Blob Storage REST API と完全に対応しているわけではありません。[Azure Data Box 1.8 以降でサポートされているバージョン](#supported-api-versions)に関するセクションをご覧ください。 したがって、ストレージのクライアント ライブラリについて、REST API と互換性のあるバージョンを把握しておく必要があります。

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 以降

| クライアント ライブラリ     |Data Box BLOB ストレージでサポートされているバージョン     | Link   |     エンドポイントの指定      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    NuGet パッケージ: https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub リリース: https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config ファイル                 |
|    Java                |    7.0.0                                           |    Maven パッケージ: https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub リリース: https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    接続文字列の設定         |
|    Node.js             |    2.8.3                                           |    NPM リンク: https://www.npmjs.com/package/azure-storage   (`npm install azure-storage@2.7.0` を実行)   <br>GitHub リリース: https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    サービス インスタンスの宣言    |
|    C++                 |    5.2.0                                           |    NuGet パッケージ: https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub リリース: https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    接続文字列の設定         |
|    PHP                 |    1.2.0                                           |    GitHub リリース:<br>共通: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Composer 経由でインストールする (詳細については下記参照)                                                                                                             |    接続文字列の設定         |
|    Python              |    1.1.0                                           |    GitHub リリース:<br>共通: https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>BLOB: https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    サービス インスタンスの宣言    |
|    Ruby                |    1.0.1                                           |    RubyGems パッケージ:<br>共通: https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub リリース:<br>共通: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    接続文字列の設定         |



### <a name="install-php-client-via-composer---current"></a>Composer 経由で PHP クライアントをインストールする (現在)

Composer 経由でインストールするには (BLOB の例):
1. 次のコードを含む composer.json という名前のファイルをプロジェクトのルートに作成します。

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. このプロジェクトのルートに `composer.phar` をダウンロードします。

3. 実行: php composer.phar install。

### <a name="endpoint-declaration"></a>エンドポイントの宣言

Azure Data Box BLOB ストレージのエンドポイントには、リージョン名と Data Box ドメインの 2 つの部分があります。 Data Box BLOB ストレージ SDK の既定のエンドポイントは `\<serial no. of the device>.microsoftdatabox.com` です。  BLOB サービス エンドポイントの詳細については、[Data Box BLOB ストレージによる接続](data-box-deploy-copy-data-via-rest.md)に関するページを参照してください。
 
## <a name="examples"></a>例

### <a name="net"></a>.NET

Data Box BLOB ストレージの場合、エンドポイント サフィックスが `app.config` ファイルで指定されています。

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Data Box BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Data Box BLOB ストレージの場合、エンドポイント サフィックスが宣言のインスタンスで指定されています。

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Data Box BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Data Box BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Data Box BLOB ストレージの場合、エンドポイント サフィックスが宣言のインスタンスで指定されています。

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Data Box BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>次のステップ

* [Azure Data Box をデプロイする](data-box-deploy-ordered.md)
