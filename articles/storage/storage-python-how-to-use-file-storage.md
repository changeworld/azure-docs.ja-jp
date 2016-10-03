<properties
	pageTitle="Python から Azure File Storage を使用する方法 | Microsoft Azure"
	description="Python から Azure File Storage を使用して、ファイルをアップロード、一覧表示、ダウンロード、削除する方法について説明します。"
	services="storage"
	documentationCenter="python"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="minet;robinsh"/>

# Python から Azure File Storage を使用する方法

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## Overview

この記事では、File Storage を使用して一般的なシナリオを実行する方法について説明します。サンプルは Python で作成され、[Microsoft Azure Storage SDK for Python] を使用しています。紹介するシナリオは、ファイルのアップロード、一覧表示、ダウンロード、および削除です。

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 共有を作成する

**FileService** オブジェクトを使うと、共有、ディレクトリ、ファイルを操作できます。次のコードでは、**FileService** オブジェクトを作成します。プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

	from azure.storage.file import FileService

次のコードでは、ストレージ アカウント名とアカウント キーを使用して **FileService** オブジェクトを作成します。'myaccount' と 'mykey' は、実際のアカウント名とキーに置き換えてください。

	file_service = **FileService** (account_name='myaccount', account_key='mykey')

次のコード サンプルでコンテナーが存在しない場合は、**FileService** オブジェクトを使用してコンテナーを作成できます。

	file_service.create_share('myshare')

## ファイルを共有にアップロードする

Azure ファイル ストレージ共有には、少なくとも、ファイルが置かれるルート ディレクトリが含まれます。このセクションでは、ローカル ストレージから共有のルート ディレクトリにファイルをアップロードする方法を紹介します。

ファイルを作成し、データをアップロードするには、**create\_file\_from\_path**、**create\_file\_from\_stream**、**create\_file\_from\_bytes**、**create\_file\_from\_text** メソッドを使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

**create\_file\_from\_path** は、指定のパスからファイルの内容をアップロードし、**create\_file\_from\_stream** は既に開いているファイルやストリームから内容をアップロードします。**create\_file\_from\_bytes** は、バイトの配列をアップロードし、**create\_file\_from\_text** は、指定のエンコード (既定では UTF-8) を使用して指定のテキスト値をアップロードします。

次の例では、**sunset.png** ファイルの内容を **myfile** ファイルにアップロードします。

	from azure.storage.file import ContentSettings
	file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## ディレクトリを作成する方法

ルート ディレクトリにすべてのファイルを置くのではなく、サブディレクトリ内に置いてストレージを整理することもできます。Azure ファイル ストレージ サービスでは、自分のアカウントで許可されるだけのディレクトリを作成できます。下のコードはルート ディレクトリの下に「**sampledir**」という名前のサブディレクトリを作成します。

	file_service.create_directory('myshare', 'sampledir')

## 共有のファイルとディレクトリを一覧表示する方法

共有のファイルとディレクトリを一覧表示するには、**list\_directories\_and\_files** メソッドを使用します。このメソッドは、ジェネレーターを返します。次のコードでは、共有内の各ファイルとディレクトリの**名前 (name)** をコンソールに出力しています。

	generator = file_service.list_directories_and_files('myshare')
	for file_or_dir in generator:
		print(file_or_dir.name)

## ファイルをダウンロードする

ファイルからデータをダウンロードするには、**get\_file\_to\_path**、**get\_file\_to\_stream**、**get\_file\_to\_bytes**、**get\_file\_to\_text** を使用します。これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

次の例は、**get\_file\_to\_path** を使用して **myfile** ファイルの内容をダウンロードし、**out-sunset.png** ファイルに格納する方法を示しています。

	file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## ファイルを削除する

最後に、ファイルを削除するには、**delete\_file** を呼び出します。

	file_service.delete_file('myshare', None, 'myfile')

## 次のステップ

これで、File Storage の基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

- [Python デベロッパー センター](/develop/python/)
- [Azure Storage Services REST API (Azure Storage サービスの REST API)](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure Storage チーム ブログ]
- [Microsoft Azure Storage SDK for Python]

[Azure Storage チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->