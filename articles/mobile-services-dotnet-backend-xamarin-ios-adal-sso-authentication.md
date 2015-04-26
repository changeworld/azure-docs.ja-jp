<properties 
	pageTitle="Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証 (Xamarin.iOS) | モバイル デベロッパー センター" 
	description="Xamarin.iOS アプリケーションで ADAL を使用してシングル サインオンのユーザーを認証する方法について説明します。" 
	documentationCenter="xamarin" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="wesmc,mahender"/>

# Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

このチュートリアルでは、Active Directory 認証ライブラリを使用して、クイック スタート プロジェクトに認証を追加します。 

ユーザーを認証できるようにするには、Azure Active Directory (AAD) にアプリケーションを登録する必要があります。この処理は 2 段階の手順で実行されます。まず、モバイル サービスを登録し、モバイル サービスに対するアクセス許可を公開する必要があります。次に、Xamarin.iOS アプリケーションを登録してこれらのアクセス許可へのアクセス権を付与する必要があります。


>[AZURE.NOTE] このチュートリアルの目的は、Xamarin.iOS アプリケーションのシングル サインオン Azure Active Directory 認証を実行できるようにするための Mobile Services のしくみを説明することにあります。Mobile Services を初めて使用する場合は、チュートリアル「[モバイル サービスの使用]」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1. [モバイル サービスを Azure Active Directory に登録する]
2. [Azure Active Directory にアプリケーションを登録する]
3. [認証を要求するようにモバイル サービスを構成する]
4. [クライアント アプリケーションに認証コードを追加する]
5. [認証を使用してクライアントをテストする]

このチュートリアルには、次のものが必要です。

* XCode 4.5 および iOS 6.0 (またはそれ以降のバージョン) 
* [Xamarin 拡張機能]付きの Visual Studio または OS X 用 [Xamarin Studio]
* 「[モバイル サービスの使用]」または[データの使用]を完了している。
* Microsoft Azure Mobile Services SDK
* [iOS 用 Active Directory 認証ライブラリに対する Xamarin バインド]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>認証を要求するようにモバイル サービスを構成する

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>クライアント アプリケーションに認証コードを追加する

1. Xamarin.iOS プロジェクトに、Active Directory 認証ライブラリに対する Xamarin バインドを追加します。Visual Studio 2013 で、**[参照]** を右クリック、**[参照の追加]** をクリックします。バインド ライブラリを選択し、**[追加]** をクリックします。また、ADAL ソースからストーリーボードを追加します。

2. QSTodoService クラスに次のコードを追加します。 

        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

        public async Task Authenticate()
        {
            var aadAuthority = @"<INSERT-AUTHORITY-HERE>";
            var aadResource = @"<INSERT-RESOURCE-URI-HERE>";
            var aadClientId = @"<INSERT-CLIENT-ID-HERE>";
            var aadRedirect = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError error;
            var aadContext = ADAuthenticationContext.AuthenticationContextWithAuthority(aadAuthority, out error);
            if (error != null)
            {
                throw new InvalidOperationException("Error creating the AAD context: " + error.ErrorDetails);
            }

            var tcs = new TaskCompletionSource<string>();
            aadContext.AcquireTokenWithResource(aadResource, aadClientId, new NSUrl(aadRedirect), result =>
                {
                    if (result.Status == ADAuthenticationResultStatus.SUCCEEDED)
                    {
                        tcs.SetResult(result.AccessToken);
                    }
                    else
                    {
                        string errorDetails;
                        if (result.Status == ADAuthenticationResultStatus.USER_CANCELLED)
                        {
                            errorDetails = "Authentication cancelled by user";
                        }
                        else
                        {
                            errorDetails = result.Error.ErrorDetails;
                        }

                        tcs.SetException(new InvalidOperationException("Error during authentication: " + errorDetails));
                    }
                });
            string accessToken = await tcs.Task;

            var token = new JObject(new JProperty("access_token", accessToken));

            try
            {
                user = await this.client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, token);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

6. 前の `AuthenticateAsync` メソッドのコードで、**INSERT-AUTHORITY-HERE** をアプリケーションをプロビジョニングしたテナントの名前と置き換えます。形式は、https://login.windows.net/tenant-name.onmicrosoft.com である必要があります。この値は、[Azure の管理ポータル]の Azure Active Directory の [ドメイン] タブからコピーできます。

7. 前の `AuthenticateAsync` メソッドのコードで、**INSERT-RESOURCE-URI-HERE** をモバイル サービスの **App ID URI** エンドポイントに置き換えます。トピック「[Azure Active Directory 認証用の登録]」に従った場合は、アプリケーション ID URI が https://todolist.azure-mobile.net/login/aad と同様になる必要があります。

8. 前の `AuthenticateAsync` メソッドのコードで、**INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。

9. 前の `AuthenticateAsync` メソッドのコードで、**INSERT-REDIRECT-URI-HERE** をモバイル サービスの /login/done endpoint エンドポイントに置き換えます。これは、https://todolist.azure-mobile.net/login/done のような文字列です。


3. QSTodoListViewController で、RefreshAsync() の呼び出しの直前に次のコードを追加することにより、**ViewDidLoad** を変更します。

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>認証を使用してクライアントをテストする

1. [実行] メニューの [実行] をクリックして、アプリケーションを開始します。 
2. Azure Active Directory にログインするための画面が表示されます。  
3. アプリケーションが認証を実行し、Todo 項目を返します。

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- Anchors. -->
[モバイル サービスを Azure Active Directory に登録する]: #register-mobile-service-aad
[Azure Active Directory にアプリケーションを登録する]: #register-app-aad
[認証を要求するようにモバイル サービスを構成する]: #require-authentication
[クライアント アプリケーションに認証コードを追加する]: #add-authentication-code
[認証を使用してクライアントをテストする]: #test-client

<!-- URLs. -->
[データの使用]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[Azure Active Directory 認証用の登録]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure の管理ポータル]: https://manage.windowsazure.com/
[iOS 用 Active Directory 認証ライブラリに対する Xamarin バインド]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[Xamarin 拡張機能]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download


<!--HONumber=42-->
