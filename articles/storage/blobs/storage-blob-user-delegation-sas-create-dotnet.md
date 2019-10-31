---
title: .NET を使用してコンテナーまたは BLOB 用のユーザー委任 SAS を作成する (プレビュー) - Azure Storage
description: .NET クライアント ライブラリを使用して、Azure Storage で Azure Active Directory 資格情報を使用するユーザー委任 SAS を作成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c75a13a20c1dbb222db69145e24838deb111fb66
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595213"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>.NET を使用してコンテナーまたは BLOB 用のユーザー委任 SAS を作成する (プレビュー)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、Azure Active Directory (Azure AD) 資格情報を使用して .NET 用 Azure Storage クライアント ライブラリを使用するコンテナーまたは Blob 用のユーザー委任 SAS を作成する方法について説明します。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Azure ID ライブラリを使用した認証 (プレビュー)

.NET 対応の Azure ID クライアント ライブラリ (プレビュー) では、セキュリティ プリンシパルが認証されます。 コードが Azure 上で実行されている場合、セキュリティ プリンシパルは Azure リソースに対するマネージド ID です。

開発環境でコードを実行している場合は、認証が自動的に処理されるか、使用しているツールに応じてブラウザー ログインが必要になることがあります。 Microsoft Visual Studio では、アクティブな Azure AD ユーザー アカウントが自動的に認証に使用されるように、シングル サインオン (SSO) がサポートされます。 SSO の詳細については、[アプリケーションへのシングル サインオン](../../active-directory/manage-apps/what-is-single-sign-on.md)に関するページを参照してください。

他の開発ツールでは、Web ブラウザー経由でログインするように求められる場合があります。 また、サービス プリンシパルを使用して、開発環境から認証することもできます。 詳細については、[ポータルでの Azure アプリ用の ID 作成](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページを参照してください。

認証後、Azure ID クライアント ライブラリでは、トークン資格情報を取得します。 このトークン資格情報は、Azure Storage に対して操作を実行するために作成するサービス クライアント オブジェクトにカプセル化されます。 ライブラリでは、適切なトークン資格情報を取得することで、これをシームレスに処理します。

Azure ID クライアント ライブラリの詳細については、「[.NET 用 Azure ID クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)」を参照してください。

## <a name="assign-rbac-roles-for-access-to-data"></a>RBAC ロールを割り当ててデータにアクセスする

Azure AD セキュリティ プリンシパルが Blob データにアクセスしようとする場合、そのセキュリティ プリンシパルはリソースへのアクセス許可を保持している必要があります。 セキュリティ プリンシパルが Azure 内のマネージド ID であるか、開発環境でコードを実行している Azure AD ユーザー アカウントであるかにかかわらず、Azure Storage での Blob データへのアクセスを許可する RBAC ロールをセキュリティ プリンシパルに割り当てる必要があります。 RBAC 経由でのアクセス許可の割り当てについては、「[Azure Active Directory を使用して Azure Blob およびキューへのアクセスを承認します](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)」にある「**アクセス権に RBAC ロールを割り当てる**」というタイトルのセクションを参照してください。

## <a name="install-the-preview-packages"></a>プレビュー パッケージをインストールする

この記事の例では、[Blob ストレージ用の Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/Azure.Storage.Blobs)の最新のプレビュー バージョンが使用されます。 プレビュー パッケージをインストールするには、NuGet パッケージ マネージャー コンソールから次のコマンドを実行します。

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

この記事の例では、Azure AD の資格情報で認証するために、[.NET 用 Azure ID クライアント ライブラリ](https://www.nuget.org/packages/Azure.Identity/)の最新のプレビュー バージョンも使用されます。 プレビュー パッケージをインストールするには、NuGet パッケージ マネージャー コンソールから次のコマンドを実行します。

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="add-using-directives"></a>using ディレクティブを追加する

Azure ID と Azure Storage クライアント ライブラリのプレビューバージョンを使用するために、次の `using` ディレクティブをコードに追加します。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>認証済みのトークン資格情報を取得する

Azure Storage への要求を承認するためにコード上で使用できるトークン資格情報を取得するには、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスのインスタンスを作成します。

次のコード スニペットでは、認証されたトークン資格情報を取得し、それを使用して Blob ストレージ用のサービス クライアントを作成する方法が示されています。

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>ユーザー委任キーを取得する

すべての SAS はキーによって署名されます。 ユーザー委任 SAS を作成するには、まず、SAS に署名するために使用されるユーザー委任キーを要求する必要があります。 ユーザー委任キーは、サービス SAS またはアカウント SAS に署名するために使用されるアカウント キーに似ていますが、Azure AD の資格情報に依存している点が異なります。 クライアントで OAuth 2.0 トークンを使用してユーザー委任キーが要求されると、ユーザーに代わって Azure Storage によってユーザー委任キーが返されます。

ユーザー委任キーを取得したら、そのキーを使用して、キーの有効期間にわたって、任意の数のユーザー委任共有アクセス署名を作成できます。 ユーザー委任キーは、それを取得するために使用された OAuth 2.0 トークンに依存しないため、キーが有効である限り、トークンを更新する必要はありません。 キーの有効期間として、最大 7 日間を指定できます。

ユーザー委任キーを要求するには、次のいずれかの方法を使用します。

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

次のコード スニペットでは、ユーザー委任キーが取得され、そのプロパティが書き出されます。

```csharp
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>SAS トークンを作成する

次のコード スニペットで、新しい[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) を作成し、ユーザー委任 SAS のパラメーターを指定する方法を示します。 その後、スニペットでは [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) を呼び出して、SAS トークン文字列が取得されます。 最後に、このコードでは、リソース アドレスと SAS トークンを含む完全な URI がビルドされます。

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>例:ユーザー委任 SAS を取得する

次の例は、セキュリティ プリンシパルを認証し、ユーザー委任 SAS を作成するための完全なコードを示しています。

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>例:ユーザー委任 SAS を使用して BLOB を読み取る

次の例では、前の例で作成されたユーザー委任 SAS を、シミュレートされたクライアント アプリケーションからテストします。 SAS が有効であれば、クライアント アプリケーションで BLOB の内容を読み取ることができます。 SAS が無効な場合 (たとえば、有効期限が切れている場合)、Azure Storage によってエラー コード 403 (禁止) が返されます。

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (StorageRequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>関連項目

- [ユーザー委任キー取得操作](/rest/api/storageservices/get-user-delegation-key)
- [ユーザー委任 SAS を作成する (REST API)](/rest/api/storageservices/create-user-delegation-sas)
