<properties 
	pageTitle="Java からファイル ストレージを使用する方法 | Microsoft Azure" 
	description="Azure ファイル サービスを使用して、ファイルをアップロード、ダウンロード、一覧表示、削除する方法について説明します。コード サンプルは Java で記述されています。" 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe" />

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/26/2015" 
	ms.author="v-dedomi"/>

# Java からファイル ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## 概要

このガイドでは、Microsoft Azure ファイル ストレージ サービスで基本的な操作を実行する方法を紹介します。Java で記述されたサンプルを利用し、共有とディレクトリを作成し、ファイルをアップロード、一覧表示、削除する方法を紹介します。Microsoft Azure のファイル ストレージ サービスを初めて利用する場合、各セクションのコンセプトをお読みください。サンプルを理解する上で非常に役立ちます。

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Java アプリケーションの作成

サンプルを作成するには、Java Development Kit (JDK) と [Azure Storage SDK for Java][] が必要です。また、Azure ストレージ アカウントを作成しておく必要があります。

## ファイル ストレージを使用するようにアプリケーションを設定します。

Azure ストレージ API を使用するには、ストレージ サービスのアクセス元にする Java ファイルの一番上に次の文を追加します。

	// Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
	import com.microsoft.azure.storage.file.*;

## Azure のストレージ接続文字列の設定

ファイル ストレージを使用するには、Azure ストレージ アカウントに接続する必要があります。最初の手順はストレージ アカウントに接続するために使用する接続文字列を構成することです。そのために静的変数を定義しましょう。

	// Configure the connection-string with your values
	public static final String storageConnectionString = 
	    "DefaultEndpointsProtocol=http;" + 
	    "AccountName=your_storage_account_name;" + 
	    "AccountKey=your_storage_account_key";

> [AZURE.NOTE]your\_storage\_account\_name と your\_storage\_account\_key を自分のストレージ アカウントの実際の値に換えます。

## Azure ストレージ アカウントに接続する

ストレージ アカウントに接続するには、**CloudStorageAccount** オブジェクトを使用し、接続文字列をその **parse** メソッドに渡す必要があります。

	// Use the CloudStorageAccount object to connect to your storage account
	try {
		CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
	} catch (InvalidKeyException invalidKey) {
		// Handle the exception
	}

**CloudStorageAccount.parse** は InvalidKeyException を投げます。そのため、try/catch ブロック内にそれを配置する必要があります。

## 共有を作成する方法

ファイル ストレージのすべてのファイルとディレクトリは「**Share**」という名前のコンテナーにあります。ストレージ アカウントには、アカウントの容量が許す限りの共有を置くことができます。共有とそのコンテンツへのアクセスを取得するには、ファイル ストレージ クライアントを使用する必要があります。

	// Create the file storage client.
	CloudFileClient fileClient = storageAccount.createCloudFileClient();

ファイル ストレージ クライアントを使用し、共有への参照を取得できます。

	// Get a reference to the file share
	CloudFileShare share = fileClient.getShareReference("sampleshare"); 

共有を実際に作成するには、CloudFileShare オブジェクトの **createIfNotExists** メソッドを使用します。

	if (share.createIfNotExists()) {
		System.out.println("New share created");
	}

この時点で、**share** は「**sampleshare**」という名前の共有の参照を保持します。

## ファイルをアップロードする方法

Azure ファイル ストレージ共有には、少なくとも、ファイルが置かれるルート ディレクトリが含まれます。このセクションでは、ローカル ストレージから共有のルート ディレクトリにファイルをアップロードする方法を紹介します。

ファイルをアップロードするための最初の手順は、ファイルを置くディレクトリの参照を取得することです。これを行うには、share オブジェクトの **getRootDirectoryReference** メソッドを呼び出します。

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

共有のルート ディレクトリの参照が与えられたので、次のコードを利用してディレクトリにファイルをアップロードできます。

	// Define the path to a local file.
	final String filePath = "C:\\temp\\Readme.txt";

	CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
	cloudFile.uploadFromFile(filePath);

## ディレクトリを作成する方法

ルート ディレクトリにすべてのファイルを置くのではなく、サブディレクトリ内に置いてストレージを整理することもできます。Azure ファイル ストレージ サービスでは、自分のアカウントで許可されるだけのディレクトリを作成できます。下のコードはルート ディレクトリの下に「**sampledir**」という名前のサブディレクトリを作成します。

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the sampledir directory 
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");
			    
	if (sampleDir.createIfNotExists()) {
		System.out.println("sampledir created");
	} else {
		System.out.println("sampledir already exists");
	}

## 共有のファイルとディレクトリを一覧表示する方法

CloudFileDirectory 参照で **listFilesAndDirectories** を呼び出すと、共有内のファイルとディレクトリの一覧を簡単に取得できます。このメソッドは繰り返しできる ListFileItem オブジェクトの一覧を返します。たとえば、次のコードはルート ディレクトリ内のファイルとディレクトリを一覧表示します。

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();
		   
	for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
		System.out.println(fileItem.getUri());
	}


## ファイルをダウンロードする方法

ファイル ストレージに頻繁に実行する操作はファイルのダウンロードです。次の例のコードは SampleFile.txt をダウンロードし、そのコンテンツを表示します。

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();
	
	//Get a reference to the directory that contains the file
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");
			    
	//Get a reference to the file you want to download
	CloudFile file = sampleDir.getFileReference("SampleFile.txt");

	//Write the contents of the file to the console.
	System.out.println(file.downloadText());

## ファイルを削除する方法

もう 1 つの一般的なファイル ストレージ操作はファイル削除です。次のコードは「**sampledir**」という名前のディレクトリ内に保存されている「SampleFile.txt」という名前のファイルを削除します。


	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory where the file to be deleted is in
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");
	
	String filename = "SampleFile.txt"
	CloudFile file;

	file = containerDir.getFileReference(filename)
	if ( file.deleteIfExists() ) {
		System.out.println(filename + " was deleted");
	}


## ディレクトリを削除する方法

ディレクトリの削除はかなり単純な作業です。ただし、注意するべきことは、ファイルや他のディレクトリが含まれるディレクトリは削除できないということです。

	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory you want to delete
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");	

	// Delete the directory
	if ( containerDir.deleteIfExists() ) {
		System.out.println("Directory deleted");
	}


## 共有を削除する方法

CloudFileShare オブジェクトで **deleteIfExists** メソッドを呼び出すと共有が削除されます。次がそのサンプル コードです。

	try
	{
	    // Retrieve storage account from connection-string.
	    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	    // Create the file client.
	   CloudFileClient fileClient = storageAccount.createCloudFileClient();

	   // Get a reference to the file share
	   CloudFileShare share = fileClient.getShareReference("sampleshare");
	   
	   if (share.deleteIfExists()) {
		   System.out.println("sampleshare deleted");
	   } 
	} catch (Exception e) {
		e.printStackTrace();
	}

## 次のステップ

その他の Azure ストレージ API に関する詳細については、次のリンク先をご覧ください。

- [Azure Storage SDK for Java]
- [Azure ストレージ クライアント SDK リファレンス]
- [Azure Storage REST API]
- [Azure Storage チーム ブログ]
- [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy)

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure ストレージ クライアント SDK リファレンス]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
 

<!---HONumber=AcomDC_1217_2015-->