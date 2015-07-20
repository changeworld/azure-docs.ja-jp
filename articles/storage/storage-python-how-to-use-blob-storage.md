<properties 
	pageTitle="Python から BLOB ストレージを使用する方法 | Microsoft Azure" 
	description="Python から Azure BLOB サービスを使用して、BLOB をアップロード、一覧表示、ダウンロード、削除する方法について説明します。" 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="huvalo"/>

# Python から BLOB ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概要

このガイドでは、Azure BLOB ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは Python で記述され、[Python Azure パッケージ][]を使用しています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 方法: コンテナーを作成する

> [AZURE.NOTE]Python または [Python Azure パッケージ][]をインストールする場合は、[Python インストール ガイド](../python-how-to-install.md)を参照してください。

**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作できます。次のコードでは、**BlobService** オブジェクトを作成します。プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage import BlobService

次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、**BlobService** オブジェクトを作成します。'myaccount' と 'mykey' の部分は、実際のアカウントとキーに置き換えてください。

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

コンテナーが存在しない場合は、**BlobService** オブジェクトを使用してコンテナーを作成できます。

	blob_service.create_container('mycontainer')

既定では、新しいコンテナーはプライベートであるため、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります (前と同じ方法で)。コンテナー内のファイルをすべてのユーザーが利用できるようにする場合は、次のコードを使用して、コンテナーを作成しパブリック アクセス レベルを渡します。

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

または、次のコードを使用してコンテナーを作成した後で、コンテナーを変更することもできます。

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

この変更後、パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが表示できますが、変更または削除できるのは、このコンテナーの作成と変更を行ったユーザーだけです。

## 方法: コンテナーに BLOB をアップロードする

データを BLOB にアップロードするには、**put_block_blob_from_path**、**put_block_blob_from_file**、**put_block_blob_from_bytes**、または **put_block_blob_from_text** メソッドを使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

**put_block_blob_from_path** は、指定のパスからファイルの内容をアップロードし、**put_block_blob_from_file** は既に開いているファイルやストリームから内容をアップロードします。**put_block_blob_from_bytes** は、バイトの配列をアップロードし、**put_block_blob_from_text** は、指定のエンコード (既定では UTF-8) を使用して指定のテキスト値をアップロードします。

次の例では、**sunset.png** ファイルの内容を **myblob** BLOB にアップロードします。

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## 方法: コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、**list_blobs** メソッドを **for** ループで使用して、コンテナー内の各 BLOB の名前を表示します。次のコードでは、コンテナー内の各 BLOB の**名前**と **URL** をコンソールに出力しています。

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## 方法: BLOB をダウンロードする

BLOB からデータをダウンロードするには、**get_blob_to_path**、**get_blob_to_file**、**get_blob_to_bytes**、または **get_blob_to_text** を使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

次の例は、**get_blob_to_path** を使用して **myblob** BLOB の内容をダウンロードし、**out-sunset.png** ファイルに格納する方法を示しています。

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## 方法: BLOB を削除する

最後に、BLOB を削除するには、**delete_blob** を呼び出します。

	blob_service.delete_blob('mycontainer', 'myblob') 

## 次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス][]
-   [Azure Storage チームのブログ][]

[Azure のデータの格納とアクセス]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure Storage チームのブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure パッケージ]: https://pypi.python.org/pypi/azure
 

<!---HONumber=July15_HO2-->