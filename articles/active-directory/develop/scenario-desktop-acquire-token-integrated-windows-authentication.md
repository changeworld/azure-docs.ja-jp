---
title: Web API を呼び出し、統合 Windows 認証でトークンを取得する (デスクトップ アプリ) | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出し、統合 Windows 認証でアプリのトークンを取得するデスクトップ アプリを構築する方法について説明します
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: f589603fc5a7bb1b9e3a62d997cf9e64efebf5af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017941"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-integrated-windows-authentication"></a>Web API を呼び出すデスクトップ アプリ: 統合 Windows 認証でトークンを取得する

ドメインまたは Azure AD 参加済みのマシンでドメイン ユーザーをサインインさせるには、統合 Windows 認証 (IWA) を使用します。

## <a name="constraints"></a>制約

- 統合 Windows 認証は、"*フェデレーション+* " ユーザー (Active Directory で作成され、Azure AD によってサポートされているユーザー) に対してのみ使用できます。 Azure AD で直接作成され、Active Directory のサポートのないユーザー ("*マネージド*" ユーザーと呼ばれます) はこの認証フローを使用できません。 この制限は、ユーザー名とパスワードのフローには影響しません。
- IWA では[多要素認証 (MFA)](../authentication/concept-mfa-howitworks.md) はバイパスされません。 MFA が構成されている状況では、MFA チャレンジが必要な場合に IWA が失敗する可能性があります。これは、MFA でユーザーの操作が必要になるためです。

    IWA は非対話型ですが、MFA にはユーザーの操作が必要です。 ID プロバイダーが MFA の実行を要求するタイミングの制御は、ユーザーではなくテナント管理者が行います。 弊社の観測によると、MFA が必要なのは、他の国/地域からサインインする場合と VPN 経由で企業ネットワークに接続されていない場合です。ただし、VPN 経由で接続されている場合であっても MFA が必要になる可能性があります。 確定的なルール セットを想定しないでください。 Azure AD では、AI を使用して、MFA が必要かどうかを継続的に学習します。 IWA が失敗した場合は、対話型認証やデバイス コード フローなどのユーザー プロンプトにフォールバックしてください。

- `PublicClientApplicationBuilder` で渡される機関の要件は次のとおりです。
  - `https://login.microsoftonline.com/{tenant}/` の形式でテナント化されている。ここで、`tenant` は、テナント ID を表す GUID またはテナントに関連付けられているドメインです。
  - 任意の職場および学校アカウント用である: `https://login.microsoftonline.com/organizations/`。
  - Microsoft 個人アカウントはサポートされていません。 /common および /consumers テナントを使用することはできません。

- 統合 Windows 認証はサイレント フローであるため、次の要件が適用されます。
  - アプリケーションのユーザーが、アプリケーションの使用に事前に同意しておく必要があります。
  - または、テナント管理者が、テナント内のすべてのユーザーによるアプリケーションの使用に事前に同意しておく必要があります。
  - つまり、以下の要件が適用されます。
    - 開発者が自分で Azure portal 上の **[許可]** ボタンを選択しておきます。
    - または、テナント管理者がアプリケーションの登録の **[API のアクセス許可]** タブにある **[{テナント ドメイン} の管理者の同意を付与/取り消す]** ボタンを選択しておきます。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)」を参照してください。
    - または、ユーザーがアプリケーションに同意する方法を指定しておきます。 詳細については、「[個々のユーザーの同意を要求する](./v2-permissions-and-consent.md#requesting-individual-user-consent)」を参照してください。
    - または、テナント管理者がアプリケーションに同意する方法を指定しておきます。 詳細については、[管理者の同意](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)に関する記事を参照してください。

- このフローは、.NET デスクトップ、.NET Core、および UWP の各アプリに対して有効です。

同意の詳細については、[Microsoft ID プラットフォームでのアクセス許可と同意](./v2-permissions-and-consent.md)に関する記事を参照してください。

## <a name="learn-how-to-use-it"></a>使用方法の確認

# <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET では、次のように使用します。

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

通常、必要なパラメーターは 1 つだけです (`scopes`)。 Windows 管理者によるポリシーの設定方法に応じて、Windows マシン上のアプリケーションではサインインしているユーザーの検索が許可されない場合があります。 その場合は、2 番目のメソッドである `.WithUsername()` を使用して、サインインしているユーザーのユーザー名を UPN 形式で渡します (例: `joe@contoso.com`)。

次のサンプルは、最新のケース (および取得可能な例外の種類の説明とその軽減策) を示しています。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }

 Console.WriteLine(result.Account.Username);
}
```

AcquireTokenByIntegratedWindowsAuthentication で使用可能な修飾子の一覧については、[AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods) に関する記事を参照してください。

# <a name="java"></a>[Java](#tab/java)

この抜粋は、[MSAL Java dev サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)から取得したものです。

```java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

このフローは macOS には適用されません。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

このフローは、MSAL ノードではまだサポートされていません。

# <a name="python"></a>[Python](#tab/python)

このフローは、MSAL Python ではまだサポートされていません。

---
## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である「[デスクトップ アプリから Web API を呼び出す](scenario-desktop-call-api.md)」に進みます。
