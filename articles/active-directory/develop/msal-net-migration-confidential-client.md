---
title: 機密クライアント アプリケーションを MSAL.NET に移行する
titleSuffix: Microsoft identity platform
description: Azure Active Directory Authentication Library for .NET から Microsoft Authentication Library for .NET への機密クライアント アプリケーションの移行方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: e00eff9bfaa64abc4d37d7e4f6d66552b2f674cb
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418631"
---
# <a name="migrate-confidential-client-applications-from-adalnet-to-msalnet"></a>機密クライアント アプリケーションを ADAL.NET から MSAL.NET に移行する

この記事では、Azure Active Directory Authentication Library for .NET (ADAL.NET) から Microsoft Authentication Library for .NET (MSAL.NET) への機密クライアント アプリケーションの移行方法について説明します。 機密クライアント アプリケーションとは、自身のために別のサービスを呼び出す Web アプリ、Web API、およびデーモン アプリケーションです。 機密アプリケーションについて詳しくは、「[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)」を参照してください。 アプリが ASP.NET Core に基づいている場合は、[Microsoft.Identity.Web](microsoft-identity-web.md) を使用します。

アプリの登録については以下のとおりです。

- 新しいアプリ登録を作成する必要はありません。 (同じクライアント ID を保持します)。
- 事前承認 (管理者の同意を得た API アクセス許可) を変更する必要はありません。

## <a name="migration-steps"></a>移行の手順

1. アプリで ADAL.NET を使用してコードを検索します。

   機密クライアント アプリケーションで ADAL を使用するコードは、`AuthenticationContext` をインスタンス化し、以下のパラメーターを使用して `AcquireTokenByAuthorizationCode` または `AcquireTokenAsync` の 1 つのオーバーライドを呼び出します。

   - `resourceId` 文字列。 この変数は、呼び出し対象の Web API のアプリ ID の URI です。
   - `IClientAssertionCertificate` のインスタンスまたは `ClientAssertion`。 このインスタンスは、アプリの ID を証明するためのアプリのクライアント資格情報を提供します。

1. ADAL.NET を使用しているアプリがあることを確認したら、MSAL.NET NuGet パッケージ [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) をインストールして、プロジェクト ライブラリの参照を更新します。 詳細については、[NuGet パッケージのインストール](https://www.bing.com/search?q=install+nuget+package)に関するページを参照してください。

1. 機密クライアントのシナリオに従ってコードを更新します。 いくつかの手順は共通で、機密クライアントのすべてのシナリオに適用されます。 それ以外の手順は、各シナリオに固有のものです。 

   機密クライアントのシナリオは次のとおりです。

   - [デーモン シナリオ](?tabs=daemon#migrate-daemon-apps): Web アプリ、Web API、およびデーモン コンソール アプリケーションでサポートされます。
   - [ダウンストリーム Web API を呼び出す Web API](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis): ユーザーの代わりにダウンストリーム Web API を呼び出す Web API でサポートされます。
   - [Web API を呼び出す Web アプリ](?tabs=authcode#migrate-a-web-api-that-calls-downstream-web-apis): ユーザーのサインインを行って、ダウンストリーム API を呼び出す Web アプリでサポートされます。

証明書とキャッシュを処理するために ADAL.NET のラッパーを指定している場合があります。 この記事では、それと同じ方法を使用して、ADAL.NET から MSAL.NET に移行するプロセスについて説明します。 ただし、このコードはデモンストレーションのみを目的としています。 これらのラッパーをコピーして貼り付けたり、コードにそのまま組み込んだりしないでください。

## <a name="daemon"></a>[Daemon](#tab/daemon)

### <a name="migrate-daemon-apps"></a>デーモン アプリを移行する

デーモン シナリオでは、OAuth2.0 [クライアント資格情報フロー](v2-oauth2-client-creds-grant-flow.md)を使用します。 これは、サービス間呼び出しとも呼ばれます。 アプリは、ユーザーのためではなく、自身のためにトークンを取得します。

#### <a name="find-out-if-your-code-uses-daemon-scenarios"></a>コードでデーモン シナリオを使用しているかどうかを確認する

アプリの ADAL コードでは、次のパラメーターを使用した `AuthenticationContext.AcquireTokenAsync` への呼び出しが含まれている場合、デーモン シナリオを使用しています。

- 最初のパラメーターとして、リソース (アプリ ID の URI)
- 2 番目のパラメーターとして、`IClientAssertionCertificate` または `ClientAssertion`

`AuthenticationContext.AcquireTokenAsync` には、`UserAssertion` 型のパラメーターはありません。 それが存在する場合、アプリは Web API であり、[ダウンストリーム Web API を呼び出す Web API](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis) のシナリオを使用しています。

#### <a name="update-the-code-of-daemon-scenarios"></a>デーモン シナリオのコードを更新する

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

この場合、`AuthenticationContext.AcquireTokenAsync` の呼び出しを `IConfidentialClientApplication.AcquireTokenClient` の呼び出しで置き換えます。

デーモン シナリオでの ADAL.NET と MSAL.NET のコードの比較を以下に示します。

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult()
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);


  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                );

  return authResult;
 }
}
```
:::column-end:::   
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult()
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }

  var authResult = await app.AcquireTokenForClient(
              new [] { $"{resourceId}/.default" })
              .ExecuteAsync()
              .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>トークン キャッシュの利点

メモリ内キャッシュの利点を得るには、`IConfidentialClientApplication` のインスタンスをメンバー変数に保持する必要があります。 トークンを要求するたびに機密クライアント アプリケーションを再作成すると、トークン キャッシュの利点は得られません。

既定のメモリ内アプリ トークン キャッシュを使用しない場合は、`AppTokenCache` をシリアル化する必要があります。 同様に、分散トークン キャッシュを実装する場合は、`AppTokenCache` をシリアル化する必要があります。 詳細については、[Web アプリまたは Web API (機密クライアント アプリケーション) 用のトークンのキャッシュ](msal-net-token-cache-serialization.md?tabs=aspnet)に関する記事、およびサンプル [active-directory-dotnet-v1-to-v2/ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache) を参照してください。

[デーモン シナリオの詳細](scenario-daemon-overview.md)と、新しいアプリケーションで MSAL.NET または Microsoft.Identity.Web を使用して実装する方法について確認してください。

## <a name="web-api-calling-downstream-web-apis"></a>[ダウンストリーム Web API を呼び出す Web API](#tab/obo)

### <a name="migrate-a-web-api-that-calls-downstream-web-apis"></a>ダウンストリーム Web API を呼び出す Web API を移行する

ダウンストリーム Web API を呼び出す Web API は、OAuth2.0 [on-behalf-of (OBO)](v2-oauth2-on-behalf-of-flow.md) フローを使用します。 Web API のコードでは、HTTP 承認ヘッダーから取得したトークンを使用し、それを検証します。 このトークンは、ダウンストリーム Web API を呼び出すトークンに対して交換されます。 このトークンは、ADAL.NET と MSAL.NET の両方で `UserAssertion` インスタンスとして使用されます。

#### <a name="find-out-if-your-code-uses-obo"></a>コードで OBO を使用しているかどうかを確認する

アプリの ADAL コードで、次のパラメーターを使用した `AuthenticationContext.AcquireTokenAsync` への呼び出しが含まれている場合、OBO を使用しています。

- 最初のパラメーターとして、リソース (アプリ ID の URI)
- 2 番目のパラメーターとして、`IClientAssertionCertificate` または `ClientAssertion`
- `UserAssertion` 型のパラメーター

#### <a name="update-the-code-by-using-obo"></a>OBO を使用してコードを更新する

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

この例では、`AuthenticationContext.AcquireTokenAsync` の呼び出しを `IConfidentialClientApplication.AcquireTokenOnBehalfOf` の呼び出しで置き換えます。

ADAL.NET と MSAL.NET の OBO サンプル コードの比較を以下に示します。

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
:::column span="":::
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId, 
  string tokenUsedToCallTheWebApi)
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                      userAssertion,
                                );

  return authResult;
 }
}
```
:::column-end:::
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string tokenUsedToCallTheWebApi)
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }


  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await app.AcquireTokenOnBehalfOf(
              new string[] { $"{resourceId}/.default" },
              userAssertion)
              .ExecuteAsync()
              .ConfigureAwait(false);
  
  return authResult;
 }
}
```
:::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>トークン キャッシュの利点

OBO のトークン キャッシュでは、分散トークン キャッシュを使用する必要があります。 詳細については、[Web アプリまたは Web API (機密クライアント アプリケーション) 用のトークン キャッシュ](msal-net-token-cache-serialization.md?tabs=aspnet)に関する記事、および[サンプル コード](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)を参照してください。

```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

[ダウンストリーム Web API を呼び出す Web API の詳細](scenario-web-api-call-api-overview.md)と、新しいアプリケーションで MSAL.NET または Microsoft.Identity.Web を使用して実装する方法について確認してください。

## <a name="web-app-calling-web-apis"></a>[Web API を呼び出す Web アプリ](#tab/authcode)

### <a name="migrate-a-web-app-that-calls-web-apis"></a>Web API を呼び出す Web アプリを移行する

アプリで ASP.NET Core を使用している場合は、Microsoft Identity. Web に更新して、すべての処理を自動で行うことを強くお勧めします。 簡単なプレゼンテーションについて、[Microsoft.Identity.Web の一般提供に関するお知らせ](https://github.com/AzureAD/microsoft-identity-web/wiki/1.0.0)に関するページをご覧ください。 Web アプリでの使用方法の詳細については、[Web アプリで Microsoft.Identity.Web を使用する理由](https://aka.ms/ms-id-web/webapp)に関するページを参照してください。

ユーザーのサインインを実行し、ユーザーの代わりに Web API を呼び出す Web アプリは、OAuth2.0 [承認コード フロー](v2-oauth2-auth-code-flow.md)を使用します。 通常の動作は次のとおりです。

1. Web アプリは、承認コード フローの第一段階を実行して、ユーザーのサインインを実行します。 これを行うために、Azure Active Directory (Azure AD) の承認エンドポイントに移動します。 ユーザーはサインインし、必要に応じて多要素認証を実行します。 この操作の結果として、アプリは承認コードを受け取ります。 ここまでは、ADAL と MSAL は関係していません。
2. アプリは、承認コード フローの第二段階を実行します。 承認コードを使用して、アクセス トークン、ID トークン、更新トークンを取得します。 アプリケーションで `redirectUri` の値を指定する必要があります。これは、Azure AD がセキュリティ トークンを提供する URI です。 アプリは、この URI を受信した後、通常は ADAL または MSAL の `AcquireTokenByAuthorizationCode` を呼び出してコードを引き換え、トークン キャッシュに格納されるトークンを取得します。
3. アプリは ADAL または MSAL を使用して `AcquireTokenSilent` を呼び出し、必要な Web API を呼び出すためのトークンを取得できるようにします。 これは、Web アプリ コントローラーから実行されます。

#### <a name="find-out-if-your-code-uses-the-auth-code-flow"></a>コードが承認コード フローを使用しているかどうかを確認する

アプリの ADAL コードは、`AuthenticationContext.AcquireTokenByAuthorizationCodeAsync` の呼び出しが含まれている場合に、承認コード フローを使用しています。

#### <a name="update-the-code-by-using-the-authorization-code-flow"></a>承認コード フローを使用してコードを更新する

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)] 

この例では、`AuthenticationContext.AcquireTokenAsync` の呼び出しを `IConfidentialClientApplication.AcquireTokenByAuthorizationCode` の呼び出しで置き換えます。

ADAL.NET と MSAL.NET のサンプルの承認コード フローの比較を以下に示します。

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
```csharp
public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
     = "https://login.microsoftonline.com/common";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string authorizationCode)
 {


  var ac = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var authResult = await ac.AcquireTokenByAuthorizationCodeAsync(
                                      authorizationCode,
                                      redirectUri,
                                      clientAssertionCert,
                                      resourceId,
                                );
  return authResult;
 }
}
```      
   :::column-end:::
   :::column span="":::
```csharp
public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority 
     = "https://login.microsoftonline.com/{tenant}";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string authorizationCode)
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .WithRedirectUri(redirectUri.ToString())
           .Build();
  }

  var authResult = await app.AcquireTokenByAuthorizationCode(
              new [] { $"{resourceId}/.default" },
              authorizationCode)
              .ExecuteAsync()
              .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

`AcquireTokenByAuthorizationCode` を呼び出すと、トークンがトークン キャッシュに追加されます。 他のリソースまたはテナント用に追加のトークンを取得するには、コントローラーで `AcquireTokenSilent` を使用します。

#### <a name="benefit-from-token-caching"></a>トークン キャッシュの利点

Web アプリで `AcquireTokenByAuthorizationCode` を使用するため、アプリはトークン キャッシュに分散トークン キャッシュを使用する必要があります。 詳細については、[Web アプリまたは Web API のトークン キャッシュ](msal-net-token-cache-serialization.md?tabs=aspnet)に関するページ、および[サンプル コード](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)を参照してください。


```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```


[Web API を呼び出す Web アプリの詳細](scenario-web-app-call-api-overview.md)と、新しいアプリケーションで MSAL.NET または Microsoft.Identity.Web を使用して実装する方法について確認してください。

---

## <a name="msal-benefits"></a>MSAL の利点

アプリでの MSAL.NET の主な利点は次のとおりです。

- **回復力**。 MSAL.NET は、以下によりアプリの回復力を高めることができます。

   - Azure AD Cached Credential Service (CCS) の利点。 CCS は Azure AD のバックアップとして動作します。
   - 呼び出す API により、[継続的アクセス評価](app-resilience-continuous-access-evaluation.md)によって有効期間の長いトークンが有効になっている場合のトークンの事前更新。

- **セキュリティ**。 呼び出す Web API で必要な場合は、所有証明 (PoP) トークンを取得できます。 詳細については、[MSAL.NET での所有証明トークン](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Proof-Of-Possession-(PoP)-tokens)に関するページを参照してください。

- **パフォーマンスとスケーラビリティ**。 ADAL.NET でキャッシュを共有する必要がない場合は、機密クライアント アプリケーションを作成するときに、レガシ キャッシュの互換性を無効にします (`.WithLegacyCacheCompatibility(false)`)。 これにより、パフォーマンスが大幅に向上します。
  
  ```csharp
  app = ConfidentialClientApplicationBuilder.Create(ClientId)
          .WithCertificate(certificate)
          .WithAuthority(authority)
          .WithLegacyCacheCompatibility(false)
          .Build();
  ```

## <a name="troubleshooting"></a>トラブルシューティング

以下のトラブルシューティング情報には、次の 2 つの前提条件があります。 

- ADAL.NET コードが動作していた。
- 同じクライアント ID を保持して、MSAL に移行した。

次のいずれかのメッセージで例外が発生する場合、 

> `AADSTS700027: Client assertion contains an invalid signature. [Reason - The key was not found.]`

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

以下の手順を使用して、例外をトラブルシューティングできます。

1. 最新バージョンの MSAL.NET を使用していることを確認します。
1. 機密クライアント アプリケーションを構築するときに設定した証明機関ホストと、ADAL で使用した証明機関ホストが類似していることを確認します。 特に、同じ[クラウド](msal-national-cloud.md) (Azure Government、Azure China 21Vianet、または Azure Germany) を使用していることを確認します。

## <a name="next-steps"></a>次の手順

[ADAL.NET と MSAL.NET アプリの違い](msal-net-differences-adal-net.md)についての詳細を確認してください。
[MSAL.NET でのトークン キャッシュのシリアル化](msal-net-token-cache-serialization.md)の詳細を確認してください。