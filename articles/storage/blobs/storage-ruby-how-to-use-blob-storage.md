---
title: "Ruby から Blob Storage (オブジェクト ストレージ) を使用する方法 | Microsoft Docs"
description: "Azure BLOB ストレージ (オブジェクト ストレージ) を使用して、非構造化データをクラウドに格納します。"
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: e2fe4c45-27b0-4d15-b3fb-e7eb574db717
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 01/18/2018
ms.author: tamram
ms.openlocfilehash: c4c6d47511acdae7afaf4a535c24c6fcc7e389b1
ms.sourcegitcommit: 09a2485ce249c3ec8204615ab759e3b58c81d8cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="how-to-use-blob-storage-from-ruby"></a>Ruby から BLOB ストレージを使用する方法
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概要
Azure Blob Storage は、非構造化データをクラウド内にオブジェクト/BLOB として格納するサービスです。 Blob Storage は、ドキュメント、メディア ファイル、アプリケーション インストーラーなど、任意の種類のテキスト データやバイナリ データを格納できます。 Blob Storage は、オブジェクト ストレージとも呼ばれます。

このガイドでは、BLOB ストレージを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Ruby API を使用して記述されています。 紹介するシナリオは、BLOB の**アップロード、一覧の取得、ダウンロード**、**削除**です。

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby アプリケーションの作成
Ruby アプリケーションを作成します。 手順については、「[App Service on Linux での Ruby アプリの作成](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby)」を参照してください。


## <a name="configure-your-application-to-access-storage"></a>アプリケーションのストレージへのアクセスの構成
Azure Storage を使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems を使用してパッケージを取得する
1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。
2. コマンド ウィンドウに「gem install azure-storage-blob」と入力して、gem と依存関係をインストールします。

### <a name="import-the-package"></a>パッケージをインポートする
任意のテキスト エディターを使用して、ストレージを使用するアプリケーションの Ruby ファイルの先頭に次のコードを追加します。

```ruby
require "azure/storage/blob"
```

## <a name="set-up-an-azure-storage-connection"></a>Azure のストレージ接続文字列の設定
azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 **AZURE\_STORAGE\_ACCOUNT** と **AZURE\_STORAGE\_ACCESS_KEY** を読み取ります。 これらの環境変数が設定されていない場合は、**Azure::Blob::BlobService::create** を使って次のコードでアカウント情報を指定する必要があります。

```ruby
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )
```

Azure ポータルでクラシックまたは Resource Manager ストレージ アカウントからこれらの値を取得するには:

1. [Azure Portal](https://portal.azure.com) にログインします。
2. 使用するストレージ アカウントを表示します。
3. 右側の [設定] ブレードで、 **[アクセス キー]**をクリックします。
4. 表示される [アクセス キー] ブレードに、アクセス キー 1 とアクセス キー 2 が表示されます。 このいずれかを使用できます。
5. コピー アイコンをクリックしてキーをクリップボードにコピーします。

## <a name="create-a-container"></a>コンテナーを作成する
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

**Azure::Storage::Blob::BlobService** オブジェクトを使って、コンテナーと BLOB を操作できます。 コンテナーを作成するには、**create\_container()** メソッドを使用します。

次のコード例では、コンテナーを作成し、既に存在している場合はエラーを出力します。

```ruby
azure_blob_service = Azure::Storage::Blob::BlobService.create_from_env
begin
    container = azure_blob_service.create_container("test-container")
rescue
    puts $!
end
```

コンテナー内のファイルを公開する場合は、コンテナーのアクセス許可を設定できます。

<strong>create\_container()</strong> の呼び出しを変更して、次のように **:public\_access\_level** オプションを渡すだけです。

```ruby
container = azure_blob_service.create_container("test-container",
    :public_access_level => "<public access level>")
```

**:public\_access\_level** オプションに指定できる値は次のとおりです。

* **blob:** BLOB に対するパブリック読み取りアクセスを指定します。 該当するコンテナー内の BLOB データは匿名要求で読み取り可能ですが、コンテナー データは参照できません。 クライアントはコンテナー内の BLOB を匿名要求で列挙することはできません。
* **container:** コンテナーと BLOB のデータに対する完全パブリック読み取りアクセスを指定します。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

別の方法として、**set\_container\_acl()** メソッドを使用してパブリック アクセス レベルを指定することでも、コンテナーのパブリック アクセス レベルを変更できます。

次のコード例では、パブリック アクセス レベルを **container**に変更します。

```ruby
azure_blob_service.set_container_acl('test-container', "container")
```

## <a name="upload-a-blob-into-a-container"></a>コンテナーに BLOB をアップロードする
BLOB にコンテンツをアップロードするには、**create\_block\_blob()** メソッドを使用して BLOB を作成し、ファイルまたは文字列を BLOB のコンテンツとして使用します。

次のコードでは、ファイル **test.png** を、コンテナー内の新しい BLOB として "image-blob" という名前でアップロードします。

```ruby
content = File.open("test.png", "rb") { |file| file.read }
blob = azure_blob_service.create_block_blob(container.name,
    "image-blob", content)
puts blob.name
```

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する
コンテナーの一覧を取得するには、**list_containers()** メソッドを使用します。
コンテナー内の BLOB の一覧を取得するには、**list\_blobs()** メソッドを使用します。 コンテナー内のすべての BLOB を一覧表示するには、サービスによって返される継続トークンに従い、このトークンでの list_blobs の実行を継続する必要があります。 詳しくは、「[List Blobs REST API](https://docs.microsoft.com/rest/api/storageservices/list-blobs)」をご覧ください。

次のコードは、コンテナー内のすべての BLOB を出力します。

```ruby
nextMarker = nil
loop do
    blobs = azure_blob_service.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

## <a name="download-blobs"></a>BLOB をダウンロードする
BLOB をダウンロードするには、**get\_blob()** メソッドを使用してコンテンツを取得します。

次のコード例は、**get\_blob()** を使用して "image-blob" のコンテンツをダウンロードし、ローカル ファイルに書き込む方法を示しています。

```ruby
blob, content = azure_blob_service.get_blob(container.name,"image-blob")
File.open("download.png","wb") {|f| f.write(content)}
```

## <a name="delete-a-blob"></a>BLOB を削除する
最後に、BLOB を削除するには、**delete\_blob()** メソッドを使用します。 次のコード例は、BLOB の削除方法を示しています。

```ruby
azure_blob_service.delete_blob(container.name, "image-blob")
```

## <a name="next-steps"></a>次の手順
さらに複雑なストレージ タスクの詳細については、次のリンク先をご覧ください。

* [Azure のストレージ チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)
* GitHub の [Azure Storage SDK for Ruby](https://github.com/azure/azure-storage-ruby) リポジトリ
* [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

