<properties 
        pageTitle="Azure Active Directory シングル サインオンで iOS アプリのユーザーを認証する" 
        description="Active Directory 認証ライブラリを使用した iOS アプリケーションへのユーザーのログイン方法について説明します。" 
        documentationCenter="Mobile" 
        authors="mattchenderson" 
        services="app-service\mobile" 
        manager="dwrede" />

<tags ms.service="app-service"
ms.workload="mobile"
ms.tgt_pltfrm="mobile-ios" 
ms.devlang="objective-c" 
ms.topic="article" 
ms.date="05/19/2015" 
ms.author="mahender" />

# iOS アプリに Azure Active Directory シングル サインオンを追加する

[AZURE.INCLUDE [app-service-mobile-selector-aad-sso](../../includes/app-service-mobile-selector-aad-sso.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このチュートリアルでは、Active Directory 認証ライブラリを使用して、クイック スタート プロジェクトに認証を追加します。

ユーザーを認証できるようにするには、Azure Active Directory (AAD) テナントにアプリケーションを登録する必要があります。この処理は 2 段階の手順で実行されます。まず、App Service を登録し、App Service に対するアクセス許可を公開する必要があります。次に、iOS アプリを登録し、これらのアクセス許可へのアクセス権を付与する必要があります。

このチュートリアルには、次のものが必要です。

* XCode 4.5 および iOS 6.0 (またはそれ以降のバージョン)
* [Mobile Apps の使用]に関するチュートリアルを完了していること。
* Microsoft Azure Mobile Services SDK
* [iOS 向け Active Directory 認証ライブラリ]

##<a name="review"></a>サーバーのプロジェクト構成を確認する (省略可能)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

## <a name="register-application"></a>Azure Active Directory にアプリケーションを登録する

[AZURE.INCLUDE [app-service-mobile-adal-register-app](../../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>認証を要求するようにアプリケーションを構成する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-adal"></a>Active Directory 認証ライブラリへの参照を追加する

1. [iOS 向け Active Directory 認証ライブラリ]をダウンロードします。

2. Xcode Navigator で、プロジェクトを選択し、**[File]** をクリックして **[Add Files to...]** を選択します。ライブラリをダウンロードした場所に移動し、**ADALiOS.xcodeproj** を選択します。

3. プロジェクトを再度選択し、**[Build Settings]** タブを開きます。**[Linking]** セクションに移動し、`-ObjC` を **[Other Linker Flags]** に追加します。

4. **[Build Phases]** タブをクリックします。**[Target Dependencies]** に、`ADALiOS` を追加します。

5. **[Link Binary With Libraries]** に、`libADALiOS.a` を追加します。

これで、プロジェクトで Active Directory 認証ライブラリを参照できます。

## <a name="add-authentication-code"></a>クライアント アプリに認証コードを追加する

2. QSTodoListViewController で、ADAL を次に含めます。

        #import "ADALiOS/ADAuthenticationContext.h"

3. その後、次のメソッドを追加します。

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

4. 上記の `loginAndGetData` メソッドのコードで、**INSERT-AUTHORITY-HERE** をアプリケーションをプロビジョニングしたテナントの名前に置き換えます。形式は、https://login.windows.net/tenant-name.onmicrosoft.com にする必要があります。この値は、[Azure の管理ポータル]の Azure Active Directory の [ドメイン] タブからコピーできます。

5. 上記の `loginAndGetData` メソッドのコードで、**INSERT-RESOURCE-URI-HERE** をモバイル アプリの**アプリ ID の URI** に置き換えます。[Azure Active Directory でモバイル アプリを構成する方法]に関するトピックに従った場合、アプリ ID の URI は https://contosogateway.azurewebsites.net/login/aad のようになります。

6. 上記の `loginAndGetData` メソッドのコードで、**INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。

7. 上記の `loginAndGetData` メソッドのコードで、**INSERT-REDIRECT-URI-HERE** を App Service ゲートウェイの /login/done エンドポイントに置き換えます。これは、https://contosogateway.azurewebsites.net/login/done のような文字列です。

8. QSTodoListViewController で、`[self refresh]` を次に置き換えて `viewDidLoad` を変更します。

        [self loginAndGetData];

## <a name="test-client"></a>認証を使用してクライアントをテストする

1. [Product] メニューの [Run] をクリックして、アプリケーションを開始します。
2. Azure Active Directory にログインするための画面が表示されます。  
3. アプリケーションが認証を実行し、Todo 項目を返します。

<!-- URLs. -->
[Azure Active Directory でモバイル アプリを構成する方法]: app-service-mobile-how-to-configure-active-directory-authentication-preview.md
[Azure の管理ポータル]: https://manage.windowsazure.com/
[iOS 向け Active Directory 認証ライブラリ]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 [Mobile Apps の使用]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
 

<!---HONumber=July15_HO4-->