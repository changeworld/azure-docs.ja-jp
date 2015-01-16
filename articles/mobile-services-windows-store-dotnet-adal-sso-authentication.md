<properties urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="" description="Windows ストア アプリケーションで ADAL を使用してシングル サインオンのユーザーを認証する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc" />

# Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証

[WACOM.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

このチュートリアルでは、Azure Active Directory でクライアント主導型ログイン操作をサポートする Active Directory 認証ライブラリを使用して Azure Active Directory クイックスタート プロジェクトに認証を追加します。[](http://msdn.microsoft.com/ja-jp/library/azure/jj710106.aspx)Azure Active Directory による[サービス主導型ログイン操作](http://msdn.microsoft.com/ja-jp/library/azure/dn283952.aspx)をサポートするには、[Mobile Services アプリケーションへの認証の追加](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/)に関するチュートリアルから始めます。

ユーザーを認証できるようにするには、Azure Active Directory (AAD) にアプリケーションを登録する必要があります。この処理は 2 段階の手順で実行されます。まず、モバイル サービスを登録し、モバイル サービスに対するアクセス許可を公開する必要があります。次に、Windows ストア アプリを登録してこれらのアクセス許可へのアクセス権を付与する必要があります。


>[AZURE.NOTE] このチュートリアルは、[クライアント主導ログイン操作](http://msdn.microsoft.com/ja-jp/library/azure/jj710106.aspx)を使用して Windows ストア アプリのシングル サインオン Azure Active Directory 認証を Mobile Services で有効にする方法に関する理解を深めるためのものです。Mobile Services を初めて使用する場合は、チュートリアル「[モバイル サービスの使用]」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1. [モバイル サービスを Azure Active Directory に登録する]
2. [Azure Active Directory] にアプリケーションを登録する
3. [認証を要求するようにモバイル サービスを構成する]
4. [クライアント アプリケーションに認証コードを追加する]
5. [認証を使用してクライアントをテストする]

このチュートリアルには、次のものが必要です。

* Windows 8.1 で実行されている Visual Studio 2013。
* 「[モバイル サービスの使用]」または「[データの使用]」を完了している。
* Microsoft Azure Mobile Services SDK の NuGet パッケージ
* Active Directory 認証ライブラリの NuGet パッケージ 

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

## <a name="register-app-aad"></a>Azure Active Directory にアプリケーションを登録する

アプリケーションを Azure Active Directory に登録するには、アプリケーションを Windows ストアに関連付け、アプリケーションのパッケージ セキュリティ ID (SID) を取得する必要があります。パッケージ SID は、Azure Active Directory のネイティブ アプリケーション設定に登録されています。


### アプリケーションと新しいストア アプリ名との関連付け

1. Visual Studio で、クライアント アプリケーション プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![][1]

2. デベロッパー センター アカウントにサインインします。

3. アプリケーションのために予約するアプリ名を入力し、**[予約]** をクリックします。

    ![][2]

4. 新しいアプリ名を選択して、**[次へ]** をクリックします。

5. **[関連付け]** をクリックして、アプリケーションをストア名と関連付けます。


### アプリケーションのパッケージ SID の取得

ここで、ネイティブ アプリケーション設定で構成されるパッケージ SID を取得する必要があります。

1. [Windows デベロッパー センター ダッシュボード]にログインし、アプリケーションで **[編集]** をクリックします。

    ![][3]

2. 次に、**[サービス]** をクリックします。

    ![][4]

3. 次に、**[Live サービス サイト]** をクリックします。 

    ![][5]

4. ページの上部にあるパッケージ SID をコピーします。

    ![][6]

### ネイティブ アプリケーション登録の作成

1. **Azure の管理ポータル**の [[Active Directory]] に移動し、目的のディレクトリをクリックします。

    ![][7] 

2. 上部にある **[アプリケーション]** タブをクリックし、アプリケーションの **[追加]** をクリックします。 

    ![][8]

3. **[組織で開発中のアプリケーションを追加]** をクリックします。

4. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[ネイティブ クライアント アプリケーション]** をクリックします。その後、クリックして続行します。

    ![][9]

5. **[リダイレクト URI]** ボックスに、先ほどコピーしたアプリケーション パッケージ SID を貼り付け、クリックしてネイティブ アプリケーション登録を完了します。

    ![][10]

6. ネイティブ アプリケーションの **[構成]** タブをクリックし、**クライアント ID** をコピーします。この情報は後で必要になります。

    ![][11]

7. **[他のアプリケーションに対するアクセス許可]** セクションが表示されるまでページを下へスクロールし、先ほど登録したモバイル サービス アプリケーションに対するフル アクセスを付与します。その後、**[保存]** をクリックします。

    ![][12]

これで、モバイル サービスが、アプリケーションからシングル サインオン ログインを受信するように AAD で構成されます。



## <a name="require-authentication"></a>認証を要求するようにモバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>クライアント アプリケーションに認証コードを追加する

1. Visual Studio で、Windows ストア クライアント アプリケーション プロジェクトを開きます。

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. Visual Studio のソリューション エクスプローラー ウィンドウで MainPage.xaml.cs を開き、次の using ステートメントを追加します。

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. 次のコードを `AuthenticateAsync` メソッドを宣言する MainPage クラスに追加します。

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

6. 上記の `AuthenticateAsync` メソッドのコードで、**INSERT-AUTHORITY-HERE** をアプリケーションをプロビジョニングするテナントの名前に置き換えます。書式は https://login.windows.net/tenant-name.onmicrosoft.com にする必要があります。[Azure の管理ポータル]の Azure Active Directory の [ドメイン] タブから値をコピーできます。

7. 前の `AuthenticateAsync` メソッドのコードで、**INSERT-RESOURCE-URI-HERE** をモバイル サービスの **App ID URI** に置き換えます。「[How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]」トピックに従った場合は、アプリケーション ID URI が https://todolist.azure-mobile.net/login/aad と同様になる必要があります。

8. `AuthenticateAsync` メソッドのコードで、**INSERT-CLIENT-ID-HERE** をネイティブのクライアント アプリケーションからコピーしたクライアント ID に置き換えます。

9. Visual Studio のソリューション エクスプローラー ウィンドウで、クライアント プロジェクトの Package.appxmanifest ファイルを開きます。**[機能]** タブをクリックし、**[エンタープライズ アプリケーション]** と **[プライベート ネットワーク (クライアント サーバー)]** を有効にします。ファイルを保存します。

    ![][14]

10. MainPage.cs ファイルで、`OnNavigatedTo` イベント ハンドラーを更新して `AuthenticateAsync` メソッドを次のように呼び出します。

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            await RefreshTodoItems();
        }


## <a name="test-client"></a>認証を使用してクライアントをテストする

1. Visual Studio で、クライアント アプリケーションを実行します。
2. Azure Active Directory にログインするための画面が表示されます。  
3. アプリケーションが認証を実行し、Todo 項目を返します。

    ![][15]



<!-- Anchors. -->
[モバイル サービスを Azure Active Directory に登録する]: #register-mobile-service-aad
[Azure Active Directory にアプリケーションを登録する]: #register-app-aad
[認証を要求するようにモバイル サービスを構成する]: #require-authentication
[JavaScript バックエンド モバイル サービス]: #javascript-authentication
[.NET バックエンド モバイル サービス]: #dotnet-authentication
[クライアント アプリケーションに認証コードを追加する]: #add-authentication-code
[認証を使用してクライアントをテストする]: #test-client

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png

<!-- URLs. -->
[How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure 管理ポータル]: https://manage.windowsazure.com/
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Windows デベロッパー センター ダッシュ ボード]: http://go.microsoft.com/fwlink/p/?LinkID=266734
