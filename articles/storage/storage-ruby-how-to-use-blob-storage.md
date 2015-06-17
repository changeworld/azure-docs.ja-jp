<properties 
	pageTitle="Ruby から BLOB ストレージを使用する方法 | Microsoft Azure" 
	description="Azure BLOB サービスを使用して、BLOB の内容をアップロード、ダウンロード、一覧表示、および削除する方法について説明します。コード サンプルは Ruby で記述されています。" 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Ruby から BLOB ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概要

このガイドでは、Azure BLOB サービスを使用して
一般的なシナリオを実行する方法について説明します。サンプルは Ruby API を使用して記述されています。
紹介するシナリオは、BLOB の**アップロード、一覧の取得、ダウンロード**、および**削除**です。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Ruby アプリケーションの作成

Ruby アプリケーションを作成します。手順については、 
[Azure での Ruby アプリケーションの作成]に関するページを参照してください。/develop/ruby/tutorials/web-app-with-linux-vm/).

## アプリケーションからストレージへのアクセスの構成

Azure ストレージを使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### RubyGems を使用してパッケージを取得する

1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。

2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

### パッケージをインポートする

任意のテキスト エディターを使用して、ストレージを使用するアプリケーションの Ruby ファイルの先頭に次のコードを追加します。

	require "azure"

## Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 **AZURE_STORAGE_ACCOUNT** および **AZURE_STORAGE_ACCESS_KEY** 
を読み取ります。これらの環境変数が設定されていない場合は、**Azure::BlobService** を使用する前に、次のコードを使用してアカウント情報を指定する必要があります。

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


これらの値を取得するには、次の手順を実行します。

1. [Azure 管理ポータル](https://manage.windowsazure.com/)にログインします。
2. 使用するストレージ アカウントを表示します。
3. ナビゲーション ウィンドウの下部にある **[キーの管理]** をクリックします。
4. ポップアップ ダイアログに、ストレージ アカウント名、プライマリ アクセス キー、およびセカンダリ アクセス キーが表示されます。アクセス キーには、プライマリとセカンダリのどちらでも使用できます。

## 方法:コンテナーを作成する

**Azure::BlobService** オブジェクトを使用して、コンテナーと BLOB を操作できます。コンテナーを作成するには、**create_container()** メソッドを使用します。

次の例では、コンテナーを作成し、既に存在している場合はエラーを出力します。

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

コンテナー内のファイルを公開する場合は、コンテナーのアクセス許可を設定できます。 

<strong>create_container()</strong> の呼び出しを変更して、次のように **:public_access_level** オプションを渡すだけです。

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


**:public_access_level** オプションに指定できる値は次のとおりです。

* **BLOB:** コンテナーと BLOB データに完全パブリック読み取りアクセスを指定します。クライアントは匿名の要求でコンテナー内に BLOB を列挙できますが、ストレージ アカウント内にコンテナーを列挙することはできません。

* **コンテナー:** BLOB にパブリック読み取りアクセスを指定します。このコンテナー内の BLOB データは匿名の要求で読み取ることができますが、コンテナー データは使用できません。クライアントは匿名の要求でコンテナー内に BLOB を列挙できません。

別の方法として、**set_container_acl()** メソッドを使用してパブリック アクセス レベルを指定することでも、コンテナーのパブリック アクセス レベルを変更できます。
 
次の例では、パブリック アクセス レベルを **container** に変更します。

	azure_blob_service.set_container_acl('test-container', "container")

## 方法:コンテナーに BLOB をアップロードする

BLOB にコンテンツをアップロードするには、**create_block_blob()** メソッドを使用して BLOB を作成し、ファイルまたは文字列を BLOB のコンテンツとして使用します。 

次のコードでは、ファイル **test.png** を、コンテナー内の新しい BLOB として "image-blob" という名前でアップロードします。

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## 方法:コンテナー内の BLOB を一覧表示する

コンテナーの一覧を取得するには、**list_containers()** メソッドを使用します。 
コンテナー内の BLOB の一覧を取得するには、**list_blobs()** メソッドを使用します。 

次のコードでは、アカウントのすべてのコンテナーからすべての BLOB の URL を出力します。

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## 方法:BLOB をダウンロードする

BLOB をダウンロードするには、**get_blob()** メソッドを使用してコンテンツを取得します。 

次の例は、**get_blob()** を使用して "image-blob" のコンテンツをダウンロードし、ローカル ファイルに書き込む方法を示しています。

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## 方法:BLOB を削除する
最後に、BLOB を削除するには、**delete_blob()** メソッドを使用します。次の例は、BLOB の削除方法を示しています。

	azure_blob_service.delete_blob(container.name, "image-blob")

## 次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先を参照してください。

- MSDN リファレンス:[Azure ストレージ](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [Azure のストレージ チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)
- GitHub の [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリ

<!--HONumber=49--> 