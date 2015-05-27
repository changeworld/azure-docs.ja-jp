<properties 
	pageTitle="Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証 (iOS) | モバイル デベロッパー センター" 
	description="iOS アプリケーションで ADAL を使用してシングル サインオンのユーザーを認証する方法について説明します。" 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="mahender"/>

# Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

##概要

このチュートリアルでは、Active Directory 認証ライブラリを使用して、クイック スタート プロジェクトに認証を追加します。

ユーザーを認証できるようにするには、Azure Active Directory (AAD) にアプリケーションを登録する必要があります。この処理は 2 段階の手順で実行されます。まず、モバイル サービスを登録し、モバイル サービスに対するアクセス許可を公開する必要があります。次に、iOS アプリケーションを登録してこれらのアクセス許可へのアクセス権を付与する必要があります。


>[AZURE.NOTE]このチュートリアルの目的は、iOS アプリケーションのシングル サインオン Azure Active Directory 認証をできるようにするための Mobile Services のしくみを説明することにあります。Mobile Services を初めて使用する場合は、チュートリアル「[モバイル サービスの使用]」を完了することをお勧めします。


##前提条件


このチュートリアルには、次のものが必要です。

* XCode 4.5 および iOS 6.0 (またはそれ以降のバージョン)
* 「[モバイル サービスの使用]」または「[データの使用]」チュートリアルを完了している。
* Microsoft Azure Mobile Services SDK
* [iOS 向け Active Directory 認証ライブラリ]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

##認証を要求するようにモバイル サービスを構成する

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##クライアント アプリケーションに認証コードを追加する

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


6. 前の `loginAndGetData` メソッドのコードで、**INSERT-AUTHORITY-HERE** をアプリケーションをプロビジョニングしたテナントの名前に置き換えます。形式は、https://login.windows.net/tenant-name.onmicrosoft.com である必要があります。この値は、[Azure の管理ポータル]の Azure Active Directory の [ドメイン] タブからコピーできます。

7. 前の `loginAndGetData` メソッドのコードで、**INSERT-RESOURCE-URI-HERE** をモバイル サービスの **App ID URI** に置き換えます。トピック「[Azure Active Directory 認証用の登録]」に従った場合は、アプリケーション ID URI が https://todolist.azure-mobile.net/login/aad と同様になる必要があります。

8. 前の `loginAndGetData` メソッドのコードで、**INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。

9. 前の `loginAndGetData` メソッドのコードで、**INSERT-REDIRECT-URI-HERE** をモバイル サービスの /login/done エンドポイントに置き換えます。これは、https://todolist.azure-mobile.net/login/done のような文字列です。


3. QSTodoListViewController で、`[self refresh]` を次に置き換えて、`ViewDidLoad` を変更します。

        [self loginAndGetData];

##認証を使用してクライアントをテストする

1. [Product] メニューの [Run] をクリックして、アプリケーションを開始します。
2. Azure Active Directory にログインするための画面が表示されます。  
3. アプリケーションが認証を実行し、Todo 項目を返します。

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[データの使用]: mobile-services-ios-get-started-data.md
[モバイル サービスの使用]: mobile-services-dotnet-backend-ios-get-started.md
[Azure Active Directory 認証用の登録]: mobile-services-how-to-register-active-directory-authentication.md
[Azure の管理ポータル]: https://manage.windowsazure.com/
[iOS 向け Active Directory 認証ライブラリ]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios

<!--HONumber=54-->