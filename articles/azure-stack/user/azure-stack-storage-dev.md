---
title: Azure Stack ストレージの開発ツールの概要 | Microsoft Docs
description: Azure Stack ストレージの開発ツールの使用方法の概要
services: azure-stack
author: mabriggs
ms.author: mabrigg
ms.date: 07/03/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 40f256b7a2be5a5a1d642983fa6ce018ee602ac2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441714"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Azure Stack ストレージの開発ツールの概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Microsoft Azure Stack には、Blob Storage、Table Storage、Queue Storage など一連のストレージ サービスがあります。

この記事は、Azure Stack ストレージの開発ツールを初めて使うときのガイドとしてご利用ください。 より詳細な情報とサンプル コードについては、該当する Azure ストレージのチュートリアルでご覧いただけます。

> [!NOTE]  
> Azure Stack ストレージと Azure ストレージには、プラットフォームごとの固有の要件など、既知の違いがあります。 たとえば、Azure Stack には、クライアント ライブラリとエンドポイント サフィックスにある特定の要件があります。 詳しくは、「[Azure Stack ストレージ: 違いと考慮事項](azure-stack-acs-differences.md)」をご覧ください。

## <a name="azure-client-libraries"></a>Azure のクライアント ライブラリ

Azure Stack ストレージでサポートされる REST API のバージョンは、1802 update 以降のバージョンでは 2017-04-17、2016-05-31、2015-12-11、2015-07-08、2015-04-05、それより前のバージョンでは 2015-04-05 となります。 Azure Stack のエンドポイントは、最新バージョンの Azure ストレージ REST API と完全に対応しているわけではありません。 したがって、ストレージのクライアント ライブラリについて、REST API と互換性のあるバージョンを把握しておく必要があります。

### <a name="1802-update-or-newer-versions"></a>1802 update 以降のバージョン

| クライアント ライブラリ | Azure Stack でサポートされるバージョン | リンク | エンドポイントの指定 |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | NuGet パッケージ:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>GitHub リリース:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | app.config ファイル |
| Java | 6.1.0 | Maven パッケージ:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>GitHub リリース:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | 接続文字列の設定 |
| Node.js | 2.7.0 | NPM リンク:<br>https://www.npmjs.com/package/azure-storage<br>(次のコマンドを実行: `npm install azure-storage@2.7.0`)<br> <br>GitHub リリース:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | サービス インスタンスの宣言 |
| C++ | 3.1.0 | NuGet パッケージ:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>GitHub リリース:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | 接続文字列の設定 |
| PHP | 1.0.0 | GitHub リリース:<br>共通: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>キュー:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>テーブル: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Composer 経由でインストールする ([詳細については以下を参照](#install-php-client-via-composer---current)) | 接続文字列の設定 |
| Python | 1.0.0 | GitHub リリース:<br>共通:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>BLOB:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>キュー:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | サービス インスタンスの宣言 |
| Ruby | 1.0.1 | RubyGems パッケージ:<br>共通:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>キュー: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>テーブル: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>GitHub リリース:<br>共通: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>キュー: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>テーブル: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | 接続文字列の設定 |

#### <a name="install-php-client-via-composer---current"></a>Composer 経由で PHP クライアントをインストールする (現在)

Composer 経由でインストールするには (BLOB の例):

1. プロジェクトのルートに **composer.json** という名前のファイルを次のコードを使用して作成します。

  ```php
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. [composer.phar](http://getcomposer.org/composer.phar) をプロジェクトのルートにダウンロードします。
3. `php composer.phar install` を実行します。

### <a name="previous-versions"></a>以前のバージョン

|クライアント ライブラリ|Azure Stack でサポートされるバージョン|リンク|エンドポイントの指定|
|---------|---------|---------|---------|
|.NET     |6.2.0|NuGet パッケージ:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>GitHub リリース:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|app.config ファイル|
|Java|4.1.0|Maven パッケージ:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>GitHub リリース:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|接続文字列の設定|
|Node.js     |1.1.0|NPM リンク:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(実行: `npm install azure-storage@1.1.0)`<br><br>GitHub リリース:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|サービス インスタンスの宣言||C++|2.4.0|NuGet パッケージ:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub リリース:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|接続文字列の設定|
|C++|2.4.0|NuGet パッケージ:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub リリース:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|接続文字列の設定|
|PHP|0.15.0|GitHub リリース:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Composer 経由でインストールする (下記参照)|接続文字列の設定|
|Python     |0.30.0|PIP パッケージ:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(実行: `pip install -v azure-storage==0.30.0)`<br><br>GitHub リリース:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|サービス インスタンスの宣言|
|Ruby|0.12.1<br>プレビュー|RubyGems パッケージ:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>GitHub リリース:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|接続文字列の設定|

#### <a name="install-php-client-via-composer---previous"></a>Composer 経由で PHP クライアントをインストールする (以前)

Composer 経由でインストールするには:

1. プロジェクトのルートに **composer.json** という名前のファイルを次のコードを使用して作成します。

  ```php
    {
          "require":{
          "Microsoft/azure-storage":"0.15.0"
          }
    }
  ```

2. [composer.phar](http://getcomposer.org/composer.phar) をプロジェクトのルートにダウンロードします。
3. `php composer.phar install` を実行します。

## <a name="endpoint-declaration"></a>エンドポイントの宣言

Azure Stack のエンドポイントには、リージョン名と Azure Stack ドメインの 2 つの部分があります。
Azure Stack Development Kit の既定のエンドポイントは **local.azurestack.external** です。
エンドポイントが不明の場合は、クラウド管理者に問い合わせてください。

## <a name="examples"></a>例

### <a name="net"></a>.NET

Azure Stack のエンドポイントのサフィックスは、app.config ファイルに指定されています。

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Azure Stack のエンドポイントのサフィックスは、接続文字列のセットアップに指定されています。

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Azure Stack のエンドポイントのサフィックスは、宣言のインスタンスに指定されています。

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Azure Stack のエンドポイントのサフィックスは、接続文字列のセットアップに指定されています。

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Azure Stack のエンドポイントのサフィックスは、接続文字列のセットアップに指定されています。

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Azure Stack のエンドポイントのサフィックスは、宣言のインスタンスに指定されています。

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Azure Stack のエンドポイントのサフィックスは、接続文字列のセットアップに指定されています。

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>BLOB ストレージ

Azure Stack に、次の Azure Blob ストレージのチュートリアルを応用できます。 Azure Stack のエンドポイントのサフィックスの具体的な要件については、前の[例](#examples)のセクションを参照してください。

* [.NET を使用して Azure Blob Storage を使用する](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Java から BLOB ストレージを使用する方法](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Node.js から BLOB ストレージを使用する方法](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [C++ から BLOB ストレージを使用する方法](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [PHP から BLOB ストレージを使用する方法](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Python から Azure BLOB ストレージを使用する方法](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Ruby から BLOB ストレージを使用する方法](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Azure Stack に、次の Azure キュー ストレージのチュートリアルを応用できます Azure Stack のエンドポイントのサフィックスの具体的な要件については、前の[例](#examples)のセクションを参照してください。

* [.NET を使用して Azure Queue Storage を使用する](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Java から Queue ストレージを使用する方法](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Node.js から Queue ストレージを使用する方法](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [C++ から Queue ストレージを使用する方法](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [PHP から Queue ストレージを使用する方法](../../storage/queues/storage-php-how-to-use-queues.md)
* [Python から Queue ストレージを使用する方法](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Ruby から Queue ストレージを使用する方法](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>テーブル ストレージ

Azure Stack に、次の Azure Table Storage のチュートリアルを応用できます Azure Stack のエンドポイントのサフィックスの具体的な要件については、前の[例](#examples)のセクションを参照してください。

* [.NET を使用して Azure Table Storage を使用する](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Java からテーブル ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-java.md)
* [Node.js から Azure Table Storage を使用する方法](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [C++ から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [PHP から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-php.md)
* [Python で Table Storage を使用する方法](../../cosmos-db/table-storage-how-to-use-python.md)
* [Ruby から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>次の手順

* [Microsoft Azure ストレージの概要](../../storage/common/storage-introduction.md)