<properties
	pageTitle="Python から Azure BLOB ストレージを使用する方法 | Microsoft Azure"
	description="Python から Azure BLOB ストレージを使用して、BLOB をアップロード、一覧表示、ダウンロード、削除する方法について説明します。"
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="emgerner"/>

# Python から Azure BLOB ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概要

この記事では、BLOB ストレージを使用して一般的なシナリオを実行する方法について説明します。サンプルは Python で記述され、[Python Azure Storage パッケージ][]を使用しています。紹介するシナリオは、BLOB のアップロード、一覧の取得、ダウンロード、および削除です。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## コンテナーを作成する

> [AZURE.NOTE]Python または [Python Azure パッケージ][]をインストールする場合は、「[Python インストール ガイド](../python-how-to-install.md)」をご覧ください。

**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作できます。次のコードでは、**BlobService** オブジェクトを作成します。プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage.blob import BlobService

次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、**BlobService** オブジェクトを作成します。'myaccount' と 'mykey' の部分は、実際のアカウントとキーに置き換えてください。

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

次のコード サンプルでコンテナーが存在しない場合は、**BlobService** オブジェクトを使用してコンテナーを作成できます。

	blob_service.create_container('mycontainer')

既定では、新しいコンテナーはプライベートであるため、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを (前と同じ方法で) 指定する必要があります。コンテナー内のファイルをすべてのユーザーが利用できるようにする場合は、次のコードを使用して、コンテナーを作成しパブリック アクセス レベルを渡します。

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container')

または、次のコードを使用してコンテナーを作成した後で、コンテナーを変更することもできます。

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

この変更後、パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが表示できますが、変更または削除できるのは、このコンテナーの作成と変更を行ったユーザーだけです。

## コンテナーに BLOB をアップロードする

データを BLOB にアップロードするには、**put\_block\_blob\_from\_path**、**put\_block\_blob\_from\_file**、**put\_block\_blob\_from\_bytes**、または **put\_block\_blob\_from\_text** メソッドを使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

**put\_block\_blob\_from\_path** は、指定のパスからファイルの内容をアップロードし、**put\_block\_blob\_from\_file** は既に開いているファイルやストリームから内容をアップロードします。**put\_block\_blob\_from\_bytes** は、バイトの配列をアップロードし、**put\_block\_blob\_from\_text** は、指定のエンコード (既定では UTF-8) を使用して指定のテキスト値をアップロードします。

次の例では、**sunset.png** ファイルの内容を **myblob** BLOB にアップロードします。

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB の一覧を取得するには、**list\_blobs** メソッドを使用します。**list\_blobs** への各呼び出しは結果のセグメントを返します。すべての結果を取得するには、結果の**next\_marker**を確認し、必要に応じて **list\_blobs** をもう一度呼び出します。次のコードでは、コンテナー内の各 BLOB の**名前 (name)** をコンソールに出力しています。

	blobs = []
	marker = None
	while True:
		batch = blob_service.list_blobs('mycontainer', marker=marker)
		blobs.extend(batch)
		if not batch.next_marker:
			break
		marker = batch.next_marker
	for blob in blobs:
		print(blob.name)

## BLOB をダウンロードする

結果の各セグメントには、最大 5000 の BLOB の変数を含めることができます。特定のセグメントに **next\_marker** が存在する場合、コンテナーには複数の BLOB が存在する可能性があります。

BLOB からデータをダウンロードするには、**get\_blob\_to\_path**、**get\_blob\_to\_file**、**get\_blob\_to\_bytes**、**get\_blob\_to\_text** を使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

次の例は、**get\_blob\_to\_path** を使用して **myblob** BLOB の内容をダウンロードし、**out-sunset.png** ファイルに格納する方法を示しています。

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## BLOB を削除する

最後に、BLOB を削除するには、**delete\_blob** を呼び出します。

	blob_service.delete_blob('mycontainer', 'myblob')

## 次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先をご覧ください。

-   [Azure Storage チームのブログ][]

詳細については、[Python デベロッパー センター](/develop/python/)も参照してください。

[Azure Storage チームのブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure パッケージ]: https://pypi.python.org/pypi/azure
[Python Azure Storage パッケージ]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=AcomDC_1203_2015-->