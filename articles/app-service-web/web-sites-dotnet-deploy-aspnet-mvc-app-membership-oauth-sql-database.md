<properties 
	pageTitle="認証および SQL DB を使用する ASP.NET MVC アプリの作成と、Azure App Service へのデプロイ" 
	description="SQL Database バックエンドを使用して ASP.NET MVC 5 Web アプリを作成し、認証および承認を追加して、それを Azure にデプロイする方法について説明します。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="riande"/>



# 認証および SQL DB を使用する ASP.NET MVC アプリの作成と、Azure App Service へのデプロイ

このチュートリアルでは、ユーザーが Facebook や Google の資格情報を使用してログインできる、安全な ASP.NET MVC 5 Web アプリケーションを構築する方法を説明します。さらに、作成したアプリケーションを [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする方法についても学習します。

Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2013 を持っていない場合は、SDK によって Visual Studio 2013 for Web Express が自動的にインストールされます。これで、Azure 向けのアプリケーションを無料で開発できます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルでは、安全なデータ主導型 Web アプリケーションを作成し、クラウド データベースを使用してクラウド上で実行します。

学習内容:

* 安全な ASP.NET MVC 5 プロジェクトを作成して、Azure App Service で [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) に発行する方法
* [OAuth](http://oauth.net/ "http://oauth.net/") および ASP.NET メンバーシップ データベースを使用してアプリケーションを保護する方法
* SQL データベースを使用して Azure にデータを保存する方法

ASP.NET MVC 5 に基づく、データベース アクセスに ADO.NET Entity Framework を使用する、簡単な連絡先リスト Web アプリケーションをビルドします。次の図は、完成したアプリケーションのログイン ページです。

![login page][rxb]

>[AZURE.NOTE]このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、<a href="/ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">MSDN サブスクライバーの特典を有効にする</a>か、<a href="/ja-jp/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">無料評価版にサインアップ</a>してください。

>Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、「[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

開発環境をセットアップするには、[Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkId=390521) 以降および最新バージョンの [Azure SDK for Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) をインストールする必要があります。この記事は、Visual Studio Update 4 および SDK 2.5.1 を対象にしています。

## ASP.NET MVC 5 アプリケーションを作成する

### プロジェクトを作成する

1. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。

	![New Project in File menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. **[新しいプロジェクト]** ダイアログ ボックスで、**[インストールされているテンプレート]** の下にある **[Visual C#]** を展開して **[Web]** を選択し、**[ASP.NET Web アプリケーション]** を選択します。

1. アプリケーションに「**ContactManager**」という名前を付けて、**[OK]** をクリックします。

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
	**注:** 必ず「ContactManager」と入力してください。これからコピーするコード ブロックは、ContactManager というプロジェクト名が前提となっています。

1. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** テンプレートを選択します。**[認証]** に **[個別ユーザー アカウント]** が設定されていること、**[クラウドでのホスト]** がオンになっていること、および **[Web アプリ]** が選択されていることを確認します。

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. 構成ウィザードでは *ContactManager* に基づいて一意の名前が提示されます。新しい App Service プランとリソース グループを作成するかどうかを決める必要があります。新しいプランまたはリソース グループを作成するかどうかを決める方法については、「[Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。このチュートリアルでは、少なくとも 1 つのリソース グループを新しく作成します。近くのリージョンを選択します。[azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") を使用して、待ち時間が最も短いデータ センターを検索することができます。次の手順でデータベースを設定します。したがって、**[OK]** はまだクリックしないでください。

   ![New plan and resource group](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. 以前にデータベースのサーバーを作成していない場合は、**[新しいサーバーの作成]** を選択し、データベース名、ユーザー名、およびパスワードを入力します。

   ![Use new database](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

3. データベース サーバーがある場合は、そのサーバーを使用して新しいデータベースを作成します。データベース サーバーは貴重なリソースであるため、一般的にはデータベースごとにデータベース サーバーを作成するのではなく、同じサーバー上にテスト用や開発用の複数のデータベースを作成してください。Web サイトとデータベースが同じリージョンにあることを確認してください。

    ![Use exsiting database](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/useexistingdb.png)

### ページのヘッダーとフッターを設定する


1. **ソリューション エクスプローラー**の *Views\\Shared* フォルダーにある *Layout.cshtml* ファイルを開きます。

	![_Layout.cshtml in Solution Explorer][newapp004]

1. *Layout.cshtml* ファイル内のマークアップを次のコードに置き換えます。変更箇所は、以下で強調表示されています。

<pre>
			&lt;!DOCTYPE html>
			&lt;html>
			&lt;head>
			    &lt;meta charset="utf-8" />
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0">
			    &lt;title>@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title>
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head>
			&lt;body>
			    &lt;div class="navbar navbar-inverse navbar-fixed-top">
			        &lt;div class="container">
			            &lt;div class="navbar-header">
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                    &lt;span class="icon-bar">&lt;/span>
			                &lt;/button>
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div>
			            &lt;div class="navbar-collapse collapse">
			                &lt;ul class="nav navbar-nav">
			                    &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li>
			                    &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li>
			                &lt;/ul>
			                @Html.Partial("_LoginPartial")
			            &lt;/div>
			        &lt;/div>
			    &lt;/div>
			    &lt;div class="container body-content">
			        @RenderBody()
			        &lt;hr />
			        &lt;footer>
			            &lt;p>&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p>
			        &lt;/footer>
			    &lt;/div>
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body>
			&lt;/html>
</pre>

### ローカルでアプリケーションを実行する

1. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

	アプリケーションのホーム ページが既定のブラウザーに表示されます。

	![Web app running locally](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

これで、Azure に展開するアプリケーションを作成するために必要な操作が完了しました。

## プロジェクトに対して SSL を有効にする ##

1. SSL を有効にします。ソリューション エクスプローラーで **ContactManager** プロジェクトをクリックし、F4 キーを押します。プロパティ ダイアログ ボックスが表示されます。**[SSL Enabled]** を True に変更します。**[SSL URL]** をコピーします。過去に SSL Web アプリを作成したことがなければ、SSL URL は https://localhost:44300/ になります。

	![enable SSL][rxSSL]
 
1. ソリューション エクスプローラーで **Contact Manager** プロジェクトを右クリックし、**[プロパティ]** をクリックします。
1. 左側のタブで **[Web]** をクリックします。
1. **SSL URL** を使用するように **[プロジェクト URL]** を変更し、ページを保存します (Ctrl + S)。

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. 次の図に示すように、Internet Explorer が Visual Studio によって起動されるブラウザーであることを確認します。

	![default browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	ブラウザー セレクターによって、Visual Studio が起動するブラウザーを指定することができます。

 	![browser selector](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

	変更を加える場合、複数のブラウザーを選択して、Visual Studio が各ブラウザーを更新するようにできます。詳細については、「[Using Browser Link in Visual Studio 2013 (Visual Studio 2013 でのブラウザー リンクの使用)](http://www.asp.net/visual-studio/overview/2013/using-browser-link)」を参照してください。


1. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。指示に従って、IIS Express が生成した自己署名証明書を信頼します。

	 ![instructions to trust the self-signed certificate that IIS Express has generated](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. **[セキュリティ警告]** ダイアログを確認して、**localhost** を表す証明書をインストールする場合は **[はい]** をクリックします。

 	![localhost IIS Express certificate warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. IE は*ホーム* ページを表示し、SSL の警告はありません。

	 ![IE with localhost SSL and no warnings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	Google Chrome も証明書を受け取り、警告なしで HTTPS コンテンツを表示します。Firefox は独自の証明書ストアを使用します。したがって、警告を表示します。

	 ![FireFox Cert Warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## Azure にアプリケーションを展開する

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
   **Web の発行**ウィザードが開きます。

1. **[Web の発行]** ダイアログ ボックスの **[発行]** をクリックします。

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	これで、作成したアプリケーションはクラウドで実行されています。このアプリケーションを次に展開するときは、変更したファイル (または新しいファイル) のみが展開されます。

	![Running in Cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## アプリケーションにデータベースを追加する

次に、アプリケーションを更新して、連絡先を表示および更新してデータをデータベースに保存する機能を追加します。アプリケーションでは、データベースの作成およびデータの読み取りと更新に Entity Framework を使用します。

### 連絡先のデータ モデル クラスを追加する

まず、コードで単純なデータ モデルを作成します。

1. **ソリューション エクスプローラー**で、Models フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。

	![Add Class in Models folder context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. **[新しい項目の追加]** ダイアログ ボックスで、新しいクラス ファイルに「*Contact.cs*」という名前を付け、[**追加**] をクリックします。

	![Add New Item dialog box][adddb002]

3. Contacts.cs ファイルの内容を次のコードに置き換えます。

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
**Contacts** クラスでは、各連絡先について保存するデータと、データベースが必要とする主キー (*ContactID*) を定義します。

### アプリケーション ユーザーが連絡先を操作できる Web ページを作成する

ASP.NET MVC のスキャフォールディング機能によって、作成、読み取り、更新、削除 (CRUD 操作) を実行するコードを自動的に生成できます。

## データのコントローラーとビューを追加する

1. プロジェクトをビルドします **(Ctrl + Shift + B)**。(スキャフォールディング機能の使用前にプロジェクトをビルドする必要があります。) 
1. **ソリューション エクスプローラー**で、Controllers フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。

	![Add Controller in Controllers folder context menu][addcode001]

5. **[スキャフォールディングの追加]** ダイアログ ボックスで、**[MVC 5 コントローラーとビュー、EF を使用]** を選択し、**[追加]** をクリックします。
	
	![Add Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)


1. **[モデル クラス]** ボックスで **Contact (ContactManager.Models)** を選択します。(下図を参照)。
1. **[データ コンテキスト クラス]** で **ApplicationDbContext (ContactManager.Models)** を選択します。この **ApplicationDbContext** が、メンバーシップ DB と連絡先データの両方に使用されます。
1. **[コントローラー名]** テキスト入力ボックスで、コントローラー名として「CmController」と入力します。 

	![New data ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. **[追加]** をクリックします。

   Visual Studio によって、**Contact** オブジェクトの CRUD データベース操作に対応したコントローラー メソッドとビューが作成されます。

## Migrations の有効化、データベースの作成、サンプル データとデータ初期化子の追加 ##

次の作業では、作成したデータ モデルに基づいてデータベースを作成するために、[Code First Migrations](http://msdn.microsoft.com/library/hh770484.aspx) 機能を有効にします。

1. **[ツール]** メニューの **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** を順に選択します。![Package Manager Console in Tools menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		enable-migrations
	**enable-migrations** コマンドによって *Migrations* フォルダーが作成され、そのフォルダーに *Configuration.cs* ファイルが保存されます。このファイルを編集して、データベースの初期データ投入を行い、Migration を構成できます。

2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		add-migration Initial


	**add-migration Initial** コマンドは、データベースを作成する *Migrations* フォルダーに **&lt;date_stamp&gt;Initial** という名前のファイルを生成します。最初のパラメーター ( **Initial** ) は省略可能で、ファイルの名前を作成するときに使用されます。新しいクラス ファイルは**ソリューション エクスプローラー**で表示できます。**Initial** クラスでは、**Up** メソッドを使用して Contacts テーブルを作成し、**Down** メソッドを使用してそのテーブルを削除します (前の状態に戻します)。
3. *Migrations\\Configuration.cs* ファイルを開きます。 
4. 次の名前空間を追加します。 

    	 using ContactManager.Models;



5. *Seed* メソッドを次のコードに置き換えます。

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	このコードでは、連絡先情報を使用してデータベースを初期化 (初期データ投入) します。シード データベースの生成の詳細については、「[Seeding and Debugging Entity Framework (EF) DBs (Entity Framework DB のシード化とデバッグ)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)」を参照してください。


6. **[パッケージ マネージャー コンソール]** で、次のコマンドを入力します。

		update-database

	![Package Manager Console commands][addcode009]

	**update-database** によって、データベースを作成する最初の Migration が実行されます。既定では、データベースは SQL Server Express LocalDB データベースとして作成されます

7. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行し、**[CM Demo]** リンクをクリックします (または、https://localhost:(port#)/Cm に移動します)。

	アプリケーションでは、登録されたデータが表示され、編集、詳細、削除のリンクが示されます。データの作成、編集、削除、表示を行うことができます。

	![MVC view of data][rx2]



## OAuth2 プロバイダーを追加する

[OAuth](http://oauth.net/ "http://oauth.net/") は、Web、モバイル、およびデスクトップのアプリケーションからシンプルで標準的な方法で安全に認証するためのオープン プロトコルです。ASP.NET MVC インターネット テンプレートは OAuth を使用して、Facebook、Twitter、Google、Microsoft を認証プロバイダーとしてサポートします。このチュートリアルでは Google のみを認証プロバイダーとして使用しますが、コードを少し変更すれば他のプロバイダーも使用できます。他のプロバイダーを実装する手順は、このチュートリアルで説明する手順とほとんど同じです。Facebook を認証プロバイダーとして使用する方法については、拙著のチョーとリアル「[MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on (Facebook や Google の OAuth2/OpenID サインオンを使用した ASP.NET MVC 5 アプリケーション)](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)」を参照してください。

このチュートリアルでは、認証の他にロールを使用して権限を付与します。*canEdit* ロールに追加したユーザーのみがデータを変更 (連絡先を作成、編集、削除) できます。

拙著のチュートリアル「[MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on (Facebook や Google の OAuth2/OpenID サインオンを使用した ASP.NET MVC 5 アプリケーション)](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog)」の「**Creating a Google app for OAuth 2 to set up a Google app for OAuth2 (OAuth 2 用の Google アプリを作成して OAuth2 用に Google アプリをセットアップする)**」の手順に従います。アプリケーションを実行してテストし、Google 認証を使用してログオンできることを確認します。

## メンバーシップ API を使用する
このセクションでは、ローカル ユーザーと *canEdit* ロールをメンバーシップ データベースに追加します。*canEdit* ロールのユーザーのみがデータを編集することができます。実行可能な操作に基づいてロール名を付けるのが望ましいので、この場合は *admin* より *canEdit* が適しています。その後、必要に応じて、(わかりにくい *superAdmin* ではなく) *canDeleteMembers* などの新しいロールを追加します。

1. *migrations\\configuration.cs* ファイルを開いて、次の `using` ステートメントを追加します。

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. 次の **AddUserAndRole** メソッドをクラスに追加します。

    
         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

1. 新しいメソッドを **Seed** メソッドから呼び出します。
	<pre>
    protected override void Seed(ContactManager.Models.ApplicationDbContext context)
    {
        <mark>AddUserAndRole(context);</mark>
        context.Contacts.AddOrUpdate(p => p.Name,
            // Code removed for brevity
    }
</pre>
<span></span>次のイメージは *Seed* メソッドへの変更を示します。

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

   このコードでは、*canEdit* という名前の新しいロールを作成し、新しいローカル ユーザー *user1@contoso.com* を作成して、*user1@contoso.com* を *canEdit* ロールに追加します。詳細については、「[ASP.NET Identity チュートリアル](http://www.asp.net/identity/overview/features-api)」を参照してください。

## 一時的なコードを使用して新しいソーシャル ログイン ユーザーを canEdit ロールに追加する  ##
このセクションでは、Account コントローラーの **ExternalLoginConfirmation** メソッドを一時的に変更して、*canEdit* ロールに新しいユーザーを追加し、OAuth プロバイダーに登録します。ここで一時的に **ExternalLoginConfirmation** メソッドを変更し、新しいユーザーが管理者ロールに自動的に追加されるようにします。ロールの追加と管理に必要なツールの準備が整うまでの間、以下に示した一時的な自動登録コードを使用することになります。将来的には、ユーザー アカウントとロールの作成および編集のために、[WSAT](http://msdn.microsoft.com/library/ms228053.aspx) と同様のツールを提供する予定です。

1. **Controllers\\AccountController.cs** ファイルを開いて、**ExternalLoginConfirmation** メソッドに移動します。
1. 次の **AddToRoleAsync** への呼び出しを **SignInAsync** の呼び出しの直前に追加します。

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   このコードは、新しく登録したユーザーを "canEdit" ロールに追加します。これにより、データの変更 (編集) を伴うアクション メソッドへのアクセス権がユーザーに割り当てられます。<pre> // POST: /Account/ExternalLoginConfirmation [HttpPost] [AllowAnonymous] [ValidateAntiForgeryToken] public async Task<ActionResult> ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl) { if (User.Identity.IsAuthenticated) { return RedirectToAction("Index", "Manage"); } if (ModelState.IsValid) { // Get the information about the user from the external login provider var info = await AuthenticationManager.GetExternalLoginInfoAsync(); if (info == null) { return View("ExternalLoginFailure"); } var user = new ApplicationUser { UserName = model.Email, Email = model.Email }; var result = await UserManager.CreateAsync(user); if (result.Succeeded) { result = await UserManager.AddLoginAsync(user.Id, info.Login); if (result.Succeeded) { <mark>await UserManager.AddToRoleAsync(user.Id, "canEdit");</mark> await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false); return RedirectToLocal(returnUrl); } } AddErrors(result); } ViewBag.ReturnUrl = returnUrl; return View(model); } </pre>

このチュートリアルの中で、アプリケーションを Azure に展開します。そこでのログオンには、Google などサード パーティの認証プロバイダーが使用されます。新しく登録されたアカウントは、*canEdit* ロールに追加されます。アプリの URL と Google ID さえあればだれでも登録し、データベースを更新することができます。そのような操作が第三者によって行われるのを防ぐためには、サイトを停止する必要があります。だれが *canEdit* ロールに追加されているかは、データベースを調べることによって確認できます。

**[パッケージ マネージャー コンソール]** で上方向キーを押し、次のコマンドを呼び出します。

		Update-Database

**Update-Database** コマンドを実行すると、**Seed** メソッドが実行され、先ほど追加した **AddUserAndRole** が実行されます。**AddUserAndRole** は *user1@contoso.com* というユーザーを作成し、*canEdit* ロールに追加します。

## SSL と Authorize 属性を使用してアプリケーションを保護する ##

このセクションでは、[Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx)) 属性を適用してアクション メソッドへのアクセスを制限します。匿名ユーザーが表示できるのは、home コントローラーの **Index** アクション メソッドだけになります。登録ユーザーは、連絡先データ (Cm コントローラーの **[Index]** ページと **[Details]** ページ)、[About] ページ、[Contact] ページを表示することができます。*canEdit* ロールを与えられているユーザーのみがアクション メソッドを実行してデータを変更できます。

1. [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) フィルターと [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) フィルターをアプリケーションに追加します。[Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 属性と [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 属性をコントローラーごとに追加する方法もありますが、セキュリティ上の理由から、通常はこれらをアプリケーション全体に適用します。アプリケーション全体に適用すれば、新しいコントローラーやアクション メソッドを追加したとき、それらが自動的に保護されます。ユーザー自身で適用する必要がありません。詳細については、「[Securing your ASP.NET MVC App and the new AllowAnonymous Attribute (ASP.NET MVC 4 アプリケーションの保護と新しい AllowAnonymous 属性)](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)」を参照してください。*App_Start\\FilterConfig.cs* ファイルを開き、*RegisterGlobalFilters* メソッドを次のように書き換えます(2 つのフィルターを追加)。<pre> public static void RegisterGlobalFilters(GlobalFilterCollection filters) { filters.Add(new HandleErrorAttribute()); <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute()); filters.Add(new RequireHttpsAttribute());</mark> } </pre>




	このコードで適用した [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) フィルターによって、匿名ユーザーは、アプリケーション内のメソッドに一切アクセスできなくなります。2 つのメソッドについては、[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を使用して承認要件を免除し、匿名ユーザーがログインしてホーム ページを表示できるようにします。[RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) により、Web アプリケーションに対するすべてのアクセスは HTTPS に限定されます。

1. [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を Home コントローラーの **Index** メソッドに追加します。[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を使用すると、特定のメソッドを認証不要として指定できます。<pre> public class HomeController : Controller { <mark>[AllowAnonymous]</mark> public ActionResult Index() { return View(); } </pre>

2. グローバルに検索すると、Account コントローラーのログイン メソッドや登録メソッドで *AllowAnonymous* が使われていることがわかります。
1. *CmController.cs* の *Cm* コントローラーで、データの変更 (Create、Edit、Delete、つまり、Index と Details を除くすべてのアクション メソッド) を伴う HttpGet メソッドと HttpPost メソッドに `[Authorize(Roles = "canEdit")]` を追加します。完成したコードは次のようになります。<pre> // GET: Cm/Create <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create() { return View(new Contact { Address = "123 N 456 W", City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT", Zip = "59405"}); } // POST: Cm/Create // To protect from overposting attacks, please enable the specific properties you want to bind to, for // more details see http://go.microsoft.com/fwlink/?LinkId=317598. [HttpPost] [ValidateAntiForgeryToken] <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact) { if (ModelState.IsValid) { db.Contacts.Add(contact); db.SaveChanges(); return RedirectToAction("Index"); } return View(contact); } // GET: Cm/Edit/5 <mark>[Authorize(Roles = "canEdit")]</mark> public ActionResult Edit(int? id) { if (id == null) { return new HttpStatusCodeResult(HttpStatusCode.BadRequest); } Contact contact = db.Contacts.Find(id); if (contact == null) { return HttpNotFound(); } return View(contact); } </pre>

1. まだ前のセッションにログインしている場合は、**ログアウト** リンクをクリックします。
1. **[About]** または **[Contact]** リンクをクリックします。匿名ユーザーはこれらのページを表示できないため、ログイン ページにリダイレクトされます。 
1. **[Register as a new user]** リンクをクリックして、電子メールが *joe@contoso.com* であるローカル ユーザーを追加します。*Joe* が Home ページ、About ページ、Contact ページを表示できることを確認します。
	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. *[CM Demo]* リンクをクリックし、データが表示されることを確認します。
1. このページ上の編集リンクをクリックすると、ログイン ページにリダイレクトされます (新しいローカル ユーザーが *canEdit* ロールに追加されていないため)。
1. ユーザー名 *user1@contoso.com*、パスワード "P_assw0rd1" ("word" の "0" はゼロ) でログインします。先ほど選択した編集ページにリダイレクトされます。<br/> このアカウントとパスワードでログインできない場合は、ソース コードからパスワードをコピーして貼り付けてみてください。それでもログインできない場合は、**AspNetUsers** テーブルの **UserName** 列を見て、*user1@contoso.com* が追加されていることを確認します。
1. データを変更できることを確認します。

## Azure にアプリケーションを展開する

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![Publish in project context menu][firsdeploy003]

	**Web の発行**ウィザードが開きます。

1. **[Web の発行]** ダイアログ ボックスの左側の **[設定]** タブをクリックします。**[v]** アイコンをクリックして **ApplicationDbContext** の **[リモート接続文字列]** を選択し、**[ContactManagerNN_dbB]** を選択します。

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. **ContactManagerContext** で、**[Code First Migrations を実行する]** を選択します。

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. **[発行]** をクリックします。
1. *user1@contoso.com* (パスワード: "P_assw0rd1") でログインし、データを編集できることを確認します。1. ログアウトします。
1. [Google Developers Console](https://console.developers.google.com/) に移動し、**[資格情報]** タブでリダイレクト URIS と JavaScript Orgins を Azure の URL を使用するように変更します。
1. Google または Facebook を使用してログインします。これにより、Google または Facebook のアカウントが **canEdit** ロールに追加されます。「*The redirect URI in the request: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI (要求のリダイレクト URI: http://contactmanager {my version}.azurewebsites.net/signin-google が、登録されたリダイレクト URI と一致しませんでした)*」というメッセージと共に HTTP 400 エラーが表示された場合は、加えた変更が反映されるまで待機する必要があります。数分経ってからこのエラーが発生する場合は、URI は正しいことを確認します。

### Web アプリを停止して第三者の登録を防ぐ  

1. **サーバー エクスプローラー**で **[Web Apps]** に移動します。
4. Web アプリを右クリックし、**[停止]** を選択します。 

	![stop web app](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s1.png)

	[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)から Web アプリを選択し、ページ下部の **[停止]** アイコンをクリックする方法もあります。

	![stop web app portal](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### AddToRoleAsync の削除、発行、テスト

1. Account コントローラーの **ExternalLoginConfirmation** メソッドから次のコードをコメント アウト、または削除します。`await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. プロジェクトをビルドします。これにより、ファイルの変更が保存され、コンパイル エラーがないか確認が行われます。
5. **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[発行]** をクリックします。

	   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. **[プレビューの開始]** をクリックします。更新する必要のあるファイルだけが展開されます。
5. Visual Studio またはポータルから Web アプリを開きます。**Web アプリが停止した状態で発行することはできません。**

	![start web app](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. Visual Studio に戻り、**[発行]** をクリックします。
3. Azure アプリケーションが既定のブラウザーで起動します。ログインして、ログアウトすると、匿名ユーザーとしてホーム ページを表示できます。  
4. **[About]** リンクをクリックします。ログイン ページにリダイレクトされます。
5. ログイン ページの **[登録]** リンクをクリックし、ローカル アカウントを作成します。読み取り専用ページにはアクセスできるが、データの変更を伴うページにはアクセスできない (*canEdit* ロールによって保護されている) ことをこのローカル アカウントを使用して確認します。ローカル アカウント アクセスは、後でこのチュートリアルの中で削除します。 

	![Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. *About* ページと *Contact* ページにアクセスできることを確認します。

	![ログオフ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. **[CM Demo]** リンクをクリックして **Cm** コントローラーに移動します。URL に *Cm* を付加することによって移動してもかまいません。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. [編集] リンクをクリックします。ログイン ページにリダイレクトされます。**[Use another service to log in]** で [Google] または [Facebook] をクリックし、過去に登録したアカウントでログインします(短時間で作業して、セッション Cookie がタイムアウトしていない場合は、以前に使用した Google アカウントまたは Facebook アカウントで自動的にログインされます)。
2. そのアカウントにログインした状態でデータを編集できることを確認します。**注:** このアプリケーションで Google からログアウトし、同じブラウザーで別の Google アカウントにログインすることはできません。1 つのブラウザーを使用している場合は、Google に移動してログアウトする必要があります。同じサード パーティの認証システム (Google など) に属している別のアカウントでログオンするためには、異なるブラウザーを使用する必要があります。

Google アカウント情報の氏名を入力しないと、NullReferenceException が発生します。


## SQL Azure DB を調べる ##

1. **サーバー エクスプローラー**で **[ContactDB]** に移動します。
2. **[ContactDB]** を右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** を選択します。
 
	![open in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. このデータベースに接続したことがない場合は、現在の IP アドレスへのアクセスを有効にするファイアウォール規則を追加するように求められることがあります。IP アドレスは、自動的に入力されます。**[ファイアウォール規則の追加]** をクリックするだけで、アクセスが有効になります。

  ![add firewall rule](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)
        
  次に、データベースの作成時に指定したユーザー名とパスワードを使用して、データベースにログインします。
 
1. **AspNetUsers** テーブルを右クリックし、**[データの表示]** を選択します。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. **canEdit** ロールへの追加用として登録した Google アカウントの ID と *user1@contoso.com* の ID をメモします。それ以外のユーザーは **canEdit** ロールに含めないようにする必要があります。(この点については、次のステップで確認します。)

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. **SQL Server オブジェクト エクスプローラー**で **[AspNetUserRoles]** を右クリックし、**[データの表示]** を選択します。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
**UserId** が、登録した Google アカウントおよび *user1@contoso.com* のものであることを確認します。


## 次のステップ

このサンプルについて執筆したチュートリアルをご覧ください。

1.	[Create a secure ASP.NET MVC 5 web app with log in, email confirmation and password reset (ログイン、電子メールの確認、およびパスワードのリセットでセキュリティで保護された ASP.NET MVC 5 web アプリを作成する)](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[ASP.NET MVC 5 app with SMS and email Two-Factor Authentication (SMS と電子メールの 2 要素認証を使用する ASP.NET MVC 5 アプリケーション)](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Best practices for deploying passwords and other sensitive data to ASP.NET and Azure (ASP.NET および Azure へパスワードや機密データをデプロイするためのベスト プラクティス)](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 (Facebook や Google の OAuth2 を使用した ASP.NET MVC 5 アプリケーションの作成)](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)。このチュートリアルでは、ユーザー登録データベースにプロファイル データを追加する方法や、Facebook を認証プロバイダーとして使用する詳細な手順について説明しています。
5.	[Getting Started with ASP.NET MVC 5 (ASP.NET MVC 5 の使用)](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

このチュートリアルの上部に表示されるソーシャル ログイン ボタンを有効にするには、「[Pretty social login buttons for ASP.NET MVC 5 (ASP.NET MVC 5 用ソーシャル ログイン ボタン)](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/)」を参照してください。

MVC および Entity Framework のプログラミングについては、Tom Dykstra が [Entity Framework と MVC について取り上げたこちらのページ](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)に、さらに詳しく解説されています。

このチュートリアルとサンプル アプリケーションは、Tom Dykstra と Barry Dorrans (Twitter [@blowdart](http://blogs.msdn.com/b/rickandy/)) の協力の下、[Rick Anderson](https://twitter.com/RickAndMSFT) (Twitter [@RickAndMSFT](https://twitter.com/blowdart)) が執筆しました。

***役に立った内容や改善点など、皆様からのご意見をお寄せください***。このチュートリアルに関してだけでなく、ここで紹介した製品に関するご意見やご要望もお待ちしております。お寄せいただいたご意見は、今後の改善に役立たせていただきます。新しいトピックについては、「[Show Me How With Code (コードの使用方法)](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code)」で要求および投票することもできます。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)をご覧ください。

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
 

<!---HONumber=July15_HO3-->