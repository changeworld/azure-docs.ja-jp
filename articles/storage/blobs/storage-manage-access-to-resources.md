---
title: Azure Blob Storage のコンテナーと BLOB に対するパブリック読み取りアクセスを有効にする | Microsoft Docs
description: コンテナーと BLOB で匿名アクセスを使用できるようにする方法、およびこれらにプログラムでアクセスする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: e0f93b0a95a228b26fae266129aea4b595b05e0f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148347"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>コンテナーと BLOB への匿名読み取りアクセスを管理する

Azure Blob Storage のコンテナーとその BLOB に対する匿名のパブリック読み取りアクセスを有効にすることができます。 そうすることで、アカウント キーを共有せず、Shared Access Signature (SAS) も必要とせずに、これらのリソースに対する読み取り専用のアクセスを付与できます。

パブリック読み取りアクセスは、特定の BLOB を匿名読み取りアクセスで常に使用できるようにするシナリオに最適です。 より細かく制御するには、Shared Access Signature を作成できます。 Shared Access Signature を使用すると、特定の期間、異なるアクセス許可を使用して制限付きのアクセスを提供できます。 Shared Access Signature の作成方法の詳細については、[Azure Storage での Shared Access Signature (SAS) の使用](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関するページを参照してください。

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>コンテナーと BLOB への匿名ユーザーのアクセス許可を付与します。

既定では、コンテナーとその中のすべての BLOB には、適切なアクセス許可を与えられたユーザーのみがアクセスできます。 匿名ユーザーにコンテナーとその BLOB に対する読み取りアクセスを許可するには、コンテナーのパブリック アクセス レベルを設定できます。 コンテナーへのパブリック アクセスを許可すると、匿名ユーザーは、要求を認可しなくても、パブリックにアクセスできるコンテナー内の BLOB を読み取ることができます。

以下のアクセス許可を備えたコンテナーを構成できます。

* **パブリック読み取りアクセスなし:** コンテナーとその BLOB には、ストレージ アカウント所有者だけがアクセスできます。 これは、すべての新しいコンテナーの既定値です。
* **BLOB に限定したパブリック読み取りアクセス:** コンテナー内の BLOB は匿名要求で読み取り可能ですが、コンテナー データは使用できません。 匿名クライアントはコンテナー内の BLOB を列挙することはできません。
* **コンテナーとその BLOB に対するパブリック読み取りアクセス:** すべてのコンテナーと BLOB のデータを匿名要求で読み取ることができます。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

コンテナーのアクセス許可は、次の方法で設定することができます。

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* ストレージ クライアント ライブラリのいずれか、または REST API を使用して、プログラムで設定

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Azure portal でコンテナーのパブリック アクセス レベルを設定する

[Azure portal](https://portal.azure.com) から、1 つまたは複数のコンテナーのパブリック アクセス レベルを更新することができます。

1. Azure Portal のストレージ アカウントに移動します。
1. メニュー ブレードの **[Blob service]** で、**[BLOB]** を選択します。
1. パブリック アクセス レベルを設定するコンテナーを選択します。
1. **[アクセス レベルの変更]** ボタンを使用して、パブリック アクセスの設定を表示します。
1. **[パブリック アクセス レベル]** ドロップダウンから目的のパブリック アクセス レベルを選択し、[OK] ボタンをクリックして選択したコンテナーに変更を適用します。

次のスクリーンショットでは、選択したコンテナーのパブリック アクセス レベルを変更する方法を示します。

![ポータルでパブリック アクセス レベルを設定する方法を示すスクリーンショット](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> 個々の BLOB のパブリック アクセス レベルを変更することはできません。 パブリック アクセス レベルは、コンテナー レベルでのみ設定されます。

### <a name="set-container-public-access-level-with-net"></a>.NET でコンテナーのパブリック アクセス レベルを設定する

C# と .NET 用ストレージ クライアント ライブラリを使用してコンテナーのアクセス許可を設定するには、まず、**GetPermissions** メソッドを呼び出して、コンテナーの既存のアクセス許可を取得します。 次に、**GetPermissions** メソッドによって返される **BlobContainerPermissions** オブジェクトに対して **PublicAccess** プロパティを設定します。 最後に、更新されたアクセス許可を使用して **SetPermissions** メソッドを呼び出します。

次の例では、コンテナーのアクセス許可を完全なパブリック読み取りアクセスに設定します。 アクセス許可を BLOB 限定のパブリック読み取りアクセスに設定するには、**PublicAccess** プロパティを **BlobContainerPublicAccessType.Blob** に設定します。 匿名ユーザーのすべてのアクセス許可を削除するには、このプロパティを **BlobContainerPublicAccessType.Off**に設定します。

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>コンテナーと BLOB に匿名でアクセスする

コンテナーと BLOB に匿名でアクセスするクライアントは、資格情報を必要としないコンストラクターを使用できます。 次の例では、コンテナーと BLOB を匿名で参照するいくつかの方法を示します。

### <a name="create-an-anonymous-client-object"></a>匿名クライアント オブジェクトを作成する

アカウントの BLOB ストレージ エンドポイントを指定することで、匿名アクセスの対象となる新しいサービス クライアント オブジェクトを作成できます。 ただし、匿名アクセスを使用できる、そのアカウントのコンテナーの名前も知っておく必要があります。

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>コンテナーを匿名で参照する

匿名で使用できるコンテナーの URL がある場合は、この URL を使用してコンテナーを直接参照することができます。

```csharp
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
```

### <a name="reference-a-blob-anonymously"></a>BLOB を匿名で参照する

匿名アクセスを使用できる BLOB の URL がある場合は、この URL を使用して BLOB を直接参照することができます。

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>匿名ユーザーが利用できる機能

コンテナーがパブリック アクセス用に構成されている場合に、匿名で呼び出すことのできる操作を次の表に示します。

| REST 操作 | コンテナーのパブリック読み取りアクセス | BLOB 専用のパブリック読み取りアクセス |
| --- | --- | --- |
| List Containers | 承認された要求のみ | 承認された要求のみ |
| コンテナーの作成 | 承認された要求のみ | 承認された要求のみ |
| コンテナーのプロパティの取得 | 匿名要求可能 | 承認された要求のみ |
| Get Container Metadata | 匿名要求可能 | 承認された要求のみ |
| Set Container Metadata | 承認された要求のみ | 承認された要求のみ |
| コンテナー ACL の取得 | 承認された要求のみ | 承認された要求のみ |
| Set Container ACL | 承認された要求のみ | 承認された要求のみ |
| Delete Container | 承認された要求のみ | 承認された要求のみ |
| BLOBs の一覧 | 匿名要求可能 | 承認された要求のみ |
| Put Blob | 承認された要求のみ | 承認された要求のみ |
| Get Blob | 匿名要求可能 | 匿名要求可能 |
| BLOB のプロパティの取得 | 匿名要求可能 | 匿名要求可能 |
| Set Blob Properties | 承認された要求のみ | 承認された要求のみ |
| BLOB のメタデータの取得 | 匿名要求可能 | 匿名要求可能 |
| Set Blob Metadata | 承認された要求のみ | 承認された要求のみ |
| Put Block | 承認された要求のみ | 承認された要求のみ |
| Get Block List (REST API) (コミット後のブロックのみ) | 匿名要求可能 | 匿名要求可能 |
| Get Block List (REST API) (コミット前のブロックのみまたは全ブロック) | 承認された要求のみ | 承認された要求のみ |
| Put Block List | 承認された要求のみ | 承認された要求のみ |
| Delete Blob | 承認された要求のみ | 承認された要求のみ |
| BLOB のコピー | 承認された要求のみ | 承認された要求のみ |
| Snapshot Blob | 承認された要求のみ | 承認された要求のみ |
| Lease Blob | 承認された要求のみ | 承認された要求のみ |
| Put Page | 承認された要求のみ | 承認された要求のみ |
| ページ範囲の取得 | 匿名要求可能 | 匿名要求可能 |
| Append Blob | 承認された要求のみ | 承認された要求のみ |

## <a name="next-steps"></a>次の手順

* [Azure Storage サービスの承認](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Shared Access Signatures (SAS) の使用](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)