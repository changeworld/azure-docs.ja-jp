---
title: Azure AD を使用して検索要求を承認する
titleSuffix: Azure Cognitive Search
description: Azure AD からトークンを取得して、検索要求を承認します
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/04/2021
ms.openlocfilehash: d4053d64b8a35bf13b10a47a685b838d89a64dc3
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518151"
---
# <a name="authorize-search-requests-using-azure-ad-preview"></a>Azure AD を使用して検索要求を承認する (プレビュー)

> [!IMPORTANT]
> データ プレーン操作 (インデックスの作成やインデックスへのクエリ実行など) のロールベースのアクセス制御は現在、パブリック プレビュー段階にあり、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下で使用できます。 この機能はパブリック クラウド リージョンでのみ使用でき、プレビュー段階にある間は操作の待ち時間に影響を与える可能性があります。

Azure Active Directory (Azure AD) では、ロールベースのアクセス制御 (RBAC) を使用して、Azure Cognitive Search サービスへのアクセス権を付与できます。 Azure AD を使用する主な利点は、資格情報をコード内に格納する必要がなくなることです。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Cognitive Search への要求を承認できます。 アプリケーションで Azure AD を使用する利点の詳細については、[Azure Active Directory との統合](../active-directory/develop/active-directory-how-to-integrate.md#benefits-of-integration)に関する記事を参照してください。

この記事では、Microsoft ID プラットフォームでの認証用にアプリケーションを構成する方法について説明します。 Microsoft ID プラットフォームの詳細については、「[Microsoft ID プラットフォームの概要](../active-directory/develop/v2-overview.md)」を参照してください。 Azure AD によって使用される OAuth 2.0 コード付与フローの概要については、[OAuth 2.0 コード付与フローを使用した Azure Active Directory Web アプリケーションへのアクセスの承認](../active-directory/develop/v2-oauth2-auth-code-flow.md)に関する記事を参照してください。

## <a name="prepare-your-search-service"></a>検索サービスを準備する

最初のステップとして、[検索サービスを作成](search-create-service-portal.md)し、ロールベースのアクセス制御 (RBAC) を使用するように構成します。

### <a name="sign-up-for-the-preview"></a>プレビューのサインアップ

検索サービスのクエリに Azure AD を使用するために必要な Azure Cognitive Search の RBAC 機能の部分は、まだ限定的なプレビュー段階です。 これらの機能を使用するには、プレビュー機能を Azure サブスクリプションに追加する必要があります。

サブスクリプションをプレビューに追加するには、次のようにします。

1. [Azure portal](https://portal.azure.com/) で検索サービスに移動します。
1. ページの左側で、 **[キー]** を選択します。
1. プレビューを示す青いバナーで、 **[登録]** を選択して、この機能をサブスクリプションに追加します。

![ポータルで rbac プレビューにサインアップする方法のスクリーンショット](media/search-howto-aad/rbac-signup-portal.png)

Azure Feature Exposure Control (AFEC) を使用してプレビューにサインアップし、"検索サービスのロールベースのアクセス制御 (プレビュー)" を検索することもできます。 プレビュー機能の追加の詳細については、「 [Azure サブスクリプションでプレビュー機能を設定](../azure-resource-manager/management/preview-features.md?tabs=azure-portal)する」を参照してください。

> [!NOTE]
> プレビューをサブスクリプションに追加すると、そのサブスクリプション内のすべてのサービスがプレビューに永続的に登録されます。 特定のサービスで RBAC を使用したくない場合は、次の手順に示すように、データ プレーン操作の RBAC を無効にすることができます。

### <a name="enable-rbac-for-data-plane-operations"></a>データ プレーン操作で RBAC を有効にする

サブスクリプションがプレビューに追加されたら、Azure AD 認証を使用できるように、引き続きデータ プレーン操作の RBAC を有効にする必要があります。 Azure Cognitive Search では、データ プレーン操作にキーベースの認証が既定で使用されますが、ロールベースのアクセス制御を許可するように設定を変更することができます。 

ロールベースのアクセス制御を有効にするには:

1. [Azure portal](https://portal.azure.com/) で検索サービスに移動します。
1. 左側のナビゲーション ウィンドウで、 **[キー]** を選択します
1. キーベースとロールベースの両方のアクセス制御を許可するか、またはロールベースのアクセス制御のみを許可するかを決定します。

![ポータルでの Azure Cognitive Search の認証オプションのスクリーンショット](media/search-howto-aad/portal-api-access-control.png)

[Azure Cognitive Search の RBAC のドキュメント](./search-security-rbac.md?tabs=config-svc-rest%2croles-powershell%2ctest-rest#step-2-preview-configuration)で説明されているように、これらの設定をプログラムで変更することもできます。

## <a name="register-an-application-with-azure-ad"></a>アプリケーションを Azure AD に登録する

認証に Azure AD を使用するための次のステップは、[Microsoft ID プラットフォーム](../active-directory/develop/quickstart-register-app.md)にアプリケーションを登録することです。 アプリケーションの作成で問題が発生する場合は、[アプリケーションの登録に必要なアクセス許可](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)があることを確認してください。

アプリケーションを Azure AD に登録するには:

1. [Azure portal](https://portal.azure.com) で Azure アカウントにサインインします。
1. **[Azure Active Directory]** を選択します。
1. **[アプリの登録]** を選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの名前を指定し、サポートされているアカウントの種類を選択します。これにより、アプリケーションを使用できるユーザーが決まります。 次に、 **[登録]** を選択します。

![アプリケーションの登録ウィザードのスクリーンショット](media/search-howto-aad/register-app.png)

ここまでで、Azure AD アプリケーションとサービス プリンシパルが作成されました。 アプリの登録の概要ページで、テナント (またはディレクトリ) ID とクライアント (またはアプリケーション) ID を記録しておきます。 それらの値は、後のステップで必要になります。

## <a name="create-a-client-secret"></a>クライアント シークレットの作成

アプリケーションでトークンを要求するときは、ID を提供するためのクライアント シークレットまたは証明書も必要になります。 このドキュメントでは、クライアント シークレットの使用方法について説明します。

1. 作成したアプリの登録に移動します。
1. **[証明書とシークレット]** を選択します。
1. **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** を選択します。
1. シークレットの説明を入力し、適切な有効期限を選択します。

![クライアント シークレットの作成ウィザードのスクリーンショット](media/search-howto-aad/create-secret.png)

シークレットの値に再度アクセスすることはできないため、この値を安全な場所に保存してください。 

## <a name="grant-your-application-permissions-to-azure-cognitive-search"></a>アプリケーションに Azure Cognitive Search へのアクセス許可を付与する

次に、検索サービスへのアクセスを Azure AD アプリケーションに許可する必要があります。 Azure Cognitive Search に用意されているさまざまな[組み込みのロール](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#built-in-roles-used-in-search)を、アプリケーションで必要なアクセス権に応じて使用できます。

一般に、必要なアクセス権のみをアプリケーションに付与するのが最善です。 たとえば、アプリケーションで検索インデックスのクエリを実行できることだけが必要な場合は、[検索インデックス データ閲覧者 (プレビュー)](../role-based-access-control/built-in-roles.md#search-index-data-reader) ロールを付与することができます。 または、検索インデックスの読み取りと書き込みができる必要がある場合は、[検索インデックス データ共同作成者 (プレビュー)](../role-based-access-control/built-in-roles.md#search-index-data-contributor) ロールを使用できます。

アプリの登録にロールを割り当てるには:

1. Azure portal を開き、検索サービスに移動します。
1. 左側のナビゲーション ペインで **[アクセス制御 (IAM)]** を選択します。
1. 右側の **[Grant access to this resource]\(このリソースへのアクセスを許可する\)** で、**[ロールの割り当てを追加]** を選択します。
1. 使用するロールを選択してから、 **[次へ]** を選択します。
1. 次のページで、 **[メンバーの選択]** を選択し、前に作成したアプリケーションを見つけます。 
1. 最後に、 **[レビューと割り当て]** を選択します。

![Azure portal でロールの割り当てを追加する方法のスクリーンショット](media/search-howto-aad/role-assignment.png)

[PowerShell を使用してロールを割り当てる](./search-security-rbac.md?tabs=config-svc-rest%2croles-powershell%2ctest-rest#step-3-assign-roles)こともできます。

### <a name="create-a-custom-role"></a>カスタム ロールを作成する

[組み込みのロール](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#built-in-roles-used-in-search)を使用するだけでなく、[カスタム ロール](../role-based-access-control/custom-roles.md)を作成して、アプリケーションで実行できる操作を正確に定義することもできます。

たとえば、インデックスを作成し、そこからデータを読み取る機能を備えた、インデックスを完全に管理できるロールが必要な場合は、次に示すロールを定義できます。

```json
{
  "Name": "Search Index Manager",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can manage search indexes and read or write to them",
  "Actions": [
    "Microsoft.Search/searchServices/indexes/*",
    
  ],
  "NotActions": [],
  "DataActions": [
      "Microsoft.Search/searchServices/indexes/documents/*"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}"
  ]
}
```

カスタム ロールは、[Azure portal](../role-based-access-control/custom-roles-portal.md)、[Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)、[Azure CLI](../role-based-access-control/custom-roles-cli.md)、または [REST API](../role-based-access-control/custom-roles-rest.md) を使用して作成できます。 上記の JSON では、PowerShell でカスタム ロールを作成するための構文が示されています。

使用できる操作の完全な一覧については、[Microsoft.Search リソース プロバイダーの操作](../role-based-access-control/resource-provider-operations.md#microsoftsearch)に関する記事を参照してください。


### <a name="grant-access-to-only-a-single-index"></a>1 つのインデックスだけへのアクセスを許可する

アプリケーションでアクセスできる範囲を 1 つのリソース (インデックスなど) に限定することが必要になる場合があります。 

現在、ポータルで 1 つのインデックスについてのみアクセスを許可することはサポートされていませんが、[PowerShell](../role-based-access-control/role-assignments-powershell.md) または [Azure CLI](../role-based-access-control/role-assignments-cli.md) で行うことができます。

PowerShell では、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用して、Azure のユーザーまたはグループの名前と、割り当てのスコープを指定します。

始める前に、Azure と AzureAD のモジュールを読み込み、自分の Azure アカウントに接続していることを確認してください。

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

個別のインデックスをスコープとしてロールの割り当てを追加するには、次のコマンドを実行します。

```powershell
New-AzRoleAssignment -ObjectId <objectId> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

## <a name="set-up-azure-ad-authentication-in-your-client"></a>クライアントで Azure AD 認証を設定する

Azure AD アプリケーションを作成し、検索サービスにアクセスするためのアクセス許可を付与したら、セキュリティ プリンシパルの認証を行って OAuth 2.0 トークンを取得するためのコードをアプリケーションに追加できる状態になります。

### <a name="azure-ad-authentication-with-the-net-sdk"></a>.NET SDK を使用した Azure AD 認証

Azure SDK を使用すると、Azure AD と簡単に統合できます。 [11.4.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2) 以降のバージョンの .NET SDK では、Azure AD 認証がサポートされています。 Azure AD 認証は、[Java](https://search.maven.org/artifact/com.azure/azure-search-documents/11.5.0-beta.3/jar)、[Python](https://pypi.org/project/azure-search-documents/11.3.0b3/)、[JavaScript](https://www.npmjs.com/package/@azure/search-documents/v/11.3.0-beta.3) 用のプレビュー SDK でもサポートされています。

まず初めに、[C# クイックスタート](search-get-started-dotnet.md)用の[ソース コード](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11)をクローンします。  現在、クイックスタートでは、キーベースの認証を使用して `SearchClient` と `SearchIndexClient` が作成されますが、少し変更して、ロールベースの認証に切り替えることができます。 [Program.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v11/AzureSearchQuickstart-v11/Program.cs) の `Main()` の先頭で `AzureKeyCredential` を使用する代わりに: 

```dotnet
AzureKeyCredential credential = new AzureKeyCredential(apiKey);

// Create a SearchIndexClient to send create/delete index commands
SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);
// Create a SearchClient to load and query documents
SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
```

`ClientSecretCredential` を使用して、検索サービスでの認証を行うことができます。

この場合、次のものが必要になります。
+ テナント (またはディレクトリ) ID。 これは、アプリ登録の概要ページから取得できます。
+ クライアント (またはアプリケーション) ID。 これは、アプリ登録の概要ページから取得できます。
+ プレビューのステップでコピーしたクライアント シークレットの値。

```dotnet
var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, tokenCredential);
```

`ClientSecretCredential` を使用するには、[Azure.Identity](https://www.nuget.org/packages/Azure.Identity/) ライブラリをインポートする必要があります。

Azure.Identity のドキュメントには、[Azure SDK for .NET での Azure AD 認証](/dotnet/api/overview/azure/identity-readme)の使用方法についての追加の詳細も記載されています。

### <a name="azure-ad-authentication-with-the-rest-api"></a>REST API を使用した Azure AD 認証

Azure SDK を使用すると OAuth 2.0 のフローが簡素化されますが、プロトコルに対して直接アプリケーションをプログラムすることもできます。 詳細については、「[Microsoft ID プラットフォームと OAuth 2.0 クライアント資格情報フロー](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)」を参照してください。

#### <a name="get-a-token"></a>トークンを取得する

最初に、Microsoft ID プラットフォームから[トークンを取得](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#get-a-token)します。

```
POST /[tenant id]/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=[client id]
&scope=https%3A%2F%2Fsearch.azure.com%2F.default
&client_secret=[client secret]
&grant_type=client_credentials
```

必要なスコープは "https://search.azure.com/.default" です。 

#### <a name="use-a-token"></a>トークンを使用する

トークンを取得したので、検索サービスに要求を発行する準備ができました。 

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
Content-Type: application/json   
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search でロールベースの承認を使用する](search-security-rbac.md)
+ [OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する](../active-directory/develop/v2-oauth2-auth-code-flow.md)
+ [Azure Active Directory との統合](../active-directory/develop/active-directory-how-to-integrate.md#benefits-of-integration)
+ [Azure カスタム ロール](../role-based-access-control/custom-roles.md)