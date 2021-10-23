---
title: Azure AD B2C を使用して iOS Swift アプリでの認証を有効にする
description: この記事では、Azure Active Directory B2C の構成要素を使用して iOS Swift アプリケーションで認証を有効にする方法について説明します。 Azure AD B2C を使用して、iOS Swift アプリケーションでユーザーをサインインおよびサインアップする方法を学習します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 404a38def1f90a145a3763d23cba2ec7a55a6782
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041407"
---
# <a name="enable-authentication-in-your-own-ios-swift-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して独自の iOS Swift アプリケーションで認証を有効にする

この記事では、独自の iOS Swift モバイル アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法について説明します。 iOS Swift アプリケーションを [iOS 用 Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) と統合する方法について説明します。 

この記事は、[サンプル iOS Swift アプリケーションで認証を構成](./configure-authentication-sample-ios-app.md)する方法に関する記事と共に利用してください。その際は、サンプルの iOS Swift アプリを独自の iOS Swift アプリに置き換えてください。 この記事の手順を完了すると、アプリケーションで、Azure AD B2C を使用したサインインが受け付けられるようになります。

## <a name="prerequisites"></a>前提条件

「[Azure AD B2C を使用してサンプル iOS Swift アプリで認証を構成する](configure-authentication-sample-ios-app.md)」の前提条件と統合の手順を確認します。

## <a name="create-an-ios-swift-app-project"></a>iOS Swift アプリ プロジェクトを作成する

iOS Swift アプリケーションがまだない場合は、以下を実行して新しいプロジェクトを設定します。

1. [Xcode](https://developer.apple.com/xcode/) を開いてから、 **[File]**  >  **[New]**  >  **[Project]** の順に選択します。
1. iOS アプリの場合は、 **[iOS]**  >  **[App]** と選択してから、 **[Next]** を選択します。
1. **[Choose options for your new project]** には、以下を指定します。
    1. **[Product name]** (`MSALiOS` など)。
    1. **[Organization identifier]** (`contoso.com` など)。
    1. **[Interface]** で、 **[Storyboard]** を選択します。
    1. **[Life cycle]** で、 **[UIKit App Delegate]** を選択します。
    1. **[Language]** で、 **[Swift]** を選択します。 
1. **[次へ]** を選択します。
1. その中にアプリを作成するフォルダーを選択してから、 **[作成]** を選択します。


## <a name="step-1-install-the-msal-library"></a>手順 1: MSAL ライブラリをインストールする

1. [CocoaPods](https://cocoapods.org/) を使用して MSAL ライブラリをインストールします。   プロジェクトの *.xcodeproj* ファイルと同じフォルダーに *podfile* ファイルが存在しない場合は、*podfile* という名前の空のファイルを作成します。 次のコードを *podfile* ファイルに追加します。

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

1. `<your-target-here>` を自分のプロジェクトの名前に置き換えます (例: `MSALiOS`)。 詳細については、「[ポッドファイル構文リファレンス](https://guides.cocoapods.org/syntax/podfile.html#podfile)」を参照してください。
1. ターミナル ウィンドウで、*podfile* が含まれるフォルダーに移動してから、*pod install* を実行して MSAL ライブラリをインストールします。
1. `pod install` コマンドを実行すると、 *\<your project name>.xcworkspace* ファイルが作成されます。 Xcode でプロジェクトを再度読み込むため、Xcode を閉じてから、 *\<your project name>.xcworkspace* を開きます。

## <a name="step-2-set-the-app-url-scheme"></a>手順 2: アプリの URL スキームを設定する

ユーザーが認証されると、Azure AD B2C アプリケーション登録で構成されたリダイレクト URI を使用して、Azure AD B2C からアプリに承認コードが送信されます。 

MSAL の既定のリダイレクト URI の形式は `msauth.[Your_Bundle_Id]://auth` です。 例としては `msauth.com.microsoft.identitysample.MSALiOS://auth` が挙げられます。ここで `msauth.com.microsoft.identitysample.MSALiOS` は URL スキームです。

この手順では、`CFBundleURLSchemes` 配列を使用して URL スキームを登録します。 アプリケーションは、この URL スキームで Azure AD B2C からのコールバックをリッスンします。 

Xcode で、ソース コード ファイルとして [*Info.plist* ファイル](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html) を開きます。 `<dict>` セクションで、次の XML スニペットを追加します。 

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

## <a name="step-3-add-the-authentication-code"></a>手順 3: 認証コードを追加する

[サンプル コード](configure-authentication-sample-ios-app.md#step-4-get-the-ios-mobile-app-sample) は、`UIViewController` クラスで構成されています。 このクラスは:

- ユーザー インターフェイスの構造を定義します。
- Azure AD B2C ID プロバイダーに関する情報を含みます。 アプリはこの情報を使用して、Azure AD B2C との信頼関係を確立します。 
- ユーザーの認証、トークンの取得、それらの検証を行うための認証コードを含みます。

ユーザーが認証を行うことになる `UIViewController` を選択します。  `UIViewController` で、このコードを、[GitHub で提供されているコード](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/blob/vNext/MSALiOS/ViewController.swift)とマージします。 

## <a name="step-4-configure-your-ios-swift-app"></a>手順 4: iOS Swift アプリを構成する

[認証コードを追加](#step-3-add-the-authentication-code)したら、Azure AD B2C 設定を使用して iOS Swift アプリを構成します。 Azure AD B2C ID プロバイダーの設定は、前のセクションで選択された `UIViewController` クラスで構成されています。 

iOS Swift アプリを構成する方法については、「[Azure AD B2C を使用してサンプル iOS Swift アプリでの認証を構成する](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app)」を参照してください。

## <a name="step-5-run-and-test-the-mobile-app"></a>手順 5: モバイル アプリを実行してテストする

1. [接続されている iOS デバイスのシミュレーター](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device)を使用して、プロジェクトを作成して実行します。
1. **[サインイン]** を選択してから、Azure AD B2C のローカルまたはソーシャル アカウントを使用して、サインアップまたはサインインします。
1. 認証に成功すると、ナビゲーション バーに自分の表示名が表示されます。

## <a name="step-6-customize-your-code-building-blocks"></a>手順 6: コードの構成要素をカスタマイズする

このセクションでは、iOS Swift アプリの認証を可能にするコードの構成要素について説明します。 UIViewController のメソッドを示して、コードをカスタマイズする方法について説明します。 

### <a name="step-61-instantiate-a-public-client-application"></a>手順 6.1: パブリック クライアント アプリケーションをインスタンス化する

パブリック クライアント アプリケーションは、アプリケーション シークレットが安全に保管されると信頼されておらず、そこにクライアント シークレットは置かれません。 [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload) で、パブリック クライアント アプリケーション オブジェクトを使用して MSAL をインスタンス化します。

次の Swift コード スニペットは、`MSALPublicClientApplicationConfig` 構成オブジェクトを使用して MSAL を初期化する方法を示しています。 

構成オブジェクトでは、Azure AD B2C 環境に関する情報が提供されます。 たとえば、Azure AD B2C に対する認証要求を作成するための、クライアント ID、リダイレクト URI、機関が提供されます。 構成オブジェクトの詳細については、[サンプル モバイル アプリの構成](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app)に関する記事を参照してください。

```swift
do {

    let signinPolicyAuthority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)
    let editProfileAuthority = try self.getAuthority(forPolicy: self.kEditProfilePolicy)
    
    let pcaConfig = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: kRedirectUri, authority: signinPolicyAuthority)
    pcaConfig.knownAuthorities = [signinPolicyAuthority, editProfileAuthority]
    
    self.applicationContext = try MSALPublicClientApplication(configuration: pcaConfig)
    self.initWebViewParams()
    
    } catch {
        self.updateLoggingText(text: "Unable to create application \(error)")
    }
```

`initWebViewParams` メソッドでは、[対話型認証](../active-directory/develop/customize-webviews.md)エクスペリエンスを構成します。 

次の Swift コード スニペットでは、システム Web ビューを使用して、`webViewParameters` クラス メンバーを初期化しています。 詳細については、「[iOS/macOS のブラウザーと WebView をカスタマイズする](../active-directory/develop/customize-webviews.md)」を参照してください。

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    self.webViewParameters?.webviewType = .default
}
```

### <a name="step-62-start-an-interactive-authorization-request"></a>手順 6.2: 対話型承認要求を開始する

対話型承認要求は、ユーザーが、システム Web ビューを使用してサインアップまたはサインインするよう求められるフローです。 ユーザーが **[サインイン]** ボタンを選択すると、`authorizationButton` メソッドが呼び出されます。

`authorizationButton` メソッドにより、承認要求に関する関連データを使用して `MSALInteractiveTokenParameters` オブジェクトが準備されます。 `acquireToken` メソッドでは、`MSALInteractiveTokenParameters` を使用して、システム Web ビューからユーザーを認証します。

次のコード スニペットは、対話型承認要求の開始方法を示しています。 

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority

applicationContext.acquireToken(with: parameters) { (result, error) in

// On error code    
guard let result = result else {
    self.updateLoggingText(text: "Could not acquire token: \(error ?? "No error information" as! Error)")
    return
}

// On success code
self.accessToken = result.accessToken
self.updateLoggingText(text: "Access token is \(self.accessToken ?? "Empty")")
}
```
 
ユーザーが、成功か失敗かのいずれかとして承認フローを完了すると、結果が `acquireToken` メソッドの[クロージャ](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)に返されます。 

`acquireToken` メソッドからは、`result` および `error` オブジェクトが返されます。 このクロージャを使用して、次のことを行います。

- 認証の完了後、情報を使用してモバイル アプリの UI を更新します。
- アクセス トークンを使用して Web API サービスを呼び出します。
- 認証エラーを処理します (例: ユーザーがサインイン フローをキャンセルした場合)。
 
### <a name="step-63-call-a-web-api"></a>手順 6.3: Web API を呼び出す

[トークンベースの承認 Web API](enable-authentication-web-api.md)を呼び出すには、アプリに有効なアクセス トークンが必要です。 アプリでは以下が実行されます。

1. Web API エンドポイントで必要なアクセス許可 (スコープ) を持つアクセス トークンを取得します。
1. この形式を使用して、HTTP 要求の承認ヘッダーで、ベアラー トークンとしてアクセス トークンを渡します。

```http
Authorization: Bearer <access-token>
```

ユーザーが[対話形式で認証](#step-62-start-an-interactive-authorization-request)されると、アプリは `acquireToken` クロージャ内でアクセス トークンを取得します。 後続の Web API 呼び出しでは、このセクションで説明したように、acquire token silent (`acquireTokenSilent`) メソッドを使用します。 

`acquireTokenSilent` メソッドは以下を実行します。

1. 要求されたスコープのアクセス トークンの、トークン キャッシュからの取り込みを試みます。 トークンが存在していて期限切れでない場合は、トークンが返されます。 
1. トークンがトークン キャッシュに存在しないか期限切れである場合、MSAL ライブラリでは、更新トークンを使用して新しいアクセス トークンの取得が試みられます。 
1. 更新トークンが存在しないか期限切れになっている場合は、例外が返されます。 この場合は、ユーザーに[対話形式でサインイン](#step-62-start-an-interactive-authorization-request)することを求めるプロンプトを表示する必要があります。  

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

次に示すように、`callGraphAPI` メソッドによってアクセス トークンが取得され、Web API が呼び出されます。

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
            self.updateLoggingText(text: "Could not call API: \(error ?? "No error information" as! Error)")
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

### <a name="step-64-sign-out-users"></a>手順 6.4: ユーザーをサインアウトする

MSAL を使用してサインアウトすると、ユーザーに関する既知のすべての情報がアプリケーションから削除されます。 このサインアウント方法を使用してユーザーをサインアウトし、UI を更新します。 たとえば、保護された UI 要素を非表示にして、サインアウト ボタンを非表示にし、サインイン ボタンを表示することができます。

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

具体的には、次の方法を学習します。
* [Azure AD B2C を使用して iOS Swift アプリで認証オプションを構成する](enable-authentication-ios-app-options.md)
* [Azure AD B2C を使用して独自の Web API で認証を有効にする](enable-authentication-web-api.md)
