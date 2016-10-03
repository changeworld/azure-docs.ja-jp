<properties
	pageTitle="AzCopy を使用して Azure BLOB ストレージ間でデータを移動する | Microsoft Azure"
	description="AzCopy を使用して Azure BLOB ストレージ間でデータを移動する"
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="bradsev" />

# AzCopy を使用して Azure BLOB ストレージ間でデータを移動する

AzCopy は、Microsoft Azure の BLOB、ファイル、テーブル ストレージとの間でデータをアップロード、ダウンロード、コピーすることを目的としたコマンド ライン ユーティリティです。

AzCopy のインストール手順と、Azure プラットフォームでの使い方に関する情報については、「[AzCopy コマンド ライン ユーティリティの概要](../storage/storage-use-azcopy.md)」を参照してください。

以下のリンクから、Azure Blob ストレージとの間でデータを移動するために使用するテクノロジについてのガイダンスを参照してください。

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] [Azure のデータ サイエンス用仮想マシン](machine-learning-data-science-virtual-machines.md)によって提供されるスクリプトを使用してセットアップされた VM を使用している場合、AzCopy は既に VM にインストールされています。

> [AZURE.NOTE] Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../storage/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。


## 前提条件

このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

- Azure サブスクリプションを設定するには、[1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。

- ストレージ アカウントの作成と、アカウントとキー情報の取得についての手順については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。


## AzCopy コマンドを実行する

AzCopy コマンドを実行するには、コマンド ウィンドウを開き、AzCopy.exe 実行可能ファイルが格納されている、コンピューターの AzCopy インストール ディレクトリに移動します。

AzCopy コマンドの基本構文は次のとおりです。

	AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] AzCopy のインストール先をシステム パスに追加し、任意のディレクトリからコマンドを実行できます。既定では、AzCopy は *%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy* または *%ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy* にインストールされています。

## ファイルを Azure BLOB にアップロードする

ファイルをアップロードするには、次のコマンドを使用します。

	# Upload from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## Azure BLOB からファイルをダウンロードする

Azure BLOB からファイルをダウンロードするには、次のコマンドを使用します。

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## Azure コンテナー間で BLOB を転送する

Azure コンテナー 間で BLOB を転送するには、次のコマンドを使用します。

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## AzCopy を使用するためのヒント

> [AZURE.TIP]   
> 1. ファイルを**アップロード**する場合、*/S* を指定すると、ファイルが再帰的にアップロードされます。このパラメーターを指定しなかった場合、サブディレクトリ内のファイルはアップロードされません。
> 2. ファイルを**ダウンロード**する場合、*/S* を指定すると、指定したディレクトリとそのサブディレクトリ内のすべてのファイル、または特定のディレクトリとそのサブディレクトリ内の指定したパターンと一致するすべてのファイルがダウンロードされるまで、コンテナーを再帰的に検索します。
> 3.  */Source* パラメーターを使用して、ダウンロードする**特定の BLOB ファイル**を指定することはできません。特定のファイルをダウンロードするには、*/Pattern* パラメーターを使用してダウンロードする BLOB ファイル名を指定します。**/S** パラメーターは、AzCopy にファイル名のパターンを再帰的に検索させるために使用できます。パターン パラメーターを指定しなかった場合、そのディレクトリ内のすべてのファイルがダウンロードされます。

<!---HONumber=AcomDC_0921_2016-->