<properties pageTitle="Azure Active Directory Graph 情報へのアクセス (Windows ストア) | モバイル デベロッパー センター" description="Windows ストア アプリケーションで、Graph API を使用して Azure Active Directory の情報にアクセスする方法について説明します。" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc"/>

# Azure Active Directory Graph 情報へのアクセス

[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]



Mobile Services で提供する他の ID プロバイダーと同様に、Azure Active Directory (AAD) プロバイダーも、ディレクトリにプログラムでアクセスできるリッチな[ Graph Client Library] をサポートしています。このチュートリアルでは、[Graph Client Library] を使用してディレクトリから取得したユーザーの詳細情報に基づいて、認証されたユーザーのアプリケーション エクスペリエンスを個人用に設定できるように、ToDoList アプリケーションを更新します。

>[AZURE.NOTE] このチュートリアルは、Azure Active Directory での認証についての知識を深めることを目的としています。事前に、Azure Active Directory 認証プロバイダーを使用してチュートリアル、「[アプリへの認証の追加]」を完了しておく必要があります。このチュートリアルでは、チュートリアル「[アプリへの認証の追加]」で使用した TodoItem アプリケーションを引き続き更新します。 



このチュートリアルでは、次の手順について説明します。:


1. [AAD でのアプリケーション登録のアクセス キーを生成する]
2. [GetUserInfo カスタム API を作成する]
3. [カスタム API を使用するようにアプリケーションを更新する]
4. [アプリケーションをテストする]

##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。:

+ [認証の使用]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [カスタム API に関するチュートリアル]<br/>カスタム API の呼び出し方法を示します。 



## <a name="generate-key"></a>AAD でのアプリケーション登録のアクセス キーを生成する


チュートリアル「[Mobile Services アプリへの認証の追加]」では、手順「[アプリケーションを登録して Azure Active Directory アカウント ログインを使用する]」を完了したときに、統合アプリケーションに対する登録を作成しました。このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>GetUserInfo カスタム API を作成する

このセクションでは、[Graph Client Library] を使用して AAD からユーザーについての追加情報を取得する GetUserInfo カスタム API を作成します。

Mobile Services でカスタム API をまったく使用したことがない場合は、このセクションを完了する前に、[カスタム API に関するチュートリアル]を参照してください。

1. Visual Studio で、モバイル サービスの .NET バックエンド プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
2. [NuGet パッケージ マネージャー] ダイアログ ボックスで、検索条件に「**ADAL**」と入力してモバイル サービスの **Active Directory 認証ライブラリ**を検索し、インストールします。
3. NuGet パッケージ マネージャーで、モバイル サービスの **Microsoft Azure Active Directory Graph Client Library** もインストールします。

4. Visual Studio で、モバイル サービス プロジェクトの **Controllers** フォルダーを右クリックし、**[追加]** をクリックして、`GetUserInfoController` という名前の新しい **Microsoft Azure Mobile Services カスタム コントローラー**を追加します。クライアントがこの API を呼び出して、Active Directory からユーザー情報を取得します。

5. 新しい GetUserInfoController.cs ファイルに、次の `using` ステートメントを追加します。

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6. GetUserInfoController.cs で、次の `GetAADToken` メソッドをクラスに追加します。

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
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientid, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    このメソッドでは、[Azure の管理ポータル]でモバイル サービスに対して構成したアプリケーション設定を使用して、Active Directory にアクセスするトークンを取得します。

7. GetUserInfoController.cs で、次の `GetAADUser` メソッドをクラスに追加します。

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


8. GetUserInfoController.cs で、`Get` メソッドを次のメソッドに置き換えます。このメソッドは、Graph Client Library の `User` オブジェクトを返し、API を呼び出すためには権限のあるユーザーが必要になります。

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9. 変更内容を保存して、構文エラーがないことを確認するためにサービスを構築します。
10. モバイル サービス プロジェクトを Azure アカウントに発行します。 


## <a name="update-app"></a>GetUserInfo を使用するようにアプリケーションを更新する

このセクションでは、カスタム API を呼び出し、AAD からユーザーに関する追加情報を返すように、チュートリアル「[アプリへの認証の追加]」で実装した `AuthenticateAsync` メソッドを更新します。 

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>次のステップ

次のチュートリアル、「[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]」では、Azure Active Directory (AAD) でロール ベースのアクセス制御を使用して、アクセスを許可する前にグループ メンバーシップを確認します。 



<!-- Anchors. -->
[AAD でのアプリケーション登録のアクセス キーを生成する]: #generate-key
[GetUserInfo カスタム API を作成する]: #create-api
[カスタム API を使用するようにアプリケーションを更新する]: #update-app
[アプリケーションをテストする]: #test-app
[次のステップ]:#next-steps

<!-- Images -->


<!-- URLs. -->
[アプリへの認証の追加]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Azure Active Directory 認証用の登録]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure の管理ポータル]: https://manage.windowsazure.com/
[カスタム API に関するチュートリアル]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[ソース管理へのプロジェクト コードの保存]: /ja-jp/documentation/articles/mobile-services-store-scripts-source-control/
[アプリケーションを登録して Azure Active Directory アカウント ログインを使用する]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph Client Library]: http://go.microsoft.com/fwlink/?LinkId=510536
[ユーザーの取得]: http://msdn.microsoft.com/ja-jp/library/azure/dn151678.aspx
[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/


<!--HONumber=42-->
