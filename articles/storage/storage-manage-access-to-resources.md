<properties 
	pageTitle="Azure ストレージ リソースへのアクセスの管理 | Microsoft Azure" 
	description="ユーザーが Azure Storage のリソースにアクセスする方法を管理する方法について説明します。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="micurd;tamram"/>

# Microsoft Azure ストレージ リソースへのアクセスの管理

## 概要

既定では、ストレージ アカウントの所有者のみがそのアカウントを使ってストレージ リソースにアクセスできます。サービスまたはアプリケーションでアクセス キーを共有せずに他のクライアントがこれらのリソースを使用できるようにする必要がある場合は、次の方法でアクセスを許可できます。

- コンテナーのアクセス許可を、コンテナーとその BLOB への匿名読み取りアクセスを許可するように設定できます。匿名の読み取りアクセスは、コンテナーと BLOB のみで利用できます。 

- 共有アクセス署名によってリソースを公開できます。これにより、リソースが使用可能になる間隔とクライアントに与える許可を指定することで、コンテナー、BLOB、テーブル、キュー、ファイル共有、ファイルへのアクセス制限を委任できるようになります。

- 保存されているアクセス ポリシーを使用して、コンテナーまたは BLOB の共有アクセス署名や、キュー、テーブル、ファイル共有やそのファイルの共有アクセス署名を管理できます。保存されているアクセス ポリシーでは、共有アクセス署名を制御する追加手段とそれらを失効させる直接的な機能が提供されます。

## コンテナーと BLOB へのアクセスの制限

既定では、コンテナーとコンテナー内の BLOB には、上位のストレージ アカウントの所有者のみがアクセスできます。コンテナーとその BLOB に対する読み取りアクセス許可を匿名ユーザーに付与する場合は、コンテナーのアクセス許可を設定し、パブリック アクセスを許可できます。パブリック アクセスが許可されたコンテナー内の BLOB は、匿名ユーザーが読み取ることができ、その際に要求の認証は不要です。

コンテナーへのアクセスは次のように管理できます。

- **完全なパブリック読み取りアクセス:** コンテナーと BLOB のデータを匿名の要求で読み取ることができます。クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

- **BLOB に限定したパブリック読み取りアクセス:** 該当するコンテナー内の BLOB データは匿名要求で読み取り可能ですが、コンテナー データは参照できません。クライアントはコンテナー内の BLOB を匿名要求で列挙することはできません。

- **パブリック読み取りアクセスなし:** コンテナーと BLOB のデータはアカウント所有者に限り読み取ることができます。

>[AZURE.NOTE]サービスで BLOB リソースをさらに緻密に制御する必要がある場合、または読み取り操作以外の操作のアクセス許可を設定する必要がある場合は、共有アクセス署名を使用して、リソースへのアクセス権をユーザーに付与できます。

### 匿名ユーザーが利用できる機能
コンテナーの ACL でパブリック アクセスが許可されている場合に、匿名ユーザーが呼び出すことのできる操作を次の表に示します。

| REST 操作 | 完全パブリック読み取りアクセス許可 | BLOB に限定したパブリック読み取りアクセス許可 |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers | 所有者のみ | 所有者のみ |
| Create Container | 所有者のみ | 所有者のみ |
| Get Container Properties | すべて | 所有者のみ |
| Get Container Metadata | すべて | 所有者のみ |
| Set Container Metadata | 所有者のみ | 所有者のみ |
| Get Container ACL | 所有者のみ | 所有者のみ |
| Set Container ACL | 所有者のみ | 所有者のみ |
| Delete Container | 所有者のみ | 所有者のみ |
| BLOBs の一覧 | すべて | 所有者のみ |
| Put Blob | 所有者のみ | 所有者のみ |
| Get Blob | すべて | すべて |
| Get Blob Properties | すべて | すべて |
| Set Blob Properties | 所有者のみ | 所有者のみ |
| Get Blob Metadata | すべて | すべて |
| Set Blob Metadata | 所有者のみ | 所有者のみ |
| Put Block | 所有者のみ | 所有者のみ |
| Get Block List (REST API) (コミット後のブロックのみ) | すべて | すべて |
| Get Block List (REST API) (コミット前のブロックのみまたは全ブロック) | 所有者のみ | 所有者のみ |
| Put Block List | 所有者のみ | 所有者のみ |
| Delete Blob | 所有者のみ | 所有者のみ |
| BLOB のコピー | 所有者のみ | 所有者のみ |
| Snapshot Blob | 所有者のみ | 所有者のみ |
| Lease Blob | 所有者のみ | 所有者のみ |
| Put Page | 所有者のみ | 所有者のみ |
| Get Page Ranges | すべて | すべて |

## 共有アクセス署名の作成と使用
共有アクセス署名 (SAS) は、ストレージ リソースに対して制限付きアクセス権を特定の期間付与する URI です。次のストレージ リソースで SAS を作成できます。

- コンテナーと BLOB
- キュー
- テーブル
- ファイル共有とファイル 

共有アクセス署名をクライアントに提供することによって、アカウント キーをクライアントと共有しなくても、クライアントはストレージ アカウントのリソースにアクセスできます。

>[AZURE.NOTE]共有アクセス署名に関する詳細な概要とチュートリアルが必要な場合、「[共有アクセス署名](storage-dotnet-shared-access-signature-part-1.md)」をご覧ください。

共有アクセス署名の URI クエリ パラメーターには、ストレージ リソースへの制御されたアクセスを許可するために必要なすべての情報が組み込まれています。SAS の URI クエリ パラメーターには、共有アクセス署名の有効期間、付与するアクセス許可、使用できるようにするリソース、要求を実行するために使用するバージョン、ストレージ サービスが要求の認証に使用する必要のある署名が含まれます。

また、共有アクセス署名 URI では、一連の署名に対して追加の制御レベル (必要に応じてリソースに対するアクセスの変更や取り消しを行う機能など) を提供する、保存済みのアクセス ポリシーを参照できます。

共有アクセス署名の URI の形式に関する詳細については、「[共有アクセス署名を使用したアクセスの委任](https://msdn.microsoft.com/library/ee395415.aspx)」を参照してください。

### 共有アクセス署名の安全な使用
共有アクセス署名は、URI が持つアクセス許可の情報に基づいて指定されたリソースへのアクセスを許可します。共有アクセス署名 URI を作成するには、必ず HTTPS を使用してください。共有アクセス署名で HTTP を使用すると、ストレージ アカウントが悪意のある使用から攻撃を受けやすくなります。

共有アクセス署名が一般ユーザー向けではないアクセス権を付与する場合は、必要な最小限のアクセス許可を使用して共有アクセス署名を作成する必要があります。また、共有アクセス署名は、セキュリティで保護された接続を使用してクライアントに安全に配布し、失効を想定して保存済みのアクセス ポリシーと関連付け、署名の有効期間を可能な限り短く指定する必要があります。

>[AZURE.NOTE]共有アクセス署名 URI は、署名の作成に使用されたアカウント キーと、保存済みのアクセス ポリシー (存在する場合) に関連付けられます。保存済みのアクセス ポリシーが指定されていない場合、共有アクセス署名を取り消すにはアカウント キーを変更する以外に方法はありません。

### 共有アクセス署名の作成
次のコード例では、コンテナーに対するアクセス ポリシーを作成し、そのコンテナーの共有アクセス署名を生成します。この共有アクセス署名はクライアントに渡すことができます。

    // The connection string for the storage account.  Modify for your account.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // As an alternative, you can retrieve storage account information from an app.config file. 
    // This is one way to store and retrieve a connection string if you are 
    // writing an application that will run locally, rather than in Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Create blob container permissions, consisting of a shared access policy 
    // and a public access setting. 
    BlobContainerPermissions blobPermissions = new BlobContainerPermissions();
    
    // The shared access policy provides 
    // read/write access to the container for 10 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the permission policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

### 共有アクセス署名の使用
共有アクセス署名を受け取るクライアントは、コードでその署名を使用して、[StorageCredentials](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.auth.storagecredentials.aspx) 型のオブジェクトを作成できます。その後で、これらの資格情報を使用して、リソースに対する操作を行うための [CloudStorageAccount](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.cloudstorageaccount.aspx) オブジェクトや [CloudBlobClient](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx) オブジェクトを作成できます。次に例を示します。

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    blob.UploadFromStream(fileStream);
    }

## 保存されているアクセス ポリシーの使用
保存されているアクセス ポリシーでは、サーバー側で共有アクセス署名を詳細に制御できます。保存されているアクセス ポリシーを設定することで、共有アクセス署名をグループ化し、ポリシーによって規定された署名を詳細に制限できます。保存されているアクセス ポリシーを使用して、署名の開始時刻、有効期限、アクセス許可を変更したり、発行済みの署名を取り消したりできます。

保存されているアクセス ポリシーを使用すると、解放した共有アクセス署名をより細かく制御できます。署名の有効期限とアクセス許可は URL に指定するのではなく、これらのパラメーターを、コンテナー、ファイル共有、キュー、共有するリソースが含まれるテーブルに格納された、保存されているアクセス ポリシーで指定することも可能です。1 つ以上の署名についてこれらのパラメーターを変更するには、保存されているアクセス ポリシーを変更します。署名を再発行する必要はありません。署名の取り消しも、保存されているアクセス ポリシーを変更することで簡単に行うことができます。

たとえば、保存されているアクセス ポリシーに関連付けられている共有アクセス署名を発行した場合、有効期限を保存されているアクセス ポリシーに指定しておけば、アクセス ポリシーを変更するだけで署名の有効期限を延長できます。新しい署名を再発行する必要はありません。

ベスト プラクティスとしては、共有アクセス署名の発行対象となる署名済みリソースに対して、保存されているアクセス ポリシーを指定することが推奨されています。この方法には保存されているポリシーを使用して発行済みの署名を変更または取り消しできる利点があるためです。保存されているポリシーを指定しない場合は、ストレージ アカウント リソースに対するリスクを最小化するために署名の有効期間を制限することをお勧めします。

### 保存されているアクセス ポリシーと共有アクセス署名の関連付け
保存されているアクセス ポリシーには、コンテナー、ファイル共有、キュー、またはテーブル内で一意の名前 (最大 64 文字) が含まれます。共有アクセス署名を保存されているアクセス ポリシーに関連付けるには、共有アクセス署名の作成時にこの識別子を指定します。共有アクセス署名 URI の *signedidentifier* フィールドは、保存されているアクセス ポリシーの識別子を指定します。

コンテナー、ファイル共有、キュー、またはテーブルには、最大 5 つの保存されているアクセス ポリシーを含めることができます。それぞれのポリシーは、任意の数の共有アクセス署名で使用できます。

>[AZURE.NOTE]保存されているアクセス ポリシーをコンテナー、ファイル共有、キュー、またはテーブルに設定したときには、有効になるまでに最大で 30 秒かかる場合があります。その間、アクセス ポリシーがアクティブになるまでは、保存されているアクセス ポリシーに関連付けられている共有アクセス署名は、ステータス コード 403 (Forbidden) が返されて失敗します。

### 共有アクセス署名のアクセス ポリシー パラメーターの指定
保存されているアクセス ポリシーでは、関連付けられている署名に対して次のパラメーターを指定できます。

- 開始時刻

- 有効期限

- アクセス許可

ストレージ リソースへのアクセスを制御する方法に応じて、保存されているアクセス ポリシー内でこれらのすべてのパラメーターを指定し、共有アクセス署名の URL からこれらを省略できます。これにより、関連付けられた署名の動作をいつでも変更または取り消しできます。また、保存されているアクセス ポリシー内で 1 つ以上のアクセス ポリシー パラメーターを指定し、残りのパラメーターを URL で指定することも可能です。すべてのパラメーターを URL で指定することも可能です。この場合、保存されているアクセス ポリシーを使用して署名を取り消すことができますが、署名の動作は変更できません。

共有アクセス署名と保存されているアクセス ポリシーの組み合わせには、署名を認証するために必要なすべてのフィールドが含まれている必要があります。必須フィールドが欠落している場合、ステータス コード 403 (Forbidden) で要求は失敗します。同様に、共有アクセス署名の URL と保存されているアクセス ポリシーの両方で同じフィールドが指定されている場合、ステータス コード 403 (Bad Request) が返されて要求は失敗します。署名を構成するフィールドの詳細については、「共有アクセス署名の作成」をご覧ください。

### 保存されているアクセス ポリシーの変更または取り消し

保存されている同じアクセス ポリシーを使用する共有アクセス署名へのアクセスを取り消すには、保存されているポリシーの一覧をポリシー名が含まれない新しい一覧で上書きして、保存されているポリシーをストレージ リソースから削除します。保存されているアクセス ポリシーの設定を変更するには、保存されているポリシーの一覧を、新しいアクセス制御の詳細を持つ同じ名前のポリシーが含まれる新しい一覧で上書きします。

## 関連項目

- [Azure ストレージ サービスの認証](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [共有アクセス署名: SAS モデルについて](storage-dotnet-shared-access-signature-part-1.md)
- [共有アクセス署名を使用したアクセスの委任](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=August15_HO6-->