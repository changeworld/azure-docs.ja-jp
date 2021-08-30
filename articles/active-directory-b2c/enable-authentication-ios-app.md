---
title: iOS Swift アプリで認証を有効にする - Azure AD B2C
description: Azure Active Directory B2C の構成要素を使用して iOS Swift アプリケーションで認証を有効にします。 Azure AD B2C を使用して、iOS Swift アプリケーションでユーザーをサインインおよびサインアップする方法を学習します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: d9efe9b3d7810eccb94906c236b34534861db092
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778845"
---
# <a name="enable-authentication-in-your-own-ios-swift-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して独自の iOS Swift アプリケーションで認証を有効にする

この記事では、独自の iOS Swift モバイル アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法について説明します。 iOS Swift アプリケーションを [MSAL for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 認証ライブラリと統合する方法について説明します。 

この記事は、[サンプル iOS Swift アプリケーションで認証を構成](./configure-authentication-sample-ios-app.md)する方法に関する記事と共に利用してください。その際は、サンプルの iOS Swift アプリを独自の iOS Swift アプリに置き換えてください。 この記事の手順を完了すると、アプリケーションで Azure AD B2C を使用したサインインが受け入れられます。

## <a name="prerequisites"></a>前提条件

[サンプル iOS Swift アプリケーションで認証を構成する](configure-authentication-sample-ios-app.md)方法に関する記事の前提条件と統合の手順を確認します。

## <a name="create-an-ios-swift-app-project"></a>iOS Swift アプリ プロジェクトを作成する

iOS Swift アプリケーションがまだない場合は、こちらの手順に従って新しいプロジェクトを設定します。

1. [Xcode](https://developer.apple.com/xcode/) を開き、 **[File]**  >  **[New]**  >  **[Project]** の順に選択します。
1. iOS アプリの場合は、 **[iOS]**  >  **[App]** を選択し、 **[Next]** を選択します。
1. **[Choose options for your new project]** に以下を指定します。
    1. **[Product name]** (`MSALiOS` など)。
    1. **[Organization identifier]** (`contoso.com` など)。
    1. **[Interface]** で、 **[Storyboard]** を選択します。
    1. **[Life cycle]** で、 **[UIKit App Delegate]** を選択します。
    1. **[Language]** で、 **[Swift]** を選択します。 
1. **[次へ]** を選択します。
1. アプリを作成するフォルダーを選択し、 **[作成]** を選択します。


## <a name="install-the-msal-library"></a>MSAL ライブラリをインストールする

1. [CocoaPods](https://cocoapods.org/) を使用して MSAL ライブラリをインストールします。   プロジェクトの `.xcodeproj` ファイルと同じフォルダーで、`podfile` ファイルが存在しない場合は、`podfile` という名前の空のファイルを作成します。 次のコードを `podfile` ファイルに追加します。

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

1. `<your-target-here>` を自分のプロジェクト名に置き換えます。 たとえば、「 `MSALiOS` 」のように入力します。 詳細については、「[ポッドファイル構文リファレンス](https://guides.cocoapods.org/syntax/podfile.html#podfile)」を参照してください。
1. ターミナル ウィンドウで、`podfile` ファイルのあるフォルダーに移動します。 `pod install` を実行して MSAL ライブラリをインストールします。
1. `pod install` コマンドを実行すると、`<your project name>.xcworkspace` ファイルが作成されます。 Xcode でプロジェクトを再度読み込むには、Xcode を閉じて `<your project name>.xcworkspace` を開きます。

## <a name="set-the-app-url-scheme"></a>アプリの URL スキームを設定する

ユーザーが認証されると、Azure AD B2C アプリケーション登録で構成されたリダイレクト URI を使用して、Azure AD B2C からアプリに認証コードが送信されます。 

MSAL の既定のリダイレクト URI の形式は `msauth.[Your_Bundle_Id]://auth` です。 たとえば、`msauth.com.microsoft.identitysample.MSALiOS://auth` のようになります (`msauth.com.microsoft.identitysample.MSALiOS` は URL スキームです)。

この手順では、`CFBundleURLSchemes` 配列を使用して URL スキームを登録します。 アプリケーションは、この URL スキームで Azure AD B2C からのコールバックをリッスンします。 

Xcode で、ソース コード ファイルとして [Info.plist ファイル](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html) を開きます。 次の XML スニペットの `<dict>` セクション内の部分を追加します。 

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.com.microsoft.identitysample.MSALiOS</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="add-the-authentication-code"></a>認証コードを追加する

[サンプル コード](configure-authentication-sample-ios-app.md#step-4-get-the-ios-mobile-app-sample) は、`UIViewController` クラスで構成されています。 このクラスは:

- ユーザー インターフェイスの構造を定義します。
- Azure AD B2C ID プロバイダーに関する情報を含みます。 アプリはこの情報を使用して、Azure AD B2C との信頼関係を確立します。 
- ユーザーの認証、トークンの取得、それらの検証を行うための認証コードを含みます。

ユーザーが認証される `UIViewController` を選択します。 コードを[こちらで提供されている](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/blob/vNext/MSALiOS/ViewController.swift)ものと共に `UIViewController` にマージします。 

## <a name="configure-your-ios-swift-app"></a>iOS Swift アプリを構成する

[認証コードを追加](#add-the-authentication-code)したら、Azure AD B2C 設定を使用して iOS Swift アプリを構成します。 Azure AD B2C ID プロバイダーの設定は、前のセクションで選択した `UIViewController` クラスで構成されています。 

[サンプル モバイル アプリを構成](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app)する方法に関するガイダンスに従ってください。

## <a name="run-and-test-the-mobile-app"></a>モバイル アプリを実行してテストする

1. [接続されている iOS デバイスのシミュレーター](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device)を使用して、プロジェクトを作成して実行します。
1. **[サインイン]** を選択します。 次に、Azure AD B2C のローカルまたはソーシャル アカウントを使用してサインアップまたはサインインします。
1. 認証が成功すると、ナビゲーション バーに表示名が表示されます。

## <a name="how-it-works"></a>動作のしくみ

このセクションでは、iOS Swift アプリの認証を可能にするコードの構成要素について説明します。 ここでは、UIViewController のメソッドと、コードのカスタマイズ方法を示します。 

### <a name="instantiate-a-public-client-application"></a>パブリック クライアント アプリケーションをインスタンス化する

パブリック クライアント アプリケーションは、アプリケーション シークレットを安全に維持するために信頼されていません。また、クライアント シークレットも含んでいません。 [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload)で、パブリック クライアント アプリケーション オブジェクトを使用して MSAL をインスタンス化します。

次のコード スニペットは、`MSALPublicClientApplicationConfig` 構成オブジェクトを使用して MSAL ライブラリを初期化する方法を示しています。 

構成オブジェクトは、Azure AD B2C 環境に関する情報 (Azure AD B2C に対する認証要求を作成するためのクライアント ID、リダイレクト URI、権限など) を提供します。 構成オブジェクトの詳細については、[サンプル モバイル アプリの構成](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app)に関する記事を参照してください。

```swift
do {

    let siginPolicyAuthority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)
    let editProfileAuthority = try self.getAuthority(forPolicy: self.kEditProfilePolicy)
    
    let pcaConfig = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: kRedirectUri, authority: siginPolicyAuthority)
    pcaConfig.knownAuthorities = [siginPolicyAuthority, editProfileAuthority]
    
    self.applicationContext = try MSALPublicClientApplication(configuration: pcaConfig)
    self.initWebViewParams()
    
    } catch {
        self.updateLoggingText(text: "Unable to create application \(error)")
    }
```

`initWebViewParams` メソッドでは、[対話型認証](../active-directory/develop/customize-webviews.md)エクスペリエンスを構成します。 

次の Swift コード スニペットでは、システム Web ビューを使用して `webViewParamaters` クラス メンバーを初期化します。 詳細については、[iOS/macOS のブラウザーと WebView のカスタマイズ](../active-directory/develop/customize-webviews.md)に関する記事を参照してください。

```swift
func initWebViewParams() {
    self.webViewParamaters = MSALWebviewParameters(authPresentationViewController: self)
    self.webViewParamaters?.webviewType = .default
}
```

### <a name="interactive-authorization-request"></a>対話型承認要求

対話型承認要求は、システム Web ビューを使用してユーザーがサインアップまたはサインインを求められるフローです。 ユーザーが **[サインイン]** ボタンをクリックすると、`authorizationButton` メソッドが呼び出されます。

`authorizationButton` メソッドにより、承認要求に関する関連データを使用して `MSALInteractiveTokenParameters` オブジェクトが準備されます。 `acquireToken` メソッドでは、`MSALInteractiveTokenParameters` を使用することで、システム Web ビューを使用してユーザーが認証されます。

次のコード スニペットは、対話型承認要求の開始方法を示しています。 

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
parameters.promptType = .selectAccount
parameters.authority = authority

applicationContext.acquireToken(with: parameters) { (result, error) in

// On error code    
guard let result = result else {
    self.updateLoggingText(text: "Could not acquire token: \(error ?? "No error informarion" as! Error)")
    return
}

// On success code
self.accessToken = result.accessToken
self.updateLoggingText(text: "Access token is \(self.accessToken ?? "Empty")")
}
```
 
ユーザーが承認フローを完了 (成功または失敗) すると、結果が `acquireToken` メソッドの[クロージャ](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)に返されます。 

acquireToken メソッドにより、`result` および `error` オブジェクトが返されます。 このクロージャを使用して、次のことを行います。

- 認証の完了後、情報を使用してモバイル アプリの UI を更新します。
- アクセス トークンを使用して Web API サービスを呼び出します。
- 認証エラー (たとえば、ユーザーがサインイン フローをキャンセルした場合など) を処理します。
 
### <a name="call-a-web-api"></a>Web API を呼び出す

[トークンベースの承認 Web API](enable-authentication-web-api.md)を呼び出すには、アプリに有効なアクセス トークンが必要です。 アプリによって次の手順が実行されます。

1. Web API エンドポイントで必要なアクセス許可 (スコープ) を持つアクセス トークンを取得します。
1. HTTP 要求の Authorization ヘッダー内で、次の形式を使用してベアラー トークンとしてアクセス トークンを渡します。

```http
Authorization: Bearer <access-token>
```

ユーザーが[対話形式で認証](#interactive-authorization-request)されると、アプリは `acquireToken` クロージャ内でアクセス トークンを取得します。 後続の Web API 呼び出しでは、このセクションで説明されているように、acquire token silent (`acquireTokenSilent`) メソッドを使用します。 

`acquireTokenSilent` メソッドによって次の手順が実行されます。

1. 要求されたスコープを持つアクセス トークンをトークン キャッシュから取り込むことを試みます。 トークンが存在し、期限切れになっていない場合は、トークンが返されます。 
1. トークンがトークン キャッシュに存在しないか期限切れである場合、MSAL ライブラリでは更新トークンを使用して新しいアクセス トークンの取得が試みられます。 
1. 更新トークンが存在しないか期限切れになっている場合は、例外が返されます。 この場合は、ユーザーに[対話形式でサインイン](#interactive-authorization-request)することを求めるプロンプトを表示する必要があります。  

次のコード スニペットは、アクセス トークンの取得方法を示しています。

```swift
do {

// Get the authority using the sign-in or sign-up user flow
let authority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)

// Get the current account from the application context
guard let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy) else {
    self.updateLoggingText(text: "There is no account available!")
    return
}

// Configure the acquire token silent parameters
let parameters = MSALSilentTokenParameters(scopes: kScopes, account:thisAccount)
parameters.authority = authority
parameters.loginHint = "username"

// Acquire token silent
self.applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        
        let nsError = error as NSError
        
        // interactionRequired means we need to ask the user to sign in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
        // among other possible reasons.
        
        if (nsError.domain == MSALErrorDomain) {
            
            if (nsError.code == MSALError.interactionRequired.rawValue) {
                
                // Start an interactive authorization code
                // Notice we supply the account here. This ensures we acquire token for the same account
                // as we originally authenticated.
                
                ...
            }
        }
        
        self.updateLoggingText(text: "Could not acquire token: \(error)")
        return
    }
    
    guard let result = result else {
        
        self.updateLoggingText(text: "Could not acquire token: No result returned")
        return
    }
    
    // On success, set the access token to the accessToken class member. 
    // The callGraphAPI method uses the access token to call a web API  
    self.accessToken = result.accessToken
    ...
}
} catch {
self.updateLoggingText(text: "Unable to construct parameters before calling acquire token \(error)")
}
```

`callGraphAPI` メソッドによってアクセス トークンが取得され、Web API が呼び出されます。

```swift
@objc func callGraphAPI(_ sender: UIButton) {
    guard let accessToken = self.accessToken else {
        self.updateLoggingText(text: "Operation failed because could not find an access token!")
        return
    }
    
    let sessionConfig = URLSessionConfiguration.default
    sessionConfig.timeoutIntervalForRequest = 30
    let url = URL(string: self.kGraphURI)
    var request = URLRequest(url: url!)
    request.setValue("Bearer \(accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    self.updateLoggingText(text: "Calling the API....")
    
    urlSession.dataTask(with: request) { data, response, error in
        guard let validData = data else {
            self.updateLoggingText(text: "Could not call API: \(error ?? "No error informarion" as! Error)")
            return
        }
        
        let result = try? JSONSerialization.jsonObject(with: validData, options: [])
        
        guard let validResult = result as? [String: Any] else {
            self.updateLoggingText(text: "Nothing returned from API")
            return
        }
        
        self.updateLoggingText(text: "API response: \(validResult.debugDescription)")
        }.resume()
}
```

### <a name="sign-out"></a>サインアウト

MSAL でサインアウトすると、ユーザーに関する既知のすべての情報がアプリケーションから削除されます。 このサインアウント方法を使用してユーザーをサインアウトし、UI を更新します。 たとえば、保護された UI 要素とサインアウト ボタンを非表示にし、サインイン ボタンを表示します。

次のコード スニペットは、ユーザーのサインアウト方法を示しています。

```swift
@objc func signoutButton(_ sender: UIButton) {
do {
    
    
    let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy)
    
    if let accountToRemove = thisAccount {
        try applicationContext.remove(accountToRemove)
    } else {
        self.updateLoggingText(text: "There is no account to signing out!")
    }
    
    ...
    
} catch  {
    self.updateLoggingText(text: "Received error signing out: \(error)")
}
}
```

## <a name="next-steps"></a>次のステップ

* [iOS Swift アプリケーションで認証オプションを構成する](enable-authentication-ios-app-options.md)
* [ユーザー自身の Web API で認証を有効にする](enable-authentication-web-api.md)
