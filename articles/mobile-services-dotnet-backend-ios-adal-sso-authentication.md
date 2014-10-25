<properties linkid="develop-mobile-tutorials-sso-with-adal-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender"></tags>

# Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows ストア C#" >Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" class="current">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

このチュートリアルでは、Active Directory 認証ライブラリを使用して、クイック スタート プロジェクトに認証を追加します。

ユーザーを認証できるようにするには、Azure Active Directory (AAD) にアプリケーションを登録する必要があります。この処理は 2 段階の手順で実行されます。まず、モバイル サービスを登録し、モバイル サービスに対するアクセス許可を公開する必要があります。次に、iOS アプリケーションを登録してこれらのアクセス許可へのアクセス権を付与する必要があります。

> [WACOM.NOTE] このチュートリアルの目的は、iOS アプリケーションのシングル サインオン Azure Active Directory 認証をできるようにするための Mobile Services のしくみを説明することにあります。Mobile Services を初めて使用する場合は、チュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [モバイル サービスを Azure Active Directory に登録する][モバイル サービスを Azure Active Directory に登録する]
2.  [Azure Active Directory にアプリケーションを登録する][Azure Active Directory にアプリケーションを登録する]
3.  [認証を要求するようにモバイル サービスを構成する][認証を要求するようにモバイル サービスを構成する]
4.  [クライアント アプリケーションに認証コードを追加する][クライアント アプリケーションに認証コードを追加する]
5.  [認証を使用してクライアントをテストする][認証を使用してクライアントをテストする]

このチュートリアルには、次のものが必要です。

-   XCode 4.5 および iOS 6.0 (またはそれ以降のバージョン)
-   「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」チュートリアルを完了している。
-   Microsoft Azure Mobile Services SDK
-   [iOS 向け Active Directory 認証ライブラリ][iOS 向け Active Directory 認証ライブラリ]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][mobile-services-dotnet-adal-register-service]]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client][mobile-services-dotnet-adal-register-client]]

## <a name="require-authentication"></a>認証を要求するようにモバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

## <a name="add-authentication-code"></a>クライアント アプリケーションに認証コードを追加する

1.  [iOS 向け Active Directory 認証ライブラリ][iOS 向け Active Directory 認証ライブラリ]をダウンロードして、プロジェクトに含めます。また、ADAL ソースからストーリーボードを追加します。

2.  QSTodoListViewController で、ADAL を次に含めます。

        #import "ADALiOS/ADAuthenticationContext.h"

3.  その後、次のメソッドを追加します。

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

4.  前の `loginAndGetData` メソッドのコードで、**INSERT-AUTHORITY-HERE** をアプリケーションをプロビジョニングしたテナントの名前と置き換えます。形式は、<https://login.windows.net/tenant-name.onmicrosoft.com> である必要があります。この値は、[Azure の管理ポータル][Azure の管理ポータル]の Azure Active Directory の [ドメイン] タブからコピーできます。

5.  前の `loginAndGetData` メソッドのコードで、**INSERT-RESOURCE-URI-HERE** をモバイル サービスの **App ID URI** に置き換えます。「[How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)][How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]」トピックに従った場合は、アプリケーション ID URI が <https://todolist.azure-mobile.net/login/aad> と同様になる必要があります。

6.  前の `loginAndGetData` メソッドのコードで、**INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。

7.  前の `loginAndGetData` メソッドのコードで、**INSERT-REDIRECT-URI-HERE** をモバイル サービスの /login/done エンドポイントに置き換えます。これは、<https://todolist.azure-mobile.net/login/done> のような文字列です。

8.  QSTodoListViewController で、`[self refresh]` を次に置き換えて、`ViewDidLoad` を変更します。

        [self loginAndGetData];

## <a name="test-client"></a>認証を使用してクライアントをテストする

1.  [Product] メニューの [Run] をクリックして、アプリケーションを開始します。
2.  Azure Active Directory にログインするための画面が表示されます。
3.  アプリケーションが認証を実行し、Todo 項目を返します。

   ![][]



  [Windows ストア C\#]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "Windows ストア C#"
  [iOS]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
  [モバイル サービスを Azure Active Directory に登録する]: #register-mobile-service-aad
  [Azure Active Directory にアプリケーションを登録する]: #register-app-aad
  [認証を要求するようにモバイル サービスを構成する]: #require-authentication
  [クライアント アプリケーションに認証コードを追加する]: #add-authentication-code
  [認証を使用してクライアントをテストする]: #test-client
  [データの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started-data/
  [iOS 向け Active Directory 認証ライブラリ]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  [mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  []: ./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png
