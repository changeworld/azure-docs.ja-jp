<properties
	pageTitle="Python から Azure BLOB ストレージ (オブジェクト ストレージ) を使用する方法 | Microsoft Azure"
	description="Azure BLOB ストレージ (オブジェクト ストレージ) を使用して、非構造化データをクラウドに格納します。"
	services="storage"
	documentationCenter="python"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
    ms.date="09/20/2016"
	ms.author="jwillis;tamram"/>

# Python から Azure BLOB ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Overview

Azure Blob Storage は、非構造化データをクラウド内にオブジェクト/BLOB として格納するサービスです。Blob Storage は、ドキュメント、メディア ファイル、アプリケーション インストーラーなど、任意の種類のテキスト データやバイナリ データを格納できます。Blob Storage は、オブジェクト ストレージとも呼ばれます。

この記事では、BLOB ストレージを使用して一般的なシナリオを実行する方法について説明します。サンプルは Python で作成され、[Microsoft Azure Storage SDK for Python] を使用しています。紹介するシナリオは、BLOB のアップロード、一覧の取得、ダウンロード、および削除です。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## コンテナーを作成する

使用する BLOB の種類に基づいて、**BlockBlobService** オブジェクト、**AppendBlobService** オブジェクト、または **PageBlobService** オブジェクトを作成します。次のコードでは、**BlockBlobService** オブジェクトを使用しています。プログラムを使用して Azure Block Blob Storage にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage.blob import BlockBlobService

次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、**BlockBlobService** オブジェクトを作成します。'myaccount' と 'mykey' は、実際のアカウント名とキーに置き換えてください。

	block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

次のコード サンプルでは、コンテナーが存在しない場合に、**BlockBlobService** オブジェクトを使用してコンテナーを作成できます。

	block_blob_service.create_container('mycontainer')

既定では、新しいコンテナーはプライベートであるため、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを (前と同じ方法で) 指定する必要があります。コンテナー内の BLOB をすべてのユーザーが利用できるようにする場合は、次のコードを使用して、コンテナーを作成しパブリック アクセス レベルを渡します。

	from azure.storage.blob import PublicAccess
	block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

または、次のコードを使用してコンテナーを作成した後で、コンテナーを変更することもできます。

	block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

この変更後、パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが表示できますが、変更または削除できるのは、このコンテナーの作成と変更を行ったユーザーだけです。

## コンテナーに BLOB をアップロードする

ブロック BLOB を作成し、データをアップロードするには、**create\_blob\_from\_path**、**create\_blob\_from\_stream**、**create\_blob\_from\_bytes**、または **create\_blob\_from\_text** メソッドを使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

**create\_blob\_from\_path** は指定のパスからファイルの内容をアップロードし、**create\_blob\_from\_stream** は既に開いているファイルやストリームから内容をアップロードします。**create\_blob\_from\_bytes** はバイトの配列をアップロードし、**create\_blob\_from\_text** は指定のエンコード (既定では UTF-8) を使用して指定のテキスト値をアップロードします。

次の例では、**sunset.png** ファイルの内容を **myblob** BLOB にアップロードします。

	from azure.storage.blob import ContentSettings
	block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
				)

## コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB の一覧を取得するには、**list\_blobs** メソッドを使用します。このメソッドは、ジェネレーターを返します。次のコードでは、コンテナー内の各 BLOB の**名前 (name)** をコンソールに出力しています。

	generator = block_blob_service.list_blobs('mycontainer')
	for blob in generator:
		print(blob.name)

## BLOB をダウンロードする

BLOB からデータをダウンロードするには、**get\_blob\_to\_path**、**get\_blob\_to\_stream**、**get\_blob\_to\_bytes**、または **get\_blob\_to\_text** を使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

次の例は、**get\_blob\_to\_path** を使用して **myblob** BLOB の内容をダウンロードし、**out-sunset.png** ファイルに格納する方法を示しています。

	block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## BLOB を削除する

最後に、BLOB を削除するには、**delete\_blob** を呼び出します。

	block_blob_service.delete_blob('mycontainer', 'myblockblob')

## 追加 BLOB への書き込み

追加 BLOB は、ログ記録などの追加操作のために最適化されています。ブロック BLOB のように、追加 BLOB はブロックで構成されますが、追加 BLOB に新しいブロックを追加する場合は常に BLOB の最後に追加されます。追加 BLOB の既存のブロックは更新したり、削除することはできません。追加 BLOB のブロック ID はブロック BLOB 用のため、公開されることはありません。

追加 BLOB 内の各ブロックは、最大 4 MB のサイズにすることができます。また追加 BLOB には最大 50,000 のブロックを含めることができます。よって追加 BLOB の最大サイズは 195 GB (4 MB X 50,000 ブロック) よりも少し大きくなります。

次の例では、新しい追加 BLOB を作成し、データを追加してシンプルなログ記録操作をシミュレートしています。

	from azure.storage.blob import AppendBlobService
	append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

	# The same containers can hold all types of blobs
	append_blob_service.create_container('mycontainer')

	# Append blobs must be created before they are appended to
	append_blob_service.create_blob('mycontainer', 'myappendblob')
	append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

	append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## 次のステップ

これで、Blob Storage の基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

- [Python デベロッパー センター](/develop/python/)
- [Azure Storage Services REST API (Azure Storage サービスの REST API)](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure Storage チーム ブログ]
- [Microsoft Azure Storage SDK for Python]

[Azure Storage チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->