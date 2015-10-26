<properties 
	pageTitle="AzCopy を使用して Azure BLOB ストレージ間でデータを移動する | Microsoft Azure" 
	description="AzCopy を使用して Azure BLOB ストレージ間でデータを移動する" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="bradsev" />

# AzCopy を使用して Azure BLOB ストレージ間でデータを移動する

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

## はじめに

AzCopy は、Microsoft Azure の BLOB、ファイル、およびテーブル ストレージ間のデータのアップロード、ダウンロード、コピーにおいて高いパフォーマンスを実現するために設計されたコマンド ライン ユーティリティです。

AzCopy のインストール手順と、Azure プラットフォームでの使い方に関する情報については、「[AzCopy コマンド ライン ユーティリティの概要](../storage-use-azcopy.md)」を参照してください。

> [AZURE.NOTE][Azure のデータ サイエンス用仮想マシン](machine-learning-data-science-virtual-machines.md)によって提供されるスクリプトを使用してセットアップされた VM を使用している場合、AzCopy は既に VM にインストールされています。

> [AZURE.NOTE]Azure BLOB ストレージの完全な概要については、「[Azure BLOB の基礎](../storage-dotnet-how-to-use-blobs.md)」と「[Azure BLOB Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)」を参照してください。

## 前提条件

このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

- Azure サブスクリプションを設定するには、「[1 か月間の無料評価版](https://azure.microsoft.com/ja-jp/pricing/free-trial/)」を参照してください。
- ストレージ アカウントの作成と、アカウントとキー情報の取得についての手順については、「[Azure ストレージ アカウントについて](../storage-create-storage-account.md)」を参照してください。

## ファイルを Azure BLOB にアップロードする

ファイルをアップロードするには、AzCopy コマンド ラインで次のコマンドを使用します。

	# Upload from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

## Azure BLOB からファイルをダウンロードする

Azure BLOB からファイルをダウンロードするには、AzCopy コマンド ラインで次のコマンドを使用します。

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

## Azure コンテナー間で BLOB を転送する

Azure コンテナー 間で BLOB を 転送するには、AzCopy コマンド ラインで次のコマンドを使用します。

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

## AzCopy を使用するためのヒント

> [AZURE.TIP]1.ファイルをアップロードする場合、/S を指定するとファイルを再帰的にアップロードします。このパラメーターを指定しなかった場合、サブディレクトリ内のファイルは 1 つもアップロードされません。2.ファイルをダウンロードする場合、/S を指定すると、指定したディレクトリとそのサブディレクトリ内のすべてのファイル、または指定されたディレクトリとそのサブディレクトリ内の指定したパターンと一致するすべてのファイルがダウンロードされるまで、コンテナーを再帰的に検索します。3./Source パラメーターを使用して、ダウンロードする特定の BLOB ファイルを指定することはできません。特定のファイルをダウンロードするには、ダウンロードする BLOB ファイル名を /Pattern パラメーターを使用して指定します。/S パラメーターは、AzCopy にファイル名のパターンを再帰的に検索させるために使用できます。パターンのパラメーターを指定しなかった場合、AzCopy はそのディレクトリ内のすべてのファイルをダウンロードします。

<!---HONumber=Oct15_HO3-->