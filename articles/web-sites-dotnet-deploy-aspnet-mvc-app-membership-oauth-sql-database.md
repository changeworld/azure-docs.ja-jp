<properties linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Website with SQL Database" pageTitle="Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Website" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Learn how to develop an ASP.NET MVC 5 website with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande" />

# メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションを Azure の Web サイトにデプロイする

***更新日: 2014 年 4 月 2 日。***

このチュートリアルでは、ユーザーが Facebook や Google の資格情報を使用してログインできる、安全な ASP.NET MVC 5 Web アプリケーションを構築する方法を説明します。さらに、作成したアプリケーションを Azure に展開する方法についても学習します。

Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2013 を持っていない場合は、SDK によって Visual Studio 2013 for Web Express が自動的にインストールされます。これで、Azure 向けのアプリケーションを無料で開発できます。Visual Studio 2012 を使用する場合は、[前のチュートリアル][前のチュートリアル]を参照してください。このバージョンのチュートリアルは、前のバージョンと比べてはるかに単純です。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルでは、安全なデータ主導型 Web アプリケーションを作成し、クラウド データベースを使用してクラウド上で実行します。

学習内容:

-   安全な ASP.NET MVC 5 プロジェクトを作成して Azure の Web サイトに発行する方法
-   [OAuth][OAuth]、[OpenID][OpenID]、および ASP.NET メンバーシップ データベースを使用してアプリケーションを保護する方法
-   新しいメンバーシップ API を使用してユーザーおよびロールを追加する方法
-   SQL データベースを使用して Azure にデータを保存する方法

ASP.NET MVC 5 に基づく、データベース アクセスに ADO.NET Entity Framework を使用する、簡単な連絡先リスト Web アプリケーションをビルドします。次の図は、完成したアプリケーションのログイン ページです。

![login page][login page]

> [WACOM.NOTE] このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、[MSDN サブスクライバーの特典を有効にする][MSDN サブスクライバーの特典を有効にする]か、[無料評価版にサインアップ][無料評価版にサインアップ]してください。

このチュートリアルの内容:

-   [開発環境を設定する][開発環境を設定する]
-   [Azure 環境を設定する][Azure 環境を設定する]
-   [ASP.NET MVC 5 アプリケーションを作成する][ASP.NET MVC 5 アプリケーションを作成する]
-   [Azure にアプリケーションを展開する][Azure にアプリケーションを展開する]
-   [アプリケーションにデータベースを追加する][アプリケーションにデータベースを追加する]
-   [OAuth プロバイダーを追加する][OAuth プロバイダーを追加する]
-   [メンバーシップ API を使用する][メンバーシップ API を使用する]
-   [Azure にアプリケーションを展開する][1]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

localhost に新しい SSL 証明書を使用するには、[Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC] 以上をインストールする必要があります。

## <a name="bkmk_setupwindowsazure"></a>Azure 環境を設定する

次のステップでは、Azure の Web サイトと SQL データベースを作成することで Azure 環境をセットアップします。

### Azure で Web サイトと SQL データベースを作成する

Azure の Web サイトは、共有ホスティング環境で実行されます。つまり、他の Azure クライアントと共有する仮想マシン (VM) 上で実行されます。共有ホスティング環境は、低コストでクラウドの利用を開始できる方法です。後で Web トラフィックが増加したら、アプリケーションの規模を変更して専用 VM 上で実行するように設定してニーズを満たすことができます。もっと複雑なアーキテクチャが必要な場合は、Azure のクラウド サービスに移行できます。クラウド サービスは専用 VM 上で実行され、ユーザーのニーズに応じて構成できます。

Azure SQL データベースは、SQL Server テクノロジに基づいて構築されたクラウドベースのリレーショナル データベース サービスです。SQL Server で動作するツールおよびアプリケーションは、SQL データベースでも動作します。

1.  [Azure の管理ポータル][Azure の管理ポータル]で、左側のタブにある **[Web サイト]** をクリックし、**[新規]** をクリックします。

    ![New button in Management Portal][New button in Management Portal]

2.  **[Web サイト]** をクリックし、**[カスタム作成]** をクリックします。

    ![Create with Database link in Management Portal][Create with Database link in Management Portal]

    **新しい Web サイト - カスタム作成**ウィザードが開きます。

3.  ウィザードの **[Web サイトの作成]** 手順で、アプリケーションの一意の URL として使用する文字列を **[URL]** ボックスに入力します。ここに入力した文字列と、このテキスト ボックスの右側に表示されている文字列を組み合わせたものが実際の URL になります。図には URL が表示されますが、その URL は既に取得されている可能性が高いため、別の URL の選択が必要になります。

    ![Create with Database link in Management Portal][2]

4.  **[データベース]** ボックスの一覧の **[無料の SQL Database の作成]** を選択します。

5.  **[リージョン]** ボックスの一覧で、Web サイトに選択したリージョンと同じリージョンを選択します。
    この設定によって、使用する VM が実行されるデータ センターが指定されます。
6.  **[DB 接続文字列名]** ボックスは、既定値の *DefaultConnection* をそのまま使用します。
7.  ボックスの下部にある右矢印をクリックします。
    ウィザードの **[データベースの設定の指定]** 手順に進みます。

8.  **[名前]** ボックスに「*ContactDB*」と入力します。(下図を参照)。
9.  **[サーバー]** ボックスで、**[新しい SQL データベース サーバー]** を選択します。(下図を参照)。または、以前に SQL Server データベースを作成した場合は、ボックスの一覧からその SQL Server を選択できます。
10. **[リージョン]** は、作成した Web サイトと同じエリアに設定します。
11. 管理者の**ログイン名**と**パスワード**を入力します。**[新しい SQL データベース サーバー]** を選択した場合は、既存の名前とパスワードではなく、このデータベースへのアクセス時に使用する新しい名前とパスワードを入力してください。以前に作成した SQL Server を選択した場合は、その SQL Server の作成時に設定したパスワードを入力します。このチュートリアルでは、**[データベースの詳細設定を構成します]** チェック ボックスをオンにしません。無料の DB の場合、設定できるのは照合順序のみです。
12. 終了したら、ダイアログ ボックスの右下にあるチェック マークをクリックします。

    ![Database Settings step of New Website - Create with Database wizard][Database Settings step of New Website - Create with Database wizard]

    次の画像では、既存の SQL Server を選択した場合のログインを示しています。

    ![Database Settings step of New Website - Create with Database wizard][3]

    管理ポータルが [Web サイト] ページに戻り、**[状態]** 列にサイトが作成中であることが示されます。しばらくすると (通常は 1 分未満)、サイトの作成に成功したことが **[状態]** 列に示されます。左側にあるナビゲーション バーでは、アカウントで所有するサイト数が **[Web サイト]** アイコンの横に表示され、データベース数が **[SQL データベース]** アイコンの横に表示されます。

## <a name="bkmk_createmvc4app"></a>ASP.NET MVC 5 アプリケーションを作成する

Azure の Web サイトを作成しましたが、まだその中にコンテンツがありません。次のステップでは、Azure に発行する Visual Studio Web アプリケーションを作成します。

### プロジェクトを作成する

1.  **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。

 ![New Project in File menu][New Project in File menu]

1.  **[新しいプロジェクト]** ダイアログ ボックスで、**[インストールされているテンプレート]** の下にある **[Visual C\#]** を展開して **[Web]** を選択し、**[ASP.NET Web アプリケーション]** を選択します。

2.  アプリケーションに「**ContactManager**」という名前を付けて、**[OK]** をクリックします。

 ![New Project dialog box][New Project dialog box]

 **注:** この画像では、名前が "MyExample" となっていますが、必ず「ContactManager」と入力してください。これからコピーするコード ブロックは、ContactManager というプロジェクト名が前提となっています。

4.  **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**MVC** テンプレートを選択し、**[リモート リソースの作成]** チェック ボックスを**オフ**にして、**[OK]** をクリックします (チェック ボックスは **"リモート リソースの作成"**ではなく **"クラウドでのホスト"** というラベルが付いている可能性があります。)

 ![New ASP.NET Project dialog box][New ASP.NET Project dialog box]

### ページのヘッダーとフッターを設定する

1.  **ソリューション エクスプローラー**の *Views\\Shared* フォルダーにある *Layout.cshtml* ファイルを開きます。

    ![\_Layout.cshtml in Solution Explorer][newapp004]

2.  "My ASP.NET MVC Application" となっている箇所 (2 か所) を「Contact Manager」に書き換えます。
3.  "Application name" を「CM Demo」に書き換えます。

4.  *Cm* コントローラーを使用するため、1 つ目のアクション リンクを更新します。*Home* を *Cm* に書き換えてください。

    ![code changes][code changes]

### ローカルでアプリケーションを実行する

1.  Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

    アプリケーションのホーム ページが既定のブラウザーに表示されます。

    ![Web site running locally][Web site running locally]

これで、Azure に展開するアプリケーションを作成するために必要な操作が完了しました。データベース機能は後で追加します。

## <a name="bkmk_deploytowindowsazure1"></a> アプリケーションの Azure への展開

1.  Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

 ![Publish in project context menu][Publish in project context menu]

 **Web の発行**ウィザードが開きます。

2.  **Web の発行**ウィザードの **[プロファイル]** タブで、**[Azure の Web サイト]** をクリックします。

 ![Import publish settings][Import publish settings]

3.  **[サインイン]** をクリックして、Azure ポータルにログインします。

 ![sign in][sign in]

 ログインすると、**[既存の Web Site の選択]** ダイアログ ボックスが表示されます。

4.  このチュートリアルのパート 1 で作成した Web サイトを選択して、**[OK]** をクリックします。

 ![select web site][select web site]

5.  **[Web の発行]** ダイアログ ボックスの **[発行]** をクリックします。

    ![Publish][Publish]

    これで、作成したアプリケーションはクラウドで実行されています。このアプリケーションを次に展開するときは、変更したファイル (または新しいファイル) のみが展開されます。

    ![Running in Cloud][Running in Cloud]

## <a name="bkmk_addadatabase"></a>アプリケーションにデータベースを追加する

次に、MVC アプリケーションを更新して、連絡先を表示および更新してデータをデータベースに保存する機能を追加します。アプリケーションでは、データベースの作成およびデータベース内のデータの読み取りと更新に Entity Framework を使用します。

### 連絡先のデータ モデル クラスを追加する

まず、コードで単純なデータ モデルを作成します。

1.  **ソリューション エクスプローラー**で、Models フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。

    ![Add Class in Models folder context menu][Add Class in Models folder context menu]

2.  **[新しい項目の追加]** ダイアログ ボックスで、新しいクラス ファイルに「*Contact.cs*」という名前を付け、[**追加**] をクリックします。

    ![Add New Item dialog box][Add New Item dialog box]

3.  Contacts.cs ファイルの内容を次のコードに置き換えます。

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

## <a name="bkmk_addcontroller"></a>データのコントローラーとビューを追加する

1.  プロジェクトをビルドします **(Ctrl + Shift + B)**。(スキャフォールディング機能の使用前にプロジェクトをビルドする必要があります。)
2.  **ソリューション エクスプローラー**で、Controllers フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。

    ![Add Controller in Controllers folder context menu][Add Controller in Controllers folder context menu]

3.  **[スキャフォールディングの追加]** ダイアログ ボックスで、**[MVC 5 コントローラーとビュー、EF を使用]** を選択し、**[追加]** をクリックします。

    ![Add Scaffold dlg][Add Scaffold dlg]

4.  **[モデル クラス]** ボックスで **Contact (ContactManager.Models)** を選択します。(下図を参照)。
5.  **[データ コンテキスト クラス]** で **ApplicationDbContext (ContactManager.Models)** を選択します。この **ApplicationDbContext** が、メンバーシップ DB と連絡先データの両方に使用されます。
6.  **[コントローラー名]** テキスト入力ボックスで、コントローラー名として「CmController」と入力します。

    ![New data ctx dlg][New data ctx dlg]

7.  **[追加]** をクリックします。

Visual Studio によって、**Contact** オブジェクトの CRUD データベース操作に対応したコントローラー メソッドとビューが作成されます。

## Migrations の有効化、データベースの作成、サンプル データとデータ初期化子の追加

次の作業では、作成したデータ モデルに基づいてデータベースを作成するために、[Code First Migrations][Code First Migrations] 機能を有効にします。

1.  **[ツール]** メニューで、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。
    ![Package Manager Console in Tools menu][Package Manager Console in Tools menu]

2.  **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

        enable-migrations

    **enable-migrations** コマンドによって *Migrations* フォルダーが作成され、そのフォルダーに *Configuration.cs* ファイルが保存されます。このファイルを編集して、データベースの初期データ投入を行い、Migration を構成できます。

3.  **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

        add-migration Initial

    **add-migration Initial** コマンドは、データベースを作成する **\<date\_stamp\>Initial** ファイルを *Migrations* フォルダーに生成します。最初のパラメーター ( **Initial** ) は省略可能で、ファイルの名前を作成するときに使用されます。新しいクラス ファイルは**ソリューション エクスプローラー**で表示できます。
    **Initial** クラスでは、**Up** メソッドを使用して Contacts テーブルを作成し、**Down** メソッドを使用してそのテーブルを削除します (前の状態に戻します)。

4.  *Migrations\\Configuration.cs* ファイルを開きます。
5.  次の名前空間を追加します。

         using ContactManager.Models;

6.  *Seed* メソッドを次のコードに置き換えます。

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

7.  **[パッケージ マネージャー コンソール]** で、次のコマンドを入力します。

        update-database

    ![Package Manager Console commands][Package Manager Console commands]

    **update-database** によって、データベースを作成する最初の Migration が実行されます。既定では、データベースは SQL Server Express LocalDB データベースとして作成されます

8.  Ctrl キーを押しながら F5 キーを押してアプリケーションを実行し、**CM Demo** リンクをクリックします (または、http://localhost:(port\#)/Cm に移動します)。

    アプリケーションでは、登録されたデータが表示され、編集、詳細、削除のリンクが示されます。データの作成、編集、削除、表示を行うことができます。

    ![MVC view of data][MVC view of data]

## <a name="addOauth"></a><span class="short-header">OAuth</span>OAuth2 および OpenID プロバイダーを追加する

[OAuth][OAuth] は、Web、モバイル、およびデスクトップのアプリケーションからシンプルで標準的な方法で安全に認証するためのオープン プロトコルです。ASP.NET MVC インターネット テンプレートは OAuth および [OpenID][OpenID] を使用して、Facebook、Twitter、Google、Microsoft を認証プロバイダーとしてサポートします。このチュートリアルでは Google のみを認証プロバイダーとして使用しますが、コードを少し変更すれば他のプロバイダーも使用できます。他のプロバイダーを実装する手順は、このチュートリアルで説明する手順とほとんど同じです。Facebook を認証プロバイダーとして使用する方法については、Facebook や Google の OAuth2/OpenID サインオンを使用した ASP.NET MVC 5 アプリケーションの作成について執筆した[拙著のチュートリアル][拙著のチュートリアル]を参照してください。

このチュートリアルでは、認証の他にロールを使用して権限を付与します。*canEdit* ロールに追加したユーザーのみがデータを変更 (連絡先を作成、編集、削除) できます。

1.  [Facebook や Google の OAuth2/OpenID サインオンを使用した ASP.NET MVC 5 アプリケーションの作成について執筆した拙著のチュートリアル][拙著のチュートリアル]の **OAuth 2 用の Google アプリを作成して OAuth2 用に Google アプリをセットアップする**手順に従います。
2.  *App\_Start\\Startup.Auth.cs* ファイルを開きます。*app.UseGoogleAuthentication()* メソッドからコメント文字を削除し、**clientId** と **clientSecret** を入力します。

<!-- -->

    public void ConfigureAuth(IAppBuilder app)
             {
    // Enable the application to use a cookie to store information for the signed in user
    app.UseCookieAuthentication(new CookieAuthenticationOptions
                {
    AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
    LoginPath = new PathString("/Account/Login")
                });
    // Use a cookie to temporarily store information about a user logging in with a third party login provider
    app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);

    app.UseGoogleAuthentication(
    clientId:"000-000.apps.googleusercontent.com",
    clientSecret: "00000000000");
             } 

1.  アプリケーションを実行し、**[ログイン]** リンクをクリックします。
2.  **[Use another service to log in]** の **[Google]** をクリックします。

    ![Goog login][Goog login]

3.  資格情報を入力します。
4.  Google の認証のサーバーはアプリケーションへのアクセス許可を要求して、電子メール アドレスとアカウントに関する基本情報を表示します。**[同意する]** をクリックします。
    ![GOOG asking for permission][GOOG asking for permission]
5.  登録ページにリダイレクトされます。ユーザー名は既定では登録するために使用した電子メール エイリアスで、必要に応じて変更できます。**[登録]** をクリックします。

    ![register][register]

## <a name="mbrDB"></a><span class="short-header">メンバーシップ DB</span>メンバーシップ API を使用する

このセクションでは、ローカル ユーザーと *canEdit* ロールをメンバーシップ データベースに追加します。*canEdit* ロールのユーザーのみがデータを編集することができます。実行可能な操作に基づいてロール名を付けるのが望ましいので、この場合は *admin* より *canEdit* が適しています。その後、必要に応じて、(わかりにくい *superAdmin* ではなく) *canDeleteMembers* などの新しいロールを追加します。

1.  *migrations\\configuration.cs* ファイルを開いて、次の `using` ステートメントを追加します。

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

2.  次の **AddUserAndRole** メソッドをクラスに追加します。

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

3.  新しいメソッドを **Seed** メソッドから呼び出します。
	<pre>
         protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            
context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }
	</pre>  
<span></span>
    次のイメージは *Seed* メソッドへの変更を示します。

    ![code image][code image]

このコードでは、*canEdit* という名前の新しいロールを作成し、新しいローカル ユーザー <*user1@contoso.com*> を作成して、<*user1@contoso.com>\* を *canEdit* ロールに追加します。詳細については、「[ASP.NET Identity resource page (ASP.NET Identity リソース ページ)](http://curah.microsoft.com/55636/aspnet-identity)」を参照してください。

## 一時的なコードを使用して新しいソーシャル ログイン ユーザーを canEdit ロールに追加する

このセクションでは、Account コントローラーの **ExternalLoginConfirmation** メソッドを一時的に変更して、*canEdit* ロールに新しいユーザーを追加し、OAuth または OpenID プロバイダーに登録します。ここで一時的に **ExternalLoginConfirmation** メソッドを変更し、新しいユーザーが管理者ロールに自動的に追加されるようにします。ロールの追加と管理に必要なツールの準備が整うまでの間、以下に示した一時的な自動登録コードを使用することになります。将来的には、ユーザー アカウントとロールの作成および編集のために、[WSAT][WSAT] と同様のツールを提供する予定です。**サーバー エクスプローラー**を使用してユーザーをロールに追加する方法については、このチュートリアルの中で紹介します。

1.  **Controllers\\AccountController.cs** ファイルを開いて、**ExternalLoginConfirmation** メソッドに移動します。
2.  次の **AddToRoleAsync** への呼び出しを **SignInAsync** の呼び出しの直前に追加します。

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   このコードは、新しく登録したユーザーを "canEdit" ロールに追加します。これにより、データの変更 (編集) を伴うアクション メソッドへのアクセス権がユーザーに割り当てられます。以下の図は、変更後のコードを示しています。

   ![code][code]

このチュートリアルの中で、アプリケーションを Azure に展開します。そこでのログオンには、Google などサード パーティの認証プロバイダーが使用されます。新しく登録されたアカウントは、*canEdit* ロールに追加されます。サイトの URL と Google ID さえあればだれでも登録し、データベースを更新することができます。そのような操作が第三者によって行われるのを防ぐためには、サイトを停止する必要があります。だれが *canEdit* ロールに追加されているかは、データベースを調べることによって確認できます。

**[パッケージ マネージャー コンソール]** で上方向キーを押し、次のコマンドを呼び出します。

        Update-Database

**Update-Database** コマンドを実行すると、**Seed** メソッドが実行され、先ほど追加した **AddUserAndRole** が実行されます。**AddUserAndRole** は <*user1@contoso.com>\* というユーザーを作成し、*canEdit* ロールに追加します。

## SSL と Authorize 属性を使用してアプリケーションを保護する

このセクションでは、[Authorize][Authorize]) 属性を適用してアクション メソッドへのアクセスを制限します。匿名ユーザーが表示できるのは、home コントローラーの **Index** アクション メソッドだけになります。登録ユーザーは、連絡先データ (Cm コントローラーの **[Index]** ページと **[Details]** ページ)、[About] ページ、[Contact] ページを表示することができます。*canEdit* ロールを与えられているユーザーのみがアクション メソッドを実行してデータを変更できます。

1.  [Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute.aspx) フィルターと [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute.aspx) フィルターをアプリケーションに追加します。[Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute.aspx) 属性と [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute.aspx) 属性をコントローラーごとに追加する方法もありますが、セキュリティ上の理由から、通常はこれらをアプリケーション全体に適用します。アプリケーション全体に適用すれば、新しいコントローラーやアクション メソッドを追加したとき、それらが自動的に保護されます。ユーザー自身で適用する必要がありません。詳細については、「[Securing your ASP.NET MVC App and the new AllowAnonymous Attribute (ASP.NET MVC 4 アプリケーションの保護と新しい AllowAnonymous 属性)](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)」を参照してください。*App\_Start\\FilterConfig.cs* ファイルを開き、*RegisterGlobalFilters* メソッドを次のように書き換えます (2 つのフィルターを追加)。
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>
<span></span>

    次のイメージは変更されたコードを示します。

    ![code image][4]

    このコードで適用した [Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute.aspx) フィルターによって、匿名ユーザーは、アプリケーション内のメソッドに一切アクセスできなくなります。2 つのメソッドについては、[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を使用して承認要件を免除し、匿名ユーザーがログインしてホーム ページを表示できるようにします。[RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute.aspx) により、Web アプリケーションに対するすべてのアクセスは HTTPS に限定されます。

2.  [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を Home コントローラーの **Index** メソッドに追加します。[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を使用すれば、特定のメソッドを認証不要として指定できます。HomeController からのイメージの抜粋を以下に示します。

    ![code][5]

3.  グローバルに検索すると、Account コントローラーのログイン メソッドや登録メソッドで *AllowAnonymous* が使われていることがわかります。
4.  *CmController.cs* で、*Cm* コントローラーでデータの変更 (Create、Edit、Delete、つまり、Index と Details を除くすべてのアクション メソッド) を伴う HttpGet メソッドと HttpPost メソッドに `[Authorize(Roles = "canEdit")]` を追加します。追加後のコードは次のようになります。

    ![img of code][img of code]

## プロジェクトに対して SSL を有効にする

1.  SSL を有効にします。ソリューション エクスプローラーで **ContactManager** プロジェクトをクリックし、F4 キーを押します。プロパティ ダイアログ ボックスが表示されます。**[SSL Enabled]** を True に変更します。**[SSL URL]** をコピーします。以前に SSL Web サイトを作成したことがなければ、SSL URL は https://localhost:44300/ になります。

    ![enable SSL][enable SSL]

2.  ソリューション エクスプローラーで **Contact Manager** プロジェクトを右クリックし、**[プロパティ]** をクリックします。
3.  左側のタブで **[Web]** をクリックします。
4.  **SSL URL** を使用するように **[プロジェクト URL]** を変更し、ページを保存します (Ctrl + S)。

    ![enable SSL][6]

5.  次の図に示すように、Internet Explorer が Visual Studio によって起動されるブラウザーであることを確認します。

    ![default browser][default browser]

    ブラウザー セレクターによって、Visual Studio が起動するブラウザーを指定することができます。

 ![browser selector][browser selector]

    変更を加える場合、複数のブラウザーを選択して、Visual Studio が各ブラウザーを更新するようにできます。 詳細については、「Using Browser Link in Visual Studio 2013 (Visual Studio 2013 でのブラウザー リンクの使用]」(http://www.asp.net/visual-studio/overview/2013/using-browser-link).

1.  Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。指示に従って、IIS Express が生成した自己署名証明書を信頼します。

    ![instructions to trust the self-signed certificate that IIS Express has generated][instructions to trust the self-signed certificate that IIS Express has generated]

2.  **[セキュリティ警告]** ダイアログを確認して、**localhost** を表す証明書をインストールする場合は **[はい]** をクリックします。

  ![localhost IIS Express certificate warning][localhost IIS Express certificate warning]

3.  IE は*ホーム* ページを表示し、SSL の警告はありません。

    ![IE with localhost SSL and no warnings][IE with localhost SSL and no warnings]

    Google Chrome も証明書を受け取り、警告なしで HTTPS コンテンツを表示します。Firefox は独自の証明書ストアを使用します。したがって、警告を表示します。マイクロソフトのアプリケーションについては、**[リスクを理解しています]** を安全にクリックできます。

    ![FireFox Cert Warning][FireFox Cert Warning]

4.  まだ前のセッションにログインしている場合は、**ログアウト** リンクをクリックします。
5.  **[About]** または **[Contact]** リンクをクリックします。匿名ユーザーはこれらのページを表示できないため、ログイン ページにリダイレクトされます。
6.  **[Register as a new user]** リンクをクリックして、電子メールが <*joe@contoso.com*> であるローカル ユーザーを追加します。*Joe* であれば Home ページ、About ページ、Contact ページを表示できることを確認します。

    ![login][login]

7.  *[CM Demo]* リンクをクリックし、データが表示されることを確認します。
8.  このページ上の編集リンクをクリックすると、ログイン ページにリダイレクトされます (新しいローカル ユーザーが *canEdit* ロールに追加されていないため)。
9.  ユーザー名 <*user1@contoso.com>\*、パスワード "P\_assw0rd1" ("word" の "0" はゼロ) でログインします。先ほど選択した編集ページにリダイレクトされます。

このアカウントとパスワードでログインできない場合は、ソース コードからパスワードをコピーして貼り付けてみてください。それでもログインできない場合は、**AspNetUsers** テーブルの **UserName** 列を見て、<*user1@contoso.com>\* が追加されていることを確認します。

1.  データを変更できることを確認します。

## <a name="bkmk_deploytowindowsazure11"></a>Azure にアプリケーションをデプロイする

1.  Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

    ![Publish in project context menu][7]

    **Web の発行**ウィザードが開きます。

2.  **[Web の発行]** ダイアログ ボックスの左側の **[設定]** タブをクリックします。**v** アイコンをクリックして **ApplicationDbContext** の **[リモート接続文字列]** を選択し、**[ContactDB]** を選択します。

    ![settings][settings]

3.  **ContactManagerContext** で、**[Code First Migrations を実行する]** を選択します。

    ![settings][8]

4.  **[発行]** をクリックします。

5.  <*user1@contoso.com>\* でログインし、データを編集できることを確認します。

6.  ログアウトします。

7.  Google または Facebook を使用してログインします。これにより、Google または Facebook のアカウントが **canEdit** ロールに追加されます。

### Web サイトを停止して第三者の登録を防ぐ

1.  **サーバー エクスプローラー**で **[Web サイト]** に移動します。
2.  各 Web サイトのインスタンスを右クリックし、**[Web サイトの停止]** を選択します。

    ![stop web site][stop web site]

    Azure の管理ポータルから Web サイトを選択し、ページ下部の **[停止]** アイコンをクリックする方法もあります。

    ![stop web site][9]

### AddToRoleAsync の削除、発行、テスト

1.  Account コントローラーの **ExternalLoginConfirmation** メソッドから次のコードをコメント アウト、または削除します。
     `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
2.  プロジェクトをビルドします。これにより、ファイルの変更が保存され、コンパイル エラーがないか確認が行われます。
3.  **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[発行]** をクリックします。

    ![Publish in project context menu][Publish in project context menu]

4.  **[プレビューの開始]** をクリックします。更新する必要のあるファイルだけが展開されます。
5.  Visual Studio またはポータルから Web サイトを開きます。**Web サイトが停止した状態で発行することはできません**。

    ![start web site][start web site]

6.  Visual Studio に戻り、**[発行]** をクリックします。
7.  Azure アプリケーションが既定のブラウザーで起動します。ログインして、ログアウトすると、匿名ユーザーとしてホーム ページを表示できます。
8.  **[About]** リンクをクリックします。ログイン ページにリダイレクトされます。
9.  ログイン ページの **[登録]** リンクをクリックし、ローカル アカウントを作成します。読み取り専用ページにはアクセスできるが、データの変更を伴うページにはアクセスできない (*canEdit* ロールによって保護されている) ことをこのローカル アカウントを使用して確認します。ローカル アカウント アクセスは、後でこのチュートリアルの中で削除します。

    ![Register][Register]

10. *About* ページと *Contact* ページにアクセスできることを確認します。

    ![Log off][Log off]

11. **[CM Demo]** リンクをクリックして **Cm** コントローラーに移動します。URL に *Cm* を付加することによって移動してもかまいません。

    ![CM page][CM page]

12. [編集] リンクをクリックします。ログイン ページにリダイレクトされます。**[Use another service to log in]** で [Google] または [Facebook] をクリックし、過去に登録したアカウントでログインします (短時間で作業して、セッション Cookie がタイムアウトしていない場合は、以前に使用した Google アカウントまたは Facebook アカウントで自動的にログインされます)。
13. そのアカウントにログインした状態でデータを編集できることを確認します。
    **注:** このアプリケーションで Google からログアウトし、同じブラウザーで別の Google アカウントにログインすることはできません。1 つのブラウザーを使用している場合は、Google に移動してログアウトする必要があります。同じサード パーティの認証システム (Google など) に属している別のアカウントでログオンするためには、異なるブラウザーを使用する必要があります。

Google アカウント情報の氏名を入力しないと、NullReferenceException が発生します。

## SQL Azure DB を調べる

1.  **サーバー エクスプローラー**で **[ContactDB]** に移動します。
2.  **[ContactDB]** を右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** を選択します。

    ![open in SSOX][open in SSOX]

  **注:** **[SQL データベース]** を展開できず、**ContactDB** を Visual Studio から表示*できない*場合は、以下の手順に従い、特定のファイアウォール ポート (またはポート範囲) を開放する必要があります。**[Azure ファイアウォール ルールを設定する]** の下の手順に従います。ファイアウォール ルールの追加後、データベースに接続できるようになるまでに数分かかる場合があります。

3.  **AspNetUsers** テーブルを右クリックし、**[データの表示]** を選択します。

    ![CM page][10]

4.  **canEdit** ロールへの追加用として登録した Google アカウントの ID と <*user1@contoso.com*> の ID をメモします。それ以外のユーザーは **canEdit** ロールに含めないようにする必要があります。この点については、次のステップで確認します。

    ![CM page][11]

3.  **SQL Server オブジェクト エクスプローラー**で **[AspNetUserRoles]** を右クリックし、**[データの表示]** を選択します。

    ![CM page][12]

**UserId** が、登録した Google アカウントおよび <*user1@contoso.com>\* のものであることを確認します。

## Azure のファイアウォール ルールを設定する

Visual Studio から SQL Azure に接続できない、またはエラー メッセージ "サーバーを開けません" が表示される場合は、このセクションの指示に従います。

![firewall error][firewall error]

使用できる IP アドレスに、目的の IP アドレスを追加する必要があります。

1.  Azure ポータルの左側のタブで **[SQL データベース]** を選択します。

    ![Select SQL][Select SQL]

2.  **[ContactDB]** をクリックします。

3.  **[この IP アドレス用に Azure ファイアウォール ルールを設定する]** リンクをクリックします。

    ![firewall rules][firewall rules]

4.  [現在の IP アドレス xxx.xxx.xxx.xxx は既存のファイアウォール ルールに含まれていません。ファイアウォール ルールを更新しますか?" というメッセージが表示されたら、**[はい]** をクリックします。企業ファイアウォールによっては、このアドレスだけでは不十分な場合もあります。そのような場合は、IP アドレスの範囲を追加する必要があります。

次に、使用できる一連の IP アドレスを追加します。

1.  Azure ポータルで **[SQL データベース]** をクリックします。
2.  **[サーバー]** タブをクリックし、構成するサーバーをクリックします。

    ![Servers tab in Azure][Servers tab in Azure]

3.  **[構成]** タブをクリックします。

4.  ルール名、開始 IP アドレス、終了 IP アドレスを追加します。

    ![ip range][ip range]

5.  ページの下部にある **[保存]** をクリックします。
6.  ご意見やご感想、また接続先となる一連の IP アドレスを追加する必要がある場合は、お知らせください。

最後に、SQL Database インスタンスに SQL Server オブジェクト エクスプローラー (SSOX) から接続します。

1.  [表示] メニューの **[SQL Server オブジェクト エクスプローラー]** をクリックします。
2.  **[SQL Server]** を右クリックし、**[SQL Server の追加]** を選択します。
3.  **[サーバーへの接続]** ダイアログ ボックスで、**[認証]** を **[SQL Server 認証]** に設定します。**[サーバー名]** と **[ログイン]** は Azure ポータルから取得されます。
4.  ブラウザーでポータルに移動し、**[SQL データベース]** を選択します。
5.  **[ContactDB]** を選択し、**[SQL データベース接続文字列を表示する]** をクリックします。
6.  **[接続文字列]** ページから **[サーバー]** と **[ユーザー ID]** をコピーします。

    ![con string][con string]

7.  **[サーバー]** と **[ユーザー ID]** の値を Visual Studio の **[サーバーへの接続]** ダイアログに貼り付けます。**[ユーザー ID]** の値が **[ログイン]** エントリに入ります。SQL DB の作成時に使用したパスワードを入力します。

    ![Connect to Server DLG][Connect to Server DLG]

これで、先ほど説明した手順に従い、Contact データベースに移動できます。

## データベース テーブルを編集して canEdit ロールにユーザーを追加するには

先ほどは、コードを使用してユーザーを canEdit ロールに追加しました。それ以外にも、メンバーシップ テーブル内のデータを直接操作する方法もあります。以下の手順では、その方法を使用してロールにユーザーを追加します。

1.  **SQL Server オブジェクト エクスプローラー**で **[AspNetUserRoles]** を右クリックし、**[データの表示]** を選択します。

    ![CM page][12]

2.  *RoleId* をコピーして空の (新しい) 行に貼り付けます。

    ![CM page][13]

3.  **AspNetUsers** テーブルから、ロールに追加するユーザーを探してその *Id* をコピーし、**AspNetUserRoles** テーブルの **[UserId]** 列に貼り付けます。

もっと簡単にユーザーとロールを管理できるように現在ツールを作成しています。

## ローカル登録の考慮事項

このプロジェクトの現在の ASP.NET メンバーシップ登録はパスワードのリセットをサポートしておらず、人間による登録を検証しません ([CAPTCHA][CAPTCHA] など)。いずれかのサードパーティ プロバイダーを使用してユーザーを認証すると、登録できるようになります。ローカル登録を無効にする場合は、次のステップを実行します。

1.  AccountController で、GET および POST *Register* メソッドから *[AllowAnonymous]* 属性を削除します。これによって、ボットや匿名ユーザーが登録されるのを防ぎます。
2.  *Views\\Shared* フォルダーの \*\_LoginPartial.cshtml\* ファイルから登録リンクを削除します。
3.  *Views\\Account\\Login.cshtml* ファイルから登録リンクを削除します。
4.  アプリケーションを展開します。

## <a name="nextsteps"></a><span class="short-header">次の手順</span>次の手順

ユーザー登録データベースにプロファイル データを追加する方法や、Facebook を認証プロバイダーとして使用する詳細な手順については、[Facebook や Google の OAuth2/OpenID サインオンを使用した ASP.NET MVC 5 アプリケーションの作成について執筆した拙著のチュートリアル][拙著のチュートリアル]を参考にしてください。

このチュートリアルの上部に表示されるソーシャル ログイン ボタンを有効にするには、「[Pretty social login buttons for ASP.NET MVC 5 (ASP.NET MVC 5 用ソーシャル ログイン ボタン)][Pretty social login buttons for ASP.NET MVC 5 (ASP.NET MVC 5 用ソーシャル ログイン ボタン)]」を参照してください。

ASP.NET MVC については、[ASP.NET MVC 5 の基本について執筆したチュートリアル][ASP.NET MVC 5 の基本について執筆したチュートリアル]をぜひご覧ください。MVC および Entity Framework のプログラミングについては、Tom Dykstra が [Entity Framework と MVC について取り上げたこちらのページ][Entity Framework と MVC について取り上げたこちらのページ]に、さらに詳しく解説されています。

このチュートリアルとサンプル アプリケーションは、Tom Dykstra と Barry Dorrans (Twitter [@blowdart][@blowdart]) の協力の下、[Rick Anderson][Rick Anderson] (Twitter [@RickAndMSFT][@RickAndMSFT]) が執筆しました。

役に立った内容や改善点など、皆様からのご意見をお寄せください。このチュートリアルに関してだけでなく、ここで紹介した製品に関するご意見やご要望もお待ちしております。お寄せいただいたご意見は、今後の改善に役立たせていただきます。新しいトピックについては、「[Show Me How With Code (コードの使用方法)](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code)」で要求および投票することもできます。

<!-- bookmarks -->
<!-- images-->

  [前のチュートリアル]: /ja-jp/develop/net/tutorials/web-site-with-sql-database-vs2012/
  [OAuth]: http://oauth.net/ "http://oauth.net/"
  [OpenID]: http://openid.net/
  [login page]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png
  [MSDN サブスクライバーの特典を有効にする]: /ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [無料評価版にサインアップ]: /ja-jp/pricing/free-trial/?WT.mc_id=A261C142F
  [開発環境を設定する]: #setupdevenv
  [Azure 環境を設定する]: #bkmk_setupwindowsazure
  [ASP.NET MVC 5 アプリケーションを作成する]: #bkmk_createmvc4app
  [Azure にアプリケーションを展開する]: #bkmk_deploytowindowsazure1
  [アプリケーションにデータベースを追加する]: #bkmk_addadatabase
  [OAuth プロバイダーを追加する]: #addOauth
  [メンバーシップ API を使用する]: #mbrDB
  [1]: #bkmk_deploytowindowsazure11
  [次のステップ]: #nextsteps
  [Visual Studio 2013 Update 2 RC]: http://go.microsoft.com/fwlink/?LinkId=390521
  [Azure の管理ポータル]: https://manage.windowsazure.com
  [New button in Management Portal]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
  [Create with Database link in Management Portal]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png
  [Database Settings step of New Website - Create with Database wizard]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png
  [New Project in File menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png
  [New Project dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png
  [New ASP.NET Project dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG
  [code changes]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png
  [Web site running locally]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png
  [Publish in project context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png
  [Import publish settings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG
  [sign in]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG
  [select web site]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png
  [Publish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png
  [Running in Cloud]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG
  [Add Class in Models folder context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png
  [Add New Item dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
  [Add Controller in Controllers folder context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png
  [Add Scaffold dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png
  [New data ctx dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG
  [Code First Migrations]: http://msdn.microsoft.com/library/hh770484.aspx
  [Package Manager Console in Tools menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png
  [Package Manager Console commands]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png
  [MVC view of data]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png
  [拙著のチュートリアル]: http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
  [Goog login]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG
  [GOOG asking for permission]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG
  [register]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG
  [code image]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG
  [WSAT]: http://msdn.microsoft.com/ja-jp/library/ms228053.aspx
  [code]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG
  [Authorize]: http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute.aspx
  [RequireHttps]: http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute.aspx
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG
  [img of code]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png
  [enable SSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png
  [default browser]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG
  [browser selector]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png
  [instructions to trust the self-signed certificate that IIS Express has generated]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG
  [localhost IIS Express certificate warning]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG
  [IE with localhost SSL and no warnings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG
  [FireFox Cert Warning]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG
  [login]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png
  [settings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png
  [stop web site]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png
  [start web site]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png
  [Register]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG
  [Log off]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG
  [CM page]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png
  [open in SSOX]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png
  [firewall error]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
  [Select SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
  [firewall rules]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
  [Servers tab in Azure]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG
  [ip range]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png
  [con string]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG
  [Connect to Server DLG]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png
  [13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png
  [CAPTCHA]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
  [ASP.NET MVC 5 の基本について執筆したチュートリアル]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Entity Framework と MVC について取り上げたこちらのページ]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [@blowdart]: http://blogs.msdn.com/b/rickandy/
  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [@RickAndMSFT]: https://twitter.com/blowdart
  [newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png
