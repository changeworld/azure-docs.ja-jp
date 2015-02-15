<properties 
	pageTitle="BLOB ストレージを使用する方法 (Python) | Microsoft Azure" 
	description="Azure BLOB サービスを使用して、BLOB をアップロード、列挙、ダウンロード、削除する方法について説明します。" 
	services="storage" 
	documentationCenter="python" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="robmcm"/>

# Python から BLOB ストレージ サービスを使用する方法
このガイドでは、Azure BLOB ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは Python API を使用して記述されています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。BLOB の詳細については、「[次のステップ][]」のセクションを参照してください。

## 目次

[Blob ストレージとは][]
 [概念][]   
 [Azure のストレージ アカウントの作成][]   
 [方法:コンテナーを作成する][]   
 [方法:コンテナーに BLOB をアップロードする][]   
 [方法:コンテナー内の BLOB を一覧表示する][]   
 [方法:BLOB をダウンロードする][]   
 [方法:BLOB を削除する][]   
 [次のステップ][]

[AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a> Azure のストレージ アカウントの作成

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>方法:コンテナーを作成する

**注:** Python またはクライアント ライブラリをインストールする必要がある場合は、「[Python Installation Guide (Python インストール ガイド)](../python-how-to-install/)」を参照してください。


**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作できます。次のコードでは、**BlobService** オブジェクトを作成します。プログラムを使用して Azure ストレージにアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage import BlobService

次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、**BlobService** オブジェクトを作成します。 'myaccount' と  'mykey' の部分は、実際のアカウントとキーに置き換えてください。

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

ストレージの BLOB はすべてコンテナー内に格納されます。コンテナーが存在しない場合は、**BlobService** オブジェクトを使用してコンテナーを作成できます。

	blob_service.create_container('mycontainer')

既定では、新しいコンテナーはプライベートであるため、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります (前と同じ方法で)。コンテナー内のファイルをすべてのユーザーが利用できるようにする場合は、次のコードを使用して、コンテナーを作成しパブリック アクセス レベルを渡します。

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

または、次のコードを使用してコンテナーを作成した後で、コンテナーを変更することもできます。

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

この変更後、パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが表示できますが、変更または削除できるのは、このコンテナーの作成と変更を行ったユーザーだけです。

## <a name="upload-blob"> </a>方法:コンテナーに BLOB をアップロードする

データを BLOB にアップロードするには、**put\_block\_blob\_from\_path**、**put\_block\_blob\_from\_file**、**put\_block\_blob\_from\_bytes**、または **put\_block\_blob\_from\_text** メソッドを使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

**put\_block\_blob\_from\_path** は指定したパスからファイルの内容をアップロードし、**put\_block\_blob\_from\_file** は既に開いているファイルまたはストリームから内容をアップロードします。**put\_block\_blob\_from\_bytes** はバイト配列をアップロードし、**put\_block\_blob\_from\_text** は指定したエンコーディング (既定値から UTF-8) を使用して、指定したテキスト値をアップロードします。

次の例では、**task1.txt** ファイルの内容を **myblob** BLOB にアップロードします。

	blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>方法:コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、**list\_blobs** メソッドを **for** ループで使用して、コンテナー内の各 BLOB の名前を表示します。次のコードでは、コンテナー内の各 BLOB の**名前**と **URL** をコンソールに出力しています。

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a>方法:BLOB をダウンロードする

BLOB からデータをダウンロードするには、**get\_blob\_to\_path**、**get\_blob\_to\_file**、**get\_blob\_to\_bytes**、または **get\_blob\_to\_text** を使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

次の例は、**get\_blob\_to\_path** を使用して **myblob** BLOB の内容をダウンロードし、**out-task1.txt** ファイルに格納する方法を示しています。

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>方法:BLOB を削除する

最後に、BLOB を削除するには、**delete_blob** を呼び出します。

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス:[Azure のデータの格納とアクセス][]
-   [Azure のストレージ チーム ブログ][]

  [次のステップ]: #next-steps
  [BLOB ストレージとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [方法:コンテナーを作成する]: #create-container
  [方法:コンテナーに BLOB をアップロードする]: #upload-blob
  [方法:コンテナー内の BLOB を一覧表示する]: #list-blob
  [方法:BLOB をダウンロードする]: #download-blobs
  [方法:BLOB を削除する]: #delete-blobs
  [方法:サイズが大きい BLOB のダウンロードとアップロード]: #large-blobs
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=42-->
