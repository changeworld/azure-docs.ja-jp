---
title: Xamarin iOS に関する考慮事項 (Microsoft Authentication Library for .NET) | Azure
description: Xamarin iOS を Microsoft Authentication Library for .NET (MSAL.NET) と共に使用する場合の固有の考慮事項について説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb07fa00b9e1d917cb64df18fff6466dd5c0193d
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406981"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>MSAL.NET を使用する Xamarin iOS に固有の考慮事項
Xamarin iOS には、MSAL.NET を使用する場合に考慮する必要がある考慮事項がいくつかあります。

- [iOS 12 と認証に関する既知の問題](#known-issues-with-ios-12-and-authentication)
- [`AppDelegate` での `OpenUrl` 関数のオーバーライドと実装](#implement-openurl)
- [キーチェーン グループの有効化](#enable-keychain-groups)
- [トークン キャッシュ共有の有効化](#enable-token-cache-sharing-across-ios-applications)
- [キーチェーン アクセスの有効化](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 と認証に関する既知の問題
Microsoft では、iOS12 と一部の種類の認証との間の非互換性について情報を提供する[セキュリティ アドバイザリ](https://github.com/aspnet/AspNetCore/issues/4647)をリリースしました。 その非互換性により、ソーシャル、WSFed、OIDC のログインが中断されます。 このアドバイザリでは、iOS12 と互換性を持たせる目的で ASP.NET によってアプリケーションに追加された現在のセキュリティ制限を取り除くために、開発者ができることについてのガイダンスも提供しています。  

Xamarin iOS 上での MSAL.NET アプリケーション開発時、iOS 12 から Web サイトにサインインしようとすると無限ループが発生することがあります (この [ADAL の問題](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)に似ています)。 

この [WebKit の問題](https://bugs.webkit.org/show_bug.cgi?id=188165)で説明されているように、iOS 12 Safari を使用した ASP.NET Core OIDC 認証でも中断が発生することがあります。

## <a name="implement-openurl"></a>OpenUrl の実装

まず、`FormsApplicationDelegate` 派生クラスの `OpenUrl` メソッドをオーバーライドし、`AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` を呼び出す必要があります。

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

また、URL スキームを定義し、別のアプリを呼び出すために自分のアプリ用にアクセス許可を要求すること、リダイレクト URL 用の特定の形式を作成し、このリダイレクト URL を [Azure portal](https://portal.azure.com) 上で登録することも必要です。

## <a name="enable-keychain-groups"></a>キーチェーン グループの有効化

トークン キャッシュを機能させるため、および `AcquireTokenSilentAsync` メソッドを機能させるためには、複数のステップに従う必要があります。
1. *`* Entitlements.plist* ファイル内でキーチェーンを有効にし、バンドル識別子で**キーチェーン グループ**を指定します。
2. iOS プロジェクト オプション ウィンドウの**バンドル署名ビュー**にある **[カスタム エンタイトルメント]** フィールドで、*`*Entitlements.plist*`* ファイルを選択します。
3. 証明書に署名するときは、XCode で同じ Apple ID を使用していることを確認してください。

## <a name="enable-token-cache-sharing-across-ios-applications"></a>iOS アプリケーション間でのトークン キャッシュ共有の有効化

MSAL 2.x 以降では、複数のアプリケーション間でトークン キャッシュを保持するために使用するキーチェーン セキュリティ グループを指定できます。 これにより、[ADAL.NET](https://aka.ms/adal-net) で開発されたアプリケーション、MSAL.NET Xamarin.iOS アプリケーション、[ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) または [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc) で開発されたネイティブ iOS アプリケーションを含め、同じキーチェーン セキュリティ グループを持つ複数のアプリケーション間でトークン キャッシュを共有できます。

トークン キャッシュを共有すると、同じキーチェーン セキュリティ グループを使用するすべてのアプリケーション間でのシングル サインオン (SSO) が可能になります。

シングル サインオンを有効にするには、すべてのアプリケーションで `PublicClientApplication.iOSKeychainSecurityGroup` プロパティを同じ値に設定する必要があります。

MSAL v3.x を使用したこの例は次のようになります。
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

MSAL v2.7.x を使用したこの例は次のようになります。

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> `KeychainSecurityGroup` プロパティは非推奨になりました。 以前、MSAL 2.x では、開発者は `KeychainSecurityGroup` プロパティを使用するときに TeamId プレフィックスを使用することを余儀なくされていました。 
> 
> MSAL 2.7.x より、MSAL では、`iOSKeychainSecurityGroup` プロパティを使用すると実行時に TeamId プレフィックスが解決されるようになりました。 このプロパティを使用するときは、値に TeamId プレフィックスを含めないでください。 
> 
> 新しい `iOSKeychainSecurityGroup` プロパティを使用します。これを使用すると開発者は TeamId を指定する必要がありません。 `KeychainSecurityGroup` プロパティは非推奨になりました。 

## <a name="enable-keychain-access"></a>キーチェーン アクセスの有効化

MSAL 2.x および ADAL 4.x では、キーチェーンにアクセスするために TeamId が使用されます。そのため、認証ライブラリによって同じ発行元のアプリケーション間でシングル サインオン (SSO) を提供できます。 

[TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId) とは何でしょうか。 それは、App Store における一意識別子 (会社または個人) です。 AppId はアプリに固有です。 複数のアプリがある場合、すべてのアプリの TeamId は同じになりますが、AppId は異なります。 キーチェーン アクセス グループには、システムによってグループごとに自動的に TeamId が付けられます。 これが、同じ発行元のアプリから共有キーチェーンにアクセスできるようにするために OS によって強制的に適用される方法です。 

`PublicClientApplication` を初期化するときに `TeamId returned null from the iOS keychain...` というメッセージを伴う `MsalClientException` を受け取った場合は、iOS Xamarin アプリ内で次のことを行う必要があります。

1. VS の [デバッグ] タブで nameOfMyApp.iOS プロパティに移動します。
2. 次に、[iOS バンドル署名] に移動します。 
3. [カスタム エンタイトルメント] の [...] をクリックし、ご自身のアプリの Entitlements.plist ファイルを選択します。
4. iOS アプリの csproj ファイルにこの行が含まれているはずです: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. プロジェクトを**リビルド**します。

これは、`Entitlements.plist` ファイル内でキーチェーン アクセスを有効にすることに "*加えて*"、以下のまたはご自身のアクセス グループを使用して行います。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>次の手順

詳細については、次に示すサンプルの readme.md ファイルの「[iOS Specific Considerations](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations)」(iOS に固有の考慮事項) の段落を参照してください。

サンプル | プラットフォーム | 説明 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | MSAL を使用して AAD v2.0 エンドポイント経由で MSA と Azure AD を認証し、その結果得られたトークンを使用して Microsoft Graph にアクセスする方法を示す、単純な Xamarin Forms アプリ。 <br>![トポロジ](media/msal-net-xamarin-ios-considerations/topology.png)