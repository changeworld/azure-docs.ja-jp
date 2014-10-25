<properties linkid="develop-mobile-tutorials-dotnet-rbac-with-aad" urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Role Based Access Control in Mobile Services and Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/21/2014" ms.author="wesmc"></tags>

# Mobile Services と Azure Active Directory でのロール ベースのアクセス制御

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title="Windows ストア C#" class="current">Windows ストア C#</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title=".NET バックエンド" class="current">.NET バックエンド</a> | 
<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="JavaScript バックエンド">JavaScript バックエンド</a>
</div>

ロール ベースのアクセス制御 (RBAC) は、ユーザーが保持できるロールにアクセス許可を割り当てる機能であり、特定のクラスのユーザーが実行できることとできないことの境界線を明確に定義します。このチュートリアルでは、Azure Mobile Services に基本的な RBAC を追加する方法について説明します。

このチュートリアルでは、ロール ベースのアクセス制御について示しながら、Azure Active Directory (AAD) で定義された Sales グループに対する各ユーザーのメンバーシップを確認します。アクセスの確認は、.NET モバイル サービス バックエンドで Azure Active Directory 用の [Graph Client Library][Graph Client Library] を使って行います。Sales グループに属するユーザーのみが、データの照会を許可されます。

> [WACOM.NOTE] このチュートリアルは、認証方法を含めた認証の知識を深めることを目的としています。最初に、Azure Active Directory 認証プロバイダーを使用して、「[認証の使用][認証の使用]」チュートリアルを完了しておく必要があります。このチュートリアルでは、「[認証の使用][認証の使用]」チュートリアルで使用した TodoItem アプリケーションを引き続き更新します。

このチュートリアルでは、次の手順について説明します。

1.  [Sales グループとメンバーシップを作成する][Sales グループとメンバーシップを作成する]
2.  [統合アプリケーションのキーを生成する][統合アプリケーションのキーを生成する]
3.  [カスタム承認属性を作成する][カスタム承認属性を作成する]
4.  [データベース操作にロール ベースのアクセス確認を追加する][データベース操作にロール ベースのアクセス確認を追加する]
5.  [クライアントのアクセスをテストする][クライアントのアクセスをテストする]

このチュートリアルには、次のものが必要です。

-   Windows 8.1 で実行されている Visual Studio 2013。
-   Azure Active Directory 認証プロバイダーを使用して、「[認証の使用][認証の使用]」チュートリアルを完了していること。
-   「[Store Server Scripts (サーバー スクリプトの格納)][Store Server Scripts (サーバー スクリプトの格納)]」チュートリアルを完了し、Git リポジトリを使用してサーバー スクリプトを格納する方法に習熟していること。

## <a name="create-group"></a>Sales グループとメンバーシップを作成する

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group][mobile-services-aad-rbac-create-sales-group]]

## <a name="generate-key"></a>統合アプリケーションのキーを生成する

「[認証の使用][認証の使用]」チュートリアルでは、手順「[Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)][Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)]」を完了したときに、統合アプリケーションに対する登録を作成しました。このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-custom-authorization-attribute"></a>モバイル サービスにカスタム承認属性を作成する

このセクションでは、モバイル サービスの操作でアクセス確認を実行するときに使用できる、新しいカスタム承認属性を作成します。この属性は、渡されたロール名に基づいて Active Directory グループを検索します。その後、そのグループのメンバーシップに基づいてアクセス確認を行います。

1.  Visual Studio で、モバイル サービスの .NET バックエンド プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

2.  [NuGet パッケージ マネージャー] ダイアログ ボックスで、検索条件に「**ADAL**」と入力してモバイル サービスの **Active Directory 認証ライブラリ**を検索し、インストールします。

3.  NuGet パッケージ マネージャーで、モバイル サービスの **Microsoft Azure Active Directory Graph Client Library** もインストールします。

4.  Visual Studio で、モバイル サービス プロジェクトを右クリックし、**[追加]**、**[新しいフォルダー]** の順にクリックします。新しいフォルダーに "**Utilities**" という名前を付けます。

5.  Visual Studio で、新しい **Utilities** フォルダーを右クリックし、"**AuthorizeAadRole.cs**" という名前の新しいクラス ファイルを追加します。

    ![][]

6.  AuthorizeAadRole.cs ファイルの先頭に次の `using` ステートメントを追加します。

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7.  AuthorizeAadRole.cs で、Utilities 名前空間に次の列挙型を追加します。この例では、**Sales** ロールだけを使用します。それ以外は、単に使用する可能性のあるグループの例です。

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8.  AuthorizeAadRole.cs で、Utilities 名前空間に次の `AuthorizeAadRole` クラス定義を追加します。

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9.  AuthorizeAadRole.cs で、`AuthorizeAadRole` クラスに次の `GetAADToken` メソッドを追加します。

    > [WACOM.NOTE] アクセス確認のたびに新しいトークンを作成するのではなく、トークンをキャッシュする必要があります。その後、[Graph Client Library][Graph Client Library] の説明に記載されているとおり、トークンの使用を試みたときに AccessTokenExpiredException 例外がスローされた場合は、キャッシュを更新します。これは、次に示すコードでは簡略化のために省かれていますが、Active Directory に対して余分なネットワーク トラフィックを軽減する効果があります。

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
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

10. AuthorizeAadRole.cs で、`AuthorizeAadRole` クラスの `OnAuthorization` メソッドを次のコードで更新します。このコードは、[Graph Client Library][Graph Client Library] を使用して、ロールに対応する Active Directory グループを検索します。その後、そのグループ内のユーザーのメンバーシップを確認して、ユーザーを承認します。

    > [WACOM.NOTE] このコードは、Active Directory グループを名前で検索します。多くの場合には、モバイル サービス アプリケーションの設定としてグループ ID を格納する方が適切です。これは、グループ名は変更される場合がありますが、ID は同じままであるためです。ただし、グループ名が変更されると、通常、少なくともロールのスコープに変更が生じるため、モバイル サービスのコードも更新が必要になる場合があります。

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed 
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
                        "User is not logged in or not a member of the required group");
            }
        }

11. AuthorizeAadRole.cs に変更を保存します。

## <a name="add-access-checking"></a>データベース操作にロール ベースのアクセス確認を追加する

1.  Visual Studio で、モバイル サービス プロジェクト内の **Controllers** フォルダーを展開します。TodoItemController.cs を開きます。

2.  TodoItemController.cs で、カスタム承認属性を含む Utilities 名前空間に対して `using` ステートメントを追加します。

        using todolistService.Utilities;

3.  TodoItemController.cs では、アクセスを確認する方法に応じて、属性をコントローラー クラスまたは個別のメソッドに追加できます。すべてのコントローラー操作で同じロールに基づいてアクセスを確認する場合は、単に属性をクラスに追加します。このチュートリアルをテストするために、属性を次のようにクラスに追加します。

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    挿入、更新、および削除の各操作についてのみアクセスを確認する場合は、次のようにそれらのメソッドに対してだけ属性を設定します。

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }

4.  TodoItemController.cs を保存し、モバイル サービスをビルドして、構文エラーがないことを確認します。
5.  モバイル サービスを Azure アカウントに発行します。

## <a name="test-client"></a>クライアントのアクセスをテストする

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app][mobile-services-aad-rbac-test-app]]



  [Windows ストア C\#]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/ "Windows ストア C#"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/ "JavaScript バックエンド"
  [Graph Client Library]: http://go.microsoft.com/fwlink/?LinkId=510536
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Sales グループとメンバーシップを作成する]: #create-group
  [統合アプリケーションのキーを生成する]: #generate-key
  [カスタム承認属性を作成する]: #create-custom-authorization-attribute
  [データベース操作にロール ベースのアクセス確認を追加する]: #add-access-checking
  [クライアントのアクセスをテストする]: #test-client
  [Store Server Scripts (サーバー スクリプトの格納)]: /ja-jp/documentation/articles/mobile-services-store-scripts-source-control/
  [mobile-services-aad-rbac-create-sales-group]: ../includes/mobile-services-aad-rbac-create-sales-group.md
  [Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png
  [mobile-services-aad-rbac-test-app]: ../includes/mobile-services-aad-rbac-test-app.md
