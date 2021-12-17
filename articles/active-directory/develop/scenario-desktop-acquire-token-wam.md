---
title: Web API を呼び出すためのトークンを Web アカウント マネージャーを使用して取得する (デスクトップ アプリ) | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出し、Web アカウント マネージャーを使用してアプリのトークンを取得するデスクトップ アプリの構築方法について説明します
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
ms.openlocfilehash: b06c771d14d0257ac69db9d3a9a40d3eadeba3e7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436262"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-wam"></a>Web API を呼び出すデスクトップ アプリ: WAM を使用してトークンを取得する

MSAL を使用すると、OS に付属の Windows 10 コンポーネントである Web アカウント マネージャーを呼び出すことができます。 このコンポーネントは認証ブローカーとして機能し、アプリのユーザーは、Windows セッションでサインインしたアカウントなど、Windows で認識されているアカウントとの統合によるベネフィットを得ることができます。

## <a name="availability"></a>可用性

MSAL 4.25 以降は、UWP、.NET クラシック、.NET Core 3.x、.NET 5 で WAM がサポートされます。

.NET クラシックおよび .NET Core 3.x の場合、WAM 機能は完全にサポートされますが、MSAL と共に [Microsoft.Identity.Client.Desktop](https://www.nuget.org/packages/Microsoft.Identity.Client.Desktop/) パッケージへの参照を追加し、`WithBroker()` ではなく `.WithWindowsBroker()` を呼び出す必要があります。

.NET 5 の場合は、`net5.0` だけではなく `net5.0-windows10.0.17763.0` (またはそれ以降) をターゲットにします。 csproj に `<SupportedOSPlatformVersion>7</SupportedOSPlatformVersion>` を追加した場合でも、アプリは以前のバージョンの Windows で実行されます。 WAM が使用できない場合、MSAL にはブラウザーが使用されます。

## <a name="wam-value-proposition"></a>WAM の価値提案

WAM などの認証ブローカーを使用すると、多くのベネフィットがあります。

- セキュリティの強化 (アプリで強力な更新トークンを管理する必要がない)
- Windows Hello、条件付きアクセス、FIDO キーのサポート向上
- Windows の [メールとアカウント] ビューとの統合
- シングル サインオンの向上 (ユーザーはパスワードを再入力する必要がない)
- ほとんどのバグ修正と機能強化が Windows に付属して提供される

## <a name="wam-limitations"></a>WAM の制限事項

- B2C 機関はサポートされていません。
- Win10、Win Server 2016、Win Server 2019 で使用できます。 Mac、Linux、以前の Windows、MSAL は、ブラウザーにフォールバックされます。

## <a name="wam-calling-pattern"></a>WAM 呼び出しパターン

WAM を使用するには、次のパターンを使用できます。

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

`.WithBroker(true)` を呼び出します。 ブローカーが存在しない場合 (Win8.1、Mac、Linux など)、MSAL はブラウザーにフォールバックされます。 リダイレクト URI 規則がブラウザーに適用されます。

## <a name="redirect-uri"></a>リダイレクト URI

WAM リダイレクト URI は MSAL で構成する必要はありませんが、アプリの登録で構成する必要があります。

### <a name="win32-net-framework--net-5"></a>Win32 (.NET framework/.NET 5)

```
ms-appx-web://microsoft.aad.brokerplugin/{client_id}
```

### <a name="uwp"></a>UWP
```csharp
            // returns smth like S-1-15-2-2601115387-131721061-1180486061-1362788748-631273777-3164314714-2766189824
            string sid = WebAuthenticationBroker.GetCurrentApplicationCallbackUri().Host.ToUpper();

            // the redirect uri you need to register
            string redirectUri = $"ms-appx-web://microsoft.aad.brokerplugin/{sid}";
```

## <a name="token-cache-persistence"></a>トークン キャッシュの永続性

MSAL のトークン キャッシュを保持することが重要です。MSAL を使用すると、内部 WAM アカウント ID をそこに保存する必要があるためです。 そうしない場合、アプリを再起動すると、`GetAccounts` API で一部のアカウントが欠落します。 UWP の場合、トークン キャッシュを保存する場所は MSAL で認識されています。

## <a name="getaccounts"></a>GetAccounts

`GetAccounts` を使用すると、以前にアプリに対話的にログインしたユーザーのアカウントが返されます。

この他に、Windows に構成されている OS 全体の職場または学校のアカウントを WAM で一覧表示できます (UWP アプリではなく Win32 アプリの場合)。 この機能にオプトインするには、`WindowsBrokerOptions` の `ListWindowsWorkAndSchoolAccounts` を **true** に設定します。 次のようにして有効にできます。

```csharp
.WithWindowsBrokerOptions(new WindowsBrokerOptions()
{
    // GetAccounts will return Work and School accounts from Windows
    ListWindowsWorkAndSchoolAccounts = true,

    // Legacy support for 1st party apps only
    MsaPassthrough = true
})
```

>[!NOTE]
> Microsoft (つまり、outlook.com など) アカウントは、プライバシー上の理由から Win32 と UWP には表示されません。

アプリケーションで Windows からアカウントを削除することはできません。 

## <a name="removeasync"></a>RemoveAsync

- MSAL のトークン キャッシュからすべてのアカウント情報を削除します。これには、MSA (つまり、個人用アカウント) のアカウント情報と、そのキャッシュに MSAL によってコピーされたその他のアカウント情報が含まれます。
- アプリ専用 (OS 全体ではない) アカウントを削除します。

>[!NOTE]
> アプリで OS アカウントを削除することはできません。 それができるのはユーザーのみです。 OS アカウントを `RemoveAsync` に渡してから、`ListWindowsWorkAndSchoolAccounts` を有効にして `GetAccounts` を呼び出しても、同じ OS アカウントが返されます。

## <a name="other-considerations"></a>その他の考慮事項

- WAM の対話型操作は、UI スレッド上で行われる必要があります。 UI スレッド上でない場合、MSAL によって意味のある例外がスローされます。 これはコンソール アプリには適用されません。
- MSAL アカウントが最初に WAM を介して取得された場合、つまり、Windows の職場または学校のアカウントを WAM で見つけることができる場合、`WithAccount` によって高速認証エクスペリエンスが提供されます。
- 同じユーザー名の職場または学校アカウントが Windows で見つからない限り、WAM でユーザー名フィールドにログイン ヒントを事前設定することはできません。
- WAM で高速認証エクスペリエンスを提供できない場合は、アカウント ピッカーが表示されます。 ユーザーは新しいアカウントを追加できます。

!["WAM アカウント ピッカー"](media/scenario-desktop-acquire-token-wam/wam-account-picker.png)

- 新しいアカウントは、Windows によって自動的に記憶されます。 職場または学校のアカウントは、組織のディレクトリに参加するか、完全にオプトアウトするオプションがあります (その場合は [メールとアカウント] に表示されません)。 Microsoft アカウントは、自動的に Windows に追加されます。 アプリでプログラムによってこれらのアカウントを一覧表示することはできません (アカウント ピッカーを使用してのみ可能)。

## <a name="troubleshooting"></a>トラブルシューティング

MSAL を使用するアプリが昇格されたプロセスとして実行されると、WAM 内のこれらの呼び出しの一部は、さまざまなプロセス セキュリティ レベルが原因で失敗するおそれがあります。 MSAL.NET と WAM の統合には、内部的にネイティブの Windows メソッド ([COM](/windows/win32/com/the-component-object-model)) が使用されます。 バージョン 4.32.0 より、アプリ プロセスが昇格されていること、および WAM からアカウントが返されていないことが MSAL で検出されると、説明的なエラー メッセージが表示されます。

1 つの解決策は、可能であれば、アプリを管理者特権として実行しないことです。 考えられるもう 1 つの回避策として、アプリの起動時に `WindowsNativeUtils.InitializeProcessSecurity` メソッドを呼び出す方法があります。 これにより、WAM によって使用される複数のプロセスのセキュリティが同じレベルに設定されます。 例として、[こちらのサンプル アプリ](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/devapps/WAM/NetCoreWinFormsWam/Program.cs#L18-L21)を参照してください。 ただし、この回避策は、基になる CLR の動作のような外部要因が原因で、成功するとは保証されません。 その場合、`MsalClientException` がスローされます。 詳細については、イシュー [#2560](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/2560) を参照してください。

## <a name="sample"></a>サンプル

[WAM を使用する WPF のサンプル](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2)

[Xamarin で WAM を使用する UWP のサンプル](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/2-With-broker)

---
## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である「[デスクトップ アプリから Web API を呼び出す](scenario-desktop-call-api.md)」に進みます。
