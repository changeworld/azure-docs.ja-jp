---
title: ADAL.NET から MSAL.NET に Microsoft Authenticator を使用して Xamarin iOS アプリケーションを移行する | Azure
description: .NET 用の Azure AD Authentication ライブラリ (ADAL.NET) から .NET 用 Microsoft 認証ライブラリ (MSAL.NET) に Microsoft Authenticator を使用して Xamarin iOS アプリケーションを移行する方法について説明します
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875604"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>ADAL.NET から MSAL.NET に Microsoft Authenticator を使用して iOS アプリケーションを移行する

お使いの ADAL.NET とその iOS のブローカーから、リリース 4.3 以降の iOS ブローカーをサポートする MSAL.NET [Microsoft 認証ライブラリ](msal-overview.md)に移行するときが来ました。 

どこから始めるか この記事では、お使いの Xamarin iOS アプリを ADAL から MSAL に移行する方法について説明します。

## <a name="prerequisites"></a>前提条件
このドキュメントでは、iOS ブローカーと統合されている Xamarin iOS アプリが既にあることを前提としています。 ない場合は、MSAL.NET に直接移行し、そこでブローカーの実装を開始することをお勧めします。 新しいアプリケーションで MSAL.NET で iOS ブローカーを起動する方法の詳細については、[このドキュメント](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)を参照してください。

## <a name="background"></a>バックグラウンド

### <a name="what-are-brokers"></a>ブローカーとは

ブローカーとは、Microsoft が提供する Android および iOS (iOS および Android に、[Microsoft Authenticator](https://www.microsoft.com/account/authenticator)、Android に Intune ポータル サイト) を対象としたアプリケーションです。 

これでは、次が可能です。

- シングル サインオン、
- 一部の[条件付きアクセス ポリシー](../conditional-access/overview.md)で必要とされるデバイス識別 ([デバイス管理](../conditional-access/conditions.md#device-platforms)に関する説明を参照)
- 一部のエンタープライズ シナリオでも必要なアプリケーション ID の検証 (たとえば、[Intune モバイルアプリケーション管理または MAM ](https://docs.microsoft.com/intune/mam-faq)に関する説明を参照)。

## <a name="migrate-from-adal-to-msal"></a>ADAL から MSAL への移行

### <a name="step-1-enable-the-broker"></a>手順 1:ブローカーを有効にします

<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
ADAL.NET では、ブローカーのサポートは、認証コンテキストごとに有効になっており、既定では無効です。 ブローカーを呼び出すには、次のように `PlatformParameters` コンストラクターで `useBroker` フラグを true に設定する必要があります。

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
また、この例では、プラットフォーム固有の iOS 用のページ レンダラーのコードの `useBroker` フラグを 
true に設定する必要があります。
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

次に、トークンを取得する呼び出しで、次のパラメーターを含めます。
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
MSAL.NET では、ブローカーのサポートは、パブリック クライアント アプリケーションごとに有効になります。 この機能は既定で無効になっています。 これを有効にするには、 

(既定で true に設定されている) `WithBroker()` パラメーターを使用してブローカーを呼び出します。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
AcquireToken の呼び出しで、次のように記述します。
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>手順 2:UIViewController() を設定する
ADAL.NET では、PlatformParameters の一部として UIViewController が渡されました (手順 1 の例を参照)。 しかし、MSAL.NET では、開発者にとってより柔軟になるよう、通常の iOS の使用では必要のない、オブジェクト ウィンドウが使用されます。 ただし、ブローカーを使用するには、ブローカーから応答を送受信するために、オブジェクト ウィンドウを設定する必要があります。 
<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
UIViewController は、iOS 固有のプラットフォーム用の PlatformParamters に渡されます。

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
MSAL.NET では、iOS 用のオブジェクト ウィンドウの設定に、次の 2 つの作業を行う必要があります。

1) `AppDelegate.cs` で `App.RootViewController` を新しい `UIViewController()` に設定します。 これを割り当てることにより、UIViewController でのブローカーへの呼び出しが保証されます。 正しく設定されていないと、次のエラーが表示されることがあります。`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) AcquireTokenInteractive の呼び出しで、`.WithParentActivityOrWindow(App.RootViewController)` を使用し、
使用するオブジェクト ウィンドウで参照を渡します。

**例:**

`App.cs`で、次のように記述します。
```CSharp
   public static object RootViewController { get; set; }
```
`AppDelegate.cs`で、次のように記述します。
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
AcquireToken の呼び出しで、次のように記述します。
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>手順 3:コールバックを処理するように AppDelegate を更新する
ADAL でも MSAL でもブローカーが呼び出されます。それに対し、ブローカーは `AppDelegate` クラスの `OpenUrl` メソッドを通じてお使いのアプリケーションにコール バックを行います。 詳細については、[こちら](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback)を参照してください。

: heavy_check_mark:**ここでは ADAL.NET と MSAL.NET の間に違いはありません**

### <a name="step-4-register-a-url-scheme"></a>手順 4:URL スキームを登録する
ADAL.NET と MSAL.NET では、URL を使用してブローカーを呼び出し、ブローカーの応答をアプリに返します。 次のように、お使いのアプリの `Info.plist` ファイルに URL スキームを登録します。

<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
URL スキームは、お使いのアプリに対して一意です。
</td><td>
その 

`CFBundleURLSchemes` 名には、 

`msauth.`

がプレフィックスとして、お使いの `CFBundleURLName` の前に含まれている必要があります。

次に例を示します。`$"msauth.(BundleId")`

```CSharp
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

> [!NOTE]
>  この URL スキームは、ブローカーから応答を受信したときにアプリを一意に識別する RedirectUri の一部になります。

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>手順 5:LSApplicationQueriesSchemes

ADAL.NET と MSAL.NET は、いずれも `-canOpenURL:` を使用してブローカーがデバイスにインストールされているかどうかを確認します。 info.plist ファイルの LSApplicationQueriesSchemes セクションには、次のように iOS ブローカー用の正しい識別子を追加します。 
<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
用途 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
用途 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>手順 6:ポータルに RedirectUri を登録する

ADAL.NET と MSAL.NET のいずれにおいても、ブローカーを対象にする場合に、redirectUri に追加の要件があります。 ポータルでお使いのアプリケーションにリダイレクト URI を追加します。
<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"` 例: `mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

例:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

ポータルでの redirectUri の登録の詳細については、[Xamarin.iOS アプリケーションでのブローカーの利用](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app)に関する説明を参照してください。

## <a name="next-steps"></a>次の手順

「[MSAL.NET を使用する Xamarin iOS に固有の考慮事項](msal-net-xamarin-ios-considerations.md)」を参照してください。 
