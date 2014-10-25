<properties linkid="develop-mobile-tutorials-sso-with-adal-xamarin-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Xamarin.iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Xamarin.iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender"></tags>

# Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows ストア C#" >Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
</div>

このチュートリアルでは、Active Directory 認証ライブラリを使用して、クイック スタート プロジェクトに認証を追加します。

ユーザーを認証できるようにするには、Azure Active Directory (AAD) にアプリケーションを登録する必要があります。この処理は 2 段階の手順で実行されます。まず、モバイル サービスを登録し、モバイル サービスに対するアクセス許可を公開する必要があります。次に、Xamarin.iOS アプリケーションを登録してこれらのアクセス許可へのアクセス権を付与する必要があります。

> [WACOM.NOTE] このチュートリアルの目的は、Xamarin.iOS アプリケーションのシングル サインオン Azure Active Directory 認証を実行できるようにするための Mobile Services のしくみを説明することにあります。Mobile Services を初めて使用する場合は、チュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [モバイル サービスを Azure Active Directory に登録する][モバイル サービスを Azure Active Directory に登録する]
2.  [Azure Active Directory にアプリケーションを登録する][Azure Active Directory にアプリケーションを登録する]
3.  [認証を要求するようにモバイル サービスを構成する][認証を要求するようにモバイル サービスを構成する]
4.  [クライアント アプリケーションに認証コードを追加する][クライアント アプリケーションに認証コードを追加する]
5.  [認証を使用してクライアントをテストする][認証を使用してクライアントをテストする]

このチュートリアルには、次のものが必要です。

-   XCode 4.5 および iOS 6.0 (またはそれ以降のバージョン)
-   [Xamarin 拡張機能][Xamarin 拡張機能]付きの Visual Studio または OS X 用 [Xamarin Studio][Xamarin Studio]
-   「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」チュートリアルを完了している。
-   Microsoft Azure Mobile Services SDK
-   [iOS 用 Active Directory 認証ライブラリに対する Xamarin バインド][iOS 用 Active Directory 認証ライブラリに対する Xamarin バインド]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][mobile-services-dotnet-adal-register-service]]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client][mobile-services-dotnet-adal-register-client]]

## <a name="require-authentication"></a>認証を要求するようにモバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

## <a name="add-authentication-code"></a>クライアント アプリケーションに認証コードを追加する

1.  Xamarin.iOS プロジェクトに、Active Directory 認証ライブラリに対する Xamarin バインドを追加します。Visual Studio 2013 で、**[参照]** を右クリック、**[参照の追加]** をクリックします。バインド ライブラリを選択し、**[追加]** をクリックします。また、ADAL ソースからストーリーボードを追加します。

2.  QSTodoService クラスに次のコードを追加します。

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

3.  前の `AuthenticateAsync` メソッドのコードで、**INSERT-AUTHORITY-HERE** をアプリケーションをプロビジョニングしたテナントの名前と置き換えます。形式は、<https://login.windows.net/tenant-name.onmicrosoft.com> である必要があります。この値は、[Azure の管理ポータル][Azure の管理ポータル]の Azure Active Directory の [ドメイン] タブからコピーできます。

4.  前の `AuthenticateAsync` メソッドのコードで、**INSERT-RESOURCE-URI-HERE** をモバイル サービスの **App ID URI** に置き換えます。「[How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)][How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]」トピックに従った場合は、アプリケーション ID URI が <https://todolist.azure-mobile.net/login/aad> と同様になる必要があります。

5.  前の `AuthenticateAsync` メソッドのコードで、**INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。

6.  前の `AuthenticateAsync` メソッドのコードで、**INSERT-REDIRECT-URI-HERE** をモバイル サービスの /login/done エンドポイントに置き換えます。これは、<https://todolist.azure-mobile.net/login/done> のような文字列です。

7.  QSTodoListViewController で、RefreshAsync(); の呼び出しの直前に次のコードを追加することにより、**ViewDidLoad** を変更します。

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>認証を使用してクライアントをテストする

1.  [実行] メニューの [実行] をクリックして、アプリケーションを開始します。
2.  Azure Active Directory にログインするための画面が表示されます。
3.  アプリケーションが認証を実行し、Todo 項目を返します。

  ![][]



  [Windows ストア C\#]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "Windows ストア C#"
  [iOS]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [モバイル サービスを Azure Active Directory に登録する]: #register-mobile-service-aad
  [Azure Active Directory にアプリケーションを登録する]: #register-app-aad
  [認証を要求するようにモバイル サービスを構成する]: #require-authentication
  [クライアント アプリケーションに認証コードを追加する]: #add-authentication-code
  [認証を使用してクライアントをテストする]: #test-client
  [Xamarin 拡張機能]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [データの使用]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [iOS 用 Active Directory 認証ライブラリに対する Xamarin バインド]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  [mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  []: ./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png
