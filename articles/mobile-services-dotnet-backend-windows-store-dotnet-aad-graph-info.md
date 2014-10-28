<properties linkid="develop-mobile-tutorials-dotnet-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc"></tags>

# Azure Active Directory Graph 情報へのアクセス

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/" title="Windows ストア C#" class="current">Windows ストア C#</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title=".NET バックエンド" class="current">.NET バックエンド</a> |
<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="JavaScript バックエンド">JavaScript バックエンド</a>
</div>

Mobile Services で提供する他の ID プロバイダーと同様に、Azure Active Directory (AAD) プロバイダーも、ディレクトリにプログラムでアクセスできるリッチな [Graph Client Library][Graph Client Library] をサポートしています。このチュートリアルでは、[Graph Client Library][Graph Client Library] を使用してディレクトリから取得したユーザーの詳細情報に基づいて、認証されたユーザーのアプリケーション エクスペリエンスを個人用に設定できるように、ToDoList アプリケーションを更新します。

> [WACOM.NOTE] このチュートリアルは、Azure Active Directory での認証についての知識を深めることを目的としています。事前に、Azure Active Directory 認証プロバイダーを使用して、「[認証の使用][認証の使用]」チュートリアルを完了しておく必要があります。このチュートリアルでは、「[認証の使用][認証の使用]」チュートリアルで使用した TodoItem アプリケーションを引き続き更新します。

このチュートリアルでは、次の手順について説明します。

1.  [AAD でのアプリケーション登録のアクセス キーを生成する][AAD でのアプリケーション登録のアクセス キーを生成する]
2.  [GetUserInfo カスタム API を作成する][GetUserInfo カスタム API を作成する]
3.  [カスタム API を使用するようにアプリケーションを更新する][カスタム API を使用するようにアプリケーションを更新する]
4.  [アプリケーションをテストする][アプリケーションをテストする]

## 前提条件

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

-   [認証の使用][認証の使用]
    TodoList サンプル アプリケーションにログイン要件を追加します。

-   [Custom API Tutorial (カスタム API チュートリアル)][Custom API Tutorial (カスタム API チュートリアル)]
    カスタム API の呼び出し方法を示します。

## <a name="generate-key"></a>AAD でのアプリケーション登録のアクセス キーを生成する

「[認証の使用][認証の使用]」チュートリアルでは、手順「[Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)][Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)]」を完了したときに、統合アプリケーションに対する登録を作成しました。このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-api"></a>GetUserInfo カスタム API を作成する

このセクションでは、[Graph Client Library][Graph Client Library] を使用して AAD からユーザーについての追加情報を取得する GetUserInfo カスタム API を作成します。

Mobile Services でカスタム API をまったく使用したことがない場合は、このセクションを完了する前に、「[Custom API Tutorial (カスタム API チュートリアル)][Custom API Tutorial (カスタム API チュートリアル)]」を参照してください。

1.  Visual Studio で、モバイル サービスの .NET バックエンド プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
2.  [NuGet パッケージ マネージャー] ダイアログ ボックスで、検索条件に「**ADAL**」と入力してモバイル サービスの **Active Directory 認証ライブラリ**を検索し、インストールします。
3.  NuGet パッケージ マネージャーで、モバイル サービスの **Microsoft Azure Active Directory Graph Client Library** もインストールします。

4.  Visual Studio で、モバイル サービス プロジェクトの **Controllers** フォルダーを右クリックし、**[追加]** をクリックして、`GetUserInfoController` という名前の新しい **Microsoft Azure モバイル サービス カスタム コントローラー**を追加します。クライアントがこの API を呼び出して、Active Directory からユーザー情報を取得します。

5.  新しい GetUserInfoController.cs ファイルに、次の `using` ステートメントを追加します。

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6.  GetUserInfoController.cs で、次の `GetAADToken` メソッドをクラスに追加します。

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = authContext.AcquireToken(GraphResourceId, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    このメソッドでは、[Azure 管理ポータル][Azure 管理ポータル]でモバイル サービスに対して構成したアプリケーション設定を使用して、Active Directory にアクセスするトークンを取得します。

7.  GetUserInfoController.cs で、次の `GetAADUser` メソッドをクラスに追加します。

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    このメソッドでは、権限のあるユーザーの Active Directory オブジェクト ID を取得し、Graph Client Library を使用して Active Diretory からユーザー情報を取得します。

8.  GetUserInfoController.cs で、`Get` メソッドを次のメソッドに置き換えます。このメソッドは、Graph Client Library の `User` オブジェクトを返し、API を呼び出すためには権限のあるユーザーが必要になります。

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9.  変更内容を保存して、構文エラーがないことを確認するためにサービスを構築します。
10. モバイル サービス プロジェクトを Azure アカウントに発行します。

## <a name="update-app"></a>GetUserInfo を使用するようにアプリケーションを更新する

このセクションでは、カスタム API を呼び出し、AAD からユーザーに関する追加情報を返すように、「[認証の使用][認証の使用]」チュートリアルに実装した `AuthenticateAsync` メソッドを更新します。

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app][mobile-services-aad-graph-info-update-app]]

## <a name="test-app"></a> アプリケーションをテストする

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app][mobile-services-aad-graph-info-test-app]]

## <a name="next-steps"></a>次のステップ

次のチュートリアル、「[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御][Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]」では、Azure Active Directory (AAD) でロール ベースのアクセス制御を使用して、アクセスを許可する前にグループ メンバーシップを確認します。



  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/ "Windows ストア C#"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/ "JavaScript バックエンド"
  [Graph Client Library]: http://go.microsoft.com/fwlink/?LinkId=510536
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [AAD でのアプリケーション登録のアクセス キーを生成する]: #generate-key
  [GetUserInfo カスタム API を作成する]: #create-api
  [カスタム API を使用するようにアプリケーションを更新する]: #update-app
  [アプリケーションをテストする]: #test-app
  [Custom API Tutorial (カスタム API チュートリアル)]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
  [Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
