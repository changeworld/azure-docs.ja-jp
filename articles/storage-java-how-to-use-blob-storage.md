<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="BLOB サービス" pageTitle="BLOB ストレージの使用方法 (Java) | Microsoft Azure" metaKeywords="Azure BLOB の概要, Azure 非構造化データ, Azure 非構造化ストレージ, Azure BLOB, Azure BLOB ストレージ, Azure BLOB Java" description="BLOB サービスを使用して、BLOB の内容をアップロード、ダウンロード、一覧表示、および削除する方法を学習します。コード サンプルは Java で記述されています。" metaCanonical="" services="storage" documentationCenter="Java" title="Java から BLOB ストレージを使用する方法" authors="" solutions="" manager="" editor="" />




# Java から BLOB ストレージを使用する方法

このガイドでは、Azure BLOB ストレージ サービスを使用して一般的な
シナリオを実行する方法について説明します。サンプルは Java で記述され、[Azure 
SDK for Java][] を利用しています。紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。BLOB 
の詳細については、「[次のステップ](#NextSteps)」のセクションを参照してください。

## <a name="Contents"> </a>目次

* [BLOB ストレージとは](#what-is)
* [概念](#Concepts)
* [Azure のストレージ アカウントの作成](#CreateAccount)
* [Java アプリケーションの作成](#CreateApplication)
* [BLOB ストレージにアクセスするようにアプリケーションを構成する](#ConfigureStorage)
* [Azure のストレージ接続文字列の設定](#ConnectionString)
* [方法: コンテナーを作成する](#CreateContainer)
* [方法: コンテナーに BLOB をアップロードする](#UploadBlob)
* [方法: コンテナー内の BLOB を一覧表示する](#ListBlobs)
* [方法: BLOB をダウンロードする](#DownloadBlob)
* [方法: BLOB を削除する](#DeleteBlob)
* [方法: BLOB コンテナーを削除する](#DeleteContainer)
* [次のステップ](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Azure のストレージ アカウントの作成</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"></a>Java アプリケーションの作成

このガイドで使用するストレージ機能は、Java アプリケーション内でローカルで
実行することも、Azure の Web ロールまたはワーカー ロールで動作するコード内で
実行することもできます。前提条件として、Java Development Kit (JDK) を
ダウンロードしてインストールしているとします。また、[Azure SDK for Java の
ページ][Azure SDK for Java]の指示に従って、Azure Libraries for Java と Azure SDK をインストール
し、Azure サブスクリプションに Azure ストレージ アカウントを作成していると
します。

アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。要件
は、Java プロジェクトをコンパイルできること、および Azure Libraries for Java 
を参照できることのみです。

## <a name="ConfigureStorage"></a>BLOB ストレージにアクセスするようにアプリケーションを構成する

Azure ストレージ API を使用して BLOB にアクセスする Java ファイルの先頭には、
次の import ステートメントを追加します。

    // Include the following imports to use blob APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

## <a name="ConnectionString"></a>Azure のストレージ接続文字列の設定

Azure ストレージ クライアントでは、ストレージ接続文字列を使用して、データ
管理サービスにアクセスするためのエンドポイントおよび資格情報を保存します。クライアント アプリケーションの実行時、ストレージ接続文字列を次の形式で指定する必要があります。*AccountName* と *AccountKey* の値には、管理ポータルに表示されるストレージ アカウントの名前とプライマリ アクセス キーを使用します。この例では、接続文字列を保持する静的フィールドを宣言する方法を示しています。

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Azure 上のロール内で実行されるアプリケーションでは、この文字列はサービス
構成ファイルである ServiceConfiguration.cscfg に格納でき、
**RoleEnvironment.getConfigurationSettings** メソッドの呼び出しを使用
してアクセスできます。次の例では、サービス構成ファイル内の 
*StorageConnectionString* という名前の **Setting** 要素から接続文字列
を取得しています。

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

## <a name="CreateContainer"></a>方法: コンテナーを作成する

CloudBlobClient オブジェクトを使用すると、コンテナーと BLOB の参照オブジェクトを取得できます。次のコードでは、**CloudBlobClient** オブジェクトを作成しています。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

BLOB はすべてコンテナー内に格納されます。**CloudBlobClient** オブジェクトにより、
使用するコンテナーへの参照を取得します。コンテナーが存在しない場合は、
**createIfNotExist** メソッドを使用して作成できます。存在する場合は、この
メソッドによって既存のコンテナーが返されます。既定では、新しいコンテナーは
プライベートであるため、このコンテナーから BLOB をダウンロードするには
ストレージ アクセス キーを指定する必要があります (前と同じ方法で)。

    // Get a reference to a container
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist
    container.createIfNotExist();

コンテナー内のファイルを公開する場合は、コンテナーのアクセス許可を設定でき
ます。

    // Create a permissions object
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container
    container.uploadPermissions(containerPermissions);

パブリック コンテナー内の BLOB は、インターネットに接続しているすべての
ユーザーが表示できますが、パブリック アクセスは読み取り専用に制限されます。

## <a name="UploadBlob"></a>方法: コンテナーに BLOB をアップロードする

ファイルを BLOB にアップロードするには、コンテナーの参照を取得し、それを
使用して BLOB の参照を取得します。BLOB の参照を取得したら、BLOB 参照時に 
upload を呼び出すことで、任意のストリームを BLOB にアップロードできます。この
処理により、BLOB が存在しない場合は作成され、存在する場合は上書きされます。
このコード サンプルはこのことを示しており、既にコンテナーが作成されている
ことを前提としています。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create or overwrite the "myimage.jpg" blob with contents from a local file
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File("c:\\myimages\\myimage.jpg");
    blob.upload(new FileInputStream(source), source.length());

## <a name="ListBlobs"></a>方法: コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を列挙するには、BLOB のアップロード時と同様に、まず
コンテナーの参照を取得します。コンテナーの **listBlobs** メソッドを for ループ
と共に使用できます。次のコードでは、コンテナー内の各 BLOB の URI を
コンソールに出力しています。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }

BLOB サービスには、コンテナー内のディレクトリという概念もあり
ます。これは、BLOB をよりフォルダーに近い構造で整理できるように
するためです。

たとえば、"photos" という名前のコンテナーを作成して、その中に "rootphoto1"、
"2010/photo1"、"2010/photo2"、および "2011/photo1" という名前の BLOB を
アップロードすることができます。このようにすると、"photos" コンテナー内に 
"2010" および "2011" という仮想ディレクトリが作成されます。"photos" コンテナー
の **listBlobs** を呼び出すと、返されるコレクションには
最上位レベルにある
ディレクトリおよび BLOB を表す **CloudBlobDirectory** および **CloudBlob** 
オブジェクトが含まれています。この例の場合は、ディレクトリ "2010" および 
"2011" と、写真 "rootphoto1" が返されます。**instanceof** 演算子を使用して
これらのオブジェクトを区別できます。

必要に応じて、**listBlobs** メソッドにパラメーターを渡すことができます。その
ためには、**useFlatBlobListing** パラメーターに true を設定しておく必要があり
ます。これにより、ディレクトリに関係なく、すべての BLOB が返されるようになり
ます。詳細については、Javadoc ドキュメントの CloudBlobContainer.listBlobs 
に関するページを参照してください。

## <a name="DownloadBlob"></a>方法: BLOB をダウンロードする

BLOB をダウンロードするには、BLOB の参照を取得するために BLOB をアップ
ロードしたときと同じ手順に従います。アップロードの例では、BLOB オブジェクト
の upload を呼び出しました。次の例では、download を呼び出して BLOB の
内容を **FileOutputStream** などのストリーム オブジェクトに転送しています。
このオブジェクトは BLOB をローカル ファイルに保持するために使用できます。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // For each item in the container
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream(blob.getName()));
        }
    }

## <a name="DeleteBlob"></a>方法: BLOB を削除する

BLOB を削除するには、BLOB の参照を取得し、**delete** メソッドを呼び出します。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg"
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob
    blob.delete();

## <a name="DeleteContainer"></a>方法: BLOB コンテナーを削除する

最後に、BLOB コンテナーを削除するには、BLOB コンテナーの参照を取得し、
delete を呼び出します。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container
    container.delete();

## <a name="NextSteps"></a>次のステップ

これで、BLOB ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納とアクセス]
-   Azure のストレージ チーム ブログ: <http://blogs.msdn.com/b/windowsazurestorage/>


[Azure SDK for Java]: http://www.windowsazure.com/ja-jp/develop/java/
[Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx

