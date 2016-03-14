<properties 
	pageTitle="Azure Active Directory の認証を使用して Azure App Service で .NET MVC Web アプリを作成する" 
	description="Azure Active Directory の認証を使用して Azure App Service に ASP.NET MVC 基幹業務アプリケーションを作成する方法について説明します。" 
	services="app-service\web, active-directory" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="02/29/2016" 
	ms.author="cephalin"/>

# Azure Active Directory の認証を使用して Azure App Service で .NET MVC Web アプリを作成する #

この記事では、[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) で [Azure Active Directory](/services/active-directory/) を ID プロバイダーとして使用して ASP.NET MVC 基幹業務アプリケーションを作成する方法について説明します。また、[Azure Active Directory Graph クライアント ライブラリ](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx)を使ってアプリケーション内のディレクトリ データを照会する方法についても説明します。

使用する Azure Active Directory テナントを Azure 専用ディレクトリにするか、オンプレミスの Active Directory (AD) とディレクトリを同期して、オンプレミス ユーザーやリモート ユーザーがシングル サインオン環境を構築できます。

>[AZURE.NOTE] Azure App Service Web Apps の場合、ボタンを数回クリックするだけで、Azure Active Directory に対する認証を構成できます。詳細については、「[Azure App Service での認証には、Active Directory を使用します](web-sites-authentication-authorization.md)」を参照してください。

<a name="bkmk_build"></a>
## 学習内容 ##

次の機能を備え、作業項目を追跡する、単純な LOB の "作成、読み取り、更新、削除" (CRUD) アプリケーションを App Service Web Apps に作成します。

- Azure Active Directory に対してユーザーを認証する
- サインインとサインアウト機能を実装する
- `[Authorize]` を使用して、CRUD 操作ごとにユーザーを承認する
- [Azure Active Directory Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx) を使用して Azure Active Directory データにクエリを実行する
- Windows Identity Foundation (WIF) の代わりに [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) を使用する。これは ASP.NET の進化形で、WIF よりも簡単に認証と承認を設定できます。

<a name="bkmk_need"></a>
## 前提条件 ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

このチュートリアルを完了するには、以下が必要です。

- 各グループのユーザーが属する Azure Active Directory テナント
- Azure Active Directory テナントでアプリケーションを作成するためのアクセス許可
- Visual Studio 2013 以降
- [Azure SDK 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) 以降。

<a name="bkmk_sample"></a>
## サンプル アプリケーションを基幹業務テンプレートとして使用する ##

このチュートリアルの [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) サンプル アプリケーションは、Azure Active Directory チームが作成し、テンプレートとして使うことで、新しい基幹業務アプリケーションを容易に作成できます。以下の機能が組み込まれています。

- [OpenID Connect](http://openid.net/connect/) を使用して Azure Active Directory で認証する
- `[Authorize]` の標準的な使用も含めて、アプリケーションで特定の操作に対してさまざまなロールを承認する方法を示すサンプルの `TaskTracker` コントローラーが含まれています。 
- ユーザーとグループを即座に割り当てることができる、定義済みロールを持つマルチテナント アプリケーションです。 

<a name="bkmk_run" />
## サンプル アプリケーションを実行する ##

1.	[WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) からサンプル ソリューションをローカル ディレクトリに複製またはダウンロードします。

2.	[シングル テナント アプリケーションとしてのサンプルの実行方法](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#how-to-run-the-sample-as-a-single-tenant-app)に関するページの手順に従って、Azure Active Directory アプリケーションとプロジェクトを設定します。アプリケーションをマルチテナントからシングル テナントに変換するには、必ずすべての手順に従ってください。

3.	先ほど作成した Azure Active Directory アプリケーションの [Azure クラシック ポータル](https://manage.windowsazure.com) ビューで、**[ユーザー]** タブをクリックします。次に、必要なロールに目的のユーザーを割り当てます。

	>[AZURE.NOTE] ロールをユーザーだけでなくグループにも割り当てる場合は、Azure Active Directory テナントを [Azure Active Directory Premium](/pricing/details/active-directory/) にアップグレードする必要があります。アプリケーションのクラシック ポータル UI に **[ユーザーとグループ] タブではなく **[ユーザー]** タブが表示される場合は、Azure Active Directory テナントの **[ライセンス]** タブに移動して Azure Active Directory Premium を試すことができます。

3.	アプリケーションの構成が完了したら、Visual Studio で `F5` キーを押して ASP.NET アプリケーションを実行します。

4.	アプリケーションが読み込まれたら、**[サインイン]** をクリックし、Azure クラシック ポータルで管理者ロールを持つユーザーでサインインします。

5.	Azure Active Directory アプリケーションを適切に構成し、Web.config で対応する設定項目を設定している場合は、ログイン ページにリダイレクトされます。Azure クラシック ポータルで Azure Active Directory アプリケーションを作成するために使用したアカウントは Azure Active Directory アプリケーションの既定の所有者であるため、そのアカウントでログオンします。
	
<a name="bkmk_deploy"></a>
## サンプル アプリケーションを Azure App Service Web Apps にデプロイする

ここでは、アプリケーションを Azure App Service の Web アプリに発行します。[README.md](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims/blob/master/README.md) には App Service Web Apps にデプロイするための手順が記載されていますが、それらの手順を実行すると、ローカルのデバッグ環境の構成が無効になります。デバッグ構成を維持したままデプロイする方法について説明します。

1. プロジェクトを右クリックし、**[発行]** を選択します。

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. **[Microsoft Azure Web Apps]** を選択します。

3. Azure にサインインしていない場合は、**[アカウントの追加]** をクリックし、Azure サブスクリプションの Microsoft アカウントを使用してサインインします。

4. サインインしたら、**[新規]** をクリックして Azure で新しい Web アプリを作成します。

5. **[ホスティング]** にて、すべての必須フィールドに必要事項を入力します。

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

5. このアプリケーションでロール マッピング、キャッシュされたトークン、すべてのアプリケーション データを格納するには、データベース接続が必要です。 **[App Service の作成]** ダイアログ ボックスで、**[サービス]** をクリックします。**[SQL Database]** の隣にあるプラス記号をクリックして新しいデータベースを追加します。

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-database.png)

5. **[SQL Database の構成]** ダイアログ ボックスで、サーバーを選択または作成して名前を設定し、**[OK]** をクリックします。

	 ![](./media/web-sites-dotnet-lob-application-azure-ad/4-config-database.png)

6. **[作成]** をクリックします。Web アプリが作成されると、**[Web の発行]** ダイアログが開きます。

7. **[宛先 URL]** で、**http** を **https** に変更します。URL 全体をテキスト エディターにコピーします。この URL は後で使用します。次に、**[次へ]** をクリックします。

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. **[組織認証を有効にする]** チェック ボックスをオフにします。

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-enable-code-first-migrations.png)

8. **[RoleClaimContext]** を展開し、**[Code First Migrations の実行 (アプリケーションの起動時に実行)]** チェック ボックスをオンにします。[Code First Migrations](https://msdn.microsoft.com/data/jj591621.aspx) は、後で追加の Code First データ モデルを定義するときに Azure でアプリのデータベース スキーマを更新するのに役立ちます。

9. **[発行]** をクリックして Web への発行に進む代わりに、**[閉じる]** をクリックします。**[はい]** をクリックし、発行プロファイルへの変更を保存します。

2. [Azure クラシック ポータル](https://manage.windowsazure.com)で Azure Active Directory テナントに移動し、**[アプリケーション]** タブをクリックします。

2. ページの下部にある **[追加]** をクリックします。

2. **[組織で開発中のアプリケーションを追加]** をクリックします。

3. **[Web アプリケーションや Web API]** を選択します。

4. アプリケーションに名前を付けて、**[次へ]** をクリックします。

5. アプリケーションのプロパティで、**[サインオン URL]** を、前の手順で保存した Web アプリの URL (例: `https://<site-name>.azurewebsites.net/`) に設定し、**[APP ID URI]** を `https://<aad-tenanet-name>/<app-name>` に設定します。次に、**[完了]** をクリックします。

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

2.	アプリケーションが作成されたら、[アプリケーション ロールの定義](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#step-2-define-your-application-roles)に関するセクションの手順に従って以前実行したようにアプリケーション マニフェストを更新します。

3.	先ほど作成した Azure Active Directory アプリケーションの [Azure クラシック ポータル](https://manage.windowsazure.com) ビューで、**[ユーザー]** タブをクリックします。次に、必要なロールに目的のユーザーを割り当てます。

6. **[構成]** タブをクリックします。

7. **[キー]** で、ドロップダウンから **[1 年]** を選択して新しいキーを作成します。

8. **[他のアプリケーションに対するアクセス許可]** の **Azure Active Directory** エントリで、**[デリゲートされたアクセス許可]** ボックスの一覧から **[サインインとユーザー プロファイルの読み取り]** と **[ディレクトリ データの読み取り]** を選択します。

	> [AZURE.NOTE] ここで実際に必要となるアクセス許可は、アプリケーションに含める機能によって異なります。中には**全体管理者**ロールを設定する必要があるアクセス許可もありますが、このチュートリアルで必要となるのは**ユーザー** ロールのみです。

9.  **[保存]** をクリックします。

10.  保存された構成のページから移動する前に以下の情報をテキスト エディターにコピーします。

	-	クライアント ID
	-	キー (ページから移動すると、キーを再度確認することはできなくなります)

11. Visual Studio で、プロジェクトの **Web.Release.config** を開きます。以下の XML を `<configuration>` タグに挿入し、各キーの値を、新しい Azure Active Directory アプリケーションの保存した情報に置き換えます。
	<pre class="prettyprint">
	&lt;appSettings>
	   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
	   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
	   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
	&lt;/appSettings></pre>

	ida:PostLogoutRedirectUri の値の末尾が "/" になっていることを確認します。

1. プロジェクトを右クリックし、**[発行]** を選択します。

2. **[発行]** をクリックして Azure App Service Web Apps に発行します。

上記の手順が完了すると、Azure クラシック ポータルに 2 つの Azure Active Directory アプリケーションが構成されます。1 つは Visual Studio のデバッグ環境用のアプリケーション、もう 1 つは Azure に発行された Web アプリ用のアプリケーションです。デバッグ中は、**Debug** 構成を Azure Active Directory に対応させるために Web.config のアプリ設定が使用されます。発行時 (既定では、**Release** 構成が発行されたとき) は、Web.Release.config のアプリ設定の変更が組み込まれた、変換された Web.config がアップロードされます。

発行した Web アプリをデバッガーにアタッチする場合 (発行した Web アプリのコードのデバッグ シンボルをアップロードする必要があります)、Azure デバッグの Debug 構成のクローンを作成できます。このとき、Web.Release.config の Azure Active Directory 設定を使用するカスタム Web.config 変換 (たとえば、Web.AzureDebug.config) が使用されます。これにより、静的構成を異なる環境間で維持できます。

<a name="bkmk_crud"></a>
## 基幹業務の機能をサンプル アプリケーションに追加する

チュートリアルのこのセクションでは、サンプル アプリケーションを利用して、目的の基幹業務の機能を構築する方法について説明します。単純な CRUD 作業項目トラッカーを作成します。これは、TaskTracker コントローラーに似ていますが、使うのは CRUD の標準的なスキャフォールディングと設計パターンです。また、そこに含まれている Scripts\\AadPickerLibrary.js を使い、Azure Active Directory Graph API からのデータでアプリケーションを強化します。

5.	Models フォルダーに WorkItem.cs という名前の新しい [Code First](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) モデルを作成し、そのコードを次のコードに置き換えます。

		using System.ComponentModel.DataAnnotations;
		
		namespace WebApp_RoleClaims_DotNet.Models
		{
		    public class WorkItem
		    {
		        [Key]
		        public int ItemID { get; set; }
		        public string AssignedToID { get; set; }
		        public string AssignedToName { get; set; }
		        public string Description { get; set; }
		        public WorkItemStatus Status { get; set; }
		    }
		
		    public enum WorkItemStatus
		    {
		        Open, 
		        Investigating, 
		        Resolved, 
		        Closed
		    }
		}

6.	DAL\\RoleClaimContext.cs を開き、次の強調表示されたコードを追加します。
	<pre class="prettyprint">
	public class RoleClaimContext : DbContext
	{
	    public RoleClaimContext() : base("RoleClaimContext") { }
	
	    public DbSet&lt;Task> Tasks { get; set; }
	    <mark>public DbSet&lt;WorkItem> WorkItems { get; set; }</mark>
	    public DbSet&lt;TokenCacheEntry> TokenCacheEntries { get; set; }
	}</pre>

7.	プロジェクトをビルドして、新しいモデルから Visual Studio のスキャフォールディング ロジックにアクセスできるようにします。

8.	新しくスキャフォールディングした項目 `WorkItemsController` を Controllers フォルダーに追加します。そのためには、**[コントローラー]** を右クリックし、**[追加]** をポイントしてから、**[新しくスキャフォールディングした項目]** を選択します。

9.	**[Entity Framework を使用したビュー付きの MVC 5 コントローラー]** を選択し、**[追加]** をクリックします。

10.	作成したモデルを選択し、**[追加]** をクリックします。

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Controllers\\WorkItemsController.cs を開きます。

11. 強調表示された [Authorize] 装飾を以下の各操作に追加します。
	<pre class="prettyprint">
	...
	
	<mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
	public class WorkItemsController : Controller
	{
		...
	
	    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
	    public ActionResult Create()
	    ...
	
	    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
	    public async Task&lt;ActionResult> Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
	    ...
	
	    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
	    public async Task&lt;ActionResult> Edit(int? id)
	    ...
	
	    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
	    public async Task&lt;ActionResult> Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
	    ...
	
	    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
	    public async Task&lt;ActionResult> Delete(int? id)
	    ...
	
	    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
	    public async Task&lt;ActionResult> DeleteConfirmed(int id)
	    ...
	}</pre>

	ロール マッピングの処理は Azure クラシック ポータルの UI で行うため、必要となるのは、各操作で正しいロールが承認されているかどうかを確認することだけです。

	> [AZURE.NOTE] 一部の操作では <code>[ValidateAntiForgeryToken]</code> 装飾を使用します。[Brock Allen](https://twitter.com/BrockLAllen) が 「[MVC 4, AntiForgeryToken and Claims (MVC 4、AntiForgeryToken とクレーム)](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/)」で説明している動作により、HTTP POST が偽造防止トークンの検証に次の理由で失敗する可能性があります。
	> + 既定で偽造防止トークンに必要となる http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider を Azure Active Directory が送信しない。
	> + Azure Active Directory が AD FS とディレクトリを同期している場合、AD FS トラストは既定で http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider の要求を送信しない。ただし、この要求を送信するように AD FS を手動で構成できます。これについては次の手順で対処します。

12.  App\_Start\\Startup.Auth.cs で、以下のコード行を `ConfigureAuth` メソッドに追加します。各名前付け解決エラーを右クリックすると、そのエラーが修正されます。

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` は、Azure Active Directory が提供する要求 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` を指定します。承認の手順についてはこれで終わりましたので (短い時間で済みました)、操作の実際の機能に時間を割くことができます。

13.	Create() と Edit() に次のコードを追加すると、後で JavaScript でいくつかの変数を使用できるようにします。各名前付け解決エラーを右クリックすると、そのエラーが修正されます。

        ViewData["token"] = AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
        ViewData["tenant"] = ConfigHelper.Tenant;

13.	`AcquireToken()` メソッドはまだ定義されていないため、今すぐ `WorkItemsController` クラスで定義します。各名前付け解決エラーを右クリックすると、そのエラーが修正されます。

        static string AcquireToken(string userObjectId)
        {
            ClientCredential cred = new ClientCredential(ConfigHelper.ClientId, ConfigHelper.AppKey);
            Claim tenantIdClaim = ClaimsPrincipal.Current.FindFirst(Globals.TenantIdClaimType);
            AuthenticationContext authContext = new AuthenticationContext(String.Format(CultureInfo.InvariantCulture, ConfigHelper.AadInstance, tenantIdClaim.Value), new TokenDbCache(userObjectId));
            AuthenticationResult result = authContext.AcquireTokenSilent(ConfigHelper.GraphResourceId, cred, new UserIdentifier(userObjectId, UserIdentifierType.UniqueId));
            return result.AccessToken;
        }
		
14.	Views\\WorkItems\\Create.cshtml (自動的にスキャフォールディングされた項目) で、`Html.BeginForm` ヘルパー メソッドを探し、以下のように変更します。
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
	{
	    @Html.AntiForgeryToken()
	
	    &lt;div class="form-horizontal">
	        &lt;h4>WorkItem&lt;/h4>
	        &lt;hr />
	        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
	
	        &lt;div class="form-group">
	            &lt;div class="col-md-10">
	                @Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type="hidden"</mark> } })
	                @Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
	            &lt;/div>
	        &lt;/div>
	
	        &lt;div class="form-group">
	            @Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10">
	                @Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
	            &lt;/div>
	        &lt;/div>
	
	        &lt;div class="form-group">
	            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10">
	                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
	            &lt;/div>
	        &lt;/div>
	
	        &lt;div class="form-group">
	            @Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10">
	                @Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
	                @Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
	            &lt;/div>
	        &lt;/div>
	
	        &lt;div class="form-group">
	            &lt;div class="col-md-offset-2 col-md-10">
	                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> />
	            &lt;/div>
	        &lt;/div>
	    &lt;/div>
	
	    <mark>&lt;script>
	            // People/Group Picker Code
	            var maxResultsPerPage = 14;
	            var input = document.getElementById("AssignedToName");
	            var token = "@ViewData["token"]";
	            var tenant = "@ViewData["tenant"]";
	
	            var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
	
	            // Submit the selected user/group to be asssigned.
	            $("#submit-button").click({ picker: picker }, function () {
	                if (!picker.Selected())
	                    return;
	                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
	            });
	    &lt;/script></mark>
	
	}</pre>

	スクリプトでは、AadPicker オブジェクトが [Azure Active Directory Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) を呼び出し、入力に一致するユーザーとグループを検索します。

15. [パッケージ マネージャー コンソール](http://docs.nuget.org/Consume/Package-Manager-Console)を開き、**Enable-Migrations –EnableAutomaticMigrations** を実行します。アプリを Azure に発行したときに選択したオプションと同様、このコマンドは、Visual Studio でデバッグする際に [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) でアプリのデータベース スキーマを更新するのに役立ちます。

15. ここで、Visual Studio デバッガーでアプリを実行するか、Azure App Service Web Apps に再発行します。アプリケーションの所有者としてログインし、`https://<webappname>.azurewebsites.net/WorkItems/Create` に移動します。これで、ドロップダウン リストから Azure Active Directory のユーザーまたはグループを選択するか、一覧をフィルター処理するためのデータを入力できます。

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. フォームの残りの部分を設定し、**[作成]** をクリックします。これで、~/WorkItems/Index ページに新しく作成した作業項目が表示されます。また、以下のスクリーンショットでは、Views\\WorkItems\\Index.cshtml の `AssignedToID` 列が削除されていることがわかります。

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	次に、**[編集]** ビューにも同じような変更を加えます。Views\\WorkItems\\Edit.cshtml で、`Html.BeginForm` ヘルパー メソッドに対して、前の手順で Views\\WorkItems\\Create.cshtml に加えたのと同じ変更を加えます (上記コードの強調表示された "Create" を "Edit" に置き換えます)。

これで完了です。

承認とさまざまな操作の基幹業務機能を WorkItems コントローラーに構成できましたので、さまざまなアプリケーション ロールのユーザーとしてログインし、アプリケーションの応答を確認してみてください。

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## 他の関連リソース

- [SSL と Authorize 属性を使用してアプリケーションを保護する](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Azure App Service での認証には、Active Directory を使用します](web-sites-authentication-authorization.md)
- [AD FS の認証を使用して Azure App Service で .NET MVC Web アプリを作成する](web-sites-dotnet-lob-application-adfs.md)
- [Microsoft Azure Active Directory のサンプルとドキュメント](https://github.com/AzureADSamples)
- [Vittorio Bertocci のブログ](http://blogs.msdn.com/b/vbertocci/)
- [VS2013 Web プロジェクトを WIF から Katana に移植する方法に関するページ](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [以前のハイブリッド クラウドではない、Azure の新しいハイブリッド接続に関するビデオ](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Active Directory と Azure Active Directory の類似点](http://technet.microsoft.com/library/dn518177.aspx)
- [ディレクトリ同期とシングル サインオンのシナリオ](http://technet.microsoft.com/library/dn441213.aspx)
- [Azure Active Directory がサポートしているトークンと要求の種類](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!---HONumber=AcomDC_0302_2016-->