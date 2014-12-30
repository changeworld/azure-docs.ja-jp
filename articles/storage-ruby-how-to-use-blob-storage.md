<properties urlDisplayName="Blob Service" pageTitle="BLOB ストレージを使用する方法 (Ruby) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Ruby" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Blob Service from Ruby" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/21/2014" ms.author="tomfitz" />





#Ruby から BLOB サービスを使用する方法
 このガイドでは、Azure BLOB サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは Ruby API を使用して記述されています。紹介するシナリオは、BLOB の**アップロード、一覧の取得、ダウンロード**、および**削除**です。BLOB の詳細については、「[次のステップ]」(#next-steps) セクションを参照してください。

##目次

* [BLOB サービスとは](#what-is)
* [概念](#concepts)
* [Azure のストレージ アカウントの作成](#CreateAccount)
* [Ruby アプリケーションの作成](#CreateRubyApp)
* [アプリケーションからストレージへのアクセスの構成](#ConfigAccessStorage)
* [Azure のストレージ接続文字列の設定](#SetupStorageConnection)
* [方法: コンテナーを作成する](#CreateContainer)
* [方法: コンテナーに BLOB をアップロードする](#UploadBlob)
* [方法: コンテナー内の BLOB を一覧表示する](#ListBlobs)
* [方法: BLOB をダウンロードする](#DownloadBlobs)
* [方法: BLOB を削除する](#DeleteBlob)
* [次のステップ](#NextSteps)


[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a id="CreateAccount"></a>Azure のストレージ アカウントの作成

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="CreateRubyApp"></a>Ruby アプリケーションを作成する

Ruby アプリケーションを作成します。手順については、 
「[Azure VM での Ruby on Rails Web アプリケーション](/ja-jp/develop/ruby/tutorials/web-app-with-linux-vm/)」を参照してください。

## <a id="ConfigAccessStorage"></a>アプリケーションからストレージへのアクセスの構成

Azure ストレージを使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### RubyGems を使用してパッケージを取得する

1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。

2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

### パッケージをインポートする

任意のテキスト エディターを使用して、ストレージを使用するアプリケーションの Ruby ファイルの先頭に次のコードを追加します。

	require "azure"

## <a id="SetupStorageConnection"></a>Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 **AZURE\_STORAGE\_ACCOUNT** および **AZURE\_STORAGE\_ACCESS_KEY** 
 を読み取ります。これらの環境変数が設定されていない場合は、**Azure::BlobService** を使用する前に、次のコードを使用してアカウント情報を指定する必要があります。

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


これらの値を取得するには、次の手順を実行します。

1. [Azure の管理ポータル]にログインします(https://manage.windowsazure.com/)。
2. 使用するストレージ アカウントを表示します。
3. ナビゲーション ウィンドウの下部にある **[キーの管理]** をクリックします。
4. ポップアップ ダイアログに、ストレージ アカウント名、プライマリ アクセス キー、およびセカンダリ アクセス キーが表示されます。アクセス キーには、プライマリとセカンダリのどちらでも使用できます。

## <a id="CreateContainer"></a>方法:コンテナーを作成する

**Azure::BlobService** オブジェクトを使用して、コンテナーと BLOB を操作できます。コンテナーを作成するには、**create\_container()** メソッドを使用します。

次の例では、コンテナーを作成し、既に存在している場合はエラーを出力します。

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

コンテナー内のファイルを公開する場合は、コンテナーのアクセス許可を設定できます。 

 <strong>create\_container()</strong>  の呼び出しを変更して、次のように **:public\_access\_level** オプションを渡すだけです。

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


**:public\_access\_level** オプションに指定できる値は次のとおりです。

* **blob:**コンテナーと BLOB のデータに対する完全パブリック読み取りアクセスを指定します。クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

* **container:**BLOB に対するパブリック読み取りアクセスを指定します。該当するコンテナー内の BLOB データは匿名要求で読み取り可能ですが、コンテナー データは参照できません。クライアントはコンテナー内の BLOB を匿名要求で列挙することはできません。

別の方法として、**set\_container\_acl()** メソッドを使用してパブリック アクセス レベルを指定することでも、コンテナーのパブリック アクセス レベルを変更できます。
 
次の例では、パブリック アクセス レベルを **container** に変更します。

	azure_blob_service.set_container_acl('test-container', "container")

## <a id="UploadBlob"></a>方法:コンテナーに BLOB をアップロードする

BLOB にコンテンツをアップロードするには、**create\_block\_blob()** メソッドを使用して BLOB を作成し、ファイルまたは文字列を BLOB のコンテンツとして使用します。 

次のコードでは、ファイル **test.png** を、コンテナー内の新しい BLOB として "image-blob" という名前でアップロードします。

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## <a id="ListBlobs"></a>方法:コンテナー内の BLOB を一覧表示する

コンテナーの一覧を取得するには、**list_containers()** メソッドを使用します。 
コンテナー内の BLOB の一覧を取得するには、**list\_blobs()** メソッドを使用します。 

次のコードでは、アカウントのすべてのコンテナーからすべての BLOB の URL を出力します。

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## <a id="DownloadBlobs"></a>方法:BLOB をダウンロードする

BLOB をダウンロードするには、**get\_blob()** メソッドを使用してコンテンツを取得します。 

次の例は、**get\_blob()** を使用して "image-blob" のコンテンツをダウンロードし、ローカル ファイルに書き込む方法を示しています。

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## <a id="DeleteBlob"></a>方法:BLOB を削除する
最後に、BLOB を削除するには、**delete\_blob()** メソッドを使用します。次の例は、BLOB の削除方法を示しています。

	azure_blob_service.delete_blob(container.name, "image-blob")

## <a id="NextSteps"></a>次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス:[Azure のデータの格納とアクセス](http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx)
-   [Azure のストレージ チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)
-   GitHub の [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリ

<!--HONumber=35_1-->
