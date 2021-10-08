---
title: 各国のクラウドアプリで MSAL を使用する | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) を使用すると、アプリケーション開発者はセキュリティで保護された Web API を呼び出すためにトークンを取得できます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、パートナーの Web API、または自分の Web API が可能です。 MSAL は、複数のアプリケーション アーキテクチャとプラットフォームをサポートします。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/21/2021
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8b8ae31aa2af84a6f8dfd4f93c90a09e58805f73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598802"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>国内クラウド環境で MSAL を使用する

ソブリン クラウドとも呼ばれる[国内クラウド](authentication-national-cloud.md)は、物理的に分離された Azure インスタンスです。 Azure のこれらのリージョンは、データの保存場所、主権、およびコンプライアンス要件を地理的な境界内で確実に守るために役立ちます。

Microsoft の世界的なクラウドに加え、Microsoft Authentication Library (MSAL) では、国内クラウドのアプリケーション開発者が、セキュリティで保護された Web API を認証して呼び出すためのトークンを取得できます。 これらの Web API には、Microsoft Graph またはその他の Microsoft API が可能です。

グローバル Azure クラウドを含め、Azure Active Directory (Azure AD) は次の各国のクラウドにデプロイされています。 

- Azure Government
- Azure China 21Vianet
- Azure Germany ([2021 年 10 月 29 日に終了](https://www.microsoft.com/cloud-platform/germany-cloud-regions))

このガイドでは、[Azure Government クラウド](https://azure.microsoft.com/global-infrastructure/government/)環境で、職場および学校アカウントにサインインし、アクセス トークンを取得し、Microsoft Graph API を呼び出す方法を示します。

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure Germany (Microsoft Cloud Deutschland)

> [!WARNING]
> Azure Germany (Microsoft Cloud Deutschland) は [2021 年 10 月 29 日に終了します](https://www.microsoft.com/cloud-platform/germany-cloud-regions)。 その日より前にグローバル Azure のリージョンに移行 "_しない_" サービスとアプリケーションにはアクセスできなくなります。

アプリケーションを Azure Germany から移行していない場合は、[Azure Germany からの移行に関する Azure Active Directory の情報](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad)に関するページに従って行うようにしてください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の前提条件を満たしていることを確認します。

### <a name="choose-the-appropriate-identities"></a>適切な ID を選択する

[Azure Government](../../azure-government/index.yml) アプリケーションでは、ユーザーの認証に Azure AD Government ID と Azure AD Public ID を使用できます。 これらの ID のどちらも使用できるため、自分のシナリオにどの機関のエンドポイントを選択すべきかを決定します。

- Azure AD Public:組織が Microsoft 365 (Public または GCC) または別のアプリケーションをサポートする Azure AD Public テナントを既に持っている場合によく使用されます。
- Azure AD Government:組織が Office 365 (GCC High または DoD) をサポートする Azure AD Government テナントを既に持っている場合、または Azure AD Government で新しいテナントを作成する場合によく使用されます。

決定したら、特別な考慮事項は、アプリの登録をどこで実行するかです。 Azure Government アプリケーションに Azure AD Public ID を選択した場合は、Azure AD Public テナントでアプリケーションを登録する必要があります。

### <a name="get-an-azure-government-subscription"></a>Azure Government サブスクリプションを取得する

Azure Government サブスクリプションを取得するには、「[Managing and connecting to your subscription in Azure Government (Azure Government でのサブスクリプションの管理と接続)](../../azure-government/compare-azure-government-global-azure.md)」を参照してください。

Azure Government サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/global-infrastructure/government/request/)を作成してください。

特定のプログラミング言語で国内クラウドを使用する方法の詳細については、お使いの言語に一致するタブを選択してください。

## <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET を使用して、国内クラウドへのユーザーのサインインを実行し、トークンを取得し、Microsoft Graph API を呼び出すことができます。

次のチュートリアルでは、.NET Core 2.2 MVC Web アプリをビルドする方法を示します。 このアプリでは、OpenID Connect を使用して、国内クラウドに属する組織のユーザーの職場および学校アカウントでのサインインを実行します。

- ユーザーのサインインを実行してトークンを取得するには、次のチュートリアルに従います: [ソブリン クラウドで Microsoft ID プラットフォームを使用して ASP.NET Core Web アプリのサインイン ユーザーを構築する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)。
- Microsoft Graph API を呼び出すには、次のチュートリアルに従います: [Microsoft の国内クラウドで職場と学校のアカウントを使用してサインインするユーザーの代わりに Microsoft ID プラットフォームを使用して ASP.NET Core 2.x Web アプリから Microsoft Graph API を呼び出す](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)。

## <a name="javascript"></a>[JavaScript](#tab/javascript)

ソブリン クラウド用の MSAL.js アプリケーションを有効にするには、次のようにします。

- クラウドに応じて、特定のポータルでアプリケーションを登録します。 ポータルの選択方法の詳細については「[アプリ登録エンドポイント](authentication-national-cloud.md#app-registration-endpoints)」を参照してください
- リポジトリにある[サンプル](https://github.com/Azure-Samples/ms-identity-javascript-tutorial)のいずれかを、クラウドに応じて構成にいくつかの変更を加えて使用します (次で説明します)。
- アプリケーションを登録したクラウドに応じて、特定の機関を使用します。 さまざまなクラウド用の機関の詳細については、「[Azure AD 認証エンドポイント](authentication-national-cloud.md#azure-ad-authentication-endpoints)」を参照してください。
- Microsoft Graph API を呼び出すには、使用しているクラウドに固有のエンドポイント URL が必要です。 すべての国内クラウド用の Microsoft Graph エンドポイントを見つけるには、「[Microsoft Graph および Graph エクスプローラー サービスのルート エンドポイント](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)」を参照してください。

機関の例は次のとおりです。

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

次に示すのは、Microsoft Graph エンドポイントとスコープの例です。

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

ソブリン クラウドでユーザーを認証し、Microsoft Graph を呼び出すための最小限のコードは次のようになります。

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        redirectUri: "/",
    }
};

// Initialize MSAL
const msalObj = new PublicClientApplication(msalConfig);

// Get token using popup experience
try {
    const graphToken = await msalObj.acquireTokenPopup({
        scopes: ["User.Read"]
    });
} catch(error) {
    console.log(error)
}

// Call the Graph API
const headers = new Headers();
const bearer = `Bearer ${graphToken}`;

headers.append("Authorization", bearer);

fetch("https://graph.microsoft.us/v1.0/me", {
    method: "GET",
    headers: headers
})
```


## <a name="python"></a>[Python](#tab/python)

ソブリン クラウド用の MSAL Python アプリケーションを有効にするには、次のようにします。

- クラウドに応じて、特定のポータルでアプリケーションを登録します。 ポータルの選択方法の詳細については「[アプリ登録エンドポイント](authentication-national-cloud.md#app-registration-endpoints)」を参照してください
- リポジトリにある[サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample)のいずれかを、クラウドに応じて構成にいくつかの変更を加えて使用します (次で説明します)。
- アプリケーションを登録したクラウドに応じて、特定の機関を使用します。 さまざまなクラウド用の機関の詳細については、「[Azure AD 認証エンドポイント](authentication-national-cloud.md#azure-ad-authentication-endpoints)」を参照してください。

    機関の例は次のとおりです。

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Microsoft Graph API を呼び出すには、使用しているクラウドに固有のエンドポイント URL が必要です。 すべての国内クラウド用の Microsoft Graph エンドポイントを見つけるには、「[Microsoft Graph および Graph エクスプローラー サービスのルート エンドポイント](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)」を参照してください。

    次に示すのは、Microsoft Graph エンドポイントとスコープの例です。

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

ソブリン クラウド用の MSAL for Java アプリケーションを有効にするには、次のようにします。

- クラウドに応じて、特定のポータルでアプリケーションを登録します。 ポータルの選択方法の詳細については「[アプリ登録エンドポイント](authentication-national-cloud.md#app-registration-endpoints)」を参照してください
- リポジトリにある[サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)のいずれかを、クラウドに応じて構成にいくつかの変更を加えて使用します (次で説明します)。
- アプリケーションを登録したクラウドに応じて、特定の機関を使用します。 さまざまなクラウド用の機関の詳細については、「[Azure AD 認証エンドポイント](authentication-national-cloud.md#azure-ad-authentication-endpoints)」を参照してください。

機関の例は次のとおりです。

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Microsoft Graph API を呼び出すには、使用しているクラウドに固有のエンドポイント URL が必要です。 すべての国内クラウド用の Microsoft Graph エンドポイントを見つけるには、「[Microsoft Graph および Graph エクスプローラー サービスのルート エンドポイント](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)」を参照してください。

次に示すのは、Graph エンドポイントとスコープの例です。

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

iOS および macOS 用の MSAL は、各国のクラウドでトークンを取得するために使用できますが、`MSALPublicClientApplication` を作成する場合は追加の構成が必要です。

たとえば、お使いのアプリケーションを国内クラウド (ここでは米国政府) のマルチテナント アプリケーションにする場合、次のように記述できます。

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

iOS および macOS 用の MSAL は、各国のクラウドでトークンを取得するために使用できますが、`MSALPublicClientApplication` を作成する場合は追加の構成が必要です。

たとえば、お使いのアプリケーションを国内クラウド (ここでは米国政府) のマルチテナント アプリケーションにする場合、次のように記述できます。

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>次のステップ

各クラウドの Azure portal の URL とトークン エンドポイントの一覧については、[国内クラウドの認証エンドポイント](authentication-national-cloud.md)に関する記事を参照してください。

国内クラウドのドキュメント:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Germany (2021 年 10 月 29 日に終了)](../../germany/index.yml)