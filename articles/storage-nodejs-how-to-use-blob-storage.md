<properties urlDisplayName="Blob Service" pageTitle="BLOB ストレージの使用方法 (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Azure BLOB サービスを使用して、BLOB の内容をアップロード、ダウンロード、一覧表示、および削除する方法について説明します。サンプルは Node.js で記述されています。" metaCanonical="" services="storage" documentationCenter="nodejs" title="How to Use the Blob Service from Node.js" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />





# Node.js から BLOB サービスを使用する方法

このガイドでは、Azure BLOB サービスを使用して一般的なシナリオを実行する方法について説明します。サンプルは Node.js API を使用して記述されています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。BLOB の詳細については、
「[次のステップ][]」を参照してください。

## 目次

* [BLOB サービスとは][]    
* [概念][]    
* [Azure のストレージ アカウントの作成][]    
* [Node.js アプリケーションの作成][]  
* [アプリケーションからストレージへのアクセスの構成][]     
* [Azure のストレージ接続文字列の設定][]  
* [方法: コンテナーを作成する][]  
* [方法: コンテナーに BLOB をアップロードする][]  
* [方法: コンテナー内の BLOB を一覧表示する][]  
* [方法: BLOB をダウンロードする][]  
* [方法: BLOB を削除する][]  
* [方法: 同時アクセス][]     
* [方法: 共有アクセス署名を操作する][]     
* [次のステップ][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Azure のストレージ アカウントの作成
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]、[Node.js クラウド サービスへのデプロイ][Node.js Cloud Service] (Windows PowerShell を使用)、または [WebMatrix による Web サイトの作成とデプロイ]に関するページを参照してください。

## <a name="configure-access"> </a>アプリケーションからストレージへのアクセスの構成

Azure Storage を使用するには、Azure Storage SDK for Node.js が必要です。ここには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure-storage**」と入力すると、次のような出力が生成されます。

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  手動で **ls** コマンドを実行して、**node\_modules** フォルダーが作成されたことを確認することもできます。このフォルダーで **azure-storage** パッケージを検索します。このパッケージには、ストレージにアクセスするために必要なライブラリが含まれています。

### パッケージをインポートする

メモ帳などのテキスト エディターを使用して、ストレージを使用するアプリケーションの **server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 AZURE\_STORAGE\_ACCOUNT および AZURE\_STORAGE\_ACCESS\_KEY、または AZURE\_STORAGE\_CONNECTION\_STRING を読み取ります。これらの環境変数が設定されていない場合は、**createBlobService** を呼び出すときにアカウント情報を指定する必要があります。

Azure Web サイトの管理ポータルで環境変数を設定する例については、「[Azure テーブル サービスを使用する Node.js Web アプリケーション]」を参照してください。

## <a name="create-container"> </a>方法:コンテナーを作成する

**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作できます。次のコードは、既存のユーザーの電話番号を変更します。
次のコードでは、**BlobService** オブジェクトを作成します。次の内容を 
**server.js** ファイルの先頭付近に追加します。

    var blobSvc = azure.createBlobService();

> [WACOM.NOTE] **createBlobServiceAnonymous** を使用してホスト アドレスを指定すると、BLOB に匿名でアクセスできます。たとえば、「`var blobSvc = azure.createBlobService('https://myblob.blob.core.windows.net/');`」のように指定します。

BLOB はすべてコンテナー内に格納されます。新しいコンテナーを作成するには、**createContainerIfNotExists** を使用します。次の例では、'mycontainer' という名前の新しいコンテナーが作成されます。

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

コンテナーが作成されると、`result` は true になります。コンテナーが既に存在する場合は、`result` は false になります。`response` には、コンテナーの [ETag ](http://en.wikipedia.org/wiki/HTTP_ETag) 情報を含む、操作に関する情報が含まれます。

###コンテナーのセキュリティ

既定では、新しいコンテナーはプライベートであり、匿名でアクセスすることはできません。コンテナーを公開して匿名でアクセスできるようにするには、コンテナーのアクセス レベルを **blob** または **container** に設定します。

* **blob** - BLOB の内容およびこのコンテナー内のメタデータへの匿名の読み取りは許可されますが、1 つのコンテナー内の全 BLOB のリストなど、コンテナーのメタデータに対する匿名読み取りは許可されません。 

* **container** - コンテナーのメタデータに加えて、BLOB の内容とメタデータに対する匿名読み取りが許可されます。 

次の例では、アクセス レベルを **blob** に設定する方法を示します。 

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

代わりに、**setContainerAcl** を使用してアクセス レベルを指定することによって、コンテナーのアクセス レベルを変更できます。次の例では、アクセス レベルを container に変更します。

    blobSvc.setContainerAcl('mycontainer', null, 'container', function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

結果には、コンテナーの現在の **ETag** 情報を含む、操作に関する情報が含まれます。

###フィルター

オプションのフィルター操作は、**BlobService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のようなシグネチャを実装するオブジェクトです。

		function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

		function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。次のコードは、**ExponentialRetryPolicyFilter** を使用する **BlobService** オブジェクトを作成します。

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-blob"> </a>方法:コンテナーに BLOB をアップロードする

BLOB はブロック ベースまたはページ ベースのいずれにもできます。ブロック blob は大量のデータを効率的にアップロードできる一方、ページ blob は読み取りと書き込みの操作に適しています。詳細については「[ブロック BLOB およびページ BLOB について]」を参照してください(http://msdn.microsoft.com/ja-jp/library/azure/ee691964.aspx)。

###ブロック blob

データをブロック blob にアップロードするには、以下のメソッドを使用します。

* **createBlockBlobFromLocalFile** - 新しいブロック blob を作成してファイルの内容をアップロードします。

* **createBlockBlobFromStream** - 新しいブロック blob を作成してストリームの内容をアップロードします。

* **createBlockBlobFromText** - 新しいブロック blob を作成して文字列の内容をアップロードします。

* **createWriteStreamToBlockBlob** - ブロック blob への書き込みストリームを提供します。

次の例では、**test.txt** ファイルの内容を **myblob** にアップロードします。

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

これらのメソッドによって返される `result` には、BLOB の **ETag** など、操作に関する情報が含まれます。

###ページ blob

データをページ blob にアップロードするには、以下のメソッドを使用します。

* **createPageBlob** - 指定の長さの新しいページ blob を作成します。

* **createPageBlobFromLocalFile** - 新しいページ blob を作成してファイルの内容をアップロードします。

* **createPageBlobFromStream** - 新しいページ blob を作成してストリームの内容をアップロードします。

* **createWriteStreamToExistingPageBlob** - 既存のページ blob への書き込みストリームを提供します。

* **createWriteStreamToNewPageBlob** - 新しい BLOB を作成してから、この BLOB に書き込むストリームを提供します。

次の例では、**test.txt** ファイルの内容を **mypageblob** にアップロードします。

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [WACOM.NOTE] ページ blob は、512 バイトの "ページ" で構成されています。512 の倍数でないサイズのデータをアップロードするとエラーになる場合があります。

## <a name="list-blob"> </a>方法:コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、**listBlobsSegmented** メソッドを使用します。特定のプレフィックスと共に BLOB を返す必要がある場合は、**listBlobsSegmentedWithPrefix** を使用します。

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

`result` には `entries` コレクションが含まれます。これは、各 BLOB を説明したオブジェクトの配列です。すべての BLOB を返すことができない場合は、`result` は、`continuationToken` も提供します。これは、追加のエントリを取得するための 2 つ目のパラメーターとして使用できます。

## <a name="download-blob"> </a>方法:BLOB をダウンロードする

BLOB からデータをダウンロードするには、以下のメソッドを使用します。

* **getBlobToFile** - BLOB の内容をファイルに書き込みます。

* **getBlobToStream** - BLOB の内容をストリームに書き込みます。

* **getBlobToText** - BLOB の内容を文字列に書き込みます。 

* **createReadStream** - BLOB から読み取るためのストリームを提供します。

次の例は、**getBlobToStream** を使用して **myblob** BLOB の内容をダウンロードし、ストリームを使用して **output.txt** ファイルに格納する方法を示しています。

    var fs=require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

`result` には、**ETag** 情報など、BLOB に関する情報が含まれます。

## <a name="delete-blob"> </a>方法:BLOB を削除する

最後に、BLOB を削除するには、**deleteBlob** を呼び出します。次の例では、**myblob** という名前の BLOB を削除します。

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

##<a name="concurrent-access"></a>方法:同時アクセス

複数のクライアントまたは複数のプロセス インスタンスからの BLOB への同時アクセスをサポートするには、**ETags** または**占有**を使用します。

* **Etag** - BLOB またはコンテナーが別のプロセスによって変更されていることを検出する手段を提供します。

* **占有** - 一定期間、BLOB に対する排他的で更新可能な書き込みアクセスまたは削除アクセスを取得する手段を提供します。

###ETag

ETag は、複数のクライアントまたはインスタンスからの BLOB への同時書き込みを許可する必要がある場合に使用する必要があります。ETag を使用すると、最初の読み取りまたは作成以降にコンテナーまたは BLOB が変更されているかどうかを確認できることから、別のクライアントまたはプロセスによってコミットされた変更の上書きを回避できます。

ETag の条件は、オプションの `options.accessConditions` パラメーターを使用して設定できます。次の例では、BLOB が既に存在し、`etagToMatch` によって ETag 値が含まれる場合に、**test.txt** ファイルのみをアップロードします。

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

通常、ETag を使用する場合には、以下のパターンがあります。

1. 作成、一覧表示、取得の操作の結果として ETag を取得する。

2. ETag 値が変更されていないことを確認するアクションを実行する。

値が変更されていた場合は、ETag 値を取得して以降、別のクライアントまたはインスタンスがこの BLOB またはコンテナーを変更したことを示しています。

###占有

占有を取得する BLOB またはコンテナーを指定して **acquireLease** メソッドを使用すると、新しい占有を取得できます。たとえば、以下では **myblob** での占有を取得します。

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log(result);
	  }
	});

**myblob** に対するその後の操作では、`options.leaseId` パラメーターを指定する必要があります。占有 ID は、**acquireLease** から `result.id` として返されます。

> [WACOM.NOTE] 既定では、占有期間は無限です。`options.leaseDuration` パラメーターを指定することで、有限の期間 (15 ～ 60 秒) を指定できます。

占有を削除するには、**releaseLease** を使用します。占有を中断するものの、元の期間が期限切れになるまでは新しい占有が取得されないようにするには、**breakLease** を使用します。

## <a name="sas"></a>方法:共有アクセス署名を操作する

共有アクセス署名 (SAS) は、ストレージ アカウント名やキーを指定せずに BLOB やコンテナーへのきめ細やかで安全なアクセスを提供する方法です。SAS は、モバイル アプリからの BLOB へのアクセスを許可する場合など、データへの制限されたアクセスを提供する場合によく使用されます。

> [WACOM.NOTE] BLOB への匿名のアクセスも許可できるものの、SAS ではより制御されたアクセスを提供することができます。SAS は生成の必要があるためです。

クラウドベースのサービスなどの信頼されたアプリケーションは、**BlobService** の **generateSharedAccessSignature** を使用して SAS を生成し、信頼されていないか、部分的に信頼されたアプリケーションにこれを提供します。たとえば、モバイル アプリなどです。SAS は、SAS が有効である期間の開始日と終了日のほか、SAS の保有者に付与されたアクセス レベルを示したポリシーを使用して生成されます。

次の例では、SAS の保有者による **myblob** BLOB に対する読み取り操作を許可する新しい共有アクセス ポリシーを生成します。このポリシーは作成後 100 分が経過すると期限切れになります。

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	    
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};
	
	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

SAS の保有者がコンテナーにアクセスするときに必要なホスト情報も提供する必要があることに注意してください。

その後、クライアント アプリケーションは、この SAS と **BlobServiceWithSAS** を使用して BLOB に対する操作を実行します。次の例では、**myblob** に関する情報を取得します。

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

生成された SAS が持つ権限は読み取りアクセスのみのため、BLOB を変更しようとした場合は、エラーが返されます。

###アクセス制御リスト

SAS のアクセス ポリシーを設定するために、アクセス制御リスト (ACL) も使用できます。複数のクライアントにコンテナーへのアクセスを許可し、各クライアントに異なるアクセス ポリシーを提供する場合に便利です。

ACL は、アクセス ポリシーの配列と、各ポリシーに関連付けられた ID を使用して実装されます。次の例では、2 つのポリシーを定義しています。1 つは "user1" 用、もう 1 つは "user2" 用です。

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

次の例では、**mycontainer** の現在の ACL を取得してから、**setBlobAcl** を使用して新しいポリシーを追加しています。この手法で以下を実行できます。

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

ACL を設定した後で、ポリシーの ID に基づいて SAS を作成できます。次の例では、"user2" 用に新しい SAS を作成しています。

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"> </a>次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。
さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス:[Azure のデータの格納とアクセス][]
-   [Azure のストレージ チーム ブログ][]
-   GitHub の [Azure Storage SDK for Node][] リポジトリ

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [次のステップ]: #next-steps
  [BLOB サービスとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [Node.js アプリケーションの作成]: #create-app
  [アプリケーションからストレージへのアクセスの構成]: #configure-access
  [Azure のストレージ接続文字列の設定]: #setup-connection-string
  [方法: コンテナーを作成する]: #create-container
  [方法: コンテナーに BLOB をアップロードする]: #upload-blob
  [方法: コンテナー内の BLOB を一覧表示する]: #list-blob
  [方法: BLOB をダウンロードする]: #download-blobs
  [方法: BLOB を削除する]: #delete-blobs
  [方法: 同時アクセス]: #concurrent-access
  [方法: 共有アクセス署名を操作する]: #sas
[Node.js アプリケーションの作成と Azure の Web サイトへの展開]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
  [ストレージを使用する Node.js クラウド サービス]: /ja-jp/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [ストレージを使用する Node.js Web アプリケーション]: /ja-jp/documentation/articles/storage-nodejs-use-table-storage-web-site/
 [WebMatrix を使用した Web サイト]: /ja-jp/documentation/articles/web-sites-nodejs-use-webmatrix/
  [REST API を使用する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh264518.aspx
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [Node.js クラウド サービス]: /ja-jp/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure Storage チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35.1-->
