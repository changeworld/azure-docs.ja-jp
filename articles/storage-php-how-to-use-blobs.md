<properties 
	pageTitle="BLOB ストレージを使用する方法 (PHP) | Microsoft Azure" 
	description="Azure BLOB サービスを使用して、BLOB をアップロード、列挙、ダウンロード、削除する方法について説明します。コード サンプルは PHP で記述されています。" 
	documentationCenter="php" 
	services="storage" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>

#PHP から BLOB ストレージを使用する方法

このガイドでは、Azure BLOB サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは PHP で記述され、[Azure SDK for PHP] [ダウンロード]. を利用しています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。BLOB の詳細については、「[次のステップ](#NextSteps)」のセクションを参照してください。

##目次

* [BLOB ストレージとは](#what-is)
* [概念](#concepts)
* [Azure のストレージ アカウントの作成](#CreateAccount)
* [PHP アプリケーションの作成](#CreateApplication)
* [BLOB サービスにアクセスするようにアプリケーションを構成する](#ConfigureStorage)
* [Azure のストレージ接続文字列の設定](#ConnectionString)
* [方法:コンテナーを作成する](#CreateContainer)
* [方法:コンテナーに BLOB をアップロードする](#UploadBlob)
* [方法:コンテナー内の BLOB を一覧表示する](#ListBlobs)
* [方法:BLOB をダウンロードする](#DownloadBlob)
* [方法:BLOB を削除する](#DeleteBlob)
* [方法:BLOB コンテナーを削除する](#DeleteContainer)
* [次のステップ](#NextSteps)

[AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Azure のストレージ アカウントの作成</h2>

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="CreateApplication"></a>PHP アプリケーションの作成</h2>

Azure BLOB サービスにアクセスする PHP アプリケーションを作成するための要件は、コード内から Azure SDK for PHP のクラスを参照することのみです。アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

このガイドで使用するサービス機能は、PHP アプリケーション内でローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできます。

<h2><a id="GetClientLibrary"></a>Azure クライアント ライブラリの入手</h2>

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="ConfigureStorage"></a>BLOB サービスにアクセスするようにアプリケーションを構成する</h2>

Azure BLOB サービス API を使用するには、次の要件があります。

1. [require_once][require_once] ステートメントを使用してオートローダー ファイルを参照する
2. 使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、**ServicesBuilder** クラスを参照する方法を示しています。

> [AZURE.NOTE]
> この例 (およびこの記事のその他の例) では、Composer を使用して Azure 向け PHP クライアント ライブラリがインストールされていることを前提としています。ライブラリを手動でまたは PEAR パッケージとしてインストールした場合は、`WindowsAzure.php`オートローダー ファイルを参照する必要があります。

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


この後のコード例では、 `require_once` ステートメントが常に記述されていますが、コード例の実行に必要なクラスのみ参照されます。

<h2><a id="ConnectionString"></a>Azure のストレージ接続文字列の設定</h2>

Azure BLOB サービス クライアントをインスタンス化するには、まず有効な接続文字列が必要です。BLOB サービスの接続文字列の形式は次のとおりです。

ライブ サービスにアクセスする場合:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

エミュレーター ストレージにアクセスする場合:

	UseDevelopmentStorage=true


いずれの Azure サービス クライアントを作成するにも、**ServicesBuilder** クラスを使用する必要があります。そのための方法は次のとおりです。

* 接続文字列を直接渡す
* **CloudConfigurationManager (CCM)** を使用して複数の外部ソースに対して接続文字列を確認する
	* 既定では 1 つの外部ソース  - (環境変数) のみサポートされています。
	* **ConnectionStringSource** クラスを継承して新しいソースを追加できます。

ここで概説している例では、接続文字列を直接渡します。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

<h2><a id="CreateContainer"></a>方法:コンテナーを作成する</h2>

**BlobRestProxy** オブジェクトの **createContainer** メソッドを使用して BLOB コンテナーを作成できます。コンテナーの作成時にコンテナーのオプションを設定できますが、この設定は必須ではありません(次の例では、コンテナーの ACL とメタデータを設定する方法を示しています)。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Blob\Models\CreateContainerOptions;
	use WindowsAzure\Blob\Models\PublicAccessType;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	// OPTIONAL: Set public access policy and metadata.
	// Create container options object.
	$createContainerOptions = new CreateContainerOptions();	

	// Set public access policy. Possible values are 
	// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
	// CONTAINER_AND_BLOBS: 	
	// Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous 
	// request, but cannot enumerate containers within the storage account.
	//
	// BLOBS_ONLY:
	// Specifies public read access for blobs. Blob data within this 
    // container can be read via anonymous request, but container data is not 
    // available. proxys cannot enumerate blobs within the container via 
	// anonymous request.
	// If this value is not specified in the request, container data is 
	// private to the account owner.
	$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
	
	// Set container metadata
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");
	
	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

**setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** の呼び出しにより、匿名の要求でコンテナーと BLOB データにアクセス可能になります。**setPublicAccess(PublicAccessType::BLOBS_ONLY)** の呼び出しにより、匿名の要求で BLOB データにのみアクセス可能になります。コンテナー ACL の詳細については、「[Set Container ACL (REST API)][container-acl]」を参照してください。

BLOB サービスのエラー コードの詳細については、「[BLOB サービスのエラー コード][error-codes]」を参照してください。

<h2><a id="UploadBlob"></a>方法:コンテナーに BLOB をアップロードする</h2>

BLOB としてファイルをアップロードするには、**BlobRestProxy->createBlockBlob** メソッドを使用します。この処理により、BLOB が存在しない場合は作成され、存在する場合は上書きされます。次のコード例では、コンテナーが既に作成されていることを前提として、[fopen][fopen] を使用してストリームとしてファイルを開いています。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";
	
	try	{
		//Upload blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

この例では、ストリームとして BLOB をアップロードしていることに注意してください。ただし BLOB は、[file\_get\_contents][file_get_contents] 関数などを使用して、文字列としてアップロードすることもできます。そのためには、この例で `$content = fopen("c:\myfile.txt", "r");` を `$content = file_get_contents("c:\myfile.txt");` に変更します。

<h2><a id="ListBlobs"></a>方法:コンテナー内の BLOB を一覧表示する</h2>

コンテナー内の BLOB を列挙するには、**BlobRestProxy->listBlobs** メソッドを **foreach** ループで使用して、結果をループ処理します。次のコードでは、コンテナー内の各 BLOB の名前とその URI をブラウザーに出力しています。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// List blobs.
		$blob_list = $blobRestProxy->listBlobs("mycontainer");
		$blobs = $blob_list->getBlobs();
		
		foreach($blobs as $blob)
		{
			echo $blob->getName().": ".$blob->getUrl()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="DownloadBlob"></a>方法: BLOB をダウンロードする</h2>

BLOB をダウンロードするには、**BlobRestProxy->getBlob** メソッドを呼び出し、結果として返された **GetBlobResult** オブジェクトの **getContentStream** メソッドを呼び出します。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

この例では、ストリーム リソースとして BLOB を取得していることに注意してください (既定の動作)。ただし [stream\_get\_contents][stream-get-contents] 関数を使用して、返されたストリームを文字列に変換できます。

<h2><a id="DeleteBlob"></a>方法:BLOB を削除する</h2>

BLOB を削除するには、コンテナー名と BLOB 名を **BlobRestProxy->deleteBlob** に渡します。 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="DeleteContainer"></a>方法:BLOB コンテナーを削除する</h2>

最後に、BLOB コンテナーを削除するには、コンテナー名を **BlobRestProxy->deleteContainer** に渡します。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="NextSteps"></a>次のステップ</h2>

これで、Azure BLOB サービスの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

- MSDN リファレンス:[Azure のデータの格納とアクセス] []
- Azure のストレージ チーム ブログ: <http://blogs.msdn.com/b/windowsazurestorage/>
- PHP ブロック BLOB の例: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>..
- PHP ブロック BLOB の例: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>.

[ダウンロード]: http://go.microsoft.com/fwlink/?LinkID=252473
[Azure のデータの格納とアクセス]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/library/windowsazure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
<!--HONumber=42-->
