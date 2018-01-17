---
title: "Azure クイックスタート - Ruby を使用して Azure Blob Storage との間でオブジェクトを転送する | Microsoft Docs"
description: "Ruby を使って Azure Blob Storage との間で双方向にオブジェクトを転送する方法を説明します"
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 12/7/2017
ms.author: v-ruogun
ms.openlocfilehash: 3b0bc01047b9aa7459cf6cc33f004cf7506e5826
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-ruby"></a>Ruby を使用して Azure Blob Storage との間でオブジェクトを転送する
このクイックスタートでは、Ruby を使って、Azure Blob Storage 内のコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。 

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。 
* [Ruby](https://www.ruby-lang.org/en/downloads/) のインストール
* rubygem パッケージを使用して、[Ruby 向けの Azure Storage ライブラリ](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage#configure-your-application-to-access-storage)をインストールします。 

```
gem install azure-storage
```

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード
このクイックスタートで使う[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git)は、基本的な Ruby アプリケーションです。  

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 Ruby のサンプル アプリケーションを開くには、storage-blobs-ruby-quickstart フォルダーを検索し、example.rb ファイルを開きます。  

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
アプリケーションでは、ストレージ アカウント名とアカウント キーを指定して、お使いのアプリケーションの `Client` インスタンスを作成する必要があります。 お使いの IDE のソリューション エクスプローラーから `example.rb` ファイルを開きます。 **accountname** および **accountkey** の値を、ご自分のアカウント名とキーに置き換えます。 

```ruby 
client = Azure::Storage.client(storage_account_name: account_name, storage_access_key: account_key)
```

## <a name="run-the-sample"></a>サンプルを実行する
このサンプルでは、"ドキュメント" フォルダーにテスト ファイルを作成します。 サンプル プログラムは、Blob Storage にテスト ファイルをアップロードし、コンテナー内の BLOB を一覧表示し、新しい名前を付けてファイルをダウンロードします。 

サンプルを実行します。 次の出力は、アプリケーションを実行するときに返される出力の例です。
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
任意のキーを押して続行すると、サンプル プログラムによってストレージ コンテナーとファイルが削除されます。 続行する前に、"ドキュメント" フォルダーで 2 つのファイルをチェックします。 それらを開いて、同じであるかどうか確認します。

[Azure Storage Explorer](http://storageexplorer.com) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 

ファイルを確認した後、任意のキーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、example.rb ファイルを開いてコードを確認します。 

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを実行して、そのしくみを理解できるようにします。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する
最初に、Blob Storage にアクセスして管理するために使うオブジェクトへの参照を作成します。 これらのオブジェクトはお互いを基にして作成され、各オブジェクトは、一覧で次にあるオブジェクトによって使われます。

* Azure Storage の **Client** オブジェクトのインスタンスを作成して、接続資格情報を設定します。 
* お使いのストレージ アカウント内の BLOB サービスを参照する **BlobService** オブジェクトを作成します。 
* アクセスしているコンテナーを表す **Container** オブジェクトを作成します。 コンテナーは、コンピューターでフォルダーを使ってファイルを整理するのと同じように、BLOB を整理するために使われます。

Cloud BLOB コンテナーを作成した後は、関心がある特定の BLOB を参照する **Block** BLOB オブジェクトを作成して、アップロード、ダウンロード、コピーなどの操作を実行できます。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[コンテナー、BLOB、メタデータの名前付けと参照](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

このセクションでは、Azure Storage クライアントのインスタンスを設定して、BLOB サービス オブジェクトをインスタンス化し、新しいコンテナーを作成した後、BLOB がパブリックになるようにコンテナーに対するアクセス許可を設定します。 コンテナーの名前は **quickstartblobs** です。 

```ruby 
# Setup a specific instance of an Azure::Storage::Client
client = Azure::Storage.client(storage_account_name: account_name, storage_access_key: account_key)

# Create the BlobService that represents the Blob service for the storage account
blob_service = client.blob_client

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
container = blob_service.create_container(container_name)   

# Set the permission so the blobs are public.
blob_service.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。  

ファイルを BLOB にアップロードするには、ローカル ドライブ上でディレクトリ名とファイル名を結合することにより、ファイルの完全なパスを取得します。 **create\_block\_blob()** メソッドを使用して、指定したパスにファイルをアップロードできます。 

サンプル コードは、アップロードとダウンロードに使うローカル ファイルを作成し、アップロードするファイルを **file\_path\_to\_file** として、BLOB の名前を **local\_file\_name** として格納します。 次の例では、ファイルを **quickstartblobs** という名前のコンテナーにアップロードします。

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_service.create_block_blob(container.name, local_file_name, full_path_to_file)
```

ブロック BLOB のコンテンツの部分更新を実行するには、**create\_block\_list()** メソッドを使用します。 ブロック BLOB の最大サイズは 4.7 TB であり、Excel スプレッドシートから大きなビデオ ファイルまで何にでも使うことができます。 ページ BLOB は、主に、IaaS VM のバックアップ用の VHD ファイルに使われます。 追加 BLOB は、ファイルに書き込んでから詳細情報を追加し続ける場合などの、ログ記録に使用されます。 追加 BLOB は、単一のライター モデルで使用する必要があります。 BLOB ストレージに格納されているほとんどのオブジェクトはブロック BLOB です。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

**list\_blobs()** メソッドを使用して、コンテナー内のファイルの一覧を取得できます。 次のコードは、BLOB の一覧を取得し、ループ処理して、コンテナー内に見つかった BLOB の名前を表示します。  

```ruby
# List the blobs in the container
puts "\n List blobs in the container"
blobs = blob_service.list_blobs(container_name)
blobs.each do |blob|
    puts "\t Blob name #{blob.name}"   
end  
```

### <a name="download-the-blobs"></a>BLOB のダウンロード

**get\_blob()** メソッドを使用して、ローカル ディスクに BLOB をダウンロードします。 次のコードは、前のセクションでアップロードされた BLOB をダウンロードします。 "_DOWNLOADED" は、ローカル ディスク上で両方のファイルを確認できるように、BLOB の名前にサフィックスとして追加されます。 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_service.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイックスタートでアップロードした BLOB が不要になった場合は、**delete\_container()** メソッドを使ってコンテナー全体を削除できます。 作成されたファイルが不要になった場合は、**delete\_blob()** メソッドを使ってファイルを削除します。

```ruby
# Clean up resources. This includes the container and the temp files
blob_service.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```

## <a name="next-steps"></a>次の手順
 
このクイックスタートでは、Ruby を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Blob Storage の操作の詳細を学習するには、Blob Storage の操作方法に進みます。

> [!div class="nextstepaction"]
> [Blob Storage の操作方法](./storage-ruby-how-to-use-blob-storage.md)


Storage Explorer と BLOB について詳しくは、「[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。
