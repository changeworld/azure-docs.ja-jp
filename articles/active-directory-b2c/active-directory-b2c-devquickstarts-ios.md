<properties pageTitle="Azure Active Directory B2C プレビュー: iOS アプリケーションからの Web API の呼び出し | Microsoft Azure" description="この記事では、OAuth 2.0 ベアラー トークンを使用して Node.js Web API を呼び出す iOS の "To-Do List" アプリを作成する方法について説明します。iOS アプリと Web API は、どちらも Azure Active Directory B2C を使用してユーザー ID を管理し、ユーザーを認証します。" services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="hero-article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Azure AD B2C プレビュー: iOS アプリケーションからの Web API の呼び出し

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C を使用すると、強力なセルフサービス方式の ID 管理機能を、わずかな手順で iOS アプリや Web アプリに追加できます。この記事では、OAuth 2.0 べアラー トークンを使用して Node.js Web API を呼び出す iOS の "To-Do List" アプリを作成する方法を説明します。iOS アプリと Web API は、どちらも Azure AD B2C を使用してユーザー ID を管理し、ユーザーを認証します。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	このクイック スタートを正しく利用するには、Azure AD B2C によって保護されている Web API を事前に用意する必要があります。すぐに使用できる .NET 用と Node.js 用の Web API があからじめビルドされています。このチュートリアルでは、Node.js Web API のサンプルが構成済みであることを前提としています。詳細については、「[B2C プレビュー: Node.js を使用して Web API をセキュリティで保護する](active-directory-b2c-devquickstarts-api-node.md)」参照してください。

> [AZURE.NOTE]
	この記事では、Azure AD B2C を使用してサインイン、サインアップ、プロファイルの管理を実装する方法については説明しません。ユーザーが認証された後の Web API の呼び出しを中心に説明します。まだ Azure AD B2C の基本を理解していない場合は、[.NET Web アプリ入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)で学習してください。

## Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。まだディレクトリを作成していない場合は、[B2C ディレクトリを作成](active-directory-b2c-get-started.md)してから先に進んでください。

## アプリケーションの作成

次に、B2C ディレクトリにアプリを作成する必要があります。これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。ここでは、アプリと Web API の両方が単一の**アプリケーション ID** で表されます。これは、アプリと Web API が 1 つの論理アプリを構成するためです。アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従います。次を行ってください。

- アプリケーションに **Web アプリまたは Web API** を含めます。
- **[応答 URL]** に「`http://localhost:3000/auth/openid/return`」と入力します。これはこのサンプル コードで使用する既定の URL です。
- アプリケーション用の**アプリケーション シークレット**を作成し、それをメモしておきます。この情報は後で必要になります。
- アプリに割り当てられた**アプリケーション ID** をコピーしておきます。この情報も後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが[ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。このアプリには、3 つの ID エクスペリエンス (サインアップ、サインイン、および Facebook を使用したサインイン) が含まれています。[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- サインアップ ポリシーで、**[表示名]** とサインアップ属性を選択します。
- すべてのポリシーで、アプリケーション要求として**表示名**と**オブジェクト ID** を選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの**名前**をコピーしておきます。名前には、`b2c_1_` というプレフィックスが付加されています。これらのポリシー名は後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーを作成したら、アプリをビルドできます。

この記事では、作成したポリシーの使用方法については説明しません。ポリシーが Azure AD B2C でどのように機能するかを学習する場合は、「[Azure AD B2C プレビュー: .NET Web アプリを構築する](active-directory-b2c-devquickstarts-web-dotnet.md)」から始めてください。

## コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS) で管理されています。[スケルトン プロジェクトを .zip ファイルとしてダウンロード](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip)し、手順に従ってサンプルを構築できます。スケルトンを複製することもできます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **このチュートリアルを完了するには、スケルトンをダウンロードする必要があります。** 完全に機能するアプリケーションを iOS 上に実装するのは複雑な作業であるため、**スケルトン**には、チュートリアルを完了した後で実行される UX コードが含まれています。これは、開発者の時間を短縮するための措置です。UX コードは、B2C を iOS アプリケーションに追加する方法のトピックと完全には連動していません。

また、完成済みのアプリも、[.zip ファイルとして入手する](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip)か、同じリポジトリの `complete` ブランチで入手できます。

次に、CocoaPods を使用して `podfile` を読み込みます。これにより、読み込む新しい Xcode ワークスペースが作成されます。CocoaPods がない場合は、[CocoaPods をセットアップするための Web サイト](https://cocoapods.org)にアクセスしてください。

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## iOS タスク アプリケーションを構成する

iOS タスク アプリが Azure AD B2C と通信できるようにするために、一般的なパラメーターをいくつか指定する必要があります。`Microsoft Tasks` フォルダーで、プロジェクトのルートにある `settings.plist` ファイルを開き、`<dict>` セクションの値を置き換えます。これらの値は、アプリケーション全体で使用されます。

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
	</array>
	<key>redirectUri</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>taskWebAPI</key>
	<string>http://localhost/tasks:3000</string>
	<key>emailSignUpPolicyId</key>
	<string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
	<key>faceBookSignInPolicyId</key>
	<string><your sign in policy for FB></string>
	<key>emailSignInPolicyId</key>
	<string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
	<key>fullScreen</key>
	<false/>
	<key>showClaims</key>
	<true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## アクセス トークンの取得とタスク API の呼び出し

このセクションでは、Microsoft のライブラリとフレームワークを使用して、Web アプリで OAuth 2.0 トークンの交換を完了する方法について説明します。認証コードとアクセス トークンについて詳しくない場合は、「[Azure AD B2C プレビュー: 認証プロトコル](active-directory-b2c-reference-protocols.md)」に目を通すことをお勧めします。

### メソッドを使用してヘッダー ファイルを作成する

選択したポリシーを使用してトークンを取得し、タスク サーバーを呼び出すためのメソッドが必要です。ここで、そのメソッドを設定します。

Xcode プロジェクトの `/Microsoft Tasks` の下に、`samplesWebAPIConnector.h` という名前のファイルを作成します。

何を実行する必要があるかを定義する次のコードを追加します。

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

これは、API に対する単純な作成、読み取り、更新、削除 (CRUD) 操作と `doPolicy` メソッドを実行するコードです。このメソッドを使用して、必要なポリシーが設定されたトークンを取得できます。

他の 2 つのヘッダー ファイルを定義する必要があることに注意してください。これらのファイルでは、タスク項目とポリシー データを保持します。次に、これらを作成します。

次のコードを含むファイル `samplesTaskItem.h` を作成します。

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

ポリシー データを保持するファイル `samplesPolicyData.h` を作成します。

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### タスク項目とポリシー項目の実装を追加する

ヘッダー ファイルを用意できたら、サンプルに使用するデータを格納するためのコードを記述できます。

次のコードを含むファイル `samplesPolicyData.m` を作成します。

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### iOS 用の ADAL を呼び出すためのセットアップ コードを記述する

UI 用のオブジェクトを格納するクイック コードが完成しました。次は、`settings.plist` で設定したパラメーターを使用して iOS 用の Active Directory 認証ライブラリ (ADAL) にアクセスするためのコードを記述する必要があります。これで、タスク サーバーに提供するアクセス トークンが取得されます。

すべての作業は、`samplesWebAPIConnector.m` で行います。

まず、`samplesWebAPIConnector.h` ヘッダー ファイルに記述した `doPolicy()` の実装を作成します。

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

このメソッドはシンプルです。作成済みの `samplesPolicyData` オブジェクト、親 `ViewController`、コールバックを入力として受け取ります。このコールバックは興味深いもので、内容は次のようになっています。

- `completionBlock` の型が `ADProfileInfo` になっており、`userInfo` オブジェクトを使用して返されます。`ADProfileInfo` は、クレームを含めて、サーバーからのすべての応答を保持する型です。
- `readApplicationSettings` にも注目してください。これは、`settings.plist` に指定したデータを読み取ります。
- 最後に、大きな `getClaimsWithPolicyClearingCache` メソッドがあります。これが iOS 用の ADAL への実際の呼び出しであり、記述する必要がある呼び出しです。後で、ここに戻ります。

次に、前述の大きなメソッド `getClaimsWithPolicyClearingCache` を記述します。これは 1 つのセクションを立てて説明するに値する大きさがあります。

### iOS 用の ADAL の呼び出しを作成する

GitHub からスケルトンをダウンロードできたら、サンプル アプリケーションで役に立つ呼び出しが複数用意されていることを確認できます。それらは、すべて `get(Claims|Token)With<verb>ClearningCache` のパターンに従っています。Objective C の規約に従ったこのパターンは、英文の表記によく似ています。たとえば、指定した追加パラメーターを使用してトークンを取得し、キャッシュをクリアする場合は、`getTokenWithExtraParamsClearingCache()` になります。

これから記述するのは、"指定したポリシーを使用してクレームとトークンを取得し、キャッシュはクリアしない" メソッド、`getClaimsWithPolicyClearingCache` です。トークンは常に ADAL から取得するため、メソッドに "クレームとトークン" を指定する必要はありません。ただし、クレームを解析するオーバーヘッドなしでトークンのみを取得する場合があるため、スケルトンには `getTokenWithPolicyClearingCache` というクレームなしのメソッドが用意されています。

このコードを記述します。

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

最初の部分は見覚えがあるはずです。

- `settings.plist` に指定した設定を読み込んで `data` に割り当てます。
- iOS 用の ADAL から送られるすべてのエラーを受け取る `ADAuthenticationError` を設定します。
- ADAL の呼び出しを設定する `authContext` を作成します。それを authority に渡して、操作を開始します。
- `authContext` に親コントローラーへの参照を渡して、それに返すことができるようにします。
- `settings.plist` 内の文字列だった `redirectURI` を、ADAL で求められる NSURL 型に変換します。
- `correlationId` を設定します。これは、クライアントとサーバー間の呼び出しで使用される UUID です。これはデバッグで役立ちます。

次に、ADAL の実際の呼び出しを開始します。以前の iOS 用 ADAL の使い方で見てきた呼び出しと違ってくるのはここからです。

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

この呼び出しは非常に単純です。

`scopes`: サインインしているユーザーにサーバーから要求し、サーバーに渡すスコープです。B2C プレビューでは、`client_id` を渡します。ただし、これは今後、読み取りスコープに変更される予定です。その際に、このドキュメントも更新されます。`additionalScopes`: アプリケーションで使用できる追加のスコープです。これは将来使用される予定です。`clientId`: ポータルから取得したアプリケーション ID。`redirectURI`: トークンが戻されるリダイレクト先。`identifier`: ユーザーを識別して、キャッシュ内に使用可能なトークンが存在するかどうかを判別できるようにする方法。これにより、サーバーに別のトークンを要求する必要がなくなります。これは `ADUserIdentifier` と呼ばれる型で実行され、使用したいものを ID として指定できます。`username` を使用してください。`promptBehavior`: これは廃止されました。`AD_PROMPT_ALWAYS` を使用してください。`extraQueryParameters`: URL エンコード形式でサーバーに渡す追加のパラメーター。`policy`: 呼び出すポリシー。このチュートリアルで最も重要な部分です。

`completionBlock` で `ADAuthenticationResult` を渡しているのが確認できます。ここには、(呼び出しが成功した場合) トークンとプロファイル情報が含まれます。

上記のコードを使用して、指定したポリシー用のトークンを取得できます。このトークンを使用して API を呼び出すことができます。

### サーバーへのタスク呼び出しを作成する

取得したトークンを API に提供して、認証される必要があります。

次の 3 つのメソッドを実装する必要があります。

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList` は、サーバー内のタスクを表す配列を提供します。`addTask` と `deleteTask` は、その後のアクションを実行し、成功した場合は `true` または `false` を返します。

最初に、`getTaskList` を書き込みます。

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

このタスク コードについてはこのチュートリアルでは説明しませんが、興味深い点があります。それは、タスクの URL を使用する `craftRequest` メソッドの存在です。このメソッドは、受け取ったアクセス トークンを使用してサーバーに対する要求を作成する際に使用します。次はそれを記述します。

次のコードを `samplesWebAPIConnector.m` ファイルに追加します。

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

このコードでは、Web URI (Uniform Resource Identifier) を受け取り、HTTP の `Bearer` ヘッダーを使用してトークンを追加して、それを返します。`getTokenClearingCache` API を呼び出します。一見おかしなものに見えるかもしれませんが、この呼び出しは、トークンをキャッシュから取得して、まだ有効であることを確認するために使用します (`getToken` 呼び出しが ADAL に依頼してこの作業を実行します)。 このコードは呼び出しごとに使用します。次に、追加のタスク メソッドを記述します。

`addTask` を記述します。

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

これは同じパターンに従っていますが、実装する必要のある最後のメソッド `convertTaskToDictionary` を導入しています。このメソッドは、配列を受け取ってディクショナリ オブジェクトに変換します。このオブジェクトは、サーバーに渡す必要があるクエリ パラメーターに容易に変換できます。このコードはシンプルです。

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

次に、`deleteTask` を記述します。

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### アプリケーションにサインアウトを追加する

実装する必要がある最後の作業は、アプリケーションのサインアウトです。これは簡単です。`sampleWebApiConnector.m` ファイルに次のコードを追加します。

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## サンプル アプリを実行する

最後に、アプリを Xcode でビルドして実行します。アプリにサインアップまたはサインインし、サインインしているユーザーのタスクを作成します。サインアウトして、別のユーザーとしてもう一度サインインし、そのユーザーのタスクを作成します。

API でタスクがユーザーごとに保存されたことを確認します。これは、API が、受信したアクセス トークンからユーザー ID を抽出したためです。

参照用に、完成したサンプルが[ここに .zip として提供されています](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip)。GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## 次のステップ

さらに高度な B2C のトピックに進むことができます。次のトピックをご覧ください。

[Node.js Web アプリからの Node.js Web API の呼び出し]()

[B2C アプリの UX のカスタマイズ]()

<!---HONumber=AcomDC_0302_2016-->