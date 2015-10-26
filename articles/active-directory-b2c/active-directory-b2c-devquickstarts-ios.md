<properties pageTitle="Azure AD B2C プレビュー: iOS アプリケーションからの Web API の呼び出し | Microsoft Azure" description="この記事では、OAuth 2.0 べアラー トークンを使用して node.js Web API を呼び出す iOS の "To-Do List" アプリを作成する方法を示します。iOS アプリと Web API は、どちらも Azure AD B2C を使用してユーザー ID を管理し、ユーザーを認証します。" services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# Azure AD B2C プレビュー: iOS アプリケーションからの Web API の呼び出し

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD B2C を使用すると、強力なセルフサービス方式の ID 管理機能をわずかな手順で iOS アプリと Web API に追加できます。この記事では、OAuth 2.0 べアラー トークンを使用して node.js Web API を呼び出す iOS の "To-Do List" アプリを作成する方法を示します。iOS アプリと Web API は、どちらも Azure AD B2C を使用してユーザー ID を管理し、ユーザーを認証します。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]このクイック スタートを完全に機能させるためには、前提条件として、Azure AD で B2C によって保護されている Web API が存在する必要があります。すぐに使用できる .Net と node.js 用の Web API が既にビルドされています。このチュートリアルでは、node.js Web API のサンプルが構成されているとみなします。「[Azure Active Directory Web-API for Node.js sample (Node.js 用 Azure Active Directory Web APIのサンプル)](active-directory-b2c-devquickstarts-api-node.md`)」を参照してください。

> [AZURE.NOTE]この記事では、サインイン、サインアップ、およびプロファイル管理を Azure AD B2C で実装する方法については説明しません。ユーザーが既に認証された後での Web API の呼び出しに焦点を合わせています。まだ行っていない場合は、先に「[Azure AD B2C プレビュー: .NET Web アプリをビルドする](active-directory-b2c-devquickstarts-web-dotnet.md)」で Azure AD B2C の基本を学習してください。

## 1\.Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリの作成](active-directory-b2c-get-started.md)に関するページを参照してください。

## 2\.アプリケーションの作成

次に、B2C ディレクトリ内にアプリを作成する必要があります。これによって、 アプリと安全に通信するために必要ないくつかの情報が Azure AD に提供されます。ここでは、アプリと Web API の両方が単一の**アプリケーション ID**で表されます。これは、アプリと Web API が 1 つの論理アプリを構成するためです。アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従います。このとき、

- アプリケーションに **Web アプリ/Web API** を含めます。
- `http://localhost:3000/auth/openid/return` を**応答 URL** として入力します。これはこのコード例で使用する既定の URL です。
- アプリケーション用の**アプリケーション シークレット**を作成し、それをメモしておきます。このプロジェクトはすぐに必要になります。
- アプリに割り当てられた**アプリケーション ID** をメモしておきます。こちらもすぐに必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\.ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが[**ポリシー**](active-directory-b2c-reference-policies.md)によって定義されます。このアプリには、3 つの ID エクスペリエンス (サインアップ、サインイン、および Facebook でのサインイン) が含まれます。[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- サインアップ ポリシーで、**[表示名]** と他のいくつかのサインアップ属性を選択します。
- すべてのポリシーで、**表示名**と**オブジェクト ID** のアプリケーション クレームを選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの **[名前]** をメモしておきます。名前には、プレフィックス `b2c_1_` が付加されます。これらのポリシー名はすぐに必要になります。 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

この記事では、作成したポリシーの使用方法については説明しません。ポリシーが Azure AD B2C でどのように機能するかを学習する場合は、「[Azure AD B2C プレビュー: .NET Web アプリを構築する](active-directory-b2c-devquickstarts-web-dotnet.md)」から始めてください。

## 4\.コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS) で管理されています。手順に従ってサンプルを構築するために、[スケルトン プロジェクトを .zip 形式でダウンロードする](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip)か、次のようにスケルトンを複製できます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE]**スケルトンをダウンロードすることは、このチュートリアルを完了するために必要な作業です。** 完全に機能するアプリケーションを iOS 上に実装するのは複雑な作業であるため、**スケルトン**には、この後のチュートリアルを完了した後で実行される UX コードが含まれています。これは、開発者の時間を短縮するための手段です。UX コードは B2C を iOS アプリケーションに追加するトピックと深い関わりはありません。

また、完成済みのアプリも、[.zip 形式で入手する](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip)か、同じリポジトリの `complete` ブランチで利用できます。


次に Cocoapods を使用してポッドファイルを読み込みます。これにより、読み込む新しい XCode ワークスペースが作成されます。Cocoapods がない場合は、[cocoapods をセットアップするための Web サイト](https://cocoapods.org)にアクセスしてください。

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5\.iOS タスク アプリケーションを構成する

iOS タスク アプリが Azure AD B2C と通信するために、指定する必要がある一般的なパラメーターがいくつかあります。`Microsoft Tasks` フォルダーで、`settings.plist` ファイルをプロジェクトのルートで開き、`<dict>` セクションの値を置き換えます。これらの値は、アプリケーション全体で使用されます。

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
		<string></string>
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

## 6\.アクセス トークンを取得し、タスク API を呼び出す

このセクションでは、Microsoft のライブラリとフレームワークを使用して、Web アプリで OAuth 2.0 トークンの交換を完了する方法を示します。**認証コード**と**アクセス トークン**の知識がない場合は、「[Azure AD B2C プレビュー: 認証プロトコル](active-directory-b2c-reference-protocols.md)」に目を通すことをお勧めします。

#### 今後使用するメソッドを持つヘッダー ファイルを作成する

選択したポリシーを使用してトークンを取得した後、タスク サーバーを呼び出すメソッドが必要です。ここでその設定を行いましょう。

XCode プロジェクトの /Microsoft Tasks の下に、`samplesWebAPIConnector.h` という名前のファイルを作成します。

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

これらは、API に関する単純な CRUD 操作と、目的のポリシーを使用してトークンを取得できる `doPolicy` メソッドです。

他にも、タスク項目とポリシー データを保持するために定義する必要がある 2 つのヘッダー ファイルがあります。次にこれらを作成しましょう。

次のコードを含む `samplesTaskItem.h` という名前のファイルを作成します。

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

ポリシー データを保持する `samplesPolicyData.h` ファイルも作成しましょう。

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### タスク項目とポリシー項目の実装を追加する

これでヘッダー ファイルは用意できました。次に、このチュートリアルで使用するデータを格納するためのコードを記述する必要があります。

次のコードを含む `samplesPolicyData.m` という名前のファイルを作成します。

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

#### iOS 用の ADAL を呼び出すためのセットアップ コードを記述する

UI 用のオブジェクトを保存するクイック コードが完成しました。次に、タスク サーバーに提供するアクセス トークンを取得するために、`settings.plist` ファイルに配置したパラメーターを使用して iOS 用の ADAL にアクセスするコードを記述する必要があります。これは冗長になる可能性があるため、集中力を保ってください。

すべての作業は、`samplesWebAPIConnector.m` の中で行われます。

まず、`samplesWebAPIConnector.h` ヘッダー ファイル内に記述した `doPolicy()` の実装を作成します。

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }
    
    NSDictionary* params = [self convertPolicyToDictionary:policy];
    
    [self getClaimsWithPolicyClearingCache:NO policy:policy params:params parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {
        
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

このメソッドは非常に単純です。このメソッドでは、入力として、少し前に作成した `samplesPolicyData` オブジェクト、親 ViewController、およびコールバックを使用します。このコール バックは興味深いものであり、その内容は次のとおりです。

1. `completionBlock` は型として ADProfileInfo を持っています。これは `userInfo` オブジェクトで返されます。ADProfileInfo は、サーバーからのすべての応答、特にクレームを保持する型です。 

2. `readApplicationSettings` があります。これは、`settings.plist` に指定したデータを読み取ります。
3. ポリシーを取得し、それを URL としてサーバーに送信するメソッド `convertPolicyToDictionary:policy` があります。このヘルパー メソッドは次の手順で記述します。
4. 最後に、やや大きな `getClaimsWithPolicyClearingCache` メソッドがあります。これが iOS 用の ADAL への実際の呼び出しであり、記述する必要がある呼び出しです。これは後で実行します。


次に、今記述したコードの下に、`convertPolicyToDictionary` メソッドを記述します。

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }
    
    return dictionary;
}

+(NSDictionary*) convertPolicyToDictionary:(samplesPolicyData*)policy
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    
    if (policy.policyID){
        [dictionary setValue:policy.policyID forKey:@"p"];
    }
    
    return dictionary;
}

```
このどちらかといえば単純なコードは、ポリシーに p を追加して、クエリが ?p=<policy> となるようにします。

次に、前述の大きなメソッド `getClaimsWithPolicyClearingCache` を記述しましょう。それは 1 つのセクションを立てて説明するに値する大きさがあります。

#### iOS 用の ADAL への呼び出しを作成する

GitHub からのスケルトンをダウンロードしている場合は、サンプル アプリケーションで役に立つさまざまな呼び出しが既に用意されています。それらは、すべて `get(Claims|Token)With<verb>ClearningCache` のパターンに従っています。Objetive C の規約に従ったこのパターンは、英文の表記とほぼ同じです。たとえば、"get a Token with extra parameters I provide you and clear the cache (指定した追加パラメーターを使用してトークンを取得し、キャッシュをクリアする)" を行うとします。これは `getTokenWithExtraParamsClearingCache()` になります。非常に単純です。

これから記述する `getClaimsWithPolicyClearingCache` は、"指定したポリシーを使用してクレームとトークンを取得し、キャッシュはクリアしない" メソッドです。トークンは常に ADALから戻るため、メソッドに "クレームとトークン" を指定する必要はありません。ただし、クレームを解析するオーバーヘッドなしでトークンのみを取得する場合があるため、スケルトンには `getTokenWithPolicyClearingCache` というクレームなしのメソッドが用意されています。

このコードを記述していきましょう。

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

最初の部分は見覚えがあるはずです。`Settings.plist` に指定した設定を読み込んで `data` に割り当てます。その後、iOS 用の ADAL から送られるすべてのエラーを受け取る `ADAuthenticationError` を設定します。ADAL の呼び出しを設定する `authContext` も作成します。それを *authority* に渡して、操作を開始します。また、`authContext` に親コントローラーへの参照を渡して、それに返すことができるようにします。さらに、`settings.plist` 内の文字列だった `redirectURI` を、ADAL で求められる NSURL 型に変換します。最後に、クライアントとサーバー間の呼び出しで使用される UUID である `correlationId` を設定します。これはデバッグで役立ちます。

次に、ADAL への実際の呼び出しを開始します。過去の iOS 用の ADAL の使い方で見てきた呼び出しと違ってくるのはここからです。

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

**scopes** - ユーザーがログインしているサーバーから要求するサーバーに渡すスコープです。B2C プレビューでは client\_id を渡します。ただし、これは、読み取りスコープに代わる予定です。このドキュメントはその時点で更新されます。**addtionalScopes** - アプリケーションで使用できるその他のスコープです。これは将来使用される予定です。 **clientId** - ポータルから取得したアプリケーション ID。 **redirectURI** - トークンが戻されるリダイレクト先。 **identifier** - ユーザーを識別して、キャッシュ内に使用可能なトークンが存在するか、常にサーバーに別のトークンを要求するかを判別できるようにする方法。これは `ADUserIdentifier` と呼ばれる型で実行され、使用するものを ID として指定できます。ユーザー名を使用する必要があります。 **promptBehavior** - これは使用されなくなりました。AD\_PROMPT\_ALWAYS にする必要があります。 **extraQueryParameters** - URL エンコード形式でサーバーに渡す追加のパラメーター。 **policy** - 呼び出すポリシー。このチュートリアルで最も重要な部分です。

completionBlock で `ADAuthenticationResult` を渡しているのがわかります。この中にトークンとプロファイル情報が格納されています (呼び出しが成功した場合)。

上記のコードを使用して、指定したポリシー用のトークンを取得できます。このトークンを使用して API を呼び出します。

#### サーバーへのタスク呼び出しを作成する

取得したトークンを API に提供して、認証される必要があります。

3 つのメソッドを実装する必要があることを思い出してください。

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

`getTasksList` は、サーバー内のタスクを表す配列を提供します。`addTask` と `deleteTask` は、その後のアクションを実行し、成功した場合は TRUE または FALSE を返します。

`getTaskList` の記述から始めましょう。

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

このタスク コードについてはこのチュートリアルでは説明しませんが、興味深い点があります。それはタスクの URL を使用する `craftRequest` メソッドの存在です。このメソッドが、受け取ったアクセス トークンを使用してサーバーに対する要求を作成するために使用するメソッドです。それを次に記述しましょう。

次のコードを `samplesWebAPIConnector.m' ファイルに追加します。

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

このタスクは、Web URI を受け取り、HTTP の `Bearer` ヘッダーにトークンを追加して、それを返します。呼び出す `getTokenClearingCache` API は、一見おかしなものに見えるかもしれませんが、この呼び出しは、トークンをキャッシュから取得し、そのトークンがまだ有効であることを確認するために使用するだけです (getToken* 呼び出しが ADAL に依頼してこの作業を代わって実行してくれます)。このコードは呼び出しごとに使用します。その他のタスク メソッドの作成に戻りましょう。

`addTask` を記述しましょう。

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

これは同じパターンに従いますが、実装する必要がある別の (最後の) メソッドが導入されています。それは、配列を受け取ってディクショナリ オブジェクトに変換する `convertTaskToDictionary` です。ディクショナリ オブジェクトは、サーバーに渡す必要があるクエリ パラメーターに容易に変換できます。このコードは非常に単純です。

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }
    
    return dictionary;
}

```

最後に、`deleteTask` を記述しましょう。

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

実装する必要がある最後の作業は、アプリケーションのサインアウトです。これは比較的単純です。`sampleWebApiConnector.m` ファイルに次のコードを追加します。

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

## 9\.サンプル アプリを実行する

最後に、アプリを xCode でビルドして実行します。アプリにサインアップまたはサインインし、サインインしているユーザーのタスクを作成します。サインアウトして、別のユーザーとしてもう一度サインインし、そのユーザーのタスクを作成します。

API でタスクがユーザーごとに保存されたことを確認します。これは、API が、受信したアクセス トークンからユーザーID を抽出したためです。

参照用に、完成したサンプルが[ここに .zip として提供されています](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip)。または、GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## 次のステップ

さらに高度な B2C のトピックに進むことができます。次のチュートリアルを試してみてください。

[node.js Web アプリからの node.js Web API の呼び出し]()

[B2C アプリの UX のカスタマイズ]()

<!---HONumber=Oct15_HO3-->