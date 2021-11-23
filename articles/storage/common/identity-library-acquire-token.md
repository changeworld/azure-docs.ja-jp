---
title: Azure ID ライブラリを使用して承認用のアクセス トークンを取得する
titleSuffix: Azure Storage
description: Azure ID クライアント ライブラリを使用して、Azure Storage 内のデータへのアクセスを承認するためにアプリケーションで使用できるアクセス トークンを取得する方法について説明します。 Azure ID ライブラリでは、同じコードを使用して、開発環境または Azure でのアクセス トークンを取得できます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2021
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 62033ff6c76c2c16a6a3b0d442fec843ae2e86af
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724019"
---
# <a name="use-the-azure-identity-library-to-get-an-access-token-for-authorization"></a>Azure ID ライブラリを使用して承認用のアクセス トークンを取得する

Azure ID クライアント ライブラリを使用すると、[Azure SDK](https://github.com/Azure/azure-sdk) を使用して Azure Active Directory (Azure AD) での承認用の OAuth 2.0 アクセス トークンを取得するプロセスが簡単になります。 .NET、Java、Python、JavaScript、Go 用の最新バージョンの Azure Storage クライアント ライブラリは、各言語用の Azure ID ライブラリに統合され、Azure Storage 要求を承認するためのアクセス トークンを取得するための簡単で安全な手段が提供されます。

Azure ID クライアント ライブラリの利点は、アプリケーションが開発環境または Azure のどちらで実行されているかにかかわらず、同じコードを使用してアクセス トークンを取得できることです。 Azure ID クライアント ライブラリからは、セキュリティ プリンシパルのためのアクセス トークンが返されます。 コードが Azure で実行されている場合は、セキュリティ プリンシパルは、Azure リソース用のマネージド ID、サービス プリンシパル、またはユーザーやグループのいずれでもかまいません。 開発環境では、クライアント ライブラリにより、ユーザーまたはサービス プリンシパルにテストのためのアクセス トークンが提供されます。

Azure ID クライアント ライブラリによって返されるアクセス トークンは、トークン資格情報にカプセル化されています。 その後、トークン資格情報を使用してサービス クライアント オブジェクトを取得し、Azure Storage に対する承認された操作の実行で使用できます。 アクセス トークンとトークン資格情報を取得する簡単な方法は、Azure ID クライアント ライブラリによって提供される **DefaultAzureCredential** クラスを使用することです。 このクラスのインスタンスによって、さまざまな一般的な方法でトークン資格情報の取得が試みられ、それは開発環境と Azure の両方で動作します。

お使いの言語に対する Azure ID クライアント ライブラリの詳細については、次の記事のいずれかを参照してください。

- [.NET 用 Azure ID クライアント ライブラリ](/dotnet/api/overview/azure/identity-readme)
- [Java 用 Azure ID クライアント ライブラリ](/java/api/overview/azure/identity-readme)
- [Python 用 Azure ID クライアント ライブラリ](/python/api/overview/azure/identity-readme)
- [JavaScript 用 Azure ID クライアント ライブラリ](/javascript/api/overview/azure/identity-readme)
- [Go 用 Azure ID クライアント ライブラリ](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/azidentity)

## <a name="assign-azure-roles-for-access-to-data"></a>データにアクセスするための Azure ロールを割り当る

Azure AD セキュリティ プリンシパルが Azure ストレージ アカウント内のデータにアクセスしようとする場合、そのセキュリティ プリンシパルはそのデータ リソースへのアクセス許可を持っている必要があります。 セキュリティ プリンシパルが Azure 内のマネージド ID であるか、開発環境でコードを実行している Azure AD ユーザー アカウントであるかにかかわらず、Azure Storage 内のデータへのアクセスを許可する Azure ロールをセキュリティ プリンシパルに割り当てる必要があります。 Azure RBAC によるデータ アクセスのためのアクセス許可の割り当ての詳細については、次の記事のいずれかを参照してください。

- [BLOB データにアクセスするための Azure ロールを割り当てる](../blobs/assign-azure-role-data-access.md)
- [キュー データにアクセスするための Azure ロールを割り当てる](../queues/assign-azure-role-data-access.md)
- [テーブル データ (プレビュー) にアクセスするための Azure ロールを割り当てる](../tables/assign-azure-role-data-access.md)

> [!NOTE]
> Azure ストレージ アカウントを作成するとき、Azure AD を介してデータにアクセスするためのアクセス許可は自動的に割り当てられません。 Azure Storage の Azure ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、コンテナー、キュー、またはテーブルのレベルで、割り当てることができます。

## <a name="authenticate-the-user-in-the-development-environment"></a>開発環境でユーザーを認証する

開発環境でコードを実行している場合は、認証が自動的に処理されるか、使用しているツールに応じてブラウザー ログインが必要になることがあります。 たとえば、Microsoft Visual Studio と Microsoft Visual Studio Code では、アクティブな Azure AD ユーザー アカウントが自動的に認証に使用されるように、シングル サインオン (SSO) がサポートされています。 SSO の詳細については、[アプリケーションへのシングル サインオン](../../active-directory/manage-apps/what-is-single-sign-on.md)に関するページを参照してください。

また、サービス プリンシパルを作成し、実行時に開発環境で読み取ることができる環境変数を設定することもできます。

## <a name="authenticate-a-service-principal-in-the-development-environment"></a>開発環境でサービス プリンシパルを認証する

開発環境で Web ブラウザーによるシングル サインオンまたはログインがサポートされていない場合は、サービス プリンシパルを使用して開発環境から認証を受けることができます。 サービス プリンシパルを作成した後、サービス プリンシパルに返された値を環境変数に追加します。

### <a name="create-the-service-principal"></a>サービス プリンシパルを作成する

Azure CLI を使用してサービス Azure ロールを割り当てるには、[az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) コマンドを呼び出します。 新しいサービス プリンシパルに割り当てる Azure Storage データ アクセス ロールを指定します。 さらに、ロール割り当て用のスコープを指定します。 Azure Storage 用に提供されている組み込みロールの詳細については、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

サービス プリンシパルにロールを割り当てるための十分なアクセス許可がない場合は、アカウント所有者または管理者にロールの割り当ての実行を依頼しなければならない可能性があります。

次の例では、Azure CLI を使用して新しいサービス プリンシパルを作成し、**ストレージ BLOB データ共同作成者** ロールをそれに割り当てて、スコープをストレージ アカウントに設定します

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac` コマンドによって、サービス プリンシパルのプロパティの一覧が JSON 形式で返されます。 これらの値をコピーして、次の手順で必要な環境変数を作成するために使用できるようにします。

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

サービス プリンシパルの作成方法の詳細については、次のいずれかの記事を参照してください。

- [ポータルで Azure AD アプリとサービス プリンシパルを作成する](../../active-directory/develop/howto-create-service-principal-portal.md)
- [Azure PowerShell で Azure サービス プリンシパルを作成する](/powershell/azure/create-azure-service-principal-azureps)
- [Azure CLI で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)

> [!IMPORTANT]
> Azure ロールの割り当ての反映には、数分かかる場合があります。

### <a name="set-environment-variables"></a>環境変数の設定

Azure ID クライアント ライブラリでは、実行時に 3 つの環境変数から値を読み取って、サービス プリンシパルが認証されます。 次の表で、各環境変数に設定する値について説明します。

|環境変数|値
|-|-
|`AZURE_CLIENT_ID`|サービス プリンシパル用のアプリ ID
|`AZURE_TENANT_ID`|サービス プリンシパルの Azure AD テナント ID
|`AZURE_CLIENT_SECRET`|サービス プリンシパル用に生成されたパスワード

> [!IMPORTANT]
> 環境変数を設定したら、コンソール ウィンドウを閉じて再度開きます。 Visual Studio または他の開発環境を使用している場合は、新しい環境変数を登録するために開発環境の再起動が必要である可能性があります。

詳細については、[ポータルでの Azure アプリ用の ID 作成](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページを参照してください。

## <a name="get-an-access-token-for-authorization"></a>承認用のアクセス トークンを取得する

Azure ID クライアント ライブラリによって提供されるクラスを使用して、Azure AD で要求を承認するためのアクセス トークンを取得できます。 Azure Storage クライアント ライブラリには、パラメーターとしてトークン資格情報を受け取るサービス クライアント オブジェクト用のコンストラクターが含まれています。 この 2 つを一緒に使用すると、Azure AD の資格情報で Azure Storage 要求を簡単に承認できます。

アプリケーションで開発環境と Azure 両方のアクセス トークンを取得する必要があるほとんどの単純なシナリオでは、**DefaultAzureCredential** クラスを使用することをお勧めします。 他のシナリオ用に、さまざまな種類のトークン資格情報も使用できます。

次の例では、DefaultAzureCredential を使用して .NET でトークンを取得する方法を示します。 その後、アプリケーションでそのトークンを使用して新しいサービス クライアントを作成し、BLOB コンテナーを作成するためにそれを使用します。 この例では .NET と Blob Storage サービスを使用していますが、**DefaultAzureCredential** クラスは他の言語や他の Azure サービスでも同じように動作します。

```csharp
static void CreateBlobContainer(string accountName, string containerName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a token credential and create a service client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                  new DefaultAzureCredential());

    // Create the container if it does not exist.
    containerClient.CreateIfNotExists();
}
```  

DefaultAzureCredential クラスを使用して、Azure Storage にアクセスするためのマネージド ID を承認する方法の詳細については、[.NET 用の Azure ID クライアント ライブラリ](/dotnet/api/overview/azure/identity-readme)に関するページを参照してください。

## <a name="see-also"></a>関連項目

- [Azure AD におけるアプリとサービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)
- [Microsoft ID プラットフォームの認証ライブラリ](../../active-directory/develop/reference-v2-libraries.md)