---
title: パブリック クライアント アプリケーションを MSAL.NET に移行する
titleSuffix: Microsoft identity platform
description: Azure Active Directory Authentication Library for .NET から Microsoft Authentication Library for .NET へのパブリック クライアント アプリケーションの移行方法について説明します。
services: active-directory
author: sahmalik
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2021
ms.author: sahmalik
ms.reviewer: saeeda, shermanouko, jmprieur
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 0bb1dc80d9bebf0488bfa2a272f5f7fa5303b31b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018017"
---
# <a name="migrate-public-client-applications-from-adalnet-to-msalnet"></a>パブリック クライアント アプリケーションを ADAL.NET から MSAL.NET に移行する

この記事では、Azure Active Directory Authentication Library for .NET (ADAL.NET) から Microsoft Authentication Library for .NET (MSAL.NET) へのパブリック クライアント アプリケーションの移行方法について説明します。 パブリック クライアント アプリケーションは、別のサービスをユーザーに代わって呼び出すデスクトップ アプリ (Win32、WPF、UWP アプリなど) およびモバイル アプリです。 パブリック クライアント アプリケーションについて詳しくは、「[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)」を参照してください。

## <a name="migration-steps"></a>移行の手順

1. アプリで ADAL.NET を使用してコードを検索します。

   パブリック クライアント アプリケーションで ADAL を使用するコードは、`AuthenticationContext` のインスタンスを作成し、次のパラメーターを使用して `AcquireTokenAsync` のオーバーライドを呼び出します。

   - `resourceId` 文字列。 この変数は、呼び出し対象の Web API のアプリ ID の URI です。
   - アプリケーションの識別子 (アプリ ID とも呼ばれる) である `clientId`。

2. ADAL.NET を使用しているアプリがあることを確認したら、MSAL.NET NuGet パッケージ [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) をインストールして、プロジェクト ライブラリの参照を更新します。 詳細については、[NuGet パッケージのインストール](https://www.bing.com/search?q=install+nuget+package)に関するページを参照してください。

3. パブリック クライアント アプリケーションのシナリオに従ってコードを更新します。 いくつかの手順は共通で、パブリック クライアントのすべてのシナリオに適用されます。 それ以外の手順は、各シナリオに固有のものです。 

   パブリック クライアントのシナリオは次のとおりです。

   - Windows で推奨されるブローカーベースの認証である [Web 認証マネージャー](scenario-desktop-acquire-token-wam.md)。
   - ユーザーにサインイン プロセスを完了するための Web ベースのインターフェイスが表示される[対話型認証](scenario-desktop-acquire-token-interactive.md)。
   - ユーザーが、Windows ドメインにサインインするために使用されたのと同じ ID を使用して署名する[統合 Windows 認証](scenario-desktop-acquire-token-integrated-windows-authentication.md) (ドメイン参加済みまたは Azure AD 参加済みマシンの場合)。
   - ユーザー名とパスワードの資格情報を指定することによってサインインが実行される[ユーザー名とパスワード](scenario-desktop-acquire-token-username-password.md)。
   - 制限された UX のデバイスに、代替デバイス上で認証フローを完了するためのデバイス コードが表示される[デバイス コード フロー](scenario-desktop-acquire-token-device-code-flow.md)。


## <a name="interactive"></a>[対話](#tab/interactive)

対話型のシナリオでは、パブリック クライアント アプリケーションにブラウザーでホストされているログイン ユーザー インターフェイスが表示され、ユーザーは対話的にサインインする必要があります。

#### <a name="find-out-if-your-code-uses-interactive-scenarios"></a>コードで対話型のシナリオを使用しているかどうかを確認する

対話型認証を使用するパブリック クライアント アプリケーションの場合、アプリの ADAL コードで `AuthenticationContext` のインスタンスを作成し、次のパラメーターを使用した `AcquireTokenAsync` の呼び出しを含めます。
 - アプリケーションの登録を表す GUID である `clientId`
 - トークンを要求している対象のリソースを示す `resourceUrl`
 - 応答 URLであるURI
 - `PlatformParameters` オブジェクト。 

 #### <a name="update-the-code-for-interactive-scenarios"></a>対話型シナリオ用にコードを更新する

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

この例では、`AuthenticationContext.AcquireTokenAsync` の呼び出しを `IPublicClientApplication.AcquireTokenInteractive` の呼び出しで置き換えます。

対話型シナリオでの ADAL.NET と MSAL.NET のコードの比較を以下に示します。

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
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await ac.AcquireTokenAsync("<clientId>",
                                    "https://resourceUrl",
                                    new Uri("https://ClientReplyUrl"),
                                    new PlatformParameters(PromptBehavior.Auto));
```
:::column-end:::   
:::column span="":::
```csharp
// 1. Configuration - read below about redirect URI
var pca = PublicClientApplicationBuilder.Create("client_id")
              .WithBroker()
              .Build();

// Add a token cache, see https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-net-token-cache-serialization?tabs=desktop

// 2. GetAccounts
var accounts = await pca.GetAccountsAsync();
var accountToLogin = // choose an account, or null, or use PublicClientApplication.OperatingSystemAccount for the default OS account

try
{
    // 3. AcquireTokenSilent 
    var authResult = await pca.AcquireTokenSilent(new[] { "User.Read" }, accountToLogin)
                              .ExecuteAsync();
}
catch (MsalUiRequiredException) // no change in the pattern
{
    // 4. Specific: Switch to the UI thread for next call . Not required for console apps.
    await SwitchToUiThreadAsync(); // not actual code, this is different on each platform / tech

    // 5. AcquireTokenInteractive
    var authResult = await pca.AcquireTokenInteractive(new[] { "User.Read" })
                              .WithAccount(accountToLogin)  // this already exists in MSAL, but it is more important for WAM
                              .WithParentActivityOrWindow(myWindowHandle) // to be able to parent WAM's windows to your app (optional, but highly recommended; not needed on UWP)
                              .ExecuteAsync();
}
```
   :::column-end:::
:::row-end:::

上記の MSAL コードでは、推奨される方法である WAM (Web 認証マネージャー) を使用しています。 WAM を使用せずに対話型認証を使用する場合は、[対話型認証](scenario-desktop-acquire-token-interactive.md)に関するページを参照してください。

## <a name="integrated-windows-authentication"></a>[統合 Windows 認証](#tab/iwa)

統合 Windows 認証では、パブリック クライアント アプリケーションが、Windows ドメインにサインインするために使用されたのと同じ ID を使用してサインインします (ドメイン参加済みまたは Azure AD 参加済みマシンの場合)。

#### <a name="find-out-if-your-code-uses-integrated-windows-authentication"></a>コードで統合 Windows 認証を使用しているかどうかを調べる

アプリの ADAL コードに、次のパラメーターを使用した、`AuthenticationContextIntegratedAuthExtensions` クラスの拡張メソッドとして使用できる `AcquireTokenAsync` の呼び出しが含まれている場合は、そこで統合 Windows 認証シナリオを使用しています。

- トークンを要求する対象のリソースを表す `resource`
- アプリケーションの登録を表す GUID である `clientId`
- トークンを要求する対象のユーザーを表す `UserCredential` オブジェクト。

#### <a name="update-the-code-for-integrated-windows-authentication-scenarios"></a>統合 Windows 認証シナリオ用にコードを更新する

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

この例では、`AuthenticationContext.AcquireTokenAsync` の呼び出しを `IPublicClientApplication.AcquireTokenByIntegratedWindowsAuth` の呼び出しで置き換えます。

統合 Windows 認証シナリオ用の ADAL.NET と MSAL.NET のコードの比較を次に示します。

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
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(resource, clientId,
                                         new UserCredential("john@contoso.com"));
```
:::column-end:::   
:::column span="":::
```csharp
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
   :::column-end:::
:::row-end:::

## <a name="username-password"></a>[ユーザー名とパスワード](#tab/up)

ユーザー名とパスワードの認証では、ユーザー名とパスワードの資格情報を指定することで、サインインが行われます。
#### <a name="find-out-if-your-code-uses-username-password-authentication"></a>コードでユーザー名とパスワードの認証を使用しているかどうかを確認する

アプリの ADAL コードに、`AuthenticationContextIntegratedAuthExtensions` クラスの拡張メソッドとして使用できる、次のパラメーターを使用した `AcquireTokenAsync` の呼び出しが含まれている場合は、ユーザー名とパスワードの認証シナリオを使用しています。

- トークンを要求する対象のリソースを表す `resource`
- アプリケーションの登録を表す GUID である `clientId`
- トークンを要求する対象のユーザーのユーザー名とパスワードを格納している `UserPasswordCredential` オブジェクト。

#### <a name="update-the-code-for-username-password-auth-scenarios"></a>ユーザー名とパスワードの認証シナリオ用にコードを更新する

この例では、`AuthenticationContext.AcquireTokenAsync` の呼び出しを `IPublicClientApplication.AcquireTokenByUsernamePassword` の呼び出しで置き換えます。

ユーザー名とパスワードのシナリオでの ADAL.NET と MSAL.NET のコードの比較を以下に示します。

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

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
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(
   resource, clientId, 
   new UserPasswordCredential("john@contoso.com", johnsPassword));

```
:::column-end:::   
:::column span="":::
```csharp
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
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
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
```
   :::column-end:::
:::row-end:::

## <a name="device-code"></a>[デバイス コード](#tab/devicecode)

デバイス コード フロー認証では、UX が制限されたデバイスに、別のデバイスで認証フローを完了するためのデバイス コードが表示されます。

#### <a name="find-out-if-your-code-uses-device-code-flow-authentication"></a>コードでデバイス コード フロー認証を使用しているかどうかを確認する

アプリの ADAL コードに、次のパラメーターを使用した `AuthenticationContext.AcquireTokenByDeviceCodeAsync` の呼び出しが含まれている場合は、デバイス コード フローのシナリオを使用しています。
- トークンを要求する対象のリソースの `resourceID` を使用して作成されたインスタンスである `DeviceCodeResult` オブジェクト インスタンスと、アプリケーションを表す GUID である `clientId`。

#### <a name="update-the-code-for-device-code-flow-scenarios"></a>デバイス コード フローのシナリオ用にコードを更新する

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

この例では、`AuthenticationContext.AcquireTokenAsync` の呼び出しを `IPublicClientApplication.AcquireTokenWithDeviceCode` の呼び出しで置き換えます。

デバイス コード フローのシナリオでの ADAL.NET と MSAL.NET のコードの比較を以下に示します。

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
static async Task<AuthenticationResult> GetTokenViaCode(AuthenticationContext ctx)
{
 AuthenticationResult result = null;
 try
 {
  result = await ac.AcquireTokenSilentAsync(resource, clientId);
 }
 catch (AdalException adalException)
 {
  if (adalException.ErrorCode == AdalError.FailedToAcquireTokenSilently
   || adalException.ErrorCode == AdalError.InteractionRequired)
  {
  try
  {
   DeviceCodeResult codeResult = await ctx.AcquireDeviceCodeAsync(resource, clientId);
   Console.WriteLine("You need to sign in.");
   Console.WriteLine("Message: " + codeResult.Message + "\n");
   result = await ctx.AcquireTokenByDeviceCodeAsync(codeResult);
  }
  catch (Exception exc)
  {
   Console.WriteLine("Something went wrong.");
   Console.WriteLine("Message: " + exc.Message + "\n");
  }
 }
 return result;
}

```
:::column-end:::   
:::column span="":::
```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
   :::column-end:::
:::row-end:::

---
### <a name="msal-benefits"></a>MSAL の利点

アプリでの MSAL.NET の主な利点は次のとおりです。

- **回復力**。 MSAL.NET は、以下によりアプリの回復力を高めることができます。

   - Azure AD Cached Credential Service (CCS) の利点。 CCS は Azure AD のバックアップとして動作します。
   - 呼び出す API により、[継続的アクセス評価](app-resilience-continuous-access-evaluation.md)によって有効期間の長いトークンが有効になっている場合のトークンの事前更新。

### <a name="troubleshooting"></a>トラブルシューティング

以下のトラブルシューティング情報には、次の 2 つの前提条件があります。 

- ADAL.NET コードが動作していた。
- 同じクライアント ID を保持して、MSAL に移行した。

次のいずれかのメッセージで例外が発生する場合、 

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

以下の手順を使用して、例外をトラブルシューティングできます。

1. 最新バージョンの MSAL.NET を使用していることを確認します。
1. 機密クライアント アプリケーションを構築するときに設定した証明機関ホストと、ADAL で使用した証明機関ホストが類似していることを確認します。 特に、同じ[クラウド](msal-national-cloud.md) (Azure Government、Azure China 21Vianet、または Azure Germany) を使用していることを確認します。

## <a name="next-steps"></a>次の手順

[ADAL.NET と MSAL.NET アプリの違い](msal-net-differences-adal-net.md)についての詳細を確認してください。
[MSAL.NET でのトークン キャッシュのシリアル化](msal-net-token-cache-serialization.md)の詳細を確認してください。
