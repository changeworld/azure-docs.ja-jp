<properties linkid="dev-nodejs-how-to-blob-storage" urlDisplayName="BLOB サービス" pageTitle="BLOB ストレージの使用方法 (Node.js) | Microsoft Azure" metaKeywords="Azure BLOB の概要, Azure 非構造化データ, Azure 非構造化ストレージ, Azure BLOB, Azure BLOB ストレージ, Azure BLOB Node.js" description="Azure BLOB サービスを使用して BLOB のコンテンツをアップロード、ダウンロード、一覧表示、および削除する方法を説明します。サンプルは Node.js で記述されています。" metaCanonical="" services="storage" documentationCenter="Node.js" title="Node.js から BLOB サービスを使用する方法" authors="" solutions="" manager="" editor="" />





# Node.js から BLOB サービスを使用する方法

このガイドでは、Azure BLOB サービスを使用して一般的なシナリオを実行する
方法について説明します。サンプルは Node.js API を使用して
記述されています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、
**ダウンロード**、および**削除**です。BLOB の詳細については、
「[次のステップ][]」のセクションを参照してください。

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
* [次のステップ][]

## <a name="what-is"> </a>BLOB サービスとは

Azure BLOB サービスは、大量の非構造化データの格納に
使用されます。これらのデータは、HTTP または HTTPS を使用して世界中の
どこからでもアクセスできます。1 つの BLOB のサイズが数百 GB になることもあり、1 つの
ストレージ アカウントには最大で 100 TB の BLOB を格納できます。BLOB の一般的な用途には、
次のようなものがあります。

-   画像またはドキュメントをブラウザーに直接配信する
-   分散アクセス用にファイルを格納する
-   ビデオおよびオーディオをストリーミング配信する
-   セキュリティで保護されたバックアップおよび障害復旧を実行する
-   内部設置型サービスまたは Azure ホステッド サービスで分析する
    データを格納する

BLOB ストレージを使用すると、データを一般に公開することも、
内部アプリケーション ストレージ用にプライベートに公開することもできます。

## <a name="concepts"> </a>概念

BLOB サービスには、次のコンポーネントが含まれます。

![Blob1](./media/storage-nodejs-how-to-use-blob-storage/blob1.jpg)

-   **URL 形式:** BLOB は、次の URL 形式を使用してアドレスを指定し、
    アクセスできます。
   
    	http://storageaccount.blob.core.windows.net/container/blob  
      
    次の URL を使用すると、図のいずれかの BLOB をアドレス指定できます。
 
	    http://sally.blob.core.windows.net/movies/MOV1.AVI

-   **ストレージ アカウント:** Azure のストレージにアクセス
    する場合には必ず、ストレージ アカウントを使用します。これは、アクセスする BLOB の名前空間の
    中でも最高レベルに位置するものです。アカウントに格納できるコンテナーの数は、
    コンテナーの合計サイズが 100 TB 未満である限り無制限です。

-   **コンテナー:** コンテナーは、一連の BLOB をグループ化します。
    すべての BLOB はコンテナーに格納されている必要があります。1 つのアカウントに格納できるコンテナーの数は無制限です。また、1 つのコンテナーに保存できる BLOB の数も無制限です。

-   **BLOB:** 任意の種類およびサイズのファイルです。BLOB には、ブロック BLOB とページ BLOB の 2 種類があります。ほとんどのファイルは
    ブロック BLOB です。1 つのブロック BLOB には、最大で 200 GB までのデータを格納できます。このチュートリアルでは、
    ブロック BLOB を使用します。もう 1 つの種類の BLOB であるページ BLOB には、最大 1 TB までの
    データを格納できます。ファイルのバイト数の範囲が頻繁に変更される
    場合には、こちらの方が効率的です。

## <a name="create-account"> </a>Azure のストレージ アカウントの作成

ストレージ操作を行うには、Azure のストレージ アカウントが必要です。ストレージ アカウントを
作成するには、次の手順に従います。([REST API を使用して][]
ストレージ アカウントを作成することもできます)。

1. [Azure 管理ポータル]にログインします。

2. ナビゲーション ウィンドウの下部にある **[新規]** をクリックします。

	![+ 新規](./media/storage-nodejs-how-to-use-blob-storage/plus-new.png)

3. **[ストレージ アカウント]**、**[簡易作成]** の順にクリックします。

	![[簡易作成] ダイアログ](./media/storage-nodejs-how-to-use-blob-storage/quick-storage.png)

4. [URL] で、ストレージ アカウントの URI で使用する
    サブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。

5. ストレージの配置先となるリージョンまたは
    アフィニティ グループを選択します。Azure アプリケーションから
    ストレージを使用する場合は、アプリケーションを展開する
    リージョンと同じリージョンを選択します。

6. **[ストレージ アカウントの作成]** をクリックします。

## <a name="create-app"> </a>Node.js アプリケーションの作成

空の Node.js アプリケーションを作成します。Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ]、[Node.js クラウド サービスへのデプロイ] (Windows PowerShell を使用)、または [WebMatrix による Web サイトの作成とデプロイ]に関するページを参照してください。

## <a name="configure-access"> </a>アプリケーションのストレージへのアクセスの構成

Azure ストレージを使用するには、Node.js azure パッケージをダウンロード
して使用する必要があります。このパッケージには、ストレージ REST サービス
と通信するための便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure**」と入力すると、
    次のような出力が生成されます。

        azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  手動で **ls** コマンドを実行して、**node\_modules** 
       フォルダーが作成されたことを確認することもできます。そのフォルダーで 
    **azure** パッケージを検索します。このパッケージには、ストレージに
    アクセスするために必要なライブラリが含まれています。

### パッケージをインポートする

メモ帳などのテキスト エディターを使用して、ストレージを使用するアプリケーションの
**server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure');

## <a name="setup-connection-string"> </a>Azure のストレージ接続文字列の設定

azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 AZURE\_STORAGE\_ACCOUNT および AZURE\_STORAGE\_ACCESS\_KEY を読み取ります。これらの環境変数が設定されていない場合は、**createBlobService** を呼び出すときにアカウント情報を指定する必要があります。

Azure クラウド サービスの構成ファイルで環境変数を設定する例については、[ストレージを使用する Node.js クラウド サービスに関するトピック]を参照してください。

Azure Web サイトの管理ポータルで環境変数を設定する例については、[ストレージを使用する Node.js Web アプリケーションに関するトピック]を参照してください。

## <a name="create-container"> </a>方法: コンテナーを作成する

**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作でき
ます。次のコードでは、**BlobService** オブジェクトを作成します。**server.js** ファイル
の先頭付近に次の内容を追加します。

    var blobService = azure.createBlobService();

BLOB はすべてコンテナー内に格納されます。**BlobService** 
オブジェクトでの **createContainerIfNotExists** の呼び出しは、指定された
コンテナーが存在する場合はそのコンテナーを返します。指定された
コンテナーが存在しない場合は、指定された名前で新しいコンテナーを作成します。既定では、新しいコンテナーはプライベートであるため、
このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを使用する必要があります。

	blobService.createContainerIfNotExists(containerName, function(error){
    	if(!error){
        	// Container exists and is private
    	}
	});


コンテナー内のファイルをパブリックに設定し、アクセス キーを使用せずにアクセスできるようにする場合は、コンテナーのアクセス レベルを
**blob** または **container** に設定できます。アクセス レベルを **blob** に設定すると、このコンテナー内の BLOB の内容とメタデータに対する匿名読み取りが許可されますが、コンテナー内のすべての BLOB のリストなど、コンテナーのメタデータに対する匿名読み取りは許可されません。アクセス レベルを **container** に設定すると、BLOB の内容とメタデータおよびコンテナーのメタデータに対する匿名読み取りが許可されます。次の例では、アクセス レベルを **blob** に設定する方法を示します。

    blobService.createContainerIfNotExists(containerName
		, {publicAccessLevel : 'blob'}
		, function(error){
			if(!error){
				// Container exists and is public
			}
		});

代わりに、**setContainerAcl** を使用してアクセス レベルを指定することによって、コンテナーのアクセス レベルを変更できます。次の例では、アクセス レベルを container に変更します。

    blobService.setContainerAcl(containerName
		, 'container'
		, function(error){
			if(!error){
				// Container access level set to 'container'
			}
		});

###フィルター

オプションのフィルター操作は、**BlobService** を使用して行われる操作に適用できます。フィルター操作には、ログ、自動的な再試行などが含まれる場合があります。フィルターは、次のようなシグネチャを実装するオブジェクトです。

		function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

		function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。次のコードは、**ExponentialRetryPolicyFilter** を使用する **BlobService** オブジェクトを作成します。

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobService = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-blob"> </a>方法: コンテナーに BLOB をアップロードする

データを BLOB にアップロードするには、**createBlockBlobFromFile**、**createBlockBlobFromStream**、または **createBlockBlobFromText** メソッドを使用します。**createBlockBlobFromFile** はファイルの内容をアップロードし、**createBlockBlobFromStream** はストリームの内容をアップロードします。**createBlockBlobFromText** は指定されたテキスト値をアップロードします。

次の例では、**test1.txt** ファイルの内容を 'test1' BLOB にアップロードします。

	blobService.createBlockBlobFromFile(containerName
		, 'test1'
		, 'test1.txt'
		, function(error){
			if(!error){
				// File has been uploaded
			}
		});

## <a name="list-blob"> </a>方法: コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、**listBlobs** メソッドを 
**for** ループで使用して、コンテナー内の各 BLOB の名前を表示します。次の
コードでは、コンテナー内の各 BLOB の名前 (**name**) をコンソールに
出力しています。

    blobService.listBlobs(containerName, function(error, blobs){
		if(!error){
			for(var index in blobs){
				console.log(blobs[index].name);
			}
		}
	});

## <a name="download-blobs"> </a>方法: BLOB をダウンロードする

BLOB からデータをダウンロードするには、**getBlobToFile**、**getBlobToStream**、または **getBlobToText** を使用します。次の例は、**getBlobToStream** を使用して **test1** BLOB の内容をダウンロードし、ストリームを使用して **output.txt** ファイルに格納する方法を示しています。

    var fs=require('fs');
	blobService.getBlobToStream(containerName
		, 'test1'
		, fs.createWriteStream('output.txt')
		, function(error){
			if(!error){
				// Wrote blob to stream
			}
		});

## <a name="delete-blobs"> </a>方法: BLOB を削除する

最後に、BLOB を削除するには、**deleteBlob** を呼び出します。次の例では、'blob1' という名前の BLOB を削除します。

    blobService.deleteBlob(containerName
		, 'blob1'
		, function(error){
			if(!error){
				// Blob has been deleted
			}
		});

## <a name="next-steps"> </a>次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス][]
-   [Azure のストレージ チーム ブログ][]
-   GitHub の [Azure SDK for Node] リポジトリ

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [次のステップ]: #next-steps
  [BLOB サービスとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [Node.js アプリケーションの作成]: #create-app
  [アプリケーションのストレージへのアクセスの構成]: #configure-access
  [Azure のストレージ接続文字列の設定]: #setup-connection-string
  [方法: コンテナーを作成する]: #create-container
  [方法: コンテナーに BLOB をアップロードする]: #upload-blob
  [方法: コンテナー内の BLOB を一覧表示する]: #list-blob
  [方法: BLOB をダウンロードする]: #download-blobs
  [方法: BLOB を削除する]: #delete-blobs
[Node.js アプリケーションの作成と Azure の Web サイトへの展開]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
  [ストレージを使用する Node.js クラウド サービス]: /ja-jp/develop/nodejs/tutorials/web-app-with-storage/
  [ストレージを使用する Node.js Web アプリケーション]: /ja-jp/develop/nodejs/tutorials/web-site-with-storage/
 [WebMatrix を使用した Web サイト]: /ja-jp/develop/nodejs/tutorials/web-site-with-webmatrix/
  [REST API を使用する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh264518.aspx
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [Node.js クラウド サービス]: {localLink:2221} "Node.js Web アプリケーション"
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/

