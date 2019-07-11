---
title: iOS の概要 - Microsoft ID プラットフォーム | Azure
description: iOS (Swift) アプリケーションで Microsoft ID プラットフォームを使用してアクセス トークンを必要とする API を呼び出す方法
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7d68f6f7079872b81b750ba71997117aaa27d33
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550571"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>iOS アプリからユーザーにサインインして Microsoft Graph を呼び出す

このチュートリアルでは、iOS アプリケーションをビルドして Microsoft ID プラットフォームに統合する方法について説明します。 具体的には、このアプリケーションは、ユーザーのサインイン、Microsoft Graph API を呼び出すアクセス トークンの取得、および Microsoft Graph API への基本的な要求を行います。  

このガイドを完了すると、アプリケーションは、個人用の Microsoft アカウント (outlook.com、live.com など) と、Azure Active Directory を使用する会社や組織の職場または学校アカウントのサインインを受け入れるようになります。

## <a name="how-this-guide-works"></a>このガイドの利用法

![このチュートリアルで生成されたサンプル アプリの動作の紹介](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

このサンプルのアプリケーションは、ユーザーにサインインし、ユーザーに代わってデータを取得します。  このデータは、承認を要求し、Microsoft ID プラットフォームによっても保護される API (ここでは Microsoft Graph API) を介してアクセスされます。

具体的には次のとおりです。

* アプリがブラウザーまたは Microsoft Authenticator を介してユーザーをサインインします。
* エンド ユーザーがアプリケーションから要求されたアクセス許可を受け入れます。 
* アプリケーションには、Microsoft Graph API 用のアクセス トークンが発行されます。
* アクセス トークンは、Web API への HTTP 要求に含められます。
* Microsoft Graph の応答を処理します。

このサンプルでは、Microsoft Authentication Library (MSAL) を使用して認証を実装します。MSAL は自動的にトークンを更新し、デバイス上の他のアプリとの間で SSO を提供し、アカウントを管理します。

## <a name="prerequisites"></a>前提条件

- このガイドで作成するサンプルには、XCode バージョン 10.x が必要です。 XCode は、[iTunes の Web サイト](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode ダウンロード URL")からダウンロードできます。
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。 依存関係マネージャーを使用するか、または手動で追加できます。 この後のセクションで[詳細](#add-msal)を説明しています。 

## <a name="set-up-your-project"></a>プロジェクトの設定

このチュートリアルでは新しいプロジェクトを作成します。 代わりに完了したチュートリアルをダウンロードする場合は、[コードをダウンロード](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)してください。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

1. Xcode を開き、 **[Create a new Xcode project (新しい Xcode プロジェクトを作成)]** を選択します。
2. **[iOS] > [Single View Application (単一ビュー アプリケーション)]** を選択し、 **[次へ]** を選択します。
3. 製品に名前を付け、 **[次へ]** を選択します。
4. アプリを作成するフォルダーを選択し、 *[作成]* をクリックします。

## <a name="register-your-application"></a>アプリケーションの登録 

次の 2 つのセクションで説明する方法のいずれかを使用して、アプリケーションを登録できます。

### <a name="register-your-app"></a>アプリの登録

1. [Azure portal](https://aka.ms/MobileAppReg) にアクセスし、`New registration` を選択します。 
2. お使いのアプリ > `Register` で **名前**を入力します。 **この段階ではリダイレクト URI を設定しないでください**。 
3. [`Manage`] セクションで [`Authentication`]  >  [`Add a platform`]  >  [`iOS`] の順に移動します。
    - プロジェクトのバンドル ID を入力します。 コードをダウンロードした場合は `com.microsoft.identitysample.MSALiOS` です。
4. [`Configure`] を選択し、`MSAL Configuration` を後で使用できるように保存します。 

## <a name="add-msal"></a>MSAL の追加

### <a name="get-msal"></a>MSAL の取得

#### <a name="cocoapods"></a>CocoaPods

[CocoaPods](https://cocoapods.org/) を使用して `MSAL` をインストールするには、target の下の `Podfile` にこれを追加します。

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

[Carthage](https://github.com/Carthage/Carthage) を使用して `MSAL` をインストールするには、`Cartfile` にこれを追加します。 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>手動

Git サブモジュールを使用するか、最新のリリースをチェックアウトして、アプリケーションでフレームワークとして使用することもできます。

### <a name="add-your-app-registration"></a>アプリ登録の追加

次に、アプリ登録をコードに追加します。 "***クライアント/アプリケーション ID***" を `ViewController.swift` に追加します。

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>URL スキームの構成

サインインしたユーザーをアプリに再びリダイレクトできるようにするために、`CFBundleURLSchemes` を登録してコールバックを可能にします。

`LSApplicationQueriesSchemes` を使用すると、アプリで Microsoft Authenticator を使用できるようになります (使用可能な場合)。 

これを行うには、`Info.plist` をソース コードとして開き、次の内容を追加して ***BUNDLE_ID*** を Azure portal で構成したものに置き換えます。

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

### <a name="import-msal"></a>MSAL のインポート

`ViewController.swift` ファイルと `AppDelegate.swift` ファイルに MSAL フレームワークをインポートします。

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>アプリの UI の作成

このチュートリアルでは、次を作成する必要があります。

- Graph API の呼び出しボタン
- サインアウト ボタン
- ログ TextView

`ViewController.swift` で、次のようにプロパティと `initUI()` を定義します。

```swift

var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

次に、ViewController.swift の `viewDidLoad()` に次の内容を追加します。

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>MSAL の使用

### <a name="initialize-msal"></a>MSAL の初期化

まず、アプリケーションに対して `MSALPublicClientConfiguration` のインスタンスを使用して `MSALPublicClientApplication` を作成する必要があります。

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-callback"></a>コールバックの処理

サインイン後のコールバックを処理するには、`appDelegate` で `MSALPublicClientApplication.handleMSALResponse` を追加します。

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>トークンの取得

これで、アプリケーションの UI 処理ロジックを実装し、MSAL を介して対話的にトークンを取得できるようになりました。 

MSAL では、トークンを取得するための主要なメソッドとして `acquireTokenSilently` と `acquireTokenInteractively` が公開されています。  

`acquireTokenSilently()` では、ユーザーのサインインが行われ、アカウントが存在すればユーザーとの対話なしにトークンが取得されます。

`acquireTokenInteractively` ではユーザーのサインインとトークンの取得が行われるときに必ず UI が表示されますが、ブラウザーのセッション Cookie または Microsoft Authenticator のアカウントを使用して対話形式で SSO が行われる場合もあります。 

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>対話形式でのユーザー トークンの取得

トークンを初めて取得するときには、`MSALInteractiveTokenParameters` を作成し、`acquireToken` を呼び出す必要があります。

1. スコープを指定して `MSALInteractiveTokenParameters` を作成します。
2. 作成したパラメーターで `acquireToken` を呼び出します。
3. エラーを適切に処理します。 詳細については、[iOS エラーの処理に関するガイド](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling)を参照してください。
4. 正常なケースを処理します。 

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
     // #2        
        applicationContext.acquireToken(with: parameters) { (result, error) in
     // #3            
            if let error = error {
                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }
            guard let result = result else {   
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
     // #4            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```



#### <a name="get-a-token-silently"></a>トークンの自動取得

更新されたトークンを自動で取得するには、`MSALSilentTokenParameters` を作成し、`acquireTokenSilent` を呼び出す必要があります。

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API を呼び出す 

`self.accessToken` を通じてトークンを取得したら、アプリでその値を HTTP ヘッダーで使用して、許可された要求を Microsoft Graph に対して行うことができます。

| ヘッダー キー    | value                 |
| ------------- | --------------------- |
| Authorization | Bearer \<access-token> |

次を `ViewController.swift` に追加します。

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Microsoft Graph API の詳細については[こちら](https://graph.microsoft.com)をご覧ください。

### <a name="use-msal-for-sign-out"></a>MSAL を使用したサインアウト

次に、サインアウトのサポートをアプリに追加します。 

重要な点として、MSAL でサインアウトするとユーザーに関する既知の情報がすべてこのアプリケーションから削除されますが、ユーザーのデバイスではアクティブなセッションが維持されます。 ユーザーが再びサインインを試みた場合に対話画面が表示される可能性がありますが、デバイス セッションがアクティブであるため、資格情報を再入力する必要はありません。 

サインアウトを追加するには、`ViewController.swift` に次のメソッドをコピーします。

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>トークンのキャッシュの有効化

MSAL では既定でアプリのトークンが iOS キーチェーンにキャッシュされます。 

トークンのキャッシュを有効にするには、Xcode プロジェクトの設定に移動し、`Capabilities tab`  >  [`Enable Keychain Sharing`] の順に選択して、[`Plus`] をクリックし、「**com.microsoft.adalcache**」と入力します。

### <a name="add-helper-methods"></a>ヘルパー メソッドの追加

次のヘルパー メソッドを追加してサンプルを完成させます。

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="multi-account-applications"></a>マルチアカウント アプリケーション

このアプリは、シングル アカウントのシナリオを対象にビルドされています。 MSAL ではマルチアカウントのシナリオもサポートされますが、その場合はアプリで追加の作業が必要となります。 トークンを必要とする各アクションに使用するアカウントをユーザーが選択するための UI を作成する必要があります。 あるいは、アプリでヒューリスティックを実装して、`getAllAccounts(...)` メソッドによって使用するアカウントを選択することもできます。

## <a name="test-your-app"></a>アプリをテストする

### <a name="run-locally"></a>ローカルで実行する

上記のコードに従っている場合は、アプリをビルドしてテスト デバイスまたはエミュレーターにデプロイします。 サインインして Azure AD または個人用 Microsoft アカウントのトークンを取得できるようになります。 ユーザーのサインイン後に、Microsoft Graph の `/me` エンドポイントから返されたデータがこのアプリに表示されます。 

問題が発生した場合は、このドキュメントまたは MSAL ライブラリで問題を開いてご連絡ください。 

### <a name="consent-to-your-app"></a>アプリに同意する

ユーザーは、アプリに初めてサインインするときに、Microsoft Identity から、要求されたアクセス許可に同意するよう求められます。  ほとんどのユーザーはこれに同意できますが、一部の Azure AD テナントではユーザーによる同意が無効になっており、全ユーザーに代わって管理者が同意を行う必要があります。  このシナリオに対応できるように、必ず Azure portal でアプリのスコープを登録しておいてください。

## <a name="help-and-support"></a>ヘルプとサポート

このチュートリアルまたは Microsoft ID プラットフォームで問題が発生した場合は、 [ヘルプとサポート](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)に関するページを参照してください。

