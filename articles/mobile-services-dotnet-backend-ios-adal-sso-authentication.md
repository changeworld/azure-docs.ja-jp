<properties urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証 (iOS) | モバイル デベロッパー センター" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="wesmc,mahender" />

# Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証

[WACOM.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

このチュートリアルでは、Active Directory 認証ライブラリを使用して、クイック スタート プロジェクトに認証を追加します。

ユーザーを認証できるようにするには、Azure Active Directory (AAD) にアプリケーションを登録する必要があります。この処理は 2 段階の手順で実行されます。まず、モバイル サービスを登録し、モバイル サービスに対するアクセス許可を公開する必要があります。次に、iOS アプリケーションを登録してこれらのアクセス許可へのアクセス権を付与する必要があります。


>[WACOM.NOTE] このチュートリアルの目的は、iOS アプリケーションのシングル サインオン Azure Active Directory 認証をできるようにするための Mobile Services のしくみを説明することにあります。Mobile Services を初めて使用する場合は、チュートリアル「[モバイル サービスの使用]」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1. [モバイル サービスを Azure Active Directory に登録する]
2. [Azure Active Directory にアプリケーションを登録する]
3. [認証を要求するようにモバイル サービスを構成する]
4. [クライアント アプリケーションに認証コードを追加する]
5. [認証を使用してクライアントをテストする]

このチュートリアルには、次のものが必要です。

* XCode 4.5 および iOS 6.0 (またはそれ以降のバージョン)
* 「[モバイル サービスの使用]」または「[データの使用]」を完了している。
* Microsoft Azure Mobile Services SDK
* [iOS 向け Active Directory 認証ライブラリ]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>認証を要求するようにモバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>クライアント アプリケーションに認証コードを追加する

1. [iOS 向け Active Directory 認証ライブラリ]をダウンロードして、プロジェクトに含めます。また、ADAL ソースからストーリーボードを追加します。

2. QSTodoListViewController で、ADAL を次に含めます。

        #import "ADALiOS/ADAuthenticationContext.h"

2. その後、次のメソッドを追加します。

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }


6. 前の `loginAndGetData` メソッドのコード内で、**INSERT-AUTHORITY-HERE** を、アプリケーションをプロビジョニングしたテナントの名前に置き換えます。この名前は https://login.windows.net/tenant-name.onmicrosoft.com 形式で指定する必要があります。この値は、[Azure の管理ポータル]の [Azure Active Directory] の [ドメイン] タブからコピーできます。

7. 前の `loginAndGetData` メソッドのコードで、**INSERT-RESOURCE-URI-HERE** を、モバイル サービスの **App ID URI** に置き換えます。「[How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]」トピックに従った場合は、アプリケーション ID URI が https://todolist.azure-mobile.net/login/aad と同様になる必要があります。

8. 前の `loginAndGetData` メソッドのコードで、**INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。

9. 前の `loginAndGetData` メソッドのコードで、**INSERT-REDIRECT-URI-HERE** を、モバイル サービスの /login/done エンドポイントに置き換えます。これは、https://todolist.azure-mobile.net/login/done のような文字列です。


3. QSTodoListViewController で、`[self refresh]` を次に置き換えて、`ViewDidLoad` を変更します。

        [self loginAndGetData];

## <a name="test-client"></a>認証を使用してクライアントをテストする

1. [Product] メニューの [Run] をクリックして、アプリケーションを開始します。
2. Azure Active Directory にログインするための画面が表示されます。  
3. アプリケーションが認証を実行し、Todo 項目を返します。

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- Anchors. -->
[モバイル サービスを Azure Active Directory に登録する]: #register-mobile-service-aad
[Azure Active Directory にアプリケーションを登録する]: #register-app-aad
[認証を要求するようにモバイル サービスを構成する]: #require-authentication
[クライアント アプリケーションに認証コードを追加する]: #add-authentication-code
[認証を使用してクライアントをテストする]: #test-client

<!-- URLs. -->
[データの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started-data/
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure 管理ポータル]: https://manage.windowsazure.com/
[iOS 向け Active Directory 認証ライブラリ]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
