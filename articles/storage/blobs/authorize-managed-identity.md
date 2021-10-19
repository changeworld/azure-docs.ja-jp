---
title: マネージド ID を使用して BLOB データへのアクセスを承認する
titleSuffix: Azure Storage
description: Azure リソースのマネージド ID を使用して、Azure VM や関数アプリなどで実行されているアプリケーションからの BLOB データ アクセスを承認します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 774bdecb65b885bcf7e930729ba2152b0bbbca39
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859589"
---
# <a name="authorize-access-to-blob-data-with-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID を使用して BLOB データへのアクセスを承認する

Azure Blob Storage では、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用した Azure Active Directory (Azure AD) 認証がサポートされています。 Azure リソースのマネージド ID により、Azure 仮想マシン (VM) で実行されているアプリケーション、関数アプリ、仮想マシン スケール セット、およびその他のサービスから Azure AD 資格情報を使用して、BLOB データへのアクセスを承認することができます。 Azure リソースのマネージド ID を Azure AD 認証と一緒に使用することで、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。

この記事では、Azure リソースのマネージド ID を使用して Azure VM から BLOB データへのアクセスを承認する方法について示します。

## <a name="enable-managed-identities-on-a-vm"></a>VM 上のマネージド ID を有効にする

Azure リソースのマネージド ID を使用して VM から BLOB へのアクセスを承認するには、最初に VM で Azure リソースのマネージド ID を有効にする必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

マネージド ID の詳細については、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

## <a name="assign-an-rbac-role-to-a-managed-identity"></a>マネージド ID に RBAC ロールを割り当てる

Azure AD セキュリティ プリンシパルが Azure ストレージ アカウント内のデータにアクセスしようとする場合、そのセキュリティ プリンシパルはそのデータ リソースへのアクセス許可を持っている必要があります。 セキュリティ プリンシパルが Azure 内のマネージド ID であるか、開発環境でコードを実行している Azure AD ユーザー アカウントであるかにかかわらず、Azure Storage 内のデータへのアクセスを許可する Azure ロールをセキュリティ プリンシパルに割り当てる必要があります。 Azure RBAC を使用してデータ アクセスのためのアクセス許可を割り当てる方法については、「[BLOB データにアクセスするための Azure ロールを割り当てる](assign-azure-role-data-access.md)」を参照してください。

> [!NOTE]
> Azure ストレージ アカウントを作成するとき、Azure AD を介してデータにアクセスするためのアクセス許可は自動的に割り当てられません。 Azure Storage の Azure ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいはコンテナーのレベルで割り当てることができます。

## <a name="use-a-managed-identity-to-create-a-block-blob-in-net"></a>.NET でマネージド ID を使用してブロック BLOB を作成する

Azure ID クライアント ライブラリを使用すると、[Azure SDK](https://github.com/Azure/azure-sdk) を使用して Azure Active Directory (Azure AD) での承認用の OAuth 2.0 アクセス トークンを取得するプロセスが簡単になります。 Azure ID クライアント ライブラリを使用してアクセス トークンを取得する場合は、アプリケーションが開発環境または Azure のどちらで実行されているかにかかわらず、同じコードを使用してトークンを取得できます。 詳細については、[Azure ID ライブラリを使用した承認のためのアクセス トークンの取得](../common/identity-library-acquire-token.md)に関するページを参照してください。

Azure Storage への要求を承認するためにコードで使用できるトークンを取得するには、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスのインスタンスを作成します。 その後、そのトークンを使用して、Azure Storage でデータ操作を実行するために承認されるサービス クライアント オブジェクトを作成できます。 **DefaultAzureCredential** クラスを使用して、Azure Storage にアクセスするためのマネージド ID を承認する方法の詳細については、[.NET 用 Azure ID クライアント ライブラリ](/dotnet/api/overview/azure/identity-readme)に関するページを参照してください。

次のコード例は、アクセス トークンを取得し、それを使用してサービス クライアント オブジェクトを作成してから、サービス クライアントを使用して新しい BLOB をアップロードする方法を示しています。

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a service client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Azure AD を使用して BLOB データに対する要求を承認するには、それらの要求に HTTPS を使用する必要があります。

## <a name="next-steps"></a>次のステップ

- [BLOB データにアクセスするための Azure ロールを割り当てる](assign-azure-role-data-access.md)
- [ネイティブまたは Web アプリケーションから BLOB またはキュー データへのアクセスを承認する](../common/storage-auth-aad-app.md)
- [チュートリアル: マネージド ID を使用して App Service からストレージにアクセスする](../../app-service/scenario-secure-app-access-storage.md)
