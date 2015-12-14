<properties 
	pageTitle="コンテナーと BLOB への匿名読み取りアクセスを管理する | Microsoft Azure" 
	description="コンテナーと BLOB で匿名アクセスを使用できるようにする方法、およびこれらにプログラムでアクセスする方法について説明します。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="carmonm" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/26/2015" 
	ms.author="tamram"/>

# コンテナーと BLOB への匿名読み取りアクセスを管理する

## 概要

既定では、ストレージ アカウントの所有者のみがそのアカウントを使ってストレージ リソースにアクセスできます。Blob Storage の場合にのみ、コンテナーとその BLOB への匿名読み取りアクセスを許可するように、コンテナーのアクセス許可を設定できます。これにより、アカウント キーを共有せずにこれらのリソースへのアクセスを許可できます。

匿名アクセスは、特定の BLOB で匿名読み取りアクセスを常に使用できるようにするシナリオに最適です。詳細な制御では、さまざまなアクセス許可を使用し、指定された期間において、制限付きアクセスを委任するための Shared Access Signature を作成できます。Shared Access Signature の作成の詳細については、[Shared Access Signatures の SAS モデル](storage-dotnet-shared-access-signature-part-1.md)に関するページを参照してください。

## コンテナーと BLOB への匿名ユーザーのアクセス許可を付与します。

既定では、コンテナーとコンテナー内の BLOB には、上位のストレージ アカウントの所有者のみがアクセスできます。コンテナーとその BLOB に対する読み取りアクセス許可を匿名ユーザーに付与する場合は、コンテナーのアクセス許可を設定し、パブリック アクセスを許可できます。パブリック アクセスが許可されたコンテナー内の BLOB は、匿名ユーザーが読み取ることができ、その際に要求の認証は不要です。

コンテナーへのアクセスは次のように管理できます。

- **完全なパブリック読み取りアクセス:** コンテナーと BLOB のデータを匿名の要求で読み取ることができます。クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

- **BLOB に限定したパブリック読み取りアクセス:** 該当するコンテナー内の BLOB データは匿名要求で読み取り可能ですが、コンテナー データは参照できません。クライアントはコンテナー内の BLOB を匿名要求で列挙することはできません。

- **パブリック読み取りアクセスなし:** コンテナーと BLOB のデータはアカウント所有者に限り読み取ることができます。

コンテナーのアクセス許可は次の方法で設定できます。

- [Azure ポータル](portal.azure.com)から設定。
- ストレージ クライアント ライブラリまたは REST API を使用して、プログラムで設定。
- PowerShell を使用して設定。Azure PowerShell からのコンテナー アクセス許可の設定については、[Azure Storage での Azure PowerShell の使用](storage-powershell-guide-full#how-to-manage-azure-blobs)に関するページを参照してください。

### Azure ポータルからコンテナーのアクセス許可を設定する

[Azure ポータル](portal.azure.com)からコンテナーのアクセス許可を設定するには、次の手順に従います。

1. ストレージ アカウントのダッシュボードに移動します。
2. 一覧からコンテナーの名前を選択します。コンテナーの名前を選択するには、[名前] 列の右側をクリックする必要があります。名前をクリックすると、コンテナーにドリル ダウンしてその BLOB が表示されます。
3. ツール バーで、**[編集]** を選択します。
4. 次のスクリーンショットに示すように、**[コンテナー メタデータの編集]** ダイアログ ボックスで、**[アクセス]** フィールドから目的のレベルのアクセス許可を選択します。

	![Edit Container Metadata dialog](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-1.png)

### .NET を使用してプログラムでコンテナーのアクセス許可を設定する

.NET クライアント ライブラリを使用してコンテナーのアクセス許可を設定するには、まず **GetPermissions** メソッドを呼び出して、コンテナーの既存のアクセス許可を取得します。次に、**GetPermissions** メソッドによって返される **BlobContainerPermissions** オブジェクトに対して **PublicAccess** プロパティを設定します。最後に、更新されたアクセス許可を使用して **SetPermissions** メソッドを呼び出します。

次の例では、コンテナーのアクセス許可を完全なパブリック読み取りアクセスに設定します。アクセス許可を BLOB 限定のパブリック読み取りアクセスに設定するには、**PublicAccess** プロパティを **BlobContainerPublicAccessType.Blob** に設定します。匿名ユーザーのすべてのアクセス許可を削除するには、このプロパティを **BlobContainerPublicAccessType.Off** に設定します。

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## コンテナーと BLOB に匿名でアクセスする

コンテナーと BLOB に匿名でアクセスするクライアントは、資格情報を必要としないコンストラクターを使用できます。次の例では、BLOB サービス リソースを匿名で参照するいくつかの方法を示します。

### 匿名クライアント オブジェクトを作成する

アカウントに BLOB サービス エンドポイントを指定することで、匿名アクセスの対象となる新しいサービス クライアント オブジェクトを作成できます。ただし、匿名アクセスを使用できる、そのアカウントのコンテナーの名前も知っておく必要があります。

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### コンテナーを匿名で参照する

匿名で使用できるコンテナーの URL がある場合は、この URL を使用してコンテナーを直接参照することができます。

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### BLOB を匿名で参照する

匿名アクセスを使用できる BLOB の URL がある場合は、この URL を使用して BLOB を直接参照することができます。

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## 匿名ユーザーが利用できる機能

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
| Append Blob | 所有者のみ | 所有者のみ |


## 関連項目

- [Azure Storage サービスの認証](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [共有アクセス署名: SAS モデルについて](storage-dotnet-shared-access-signature-part-1.md)
- [Shared Access Signature を使用したアクセスの委任](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=AcomDC_1203_2015-->