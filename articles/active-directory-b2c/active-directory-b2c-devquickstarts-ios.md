---
title: "Azure Active Directory B2C: サード パーティ ライブラリを使用して iOS アプリケーションから Web API を呼び出す | Microsoft Docs"
description: "この記事では、OAuth 2.0 ベアラー トークンとサード パーティ ライブラリを使用して、Node.js Web API を呼び出す iOS の &quot;To-Do List&quot; アプリを作成する方法について説明します。"
services: active-directory-b2c
documentationcenter: ios
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: d818a634-42c2-4cbd-bf73-32fa0c8c69d3
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: objectivec
ms.topic: hero-article
ms.date: 07/26/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1b570e66afb7a4d3f7fc9b65600bfa7dc0fcc4b5


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>Azure AD B2C: サード パーティ ライブラリを使用して iOS アプリケーションから Web API を呼び出す
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Microsoft の ID プラットフォームには、OAuth2 や OpenID Connect といったオープンな標準が使用されています。 そのため開発者は、好きなライブラリを活用して Microsoft のサービスと連携させることができます。 Microsoft では、そのプラットフォームを他のライブラリから使用する開発者のために、サード パーティのライブラリから Microsoft の ID プラットフォームに接続するための構成方法を紹介するチュートリアルを作成しています。この記事もそうしたチュートリアルの一つです。 Microsoft の ID プラットフォームには、[RFC6749 OAuth2 仕様](https://tools.ietf.org/html/rfc6749)を実装するほとんどのライブラリから接続できます。

ここで紹介する構成サンプルは OAuth2 や OpenID Connect に精通している読者を想定しており、それ以外の方にとっては、あまり参考にならない可能性があります。 その場合は、 [対応プロトコルについて簡単に解説したこちらの記事](active-directory-b2c-reference-protocols.md)に目を通すことをお勧めします。

> [!NOTE]
> Microsoft のプラットフォーム機能のうち、これらの標準に含まれている式が使われているいくつかの機能 (条件付きアクセスや Intune ポリシー管理) については、オープン ソースの Microsoft Azure Identity Libraries を使用する必要があります。 
> 
> 

Azure Active Directory のシナリオおよび機能のすべてが B2C プラットフォームでサポートされているわけではありません。  B2C プラットフォームを使用する必要があるかどうかを判断するには、 [B2C の制限事項](active-directory-b2c-limitations.md)に関するページをお読みください。

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C ディレクトリの取得
Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。 ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。 まだディレクトリを作成していない場合は、 [B2C ディレクトリを作成](active-directory-b2c-get-started.md) してから先に進んでください。

## <a name="create-an-application"></a>アプリケーションの作成
次に、B2C ディレクトリにアプリを作成する必要があります。 これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。 ここでは、アプリと Web API の両方が単一の**アプリケーション ID** で表されます。これは、アプリと Web API が 1 つの論理アプリを構成するためです。 アプリを作成するには、 [こちらの手順](active-directory-b2c-app-registration.md)に従ってください。 次を行ってください。

* **モバイル デバイス** をアプリケーションに含めます。
* アプリに割り当てられた **アプリケーション ID** をコピーしておきます。 この情報も後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>ポリシーの作成
Azure AD B2C では、すべてのユーザー エクスペリエンスが [ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。 このアプリには、サインインとサインアップを組み合わせた 1 つの ID エクスペリエンスが含まれています。 [ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、このポリシーを種類ごとに作成する必要があります。 ポリシーを作成するときは、以下の操作を必ず実行してください。

* ポリシーで、 **[表示名]** とサインアップ属性を選択します。
* すべてのポリシーで、アプリケーション要求として **[表示名]** と **[オブジェクト ID]** を選択します。 その他のクレームも選択できます。
* ポリシーの作成後、各ポリシーの **名前** をコピーしておきます。 名前には、 `b2c_1_`というプレフィックスが付加されています。  このポリシー名は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

ポリシーを作成したら、アプリを構築できます。

## <a name="download-the-code"></a>コードのダウンロード
このチュートリアルのコードは、 [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)で管理されています。  チュートリアルを先に進めるため、 [アプリを .zip ファイル (/archive/master.zip) としてダウンロードする](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)か、次のように複製することができます。

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

または、完成済みのコードをダウンロードして、すぐに開始することもできます。 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>サード パーティ製ライブラリ nxoauth2 のダウンロードとワークスペースの起動
このチュートリアルでは、Mac OS X & iOS (Cocoa & Cocoa Touch) 用の OAuth2 ライブラリ OAuth2Client を GitHub からクローンして使用します。 このライブラリのベースとなっているのは、OAuth2 仕様のドラフト 10 です。 このライブラリは、ネイティブ アプリケーション プロファイルを実装し、エンド ユーザーの承認エンドポイントをサポートしています。 これらの条件さえ整えば、Microsoft の ID プラットフォームと連携させることができます。

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>CocoaPods を使用してプロジェクトにライブラリを追加する
CocoaPods は、Xcode プロジェクト用の依存関係マネージャーです。 上記のインストール手順は、CocoaPods によって自動的に管理されます。

```
$ vi Podfile
```
このポッドファイルに次のコードを追加します。

```
 platform :ios, '8.0'

 target 'SampleforB2C' do

 pod 'NXOAuth2Client'

 end
```

次に Cocoapods を使用してポッドファイルを読み込みます。 これにより、読み込む新しい XCode ワークスペースが作成されます。

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>プロジェクトの構造
プロジェクトのスケルトンには、次の構造をセットアップしてあります。

* **マスター ビュー** (タスク ウィンドウを含む)
* **タスクの追加ビュー** (選択したタスクに関するデータ用のビュー)
* **ログイン ビュー** (ユーザーがアプリにサインインするときに使用する)

ここでは、プロジェクト内の各種ファイルにアクセスして認証機能を追加していきます。 その他のコード要素 (ビジュアル コードなど) は ID と密接な関係があるとは言えませんが、参考までに記載しています。

## <a name="create-the-settingsplist-file-for-your-application"></a>アプリケーション用の `settings.plist` ファイルを作成する
各種構成値を配置して一元的に管理できる場所があると、アプリケーションを構成しやすくなります。 また、アプリケーションでの各設定の役割を把握するうえでも役立ちます。 ここでは、これらの値をアプリケーションに設定する方法として、 *プロパティ リスト* を活用します。

* アプリケーション ワークスペースで `Supporting Files` の下に `settings.plist` ファイルを作成して開きます。
* 以下に示す値を入力します (それぞれの値の詳細についてはすぐに説明します)。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

ではこのコードについて詳しく見ていきましょう。

`authURL`、`loginURL`、`bhh`、`tokenURL` については、テナント名の入力が必要であることがわかります。 これは、自分に割り当てられた B2C テナントのテナント名です。 たとえば、`kidventusb2c.onmicrosoft.com` のようにします。Microsoft のオープン ソースである Microsoft Azure Identity Libraries を使用する場合、このデータは、Microsoft のメタデータ エンドポイントを使用して自動的に取得されます。 これらの値を自動的に抽出するためのさまざまな工夫が Microsoft によって施されています。

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

`keychain` の値は、NXOAuth2Client ライブラリがトークンの格納先となるキーチェーンを作成する際に使用するコンテナーです。 多数のアプリで SSO を利用したい場合は、それぞれのアプリケーションで同じキーチェーンを指定できるほか、XCode の Entitlement からそのキーチェーンの使用を要求することができます。 この点については、Apple のドキュメントを参照してください。

各 URL の末尾にある `<policy name>` は、上記で作成したポリシーを配置する場所です。 アプリはフローに応じてこれらのポリシーを呼び出します。

`taskAPI` は、タスクを追加するか、既存のタスクに対してクエリを実行するために、B2C トークンを使用して呼び出す REST エンドポイントです。 この値は、このサンプル専用に設定されています。 この値を変更せずにそのまま使用しても、サンプルは動作します。

残りはライブラリを使用するために必要となる値で、目的のコンテキストに伝える値を保持するために使用されます。

`settings.plist` ファイルは作成したので、次はファイルを読み取るコードが必要です。

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>設定を読み取るための AppData クラスをセットアップする
上で作成した `settngs.plist` ファイルを解析するだけの簡単なファイルを作成し、今後どのクラスからでも設定を取得できるようにしましょう。 クラスから要求されるたびにデータの新しいコピーが作成されるのは望ましくないため、シングルトン パターンを使用し、設定が要求されるたびに作成される同じインスタンスのみを返します。

* `AppData.h` ファイルを作成します。

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* `AppData.m` ファイルを作成します。

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

これで、後述する例のように、いずれかのクラスで `  AppData *data = [AppData getInstance];` を呼び出すだけで、データに簡単にアクセスできるようになりました。

## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>AppDelegate で NXOAuth2Client ライブラリをセットアップする
NXOAuthClient ライブラリでは、いくつかの値を設定する必要があります。 それが完了したら、取得したトークンを使って REST API を呼び出すことができます。 アプリケーションを読み込むときには常に `AppDelegate` が呼び出されることがわかっているため、設定する値はそのファイルに記述するのが妥当です。

* `AppDelegate.m` ファイルを開きます。
* 後で使用するいくつかのヘッダー ファイルをインポートします。

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* `setupOAuth2AccountStore` メソッドを AppDelegate に追加します。

AccountStore を作成し、それに `settings.plist` ファイルから読み取ったデータをフィードする必要があります。

B2C サービスに関して、この時点で把握しておくべき点がいくつかあります。それらを把握しておけば、コードの内容もより理解しやすくなります。

1. Azure AD B2C では、クエリ パラメーターによって指定される " *ポリシー* " を使用して、要求を処理します。 これにより、Azure Active Directory はアプリケーション専用の独立したサービスとして機能できます。 このような追加のクエリ パラメーターを指定するには、カスタム ポリシー パラメーターを使用して `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` メソッドを指定する必要があります。 
2. Azure AD B2C では、他の OAuth2 サーバーとほぼ同じ方法でスコープを使用します。 ただし、B2C はリソースへのアクセスにも、ユーザーの認証にも使用されることから、フローを正しく機能させるためには、いくつかのスコープが欠かせません。 その 1 つが `openid` スコープです。 Microsoft Identity SDK では、自動的に `openid` スコープが指定されるため、SDK 構成にこのスコープを含める必要はありません。 ただし、ここではサード パーティのライブラリを使用するため、このスコープを指定する必要があります。

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
次に、これを AppDelegate の `didFinishLaunchingWithOptions:` メソッドの下で呼び出すようにします。 

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>認証要求の処理に使用する `LoginViewController` クラスを作成する
アカウントのサインインには WebView を使用します。 このビューを介して SMS テキスト メッセージなどの付加的な要素の入力をユーザーに促したり (構成されている場合)、エラー メッセージをユーザーに返したりすることができます。 ここで WebView を設定した後、WebView 内で発生する Microsoft Identity Service からのコールバックに対処するコードを記述します。

* `LoginViewController.h` クラスを作成します。

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

これらの各メソッドを以下で作成します。

> [!NOTE]
> `loginView` はストーリーボード内の実際の Web ビューにバインドしてください。 そうしないと、認証時にポップアップ表示できる Web ビューが存在しない状態になります。
> 
> 

* `LoginViewController.m` クラスを作成します。
* 認証時に状態を保持するいくつかの変数を追加します。

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* WebView のメソッドをオーバーライドして認証を処理する

上記のログインをユーザーから要求されたときの動作を WebView に対して指定する必要があります。 以下のコードをそのまま貼り付けてご使用ください。

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* OAuth2 要求の結果を処理するコードを記述する

WebView から返された redirectURL を処理するコードが必要です。 OAuth2 要求に失敗した場合は、再試行します。 このときライブラリから生成されたエラーをコンソールに表示したり、非同期で処理したりすることができます。 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* 通知ファクトリをセットアップします。

上の `AppDelegate` で作成したものと同じメソッドを作成しますが、今回はサービス内で何が行われているかを通知するために、`NSNotification` をいくつか追加します。 トークンに変更が生じたときに通知を実行するオブザーバーを設定します。 トークンを取得したら、ユーザーを `masterView`に戻します。

```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* サイン ネイティブの要求が開始されるたびにユーザー向けの処理を実行するコードを追加します。

認証要求を受け取るたびに呼び出されるメソッドを作成しましょう。 これは、Web ビューを実際に作成するメソッドとなります。

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* 最後に、上で作成したすべてのメソッドを、 `LoginViewController` が読み込まれるたびに呼び出します。 これを実行するには、Apple から提供されている `viewDidLoad` メソッドに、作成した各メソッドを追加します。

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

これで、サインインのためにアプリケーションを操作する際の主な手段が作成できました。 サインインした後は、受け取ったトークンを使用する必要があります。 そのため、このライブラリを使用して REST API を呼び出すヘルパー コードをいくつか作成します。

## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>REST API への要求を処理する `GraphAPICaller` クラスを作成する
アプリを読み込むたびに、構成が読み込まれます。 トークンを取得したら、この構成に少し手を加える必要があります。 

* `GraphAPICaller.h` ファイルを作成します。

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

このコードを見ると、これから 2 つのメソッド (API からタスクを取得するメソッドと、API にタスクを追加するメソッド) を作成することがわかります。

インターフェイスをセットアップしたので、実装を追加しましょう。

* 認証要求の処理に使用する `GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する
最後に、アプリを Xcode でビルドして実行します。 アプリにサインアップまたはサインインし、サインインしているユーザーのタスクを作成します。 サインアウトして、別のユーザーとしてもう一度サインインし、そのユーザーのタスクを作成します。

API でタスクがユーザーごとに保存されたことを確認します。これは、API が、受信したアクセス トークンからユーザー ID を抽出したためです。

## <a name="next-steps"></a>次のステップ
さらに高度な B2C のトピックに進むことができます。 次のトピックをご覧ください。

[Node.js Web アプリからの Node.js Web API の呼び出し]()

[B2C アプリの UX のカスタマイズ]()




<!--HONumber=Nov16_HO2-->


