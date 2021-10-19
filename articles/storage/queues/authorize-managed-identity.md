---
title: マネージド ID を使用してキュー データへのアクセスを認可する
titleSuffix: Azure Storage
description: Azure リソースのマネージド ID を使用して、Azure VM や関数アプリなどで実行されているアプリケーションからのキュー データ アクセスを認可します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 0aac0689a18d9c2a525fbd15aa2b47e4ca960ebb
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859577"
---
# <a name="authorize-access-to-queue-data-with-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID を使用してキュー データへのアクセスを認可する

Azure Queue Storage では、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用した Azure Active Directory (Azure AD) 認証がサポートされています。 Azure リソースのマネージド ID では、Azure 仮想マシン (VM)、関数アプリ、仮想マシン スケール セット、その他のサービスで実行されているアプリケーションからの Azure AD 資格情報を使用したキュー データへのアクセスを認可できます。 Azure リソースのマネージド ID を Azure AD 認証と一緒に使用することで、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。

この記事では、Azure リソースのマネージド ID を使用して、Azure VM からキュー データへのアクセスを認可する方法について説明します。

## <a name="enable-managed-identities-on-a-vm"></a>VM 上のマネージド ID を有効にする

Azure リソースのマネージド ID を使用して VM からキューへのアクセスを認可するには、まず、その VM 上で Azure リソースのマネージド ID を有効にしておく必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

マネージド ID の詳細については、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

## <a name="assign-an-rbac-role-to-a-managed-identity"></a>マネージド ID に RBAC ロールを割り当てる

Azure AD セキュリティ プリンシパルで Azure Storage アカウント内のデータにアクセスしようとするとき、そのセキュリティ プリンシパルにはデータ リソースへのアクセス許可が必要です。 セキュリティ プリンシパルが Azure 内のマネージド ID か、または開発環境でコードを実行している Azure AD ユーザー アカウントのどちらであるかにかかわらず、そのセキュリティ プリンシパルには、Azure Storage 内のデータへのアクセスを許可する Azure ロールを割り当てる必要があります。 Azure RBAC を使用してデータ アクセスのためのアクセス許可を割り当てる方法については、「[キュー データにアクセスするための Azure ロールを割り当てる](assign-azure-role-data-access.md)」を参照してください。

> [!NOTE]
> Azure ストレージ アカウントを作成するとき、Azure AD を介してデータにアクセスするためのアクセス許可は自動的に割り当てられません。 Azure Storage の Azure ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、またはキューのレベルで割り当てることができます。

## <a name="use-a-managed-identity-to-create-a-queue-in-net"></a>.NET でマネージド ID を使用してキューを作成する

Azure ID クライアント ライブラリでは、[Azure SDK](https://github.com/Azure/azure-sdk) を使用して Azure Active Directory (Azure AD) での認可のための OAuth 2.0 アクセス トークンを取得するプロセスを簡素化します。 Azure ID クライアント ライブラリを使用してアクセス トークンを取得する場合は、アプリケーションが開発環境または Azure のどちらで実行されているかにかかわらず、同じコードを使用してトークンを取得できます。 詳細については、[Azure ID ライブラリを使用した認可のためのアクセス トークンの取得](../common/identity-library-acquire-token.md)に関するページを参照してください。

Azure Storage への要求を認可するためにコードで使用できるトークンを取得するには、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスのインスタンスを作成します。 その後、そのトークンを使用して、Azure Storage でデータ操作を実行するために認可されるサービス クライアント オブジェクトを作成できます。 **DefaultAzureCredential** クラスを使用して、Azure Storage にアクセスするためのマネージド ID を認可する方法の詳細については、「[.NET 用 Azure ID クライアント ライブラリ](/dotnet/api/overview/azure/identity-readme)」を参照してください。

次のコード例は、認証されたトークン資格情報を取得し、それを使用してサービス クライアント オブジェクトを作成した後、そのサービス クライアントを使用してキューを作成する方法を示しています。

```csharp
public static void CreateQueue(string accountName, string queueName)
{
    // Construct the blob container endpoint from the arguments.
    string queueEndpoint = string.Format("https://{0}.queue.core.windows.net/{1}",
                                                accountName,
                                                queueName);

    // Get a token credential and create a service client object for the queue.
    QueueClient queueClient = new QueueClient(new Uri(queueEndpoint), 
                                                new DefaultAzureCredential());

    try
    {
        // Create the queue.
        queueClient.CreateIfNotExists();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine("Exception: {0}", e.Message);
    }
}
```

> [!NOTE]
> Azure AD を使用してキュー データに対する要求を認可するには、これらの要求に HTTPS を使用する必要があります。

## <a name="next-steps"></a>次のステップ

- [キュー データにアクセスするための Azure ロールを割り当てる](assign-azure-role-data-access.md)
- [ネイティブ アプリケーションまたは Web アプリケーションから BLOB またはキュー データへのアクセスを認可する](../common/storage-auth-aad-app.md)
