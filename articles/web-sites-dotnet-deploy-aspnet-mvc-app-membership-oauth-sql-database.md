<properties urlDisplayName="Website with SQL Database" pageTitle="メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションを Azure の Web サイトにデプロイする" metaKeywords="Azure の hello world チュートリアル, Azure 入門チュートリアル, SQL Database チュートリアル, Azure .NET hello world チュートリアル, Azure C# hello world チュートリアル, SQL Azure C# チュートリアル" description="SQL Database バックエンドを使用して ASP.NET MVC 5 Web サイトを作成し、それを Azure にデプロイする方法について説明します。" metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="riande" /> 



# メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションを Azure の Web サイトにデプロイする

***2014 年 10 月 12 日更新***

このチュートリアルでは、ユーザーが Facebook や Google の資格情報を使用してログインできる、安全な ASP.NET MVC 5 Web アプリケーションを構築する方法を説明します。さらに、作成したアプリケーションを Azure にデプロイする方法についても学習します。

Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2013 を持っていない場合は、SDK によって Visual Studio 2013 for Web Express が自動的にインストールされます。これで、Azure 向けのアプリケーションを無料で開発できます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルでは、安全なデータ主導型 Web アプリケーションを作成し、クラウド データベースを使用してクラウド上で実行します。

学習内容: 

* 安全な ASP.NET MVC 5 プロジェクトを作成して Azure の Web サイトに発行する方法
*  [OAuth](http://oauth.net/ "http://oauth.net/") および ASP.NET メンバーシップ データベースを使用してアプリケーションを保護する方法
* SQL データベースを使用して Azure にデータを保存する方法

ASP.NET MVC 5 に基づき、データベース アクセスに ADO.NET Entity Framework を使用する、簡単な連絡先リスト Web アプリケーションを構築します。次の図は、完成したアプリケーションのログイン ページです。

![login page][rxb]

>[WACOM.NOTE] このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、<a href="/ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">MSDN サブスクライバーの特典を有効にする</a>か、<a href="/ja-jp/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">無料評価版にサインアップ</a>してください。


このチュートリアルの内容: 

- [開発環境を設定する](#setupdevenv)
- [ASP.NET MVC 5 アプリケーションを作成する][createapplication]
- [アプリケーションを Azure にデプロイする][deployapp1]
- [アプリケーションにデータベースを追加する][adddb]
- [OAuth プロバイダーを追加する][]
- [Azure にアプリケーションをデプロイする][deployapp11]
- [次のステップ][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

localhost に新しい SSL 証明書を使用するには、[Visual Studio 2013 Update 3 RC](http://go.microsoft.com/fwlink/?LinkId=390521) 以降をインストールする必要があります。

<h2><a name="bkmk_createmvc4app"></a>ASP.NET MVC 5 アプリケーションを作成する</h2>

### プロジェクトを作成する

1. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。

	![New Project in File menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. **[新しいプロジェクト]** ダイアログ ボックスで、**C#** を展開して **[インストールされているテンプレート]** の下にある **[Web]** を選択し、**[ASP.NET Web アプリケーション]** を選択します。

1. アプリケーションに「**ContactManager**」という名前を付けて、**[OK]** をクリックします。

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**注: **「ContactManager」と入力してください。これからコピーするコード ブロックは、ContactManager というプロジェクト名が前提となっています。 

1. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** テンプレートを選択します。**認証**が**各ユーザー アカウント*に設定され、**[クラウドでのホスト]** がオンになり、**[Web サイト]** が選択されていることを確認します。

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. 構成ウィザードでは *ContactManager* に基づいて一意の名前が提示されます (次の図を参照してください)。近くのリージョンを選択します。[azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") を使用して、待ち時間が最も短いデータ センターを検索することができます。 
2. 以前にデータベースのサーバーを作成していない場合は、**[新しいサーバーの作成]** を選択してデータベース ユーザー名とパスワードを入力します。

	![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

データベース サーバーを使用している場合、それを使用して新しいデータベースを作成します。データベース サーバーは貴重なリソースであるため、一般的にはデータベースごとにデータベース サーバーを作成するのではなく、同じサーバー上にテスト用や開発用の複数のデータベースを作成してください。Web サイトとデータベースは同じリージョンにしてください。

![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### ページのヘッダーとフッターを設定する


1. **ソリューション エクスプローラー**の *Views\Shared* フォルダーにある *Layout.cshtml* ファイルを開きます。

	![_Layout.cshtml in Solution Explorer][newapp004]

1. *Layout.cshtml* ファイル内のマークアップを次のコードに置き換えます。次のように変更が強調表示されます。

<pre>
			&lt;!DOCTYPE html&gt;
			&lt;html&gt;
			&lt;head&gt;
			    &lt;meta charset="utf-8" /&gt;
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
			    &lt;title&gt;@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title&gt;
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head&gt;
			&lt;body&gt;
			    &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt;
			        &lt;div class="container"&gt;
			            &lt;div class="navbar-header"&gt;
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                &lt;/button&gt;
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div&gt;
			            &lt;div class="navbar-collapse collapse"&gt;
			                &lt;ul class="nav navbar-nav"&gt;
			                    &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt;
			                &lt;/ul&gt;
			                @Html.Partial("_LoginPartial")
			            &lt;/div&gt;
			        &lt;/div&gt;
			    &lt;/div&gt;
			    &lt;div class="container body-content"&gt;
			        @RenderBody()
			        &lt;hr /&gt;
			        &lt;footer&gt;
			            &lt;p&gt;&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p&gt;
			        &lt;/footer&gt;
			    &lt;/div&gt;
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required:false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![code changes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### ローカルでアプリケーションを実行する

1. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

	アプリケーションのホーム ページが既定のブラウザーに表示されます。

	![Web site running locally](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

これで、Azure にデプロイするアプリケーションを作成するために必要な操作が完了しました。 

## プロジェクトに対して SSL を有効にする ##

1. SSL を有効にします。ソリューション エクスプローラーで **ContactManager** プロジェクトをクリックし、F4 キーを押します。プロパティ ダイアログ ボックスが表示されます。**[SSL Enabled]** を True に変更します。**[SSL URL]** をコピーします。以前に SSL Web サイトを作成したことがなければ、SSL URL は https://localhost:44300/ です。

	![enable SSL][rxSSL]
 
1. ソリューション エクスプローラーで **Contact Manager** プロジェクトを右クリックし、**[プロパティ]** をクリックします。
1. 左側のタブで **[Web]** をクリックします。
1. **SSL URL** を使用するように **[プロジェクト URL]** を変更し、ページを保存します (Ctrl + S)。

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. 次の図に示すように、Internet Explorer が Visual Studio によって起動されるブラウザーであることを確認します。

	![default browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	ブラウザー セレクターによって、Visual Studio が起動するブラウザーを指定することができます。

 	![browser selector](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	変更を加える場合、複数のブラウザーを選択して、Visual Studio が各ブラウザーを更新するようにできます。詳細については、「[Using Browser Link in Visual Studio 2013 (Visual Studio 2013 でのブラウザー リンクの使用)](http://www.asp.net/visual-studio/overview/2013/using-browser-link)」を参照してください。


1. Ctrl + F5 キーを押して、アプリケーションを実行します。指示に従って、IIS Express が生成した自己署名証明書を信頼します。

	 ![instructions to trust the self-signed certificate that IIS Express has generated](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. **[セキュリティ警告]** ダイアログを確認して、**localhost** を表す証明書をインストールする場合は **[はい]** をクリックします。

 	![localhost IIS Express certificate warning ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. IE は*ホーム*ページを表示し、SSL の警告はありません。

	 ![IE with localhost SSL and no warnings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome も証明書を受け取り、警告なしで HTTPS コンテンツを表示します。Firefox は独自の証明書ストアを使用します。したがって、警告を表示します。

	 ![FireFox Cert Warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>Azure にアプリケーションを展開する</h2>

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   **[Web の発行]** ウィザードが開きます。

1. **[Web の発行]** ダイアログ ボックスの **[発行]** をクリックします。

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

現在、作成したアプリケーションはクラウドで実行されています。このアプリケーションを次にデプロイするときは、変更したファイル (または新しいファイル) のみがデプロイされます。

	![Running in Cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>アプリケーションにデータベースを追加する</h2>

次に、アプリケーションを更新して、連絡先を表示および更新してデータをデータベースに保存する機能を追加します。アプリケーションは、Entity Framework (EF) を使用してデータベースを作成し、データを読み取り更新します。

### 連絡先のデータ モデル クラスを追加する

まず、コードで単純なデータ モデルを作成します。

1. **ソリューション エクスプローラー**で、Models フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。

	![Add Class in Models folder context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. **[新しい項目の追加]** ダイアログ ボックスで、新しいクラス ファイルに「*Contact.cs*」という名前を付け、**[追加]** をクリックします。

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
**Contacts** クラスでは、各連絡先について保存するデータと、データベースが必要とする主キー *ContactID* を定義します。

### アプリケーション ユーザーが連絡先を操作できる Web ページを作成する

ASP.NET MVC のスキャフォールディング機能によって、作成、読み取り、更新、削除 (CRUD 操作) を実行するコードを自動的に生成できます。

<h2><a name="bkmk_addcontroller"></a>データのコントローラーとビューを追加する</h2>

1. プロジェクトをビルドします **(Ctrl + Shift + B)**。(スキャフォールディング機能の使用前にプロジェクトをビルドする必要があります。) 
1. **ソリューション エクスプローラー**で、Controllers フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。

	![Add Controller in Controllers folder context menu][addcode001]

5. **[スキャフォールディングの追加]** ダイアログ ボックスで、**[MVC 5 コントローラーとビュー、EF を使用]** を選択し、**[追加]** をクリックします。
	
	![Add Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. **[モデル クラス] **ボックスの一覧で **Contact (ContactManager.Models)** を選択します。(下図を参照)。
1. **[データ コンテキスト クラス]** で **ApplicationDbContext (ContactManager.Models)** を選択します。この **ApplicationDbContext** が、メンバーシップ DB と連絡先データの両方に使用されます。
1. **[コントローラー名]** テキスト入力ボックスで、コントローラー名として「CmController」と入力します。 

	![New data ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. **[追加]** をクリックします。

Visual Studio によって、**Contact** オブジェクトの CRUD データベース操作に対応したコントローラー メソッドとビューが作成されます。

## Migrations の有効化、データベースの作成、サンプル データとデータ初期化子の追加 ##

次の作業では、作成したデータ モデルに基づいてデータベースを作成するために、[Code First Migrations](http://msdn.microsoft.com/library/hh770484.aspx) 機能を有効にします。

1. **[ツール]** メニューの **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。
	![Package Manager Console in Tools menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		enable-migrations
	**enable-migrations** コマンドによって *Migrations* フォルダーが作成され、そのフォルダーに *Configuration.cs* ファイルが格納されます。このファイルを編集して、データベースに初期データを投入し、データ移行を構成します。 

2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		add-migration Initial


	**add-migration Initial** コマンドは、データベースを作成する *Migrations* フォルダーに **&lt;date_stamp&gt;Initial** という名前のファイルを生成します。最初のパラメーター ( **Initial** ) は省略可能で、ファイルの名前を作成するときに使用されます。新しいクラス ファイルは**ソリューション エクスプローラー**で表示できます。
	**Initial** クラスでは、**Up** メソッドを使用して Contacts テーブルを作成し、**Down** メソッドを使用してそのテーブルを削除します (前の状態に戻す際に使用します)。
3. *Migrations\Configuration.cs* ファイルを開きます。 
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

	**update-database** によって、データベースを作成する最初の移行が実行されます。既定では、データベースは SQL Server Express LocalDB データベースとして作成されます  

7. Ctrl + F5 キーを押してアプリケーションを実行し、**CM Demo** リンクをクリックします (または、http://localhost:(port#)/Cm に移動します)。

アプリケーションでは、登録されたデータが表示され、編集、詳細、削除のリンクが示されます。データの作成、編集、削除、表示ができます。

	![MVC view of data][rx2]



<h2><a name="addOauth"></a>OAuth2 プロバイダーを追加する</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") は、Web、モバイル、およびデスクトップのアプリケーションからシンプルで標準的な方法で安全に認証するためのオープン プロトコルです。ASP.NET MVC インターネット テンプレートは OAuth を使用して、Facebook、Twitter、Google、Microsoft を認証プロバイダーとしてサポートします。このチュートリアルでは Google のみを認証プロバイダーとして使用しますが、コードを少し変更すれば他のプロバイダーも使用できます。他のプロバイダーを実装する手順は、このチュートリアルで説明する手順とほとんど同じです。Facebook を認証プロバイダーとして使用する方法については、[Facebook、Twitter、LinkedIn 、および Google の OAuth2/OpenID サインオンを使用した MVC 5 アプリケーションの作成](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)について執筆した拙著のチュートリアルを参照してください。

このチュートリアルでは、認証の他にロールを使用して権限を付与します。*canEdit* ロールに追加したユーザーのみがデータを変更 (連絡先を作成、編集、削除) できます。

[Facebook、Twitter、LinkedIn、および Google の OAuth2 サインオンを使用した MVC 5 アプリケーションの作成](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog)について執筆した拙著のチュートリアルの **OAuth 2 用の Google アプリを作成して OAuth2 用に Google アプリをセットアップする**手順に従います。Google 認証を使用してログオンできることをアプリケーションを実行してテストします。

<h2><a name="mbrDB"></a>メンバーシップ API を使用する</h2>
このセクションでは、ローカル ユーザーと *canEdit* ロールをメンバーシップ データベースに追加します。*canEdit* ロールのユーザーのみがデータを編集することができます。実行可能な操作に基づいてロール名を付けるのが望ましいので、この場合は *admin* より *canEdit* が適しています。その後、必要に応じて、(わかりにくい *superAdmin* ではなく) *canDeleteMembers* などの新しいロールを追加します。

1. *Migrations\configuration.cs* ファイルを開き、次の `using` ステートメントを追加します。

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
<span></span>
	The following images shows the changes to *Seed* method:

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   This code creates a new role called *canEdit*, creates a new local user *user1@contoso.com*, and adds *user1@contoso.com* to the *canEdit* role. For more information, see the [ASP.NET Identity resource page](http://curah.microsoft.com/55636/aspnet-identity).

## Use Temporary Code to Add New Social Login Users to the canEdit Role  ##
In this section you will temporarily modify the **ExternalLoginConfirmation** method in the Account controller to add new users registering with an OAuth provider to the *canEdit* role. We will temporarily modify the **ExternalLoginConfirmation** method to automatically add new users to an administrative role. Until we provide a tool to add and manage roles, we'll use the temporary automatic registration code below. We hope to provide a tool similar to [WSAT](http://msdn.microsoft.com/ja-jp/library/ms228053.aspx) in the future which allow you to create and edit user accounts and roles. Later in the tutorial I'll show how you can use **Server Explorer** to add users to roles.  

1. Open the **Controllers\AccountController.cs** file and navigate to the **ExternalLoginConfirmation** method.
1. Add the following call to **AddToRoleAsync** just before the **SignInAsync** call.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   The code above adds the newly registered user to the "canEdit" role, which gives them access to action methods that change (edit) data. An image of the code change is shown below:

   ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Later in the tutorial you will deploy the application to Azure, where you will log-on with Google or another third party authentication provider. This will add your newly registered account to the *canEdit* role. Anyone who finds your site's URL and has a Google ID can then register and update your database. To prevent other people from doing that, you can stop the site. You'll be able to verify who is in the *canEdit* role by examining the database.

In the **Package Manager Console** hit the up arrow key to bring up the following command:

		Update-Database

Run the  **Update-Database** command which will run the **Seed** method, and that will run the **AddUserAndRole** you just added. The **AddUserAndRole** will create the user *user1@contoso.com* and add her to the *canEdit* role.

## Protect the Application with SSL and the Authorize Attribute ##

In this section you will apply the [Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute.aspx) attribute to restrict access to the action methods. Anonymous users will be able to view the **Index** action method of the home controller only. Registered users will be able to see contact data (The **Index** and **Details** pages of the Cm controller), the About, and the Contact pages. Only users in the *canEdit* role will be able to access action methods that change data.

1. Add the [Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute.aspx) filter and the [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute.aspx) filter to the application. An alternative approach is to add the [Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute.aspx) attribute and the [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute.aspx) attribute to each controller, but it's considered a security best practice to apply them to the entire application. By adding them globally, every new controller and action method you add will automatically be protected, you won't need to remember to apply them. For more information see [Securing your ASP.NET MVC  App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Open the *App_Start\FilterConfig.cs* file and replace the *RegisterGlobalFilters* method with the following (which adds the two filters):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>


	The following image shows the changed code:


	The [Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute.aspx) filter applied in the code above will prevent anonymous users from accessing any methods in the application. You will use the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to opt out of the authorization requirement in a couple methods, so anonymous users can log in and can view the home page. The  [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute.aspx) will require all access to the web app be through HTTPS.

1. Add the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to the **Index** method of the Home controller. The [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute enables you to white-list the methods you want to opt out of authorization. An image of a portion of the HomeController is shown below:	

  	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Do a global search for *AllowAnonymous*, you can see it is used in the log in and registration methods of the Account controller.
1. In *CmController.cs*, add `[Authorize(Roles = "canEdit")]` to the HttpGet and HttpPost methods that change data (Create, Edit, Delete, every action method except Index and Details) in the *Cm* controller. A portion of the completed code is shown below: 

   	![img of code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. If you are still logged in from a previous session, hit the **Log out** link.
1. Click on the **About** or **Contact** links. You will be redirected to the log in page because anonymous users cannot view those pages. 
1. Click the **Register as a new user** link and add a local user with email *joe@contoso.com*. Verify *Joe* can view the Home, About and Contact pages. 

	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Click the *CM Demo* link and verify you see the data. 
1. Click an edit link on the page, you will be redirected to the log in page (because a new local user is not added to the *canEdit* role).
1. Log in as *user1@contoso.com* with password of "P_assw0rd1" (the "0" in "word" is a zero). You will be redirected to the edit page you previously selected. <br/>
   If you can't log in with that account and password, try copying the password from the source code and pasting it. If you still can't log in, check the **UserName** column of the **AspNetUsers** table to verify *user1@contoso.com* was added. 

1. Verify you can make data changes.

<h2><a name="bkmk_deploytowindowsazure11"></a>Azure にアプリケーションを展開する</h2>

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![Publish in project context menu][firsdeploy003]

	**Web の発行**ウィザードが開きます。

1. **[Web の発行]** ダイアログ ボックスの左側の **[設定]** タブをクリックします。**[v]** アイコンをクリックして **ApplicationDbContext** の **[リモート接続文字列]** を選択し、**[ContactManagerNN_dbB]** を選択します。

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. **ContactManagerContext** で、**[Code First Migrations を実行する]** を選択します。

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. **[発行]** をクリックします。
1. *user1@contoso.com* (パスワード "P_assw0rd1") としてログインし、データを編集できることを確認します。
1. ログアウトします。
1. [Google Developers Console](https://console.developers.google.com/) に移動し、**[資格情報]** タブでリダイレクト URIS および JavaScript Orgins を更新して Azure の URL を使用します。
1. Google または Facebook を使用してログインします。これにより、Google または Facebook のアカウントが **canEdit** ロールに追加されます。HTTP 400 エラーが発生し、*要求のリダイレクト URI: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI.*, you'll have to wait until the changes you made are propagated. If you get this error after more than a minute, verify the URIs are correct.

### Web サイトを停止して第三者の登録を防ぐ  

1. **サーバー エクスプローラー**で **[Web サイト]** に移動します。
4. 各 Web サイトのインスタンスを右クリックし、**[Web サイトの停止]** を選択します。 

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	Azure の管理ポータルから Web サイトを選択し、ページ下部の **[停止]** アイコンをクリックする方法もあります。

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### AddToRoleAsync の削除、発行、テスト

1. Account コントローラーの **ExternalLoginConfirmation** メソッドから次のコードをコメント アウト、または削除します。 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. プロジェクトをビルドします。これにより、ファイルの変更が保存され、コンパイル エラーがないか確認が行われます。
5. **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[発行]** をクリックします。

	   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. **[プレビューの開始]** をクリックします。更新する必要のあるファイルだけがデプロイされます。
5. Visual Studio またはポータルから Web サイトを開きます。**Web サイトが停止した状態で発行することはできません**。

	![start web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Visual Studio に戻り、**[発行]** をクリックします。
3. Azure アプリケーションが既定のブラウザーで起動します。ログインして、ログアウトすると、匿名ユーザーとしてホーム ページを表示できます。  
4. **[About]** リンクをクリックします。ログイン ページにリダイレクトされます。
5. ログイン ページの **[Register]** リンクをクリックし、ローカル アカウントを作成します。読み取り専用ページにはアクセスできるが、データの変更を伴うページにはアクセスできない (*canEdit* ロールによって保護されている) ことをこのローカル アカウントを使用して確認します。ローカル アカウント アクセスは、後でこのチュートリアルの中で削除します。 

	![Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. *About* ページと *Contact* ページに移動できることを確認します。

	![Log off](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. **[CM Demo]** リンクをクリックして **Cm** コントローラーに移動します。URL に *Cm* を付加することによって移動してもかまいません。 

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. [編集] リンクをクリックします。ログイン ページにリダイレクトされます。**[Use another service to log in]** で [Google] または [Facebook] をクリックし、過去に登録したアカウントでログインします (短時間で作業して、セッション Cookie がタイムアウトしていない場合は、以前に使用した Google アカウントまたは Facebook アカウントで自動的にログインされます)。
2. そのアカウントにログインした状態でデータを編集できることを確認します。
 	**注: **このアプリケーションで Google からログアウトし、同じブラウザーで別の Google アカウントにログインすることはできません。1 つのブラウザーを使用している場合は、Google に移動してログアウトする必要があります。同じサード パーティの認証システム (Google など) に属している別のアカウントでログオンするためには、異なるブラウザーを使用する必要があります。

Google アカウント情報の氏名を入力しないと、NullReferenceException が発生します。


## SQL Azure DB を調べる##

1. **サーバー エクスプローラー**で **[ContactDB]** に移動します。
2. **[ContactDB]** を右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** を選択します。
 
	![open in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**注: ****[SQL Databases]** を展開できず、**ContactDB** を Visual Studio から表示*できない*場合は、以下の手順に従い、特定のファイアウォール ポート (またはポート範囲) を開放する必要があります。**[Azure ファイアウォール ルールを設定する]** の下の手順に従います。ファイアウォール ルールの追加後、データベースに接続できるようになるまでに数分かかる場合があります。
 
1. **AspNetUsers** テーブルを右クリックし、**[データの表示]** を選択します。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. **canEdit** ロールへの追加用として登録した Google アカウントの ID と *user1@contoso.com* の ID をメモします。それ以外のユーザーは **canEdit** ロールに含めないようにする必要があります。(この点については、次のステップで確認します。)

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. **SQL Server オブジェクト エクスプローラー**で **[AspNetUserRoles]** を右クリックし、**[データの表示]** を選択します。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
**UserId** が、登録した Google アカウントおよび *user1@contoso.com* のものであることを確認します。 


## Azure のファイアウォール ルールを設定する##

Visual Studio から SQL Azure に接続できない、またはエラー メッセージ "サーバーを開けません" が表示される場合は、このセクションの指示に従います。

![firewall error](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

使用できる IP アドレスに、目的の IP アドレスを追加する必要があります。

1. Azure ポータルの左側のタブで **[SQL データベース]** を選択します。

	![Select SQL][rx6]

1. **[ContactDB]** をクリックします。

1. **[この IP アドレス用に Azure ファイアウォール ルールを設定する]** リンクをクリックします。

	![firewall rules][rx7]

1. "現在の IP アドレス xxx.xxx.xxx.xxx は既存のファイアウォール ルールに含まれていません。ファイアウォール ルールを更新しますか?" というメッセージが表示されたら、**[はい]** をクリックします。企業ファイアウォールによっては、このアドレスだけでは不十分な場合もあります。そのような場合は、IP アドレスの範囲を追加する必要があります。

次に、使用できる一連の IP アドレスを追加します。

1. Azure ポータルで **[SQL データベース]** をクリックします。
1. **[サーバー]** タブを選択し、構成するサーバーをクリックします。

	![Servers tab in Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. **[構成]** タブをクリックします。

1. ルール名、開始 IP アドレス、終了 IP アドレスを追加します。

	![ip range][rx9]

1. ページの下部にある **[保存]** をクリックします。
1. ご意見やご感想、また接続先となる一連の IP アドレスを追加する必要がある場合は、お知らせください。

最後に、SQL Database インスタンスに SQL Server オブジェクト エクスプローラー (SSOX) から接続します。

1. [表示] メニューの **[SQL Server オブジェクト エクスプローラー]** をクリックします。
1. **[SQL Server]** を右クリックし、**[SQL Server の追加]** を選択します。
1. **[サーバーへの接続]** ダイアログ ボックスで、**[認証]** を **[SQL Server 認証]** に設定します。**[サーバー名]** と **[ログイン]** は Azure ポータルから取得されます。
1. ブラウザーでポータルに移動し、**[SQL Database]** を選択します。
1. **[ContactDB]** を選択し、**[SQL Database 接続文字列を表示する]** をクリックします。
1. **[接続文字列]** ページから **[サーバー]** と **[ユーザー ID]** をコピーします。
 
	![con string](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. **[サーバー]** と **[ユーザー ID]** の値を Visual Studio の **[サーバーへの接続]** ダイアログに貼り付けます。**[ユーザー ID]** の値が **[ログイン]** エントリに入ります。SQL DB の作成時に使用したパスワードを入力します。

	![Connect to Server DLG](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

これで、先ほど説明した手順に従い、Contact データベースに移動できます。


## データベース テーブルを編集して canEdit ロールにユーザーを追加するには

先ほどは、コードを使用してユーザーを canEdit ロールに追加しました。それ以外にも、メンバーシップ テーブル内のデータを直接操作する方法もあります。以下の手順では、その方法を使用してロールにユーザーを追加します。

2. **SQL Server オブジェクト エクスプローラー**で **[AspNetUserRoles]** を右クリックし、**[データの表示]** を選択します。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. *RoleId* をコピーして空の (新しい) 行に貼り付けます。
	
	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. **AspNetUsers** テーブルから、ロールに追加するユーザーを探してそのユーザーの *Id* をコピーし、**AspNetUserRoles** テーブルの **[UserId]** 列に貼り付けます。

もっと簡単にユーザーとロールを管理できるように現在ツールを作成しています。


<h2><a name="nextsteps"></a>次のステップ</h2>

このサンプルについて執筆したチュートリアルをご覧ください。

1.	[Create a secure ASP.NET MVC 5 web app with log in, email confirmation and password reset (ログイン、電子メールの確認、およびパスワードのリセットでセキュリティで保護された ASP.NET MVC 5 web アプリを作成する)](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[SMS と 2 要素認証を電子メールでの ASP.NET MVC 5 アプリケーション](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Best practices for deploying passwords and other sensitive data to ASP.NET and Azure (ASP.NET および Azure へパスワードや機密データをデプロイするためのベスト プラクティス)](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
4.	[Create an ASP.NET MVC 5 App with Facebook and Google OAuth2 (Facebook や Google の OAuth2 を使用した ASP.NET MVC 5 アプリケーションの作成)](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on )。このチュートリアルでは、ユーザー登録データベースにプロファイル データを追加する方法や、Facebook を認証プロバイダーとして使用する詳細な手順について説明しています。
5.	[Getting Started with ASP.NET MVC 5 (ASP.NET MVC 5 の使用)](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

このチュートリアルの上部に表示されるソーシャル ログイン ボタンを有効にするには、「[Pretty social login buttons for ASP.NET MVC 5 (ASP.NET MVC 5 用ソーシャル ログイン ボタン)](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/)」を参照してください。

MVC および Entity Framework のプログラミングについては、Tom Dykstra が「[Getting Started with EF and MVC (Entity Framework と MVC の使用)](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)」で、さらに詳しく解説しています。

このチュートリアルとサンプル アプリケーションは、Tom Dykstra と Barry Dorrans (Twitter [@blowdart](http://blogs.msdn.com/b/rickandy/)) の協力の下、[Rick Anderson](https://twitter.com/RickAndMSFT) (Twitter [@RickAndMSFT](https://twitter.com/blowdart)) が執筆しました。 

**役に立った内容や改善点など、*皆様からのご意見をお寄せください***。このチュートリアルに関してだけでなく、ここで紹介した製品に関するご意見やご要望もお待ちしております。お寄せいただいたご意見は、今後の改善に役立たせていただきます。新しいトピックについては、「[Show Me How With Code (コードの使用方法)](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code)」で要求および投票することもできます。

<!-- bookmarks -->
[OAuth プロバイダーを追加する]: #addOauth
[メンバーシップ API を使用する]:#mbrDB
[データ展開スクリプトを作成する]:#ppd
[メンバーシップ データベースを更新する]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

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

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[Azure の Web サイトでの ASP.NET に関する重要な情報]: #aspnetwindowsazureinfo
[次のステップ]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
























