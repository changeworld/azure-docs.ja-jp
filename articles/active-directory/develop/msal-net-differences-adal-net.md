---
title: ADAL.NET アプリと MSAL.NET アプリの違い | Azure
titleSuffix: Microsoft identity platform
description: .NET 用 Microsoft 認証ライブラリ (MSAL.NET) と .NET 用 Azure AD 認証ライブラリ (ADAL.NET) の違いについて学習します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 06/09/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 5dfc0c3c006c42f6e56e9f2f15311a2b99396fe4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424099"
---
# <a name="differences-between-adalnet-and-msalnet-apps"></a>ADAL.NET アプリと MSAL.NET アプリの違い

ADAL を使用するアプリケーションを MSAL を使用するように移行すると、セキュリティと回復性の利点が得られます。 この記事では、MSAL.NET と ADAL.NET の違いについて説明します。 ほとんどの場合、MSAL.NET と Microsoft ID プラットフォーム (最新世代の Microsoft 認証ライブラリ) を使用します。 MSAL.NET を使用して、Azure AD (職場と学校のアカウント)、Microsoft (個人用) アカウント (MSA)、または Azure AD B2C でアプリケーションにサインインしているユーザーのためにトークンを取得します。

ADAL.NET と開発者向け Azure AD (v1.0) エンドポイントについて既によく理解している場合は、[Microsoft ID プラットフォームの違い](../azuread-dev/azure-ad-endpoint-comparison.md)に関するページを参照してください。 アプリケーションで以前のバージョンの [Active Directory フェデレーション サービス (AD FS)](/windows-server/identity/active-directory-federation-services) を使用してユーザーをサインインさせる必要がある場合は、引き続き ADAL.NET を使用する必要があります。 詳細については、「[ADFS support](https://aka.ms/msal-net-adfs-support)」 (ADFS のサポート) を参照してください。

|   | **ADAL.NET**  | **MSAL.NET**  |
|-----------------------------|--------------------------------------------|---------------------|
| **NuGet パッケージと名前空間**  |ADAL は [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet パッケージから使用されていました。 名前空間は `Microsoft.IdentityModel.Clients.ActiveDirectory` でした。  | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet パッケージを追加し、`Microsoft.Identity.Client` 名前空間を使用します。 機密クライアント アプリケーションを構築する場合は、「[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)」を確認してください。 |
| **スコープとリソース**  | ADAL.NET では "*リソース*" のトークンが取得されます。 |  MSAL.NET では "*スコープ*" のトークンが取得されます。 複数の MSAL.NET `AcquireTokenXXX` オーバーライドでは、スコープ (`IEnumerable<string> scopes`) というパラメーターが必要になります。 このパラメーターは、要求されるアクセス許可とリソースを宣言する文字列のシンプルなリストです。 よく知られているスコープは、[Microsoft Graph のスコープ](/graph/permissions-reference)です。 [MSAL.NET を使用して v1.0 リソースにアクセスする](#scopes)こともできます。 |
| **コア クラス**  | ADAL.NET では、機関経由の、セキュリティ トークン サービス (STS) または承認サーバーへの接続の表現として、[AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) を使用していました。 | MSAL.NET は[クライアント アプリケーション](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications)を中心として設計されています。 パブリック クライアント アプリケーション用の `IPublicClientApplication` インターフェイスと、機密クライアント アプリケーション用の `IConfidentialClientApplication`、および両方の種類のアプリケーションに共通のコントラクト用の基底インターフェイス `IClientApplicationBase` が定義されます。|
| **トークンの取得**  | パブリック クライアントでは、ADAL は `AcquireTokenAsync` と `AcquireTokenSilentAsync` を認証の呼び出しに使用します。 | パブリック クライアントでは、MSAL は `AcquireTokenInteractive` と `AcquireTokenSilent` を同じ認証の呼び出しに使用します。 パラメーターは ADAL のものとは異なります。 <br><br>機密クライアント アプリケーションでは、シナリオに応じて明示的な名前を持つ[トークンの取得方法](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-Tokens)があります。 もう 1 つの違いは、MSAL.NET では、AcquireTokenXX の呼び出しごとにアプリケーションの `ClientID` を渡す必要がなくなったことです。 `ClientID` は、`IPublicClientApplication` または `IConfidentialClientApplication` の構築時に 1 回だけ設定されます。|
|  **IAccount と IUser**  | ADAL では、IUser インターフェイス経由でユーザーの概念が定義されます。 ただし、ユーザーとは人間またはソフトウェア エージェントです。 そのため、ユーザーは Microsoft ID プラットフォームで 1 つ以上のアカウント (複数の Azure AD アカウント、Azure AD B2C、Microsoft の個人用アカウント) を所有することができます。 ユーザーは、1 つ以上の Microsoft ID プラットフォームのアカウントを担当することもできます。 | MSAL.NET では、(IAccount インターフェイス経由で) アカウントの概念が定義されます。 IAccount インターフェイスは 1 つのアカウントに関する情報を表します。 ユーザーは、テナントごとに複数のアカウントを持つことができます。 MSAL.NET では、ホーム アカウント情報が提供されるので、ゲスト シナリオより詳細な情報が提供されます。 詳細については、[IUser と IAccount の違い](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/msal-net-2-released#iuser-is-replaced-by-iaccount)に関するページを参照してください。|
|  **キャッシュの永続化**  | ADAL.NET では、`TokenCache` クラスを拡張し、セキュリティで保護されたストレージ (.NET Framework と .NET Core) のないプラットフォームで、`BeforeAccess` および `BeforeWrite` メソッドを使用して、目的の永続化機能を実装することができます。 詳細については、[ADAL.NET でのトークン キャッシュのシリアル化](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)に関するページを参照してください。 | MSAL.NET では、トークン キャッシュがシールド クラスとなり、拡張することはできません。 そのため、トークン キャッシュの永続化の実装は、シールド トークン キャッシュと対話するヘルパー クラスの形式でなければなりません。 この対話については、[MSAL.NET でのトークン キャッシュのシリアル化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)に関する記事で説明されています。 パブリック クライアント アプリケーション ([パブリック クライアント アプリケーション用のトークン キャッシュ](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)に関するページを参照) と、機密クライアント アプリケーション ([Web アプリまたは Web API 用のトークン キャッシュ](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)に関するページを参照) では、シリアル化は異なります。 |
| **共通の機関** | ADAL では Azure AD v1.0 を使用します。 Azure AD v1.0 の `https://login.microsoftonline.com/common` 機関 (ADAL が使用) では、ユーザーは任意の AAD 組織 (職場または学校) アカウントを使用してサインインできます。 Azure AD v1.0 では、Microsoft の個人用アカウントでサインインすることはできません。 詳細については、[ADAL.NET での機関の検証](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)に関するページを参照してください。 | MSAL では Azure AD v2.0 を使用します。 Azure AD v2.0 の `https://login.microsoftonline.com/common` 機関 (MSAL が使用) では、ユーザーは任意の AAD 組織 (職場または学校) アカウントまたは Microsoft の個人用アカウントを使用してサインインできます。 MSAL での組織アカウント (職場または学校アカウント) のみを使用したサインインを制限するには、`https://login.microsoftonline.com/organizations` エンドポイントを使用する必要があります。  詳細については、[パブリック クライアント アプリケーション](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication)に関するページの `authority` パラメーターについての記事を参照してください。 |

## <a name="supported-grants"></a>サポートされている許可

次に示すのは、パブリック クライアント アプリケーションと機密クライアント アプリケーションの両方に対して MSAL.NET と ADAL.NET でサポートされている許可を比較してまとめたものです。

### <a name="public-client-applications"></a>パブリック クライアント アプリケーション

次の図は、パブリック クライアント アプリケーションの ADAL.NET と MSAL.NET の違いをいくつかまとめたものです。

[![パブリック クライアント アプリケーションの ADAL.NET と MSAL.NET のいくつのか違いを示すスクリーンショット。](media/msal-compare-msaldotnet-and-adaldotnet/differences.png)](media/msal-compare-msaldotnet-and-adaldotnet/differences.png#lightbox)

以下は、デスクトップおよびモバイル アプリケーション用の ADAL.NET と MSAL.NET でサポートされている許可です。

Grant                             | MSAL.NET                                                                                                                     | ADAL.NET                                                                                                                                                                                                   |
--------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
Interactive                       | [MSAL.NET での対話型のトークンの取得](scenario-desktop-acquire-token-interactive.md)    | [対話型認証](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows)                                              |
統合 Windows 認証 | [統合 Windows 認証](scenario-desktop-acquire-token-integrated-windows-authentication.md)         | [Windows での統合認証 (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos))  |
ユーザー名/パスワード               | [ユーザー名とパスワード認証](scenario-desktop-acquire-token-username-password.md)                      | [ユーザー名とパスワードでのトークンの取得](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)                                        |
デバイス コード フロー                  | [デバイス コード フロー](scenario-desktop-acquire-token-device-code-flow.md)                    | [Web ブラウザーを使用しないデバイスのデバイス プロファイル](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers)                                |

### <a name="confidential-client-applications"></a>機密クライアント アプリケーション

次の図は、機密クライアント アプリケーションの ADAL.NET と MSAL.NET の違いをいくつかまとめたものです。
 
[![機密クライアント アプリケーションの ADAL.NET と MSAL.NET のいくつのか違いを示すスクリーンショット。](media/msal-net-migration/confidential-client-application.png)](media/msal-net-migration/confidential-client-application.png#lightbox)


以下は、Web アプリケーション、Web API、デーモン アプリケーション用の ADAL.NET、MSAL.NET、および Microsoft.Identity.Web でサポートされている許可です。

アプリの種類              | Grant              | MSAL.NET                                                                                                                             | ADAL.NET                                                                                                                                                                                              |
------------------------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
Web アプリ、Web API、デーモン | クライアントの資格情報 | [MSAL.NET のクライアント資格情報フロー](scenario-daemon-acquire-token.md#acquiretokenforclient-api)                        | [ADAL.NET のクライアント資格情報フロー](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows)                                                               |
Web API                  | 次の代理       | [MSAL.NET での次の代理](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/on-behalf-of)                 | [ADAL.NET でのユーザーの代わりのサービス間呼び出し](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user)               |
Web アプリ                  | 承認コード          | [MSAL.NET を使用する Web アプリの承認コードでのトークンの取得](scenario-web-app-call-api-acquire-token.md)  | [ADAL.NET を使用する Web アプリの承認コードでのトークンの取得](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) |

## <a name="migrating-from-adal-2x-with-refresh-tokens"></a>更新トークンを使用した ADAL 2.x からの移行

ADAL.NET v2.X では、更新トークンが公開されました。これにより、これらのトークンをキャッシュし、ADAL 2.x で提供される `AcquireTokenByRefreshToken` メソッドを使用することで、そのトークンの使用に関するソリューションを開発することができるようになります。

これらのソリューションのいくつかは、次のようなシナリオで使用されました。

- ユーザーがアプリに接続されなくなった、またはサインインできなくなった場合にダッシュボードの更新などのアクションをユーザーの代わりに行う実行時間の長いサービス。
- クライアントが更新トークンを Web サービスに渡せるようにする WebFarm シナリオ (キャッシュはサーバー側ではなく、クライアント側で行われます (暗号化された Cookie))。

MSAL.NET では、セキュリティ上の理由により、更新トークンは公開されません。 MSAL によって、トークンの更新が処理されます。

さいわい、MSAL.NET では、API で以前の更新トークン (ADAL で取得) を `IConfidentialClientApplication` に移行することができます。

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration
/// scenarios where you have a RefreshToken available.
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint.
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```

この方法では、必要なスコープ (リソース) と共に、以前使用された更新トークンを提供することができます。 更新トークンは新しいものと交換され、アプリケーションにキャッシュされます。

この方法は、一般的ではないシナリオ用であるため、最初に `IByRefreshToken` にキャストしないと、`IConfidentialClientApplication` で簡単にはアクセスできません。

次のコード スニペットでは、機密クライアント アプリケーションでの移行コードがいくつか示されています。

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;

AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

`GetCachedRefreshTokenForSignedInUser` では、ADAL 2.x を使用していた以前のバージョンのアプリケーションによって、ストレージに格納された更新トークンを取得します。 `GetTokenCacheForSignedInUser` では、サインインしたユーザーのためにキャッシュを逆シリアル化します (機密クライアント アプリケーションでユーザーごとに 1 つのキャッシュが必要であるため)。

新しい更新トークンがキャッシュに格納されている間、アクセス トークンと ID トークンが `AuthenticationResult` の値で返されます。 更新トークンが使用可能なさまざまな統合シナリオで、この方法を使用することもできます。

## <a name="v10-and-v20-tokens"></a>v1.0 トークンと v2.0 トークン

トークンには v1.0 トークンと v2.0 トークンの 2 つのバージョンがあります。 v1.0 エンドポイント (ADAL で使用) では v1.0 ID トークンが出力され、v2.0 エンドポイント (MSAL で使用) では v2.0 ID トークンが出力されます。 ただし、どちらのエンドポイントでも、Web API で受け入れられるバージョンのトークンのアクセス トークンが出力されます。 Web API のアプリケーション マニフェストのプロパティでは、開発者は、受け入れられるトークンのバージョンを選択することができます。 [アプリケーション マニフェスト](reference-app-manifest.md)に関するリファレンス ドキュメントの `accessTokenAcceptedVersion` についての記事を参照してください。

v1.0 アクセス トークンと v2.0 アクセス トークンの詳細については、[Azure Active Directory アクセス トークン](access-tokens.md)に関するページを参照してください。

## <a name="exceptions"></a>例外

### <a name="interaction-required-exceptions"></a>対話に必要な例外

MSAL.NET を使用して、`MsalUiRequiredException` をキャッチします ([AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token) に関するページを参照)。

```csharp
catch(MsalUiRequiredException exception)
{
 try {"try to authenticate interactively"}
}
```

詳細については、「[MSAL.NET におけるエラーと例外の処理](msal-error-handling-dotnet.md)」を参照してください。

ADAL.NET には、あまり明示的でない例外がありました。 たとえば、ADAL でサイレント認証が失敗した場合のプロシージャは、例外をキャッチして、`user_interaction_required` エラー コードを探すことでした。

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

詳細については、ADAL.NET を使用して[パブリック クライアント アプリケーションでトークンを取得する場合に推奨されるパターン](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token)に関するページを参照してください。

### <a name="prompt-behavior"></a>プロンプトの動作

MSAL.NET のプロンプトの動作は、ADAL.NET のプロンプトの動作と同じです。

|  ADAL.NET | MSAL.NET | 説明 |
| ----------- | ----------- | -------------|
| `PromptBehavior.Auto`| `NoPrompt`| Azure AD により、最適な動作が選ばれます (ユーザーがサインインに 1 つのアカウントのみを使っている場合は自動的にサインインされ、サインインに複数のアカウントを使っている場合アカウント セレクターが表示されます)。 |
| `PromptBehavior.Always`| `ForceLogin` | サインイン ボックスをリセットし、ユーザーに資格情報を再入力させます。 |
| `PromptBehavior.RefreshSession`| `Consent`| すべてのアクセス許可に対してユーザーに再度同意させます。 |
| `PromptBehavior.Never`| `Never`| 使わないでください。代わりに[パブリック クライアント アプリの推奨パターン](scenario-desktop-acquire-token.md?tabs=dotnet)を使ってください。 |
| `PromptBehavior.SelectAccount`| `SelectAccount`| アカウント セレクターを表示し、ユーザーにアカウントを選ばせます。 |

### <a name="handling-claim-challenge-exceptions"></a>要求チャレンジ例外の処理

トークンの取得時には、リソースでユーザーからの要求がさらに必要な場合 (2 要素認証など)、Azure AD によって例外がスローされます。

MSAL.NET では、要求チャレンジ例外が次のように処理されます。

- `Claims` は `MsalServiceException` に表示されます。
- `AcquireTokenXXX` ビルダーに適用できる `.WithClaim(claims)` メソッドがあります。

詳細については、[MsalUiRequiredException の処理](msal-error-handling-dotnet.md#msaluirequiredexception)に関するページを参照してください。

ADAL.NET では、要求チャレンジ例外が次のように処理されていました。

- `AdalClaimChallengeException` は例外 (`AdalServiceException` から派生) です。 `Claims` メンバーには、予期される要求がある JSON フラグメントがいくつか含まれています。 
- この例外を受け取るパブリック クライアント アプリケーションでは、要求パラメーターを指定して `AcquireTokenInteractive` オーバーライドを呼び出す必要がありました。 この `AcquireTokenInteractive` のオーバーライドでは、キャッシュのヒットは不要であるため試行もされません。 理由は、キャッシュ内のトークンに適切な要求がないためです (それ以外の場合、`AdalClaimChallengeException` はスローされていません)。 そのため、キャッシュを確認する必要はありません。 `ClaimChallengeException` は、OBO を行う WebAPI で受け取ることができますが、`AcquireTokenInteractive` は、この Web API を呼び出すパブリック クライアント アプリケーションで呼び出す必要があります。

サンプルを含む詳細については、[AdalClaimChallengeException の処理](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)に関するページを参照してください。


## <a name="scopes"></a>スコープ

ADAL では `resourceId` 文字列を含むリソースの概念が使用されますが、MSAL.NET ではスコープが使用されます。 Azure AD で使用されるロジックは次のとおりです。

- v1.0 アクセス トークン (使用可能な場合のみ) を使用する ADAL (v1.0) エンドポイントの場合、`aud=resource` となります。
- v2.0 トークンを受け入れるリソースのためにアクセス トークンを要求する MSAL (v2.0 エンドポイント) の場合は、`aud=resource.AppId` となります。
- v1.0 アクセス トークンを受け入れるリソースのためにアクセス トークンを要求する MSAL (v2.0 エンドポイント) の場合、Azure AD では、要求されたスコープから目的の対象ユーザーを解析します。 これは、最後のスラッシュの前のすべてを取得し、それをリソース識別子として使用して行います。 そのため、`https://database.windows.net` で `https://database.windows.net/` の対象ユーザーが予期される場合、`https://database.windows.net//.default` のスコープを要求する必要があります (./default の前の二重スラッシュに注意してください)。 これは、次の例 1 と例 2 で示されています。

### <a name="example-1"></a>例 1

v1.0 トークンを受け入れるアプリケーションのためにトークンを取得する場合 (Microsoft Graph API (`https://graph.microsoft.com`) など)、目的のリソース識別子とそのリソースの目的の OAuth2 アクセス許可を連結して、`scopes` を作成する必要があります。

たとえば、App ID URI が `ResourceId` である v1.0 Web API を使用してユーザーの名前にアクセスするには、以下を使用します。

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Microsoft Graph API (`https://graph.microsoft.com/`) を使用して、MSAL.NET Azure Active Directory で読み取りと書き込みを行う場合は、次のコード スニペットのようにスコープのリストを作成します。

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

### <a name="example-2"></a>例 2

resourceId が "/" で終わる場合は、スコープ値を書き込むときに二重の "/" を使用する必要があります。 たとえば、Azure Resource Manager API (`https://management.core.windows.net/`) に対応するスコープを書き込む場合は、次のスコープを要求します (2 つのスラッシュに注意してください)。

```csharp
var resource = "https://management.core.windows.net/"
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

これは、Resource Manager API ではその対象ユーザーの要求 (`aud`) でスラッシュが予期されるためです。したがって、API 名をスコープと区別するためのスラッシュがあります。

v1.0 アプリケーションのすべての静的なスコープのためにトークンを取得する場合は、次のコード スニペットに示すようにスコープのリストを作成します。

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

クライアント資格情報フローの場合、`/.default` スコープも渡します。 このスコープにより、管理者がアプリケーションの登録で同意したアプリレベルのすべてのアクセス許可が、Azure AD に示されます。

## <a name="next-steps"></a>次の手順

[アプリを ADAL から MSAL に移行する](msal-net-migration.md)
[MSAL.NET を使用するために ADAL.NET 機密クライアント アプリを移行する](msal-net-migration-confidential-client.md)
