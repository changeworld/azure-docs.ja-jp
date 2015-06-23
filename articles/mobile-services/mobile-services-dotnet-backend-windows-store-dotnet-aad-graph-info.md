<properties 
	pageTitle="Azure Active Directory Graph 情報へのアクセス (Windows ストア) | モバイル デベロッパー センター" 
	description="Windows ストア アプリケーションで、Graph API を使用して Azure Active Directory の情報にアクセスする方法について説明します。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="wesmc"/>

# Azure Active Directory Graph 情報へのアクセス



[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]

##概要

Mobile Services で提供する他の ID プロバイダーと同様に、Azure Active Directory (AAD) プロバイダーも、ディレクトリにプログラムでアクセスできるリッチな Graph API をサポートしています。このチュートリアルでは、[Graph REST Library] を使用してディレクトリから取得したユーザーの詳細情報を返す、認証されたユーザーのアプリケーション エクスペリエンスを個人用に設定できるように、ToDoList アプリケーションを更新します。

Azure AD Graph API の詳細については、「[Azure Active Directory Graph チームのブログ]」を参照してください。


>[AZURE.NOTE]このチュートリアルは、Azure Active Directory での認証についての知識を深めることを目的としています。事前に、Azure Active Directory 認証プロバイダーを使用して、チュートリアル「[アプリへの認証の追加]」を完了しておく必要があります。このチュートリアルでは、「[アプリへの認証の追加]」で使用した TodoItem アプリケーションを引き続き更新します。




##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [アプリへの認証の追加] <br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [Custom API Tutorial (カスタム API チュートリアル)]<br/>カスタム API の呼び出し方法を示します。



## <a name="generate-key"></a>AAD でのアプリケーション登録のアクセス キーを生成する


チュートリアル「[アプリへの認証の追加]」では、手順「[アプリケーションを登録して Azure Active Directory アカウント ログインを使用する]」を完了したとき、統合アプリケーションに対する登録を作成しました。このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>GetUserInfo カスタム API を作成する

このセクションでは、Azure AD Graph API を使用して AAD からユーザーについての追加情報を取得する GetUserInfo カスタム API を作成します。

Mobile Services でカスタム API をまったく使用したことがない場合は、このセクションを完了する前に、「[Custom API Tutorial (カスタム API チュートリアル)]」を参照してください。

1. Visual Studio で、モバイル サービスの .NET バックエンド プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
2. [NuGet パッケージ マネージャー] ダイアログ ボックスで、検索条件に「**ADAL**」と入力してモバイル サービスの **Active Directory 認証ライブラリ**を検索し、インストールします。このチュートリアルは、ADAL パッケージの 3.0.110281957-alpha (プレリリース) バージョンで最後にテストされています。


3. Visual Studio で、モバイル サービス プロジェクトの **Controllers** フォルダーを右クリックし、**[追加]** をクリックして、`GetUserInfoController` という名前の新しい **Microsoft Azure モバイル サービス カスタム コントローラー**を追加します。クライアントがこの API を呼び出して、Active Directory からユーザー情報を取得します。

4. 新しい GetUserInfoController.cs ファイルに、次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.Threading.Tasks;
		using Newtonsoft.Json;
		using System.IO;

5. 新しい GetUserInfoController.cs ファイルで、次の `UserInfo` クラスを追加して、AAD から収集する情報を保持します。

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
	
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ",otherMails);
	        }
	    }

6. GetUserInfoController.cs で、`GetUserInfoController` クラスに次のメンバー変数を追加します。

        private const string AadInstance = "https://login.windows.net/{0}";
        private const string GraphResourceId = "https://graph.windows.net/";
        private const string APIVersion = "?api-version=2013-04-05";

        private string tenantdomain;
        private string clientid;
        private string clientkey;
        private string token = null;


7. GetUserInfoController.cs で、次の `GetAADToken` メソッドをクラスに追加します。

        private async Task<string> GetAADToken()
        {
            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;
            else
                Services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

    このメソッドでは、[Azure 管理ポータル]でモバイル サービスに対して構成したアプリケーション設定を使用して、Active Directory にアクセスするトークンを取得します。

7. GetUserInfoController.cs で、次の `GetAADUser` メソッドをクラスに追加します。

        private async Task<UserInfo> GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetAADUser() : No ServiceUser or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetAADUser() : Could not get AAD credientials for the logged in user.");
                return null;
            }

            if (token == null)
                await GetAADToken();

            if (token == null)
            {
                Services.Log.Error("GetAADUser() : No token.");
                return null;
            }

            // User the AAD Graph REST API to get the user's information
            string url = GraphResourceId + tenantdomain + "/users/" + clientAadCredentials.ObjectId + APIVersion;
            Services.Log.Info("GetAADUser() : Request URL : " + url);
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.ContentType = "application/json";
            request.Headers.Add("Authorization", token);
            UserInfo userinfo = null;
            try
            {
                WebResponse response = await request.GetResponseAsync();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string userjson = sr.ReadToEnd();
                userinfo = JsonConvert.DeserializeObject<UserInfo>(userjson);
                Services.Log.Info("GetAADUser user : " + userinfo.ToString());
            }
            catch(Exception e)
            {
                Services.Log.Error("GetAADUser exception : " + e.Message);
            }

            return userinfo;
        }

    このメソッドでは、権限のあるユーザーの Active Directory オブジェクト ID を取得し、Graph REST API を使用して Active Diretory からユーザー情報を取得します。


8. GetUserInfoController.cs で、`Get` メソッドを次のメソッドに置き換えます。このメソッドは、Graph REST API を使用して Azure Active Directory からユーザー エンティティを返します。API を呼び出すｒには承認されたユーザーが必要です。

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public async Task<UserInfo> Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return await GetAADUser();
        }

9. 変更内容を保存して、構文エラーがないことを確認するためにサービスを構築します。
10. モバイル サービス プロジェクトを Azure アカウントに発行します。 


## <a name="update-app"></a>GetUserInfo を使用するようにアプリケーションを更新する

このセクションでは、カスタム API を呼び出し、AAD からユーザーに関する追加情報を返すように、チュートリアル「[アプリへの認証の追加]」で実装した `AuthenticateAsync` メソッドを更新します。

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>次のステップ

次のチュートリアル、「[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]」では、Azure Active Directory (AAD) でロール ベースのアクセス制御を使用して、アクセスを許可する前にグループ メンバーシップを確認します。



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images -->


<!-- URLs. -->
[アプリへの認証の追加]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure 管理ポータル]: https://manage.windowsazure.com/
[Graph REST Library]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Custom API Tutorial (カスタム API チュートリアル)]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[アプリケーションを登録して Azure Active Directory アカウント ログインを使用する]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Active Directory Graph チームのブログ]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]: mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac.md

<!--HONumber=54--> 