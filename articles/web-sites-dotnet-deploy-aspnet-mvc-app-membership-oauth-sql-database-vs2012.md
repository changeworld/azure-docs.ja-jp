<properties linkid="dev-net-tutorials-web-app-with-sql-azure" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande" />

# メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Azure の Web サイトにデプロイする

***更新日: 2013 年 10 月 15 日。***

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/net/tutorials/web-site-with-sql-database/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/ja-jp/develop/net/tutorials/web-site-with-sql-database-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>注</strong><p><a href="/ja-jp/develop/net/tutorials/web-site-with-sql-database/">このチュートリアルの新しいバージョン</a>が提供されています。Visual Studio 2012 を使用する場合はこのバージョンを引き続き利用できますが、新しいバージョンの方が格段に取り組みやすい内容となっています。</p></div>

このチュートリアルでは、ユーザーが Facebook、Yahoo、Google の資格情報を使用してログインできる、安全な ASP.NET MVC 4 Web アプリケーションを構築する方法について説明します。さらに、作成したアプリケーションを Azure に展開する方法についても学習します。

Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2012 を持っていない場合は、SDK によって Visual Studio 2012 for Web Express が自動的にインストールされます。これで、Azure 向けのアプリケーションを無料で開発できます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルでは、安全なデータ主導型 Web アプリケーションを作成し、クラウド データベースを使用してクラウド上で実行します。

学習内容:

-   Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する方法
-   安全な ASP.NET MVC 4 プロジェクトを作成して Azure の Web サイトに発行する方法
-   OAuth および ASP.NET メンバーシップ データベースを使用してアプリケーションを保護する方法
-   メンバーシップ データベースを Azure にデプロイする方法
-   SQL データベースを使用して Azure にデータを保存する方法
-   Visual Studio を使用して、メンバーシップ データベースを更新および管理する方法

ASP.NET MVC 4 に基づく、データベース アクセスに ADO.NET Entity Framework を使用する、簡単な連絡先リスト Web アプリケーションをビルドします。次の図は、完成したアプリケーションのログイン ページです。

![login page][login page]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

このチュートリアルの内容:

-   [開発環境を設定する][開発環境を設定する]
-   [Azure 環境を設定する][Azure 環境を設定する]
-   [ASP.NET MVC 4 アプリケーションを作成する][ASP.NET MVC 4 アプリケーションを作成する]
-   [Azure にアプリケーションを展開する][Azure にアプリケーションを展開する]
-   [アプリケーションにデータベースを追加する][アプリケーションにデータベースを追加する]
-   [OAuth プロバイダーを追加する][OAuth プロバイダーを追加する]
-   [メンバーシップ データベースにロールを追加する][メンバーシップ データベースにロールを追加する]
-   [データ展開スクリプトを作成する][データ展開スクリプトを作成する]
-   [Azure にアプリケーションを展開する][1]
-   [メンバーシップ データベースを更新する][メンバーシップ データベースを更新する]
-   [次のステップ][次のステップ]

## <a name="bkmk_setupdevenv"></a>開発環境を設定する

最初に、.NET Framework 対応の Azure SDK をインストールして、開発環境を設定します

1.  Azure SDK for .NET をインストールするには、次のリンクをクリックします。Visual Studio 2012 をまだインストールしていない場合は、次のリンクをクリックするとインストールされます。このチュートリアルには Visual Studio 2012 が必要です。
    [Visual Studio 2012 用の Azure SDK][Visual Studio 2012 用の Azure SDK]
2.  インストール プログラムの実行または保存を求めるメッセージが表示されたら、**[実行]** をクリックします。
3.  Web Platform Installer のウィンドウで、**[インストール]** をクリックし、インストールの手順を進めます。

![Web Platform Installer - Azure SDK for .NET][Web Platform Installer - Azure SDK for .NET]

インストールが完了すると、開発に必要なツールがすべて揃います。

## <a name="bkmk_setupwindowsazure"></a>Azure 環境を設定する

次のステップでは、Azure の Web サイトと SQL データベースを作成することで Azure 環境をセットアップします。

### Azure で Web サイトと SQL データベースを作成する

Azure の Web サイトは、共有ホスティング環境で実行されます。つまり、他の Azure クライアントと共有する仮想マシン (VM) 上で実行されます。共有ホスティング環境は、低コストでクラウドの利用を開始できる方法です。後で Web トラフィックが増加したら、アプリケーションの規模を変更して専用 VM 上で実行するように設定してニーズを満たすことができます。もっと複雑なアーキテクチャが必要な場合は、Azure のクラウド サービスに移行できます。クラウド サービスは専用 VM 上で実行され、ユーザーのニーズに応じて構成できます。

Azure SQL データベースは、SQL Server テクノロジに基づいて構築されたクラウドベースのリレーショナル データベース サービスです。SQL Server で動作するツールおよびアプリケーションは、SQL データベースでも動作します。

1.  [Azure の管理ポータル][Azure の管理ポータル]で、左側のタブにある **[Web サイト]** をクリックし、**[新規]** をクリックします。

![New button in Management Portal][New button in Management Portal]

1.  **[カスタム作成]** をクリックします。

    ![Create with Database link in Management Portal][Create with Database link in Management Portal]

**新しい Web サイト - カスタム作成**ウィザードが開きます。

1.  ウィザードの **[新しい Web サイト]** 手順で、アプリケーションの一意の URL として使用する文字列を **[URL]** ボックスに入力します。ここに入力した文字列と、このテキスト ボックスの右側に表示されている文字列を組み合わせたものが実際の URL になります。図には "contactmgr2" と表示されていますが、その URL は既に取得されている可能性が高いため、別の URL の選択が必要になります。

    ![Create with Database link in Management Portal][2]

2.  **[データベース]** ドロップダウン リストで、**[新しい SQL データベースを作成します]** を選択します。

3.  **[リージョン]** ボックスの一覧で、Web サイトに選択したリージョンと同じリージョンを選択します。この設定では、どのデータ センターで VM を実行するかを指定します。**[DB 接続文字列名]** に「*connectionString1*」と入力します。

    ![Create a New Website step of New Website - Create with Database wizard][2]

4.  ボックスの下部にある右矢印をクリックします。ウィザードの **[データベースの設定]** 手順に進みます。

5.  **[名前]** ボックスに「*ContactDB*」と入力します。

6.  **[サーバー]** ボックスで、**[新しい SQL データベース サーバー]** を選択します。または、以前に SQL Server データベースを作成した場合は、ボックスの一覧からその SQL Server を選択できます。

7.  管理者の**ログイン名**と**パスワード**を入力します。**[新しい SQL データベース サーバー]** を選択した場合は、既存の名前とパスワードではなく、このデータベースへのアクセス時に使用する新しい名前とパスワードを入力してください。以前に作成した SQL Server を選択した場合は、その SQL Server の作成時に設定したパスワードを入力します。このチュートリアルでは、**[データベースの詳細設定を構成します]** チェック ボックスをオンにしません。**詳細設定**では、DB サイズ (既定値は 1 GB ですが 150 GB まで拡張可能) と照合順序を指定できます。

8.  終了したら、ダイアログ ボックスの下部にあるチェック マークをクリックします。

    ![Database Settings step of New Website - Create with Database wizard][Database Settings step of New Website - Create with Database wizard]

    次の画像では、既存の SQL Server を選択した場合のログインを示しています。
    ![Database Settings step of New Website - Create with Database wizard][3]

    管理ポータルが [Web サイト] ページに戻り、**[状態]** 列にサイトが作成中であることが示されます。しばらくすると (通常は 1 分未満)、サイトの作成に成功したことが **[状態]** 列に示されます。左側にあるナビゲーション バーでは、アカウントで所有するサイト数が **[Web サイト]** アイコンの横に表示され、データベース数が **[SQL データベース]** アイコンの横に表示されます。

## <a name="bkmk_createmvc4app"></a>ASP.NET MVC 4 アプリケーションを作成する

Azure の Web サイトを作成しましたが、まだその中にコンテンツがありません。次の手順では、Azure に発行する Visual Studio Web アプリケーション プロジェクトを作成します。

### プロジェクトを作成する

1.  Visual Studio 2012 を起動します。
2.  **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3.  **[新しいプロジェクト]** ダイアログ ボックスで、**[インストールされているテンプレート]** の下にある **[Visual C\#]** を展開して **[Web]** を選択し、**[ASP.NET MVC 4 Web アプリケーション]** を選択します。既定の **[.NET Framework 4.5]** をそのまま使用します。アプリケーションに「**ContactManager**」という名前を付けて、**[OK]** をクリックします。

    ![New Project dialog box][New Project dialog box]

4.  **[新しい ASP.NET MVC 4 プロジェクト]** ダイアログ ボックスで、**[インターネット アプリケーション]** テンプレートを選択します。**[ビュー エンジン]** ボックスの一覧で既定の [Razor] をそのまま使用し、**[OK]** をクリックします。

    ![New ASP.NET MVC 4 Project dialog box][New ASP.NET MVC 4 Project dialog box]

### ページのヘッダーとフッターを設定する

1.  **ソリューション エクスプローラー**で、Views\\Shared フォルダーを展開し、\*\_Layout.cshtml\* ファイルを開きます。

    ![\_Layout.cshtml in Solution Explorer][\_Layout.cshtml in Solution Explorer]

2.  "My ASP.NET MVC Application" となっている箇所をすべて「Contact Manager」に書き換えます。
3.  "your logo here" を「CM Demo」に書き換えます。

### ローカルでアプリケーションを実行する

1.  Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。アプリケーションのホーム ページが既定のブラウザーに表示されます。
    ![To Do List home page][To Do List home page]

これで、Azure に展開するアプリケーションを作成するために必要な操作が完了しました。データベース機能は後で追加します。

## <a name="bkmk_deploytowindowsazure1"></a> アプリケーションの Azure への展開

1.  ブラウザーで、[Azure の管理ポータル][4]を開きます。

2.  **[Web サイト]** タブで、前の手順で作成したサイトの名前をクリックします。

    ![Contact manager application in Management Portal Websites tab][Contact manager application in Management Portal Websites tab]

3.  ウィンドウの右側で、**[発行プロファイルのダウンロード]** をクリックします。

    ![Quickstart tab and Download Publishing Profile button][Quickstart tab and Download Publishing Profile button]

    この手順では、アプリケーションを Web サイトにデプロイするために必要な設定をすべて含むファイルがダウンロードされます。このファイルを Visual Studio にインポートすると、この情報を手動で入力する必要はありません。

4.  Visual Studio からアクセスできるフォルダーに .*publishsettings* ファイルを保存します。

    ![saving the .publishsettings file][saving the .publishsettings file]

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

5.  Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

    ![Publish in project context menu][Publish in project context menu]

    **Web の発行**ウィザードが開きます。

6.  **Web の発行**ウィザードの **[プロファイル]** タブで、**[インポート]** をクリックします。

    ![Import publish settings][Import publish settings]

    **[発行プロファイルのインポート]** ダイアログ ボックスが表示されます。

7.  以前に Visual Studio で Azure サブスクリプションを追加していない場合は、次の手順に従います。これらの手順で、**[Azure の Web サイトからインポート]** のドロップダウン リストに Web サイトが含まれるようにサブスクリプションを追加します。

    a. **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure サブスクリプションの追加]** をクリックします。

    ![add win az sub][add win az sub]

    b. **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[サブスクリプション ファイルのダウンロード]** をクリックします。

    ![download sub][download sub]

    c. ブラウザー ウィンドウで、*.publishsettings* ファイルを保存します。

    ![download pub file][download pub file]

    > [WACOM.NOTE]
    >  .publishsettings ファイルには、Azure のサブスクリプションとサービスの管理に使用される資格情報 (デコード済み) が保存されています。このファイルのセキュリティに関するベスト プラクティスは、このファイルをソース ディレクトリの外 (Libraries\\Documents フォルダーなど) に一時的に保存し、インポートが完了したらそのファイルを削除することです。悪意のあるユーザーが .publishsettings ファイルへのアクセス許可を取得すると、Azure サービスを編集、作成、削除できるためです。

    d. **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[参照]** をクリックし、*.publishsettings* ファイルに移動します。

    ![download sub][download sub]

    e. **[インポート]** をクリックします。

    ![import][import]

8.  **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure の Web サイトからインポート]** をクリックし、ドロップダウン リストから Web サイトを選択して **[OK]** をクリックします。

    ![Import Publish Profile][Import Publish Profile]

    これで、作成したアプリケーションはクラウドで実行されています。このアプリケーションを次に展開するときは、変更したファイル (または新しいファイル) のみが展開されます。

    ![To Do List home page running in Azure][To Do List home page running in Azure]

## <a name="bkmk_addadatabase"></a>アプリケーションにデータベースを追加する

次に、MVC アプリケーションを更新して、連絡先を表示および更新してデータをデータベースに保存する機能を追加します。アプリケーションでは、データベースの作成およびデータベース内のデータの読み取りと更新に Entity Framework を使用します。

### 連絡先のデータ モデル クラスを追加する

まず、コードで単純なデータ モデルを作成します。

1.  **ソリューション エクスプローラー**で、Models フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。

![Add Class in Models folder context menu][Add Class in Models folder context menu]

1.  **[新しい項目の追加]** ダイアログ ボックスで、新しいクラス ファイルに「*Contact.cs*」という名前を付け、[**追加**] をクリックします。

![Add New Item dialog box][Add New Item dialog box]

1.  Contacts.cs ファイルの内容を次のコードに置き換えます。

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

3.  **[コントローラーの追加]** ダイアログ ボックスで、コントローラー名として「HomeController」と入力します。
4.  **[スキャフォールディングのオプション]** の [テンプレート] で **[Entity Framework を使用した、読み取り/書き込み操作とビューのある MVC コントローラー]** を選択します。
5.  モデル クラスとして **Contact** を、データ コンテキスト クラスとして **[\<新しいデータ コンテキスト...\>]** を選択します。

    ![Add Controller dialog box][Add Controller dialog box]

6.  **[新しいデータ コンテキスト]** ダイアログ ボックスで、既定の *ContactManager.Models.ContactManagerContext* をそのまま使用します。
    ![Add Controller dialog box][5]

7.  **[OK]** をクリックし、**[コントローラーの追加]** ダイアログ ボックスで **[追加]** をクリックします。
8.  **[コントローラーの追加]** の上書き確認のダイアログ ボックスで、すべてのチェック ボックスがオンになっていることを確認し、**[OK]** をクリックします。

    ![Add Controller message box][Add Controller message box]

Visual Studio によって、**Contact** オブジェクトの CRUD データベース操作に対応したコントローラー メソッドとビューが作成されます。

## Migrations の有効化、データベースの作成、サンプル データとデータ初期化子の追加

次の作業では、作成したデータ モデルに基づいてデータベースを作成するために、[Code First Migrations][Code First Migrations] 機能を有効にします。

1.  **[ツール]** メニューの **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。

    ![Package Manager Console in Tools menu][Package Manager Console in Tools menu]

2.  **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

        enable-migrations -ContextTypeName ContactManagerContext

    ![enable-migrations][enable-migrations]
    コンテキストの型名 (**ContactManagerContext**) を指定する必要があります。プロジェクトに [DbContext][DbContext] の 2 つの派生クラスがあるためです。前の手順で追加した **ContactManagerContext** と、メンバーシップ データベースに使用される **UsersContext** です。**ContactManagerContext** クラスは Visual Studio のスキャフォールディング ウィザードによって追加されました。

    **enable-migrations** コマンドによって *Migrations* フォルダーが作成され、そのフォルダーに *Configuration.cs* ファイルが保存されます。このファイルを編集して Migration を構成できます。

3.  **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

        add-migration Initial

    **add-migration Initial** コマンドは、データベースを作成する **\<date\_stamp\>Initial** ファイルを *Migrations* フォルダーに生成します。最初のパラメーター ( **Initial** ) は省略可能で、ファイルの名前を作成するときに使用されます。新しいクラス ファイルは**ソリューション エクスプローラー**で表示できます。

    **Initial** クラスでは、**Up** メソッドを使用して Contacts テーブルを作成し、**Down** メソッドを使用してそのテーブルを削除します (前の状態に戻します)。

4.  *Migrations\\Configuration.cs* ファイルを開きます。
5.  次の名前空間を追加します。

         using ContactManager.Models;

6.  *Seed* メソッドを次のコードに置き換えます。

        protected override void Seed(ContactManager.Models.ContactManagerContext context)
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

    このコードでは、連絡先情報を使用してデータベースを初期化します。シード データベースの生成の詳細については、「[Seeding and Debugging Entity Framework (EF) DBs (Entity Framework DB のシード化とデバッグ)][Seeding and Debugging Entity Framework (EF) DBs (Entity Framework DB のシード化とデバッグ)]」を参照してください。

7.  **[パッケージ マネージャー コンソール]** で、次のコマンドを入力します。

        update-database

    ![Package Manager Console commands][Package Manager Console commands]

    **update-database** によって、データベースを作成する最初の Migration が実行されます。既定では、データベースは SQL Server Express LocalDB データベースとして作成されます (SQL Server Express をインストールしている場合を除き、この例では、SQL Server Express インスタンスを使用してデータベースが作成されます)。

8.  Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

アプリケーションでは、登録されたデータが表示され、編集、詳細、削除のリンクが示されます。

![MVC view of data][MVC view of data]

## <a name="addOauth"></a><span class="short-header">OAuth</span>OAuth プロバイダーを追加する

[OAuth][OAuth] は、Web、モバイル、およびデスクトップのアプリケーションからシンプルで標準的な方法で安全に認証するためのオープン プロトコルです。ASP.NET MVC インターネット テンプレートは OAuth を使用して、Facebook、Twitter、Google、Yahoo、Microsoft を認証プロバイダーとしてサポートします。このチュートリアルでは Facebook、Google、Yahoo のみを認証プロバイダーとして使用しますが、コードを少し変更すれば他のプロバイダーも使用できます。他のプロバイダーを実装する手順は、このチュートリアルで説明する手順とほとんど同じです。

このチュートリアルでは、認証の他にロールを使用して権限を付与します。canEdit ロールに追加したユーザーのみが連絡先を作成、編集、削除できます。

## 外部プロバイダーを登録する

外部プロバイダーから取得した資格情報を使用してユーザーを認証するには、そのプロバイダーに Web サイトを登録し、鍵と接続シークレットを取得する必要があります。Google と Yahoo の場合は、登録して鍵を取得する必要がありません。

このチュートリアルでは、これらのプロバイダーに登録するための手順を詳しく説明しません。複雑な手順ではないので、プロバイダーの指示に従ってサイトを登録してください。サイトの登録については、次の開発者向けサイトを参照してください。

-   [Facebook][Facebook]
-   [Microsoft][Microsoft]
-   [Twitter][Twitter]

[][]<https://developers.facebook.com/apps></a> ページへ移動し、必要であればログインします。**[Register as a Developer]** ボタンをクリックして登録手続きを行います。登録が完了したら、**[新しいアプリケーションを作成]** をクリックします。アプリケーションの名前を入力します。アプリケーションの名前空間を入力する必要はありません。

![Create New FB app][Create New FB app]

**[App Domain]** に「localhost」と入力し、**[Site URL]** に「<http://localhost/>」と入力します。**[Sandbox Mode]** で **[有効]** をクリックし、**[変更を保存]** をクリックします。

このアプリケーションに OAuth を実装するには、**[App ID]** と **[App Secret]** が必要です。
![New FB app][New FB app]

## テスト ユーザーを作成する

画面左側の **[設定]** で **[開発者の役割]** をクリックします。(**テスト担当者**行ではなく) **テスト ユーザー**行の **[作成]** リンクをクリックします。

![FB testers][FB testers]

**[変更]** リンクをクリックし、テスト ユーザーの電子メールを取得します (アプリケーションにログインするとき使用します)。**[もっと見る]** リンクをクリックし、次に **[編集]** をクリックしてテスト ユーザーのパスワードを設定します。

## プロバイダーのアプリケーション ID と鍵を追加する

*App\_Start\\AuthConfig.cs* ファイルを開きます。*RegisterFacebookClient* メソッドからコメント文字を削除し、App ID と App Secret を追加します。実際に取得した値を使用してください。ここに表示されている値は使用できません。*OAuthWebSecurity.RegisterGoogleClient* 呼び出しからコメント文字を削除し、次のように *AuthWebSecurity.RegisterYahooClient* を追加します。Google プロバイダーと Yahoo プロバイダーの場合、登録して鍵を取得する必要がありません。
警告: アプリケーション ID とシークレットを安全な場所に保管してください。アプリケーション ID とシークレットが悪意のあるユーザーに漏れると、アプリケーションに不正アクセスされる可能性があります。

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "enter numeric key here",
                appSecret: "enter numeric secret here");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }

1.  アプリケーションを実行し、**[ログイン]** リンクをクリックします。
2.  **[Facebook]** ボタンをクリックします。
3.  Facebook の資格情報、またはいずれかのテスト ユーザーの資格情報を入力します。
4.  **[OK]** をクリックします。アプリケーションから Facebook のリソースにアクセスできるようになります。
5.  登録ページにリダイレクトされます。テスト アカウントを使用してログインした場合は、もう少し短い**ユーザー名**に変更できます ("Bill FB test" など)。**[登録]** ボタンをクリックします。ユーザー名と電子メール エイリアスがメンバーシップ データベースに保存されます。
6.  別のユーザーを登録します。現在、ログイン システムのバグにより、システムからログオフした後、同じプロバイダーを使用して別のユーザーとしてログインできません (つまり、Facebook アカウントをログオフした後、別の Facebook アカウントで再びログインすることはできません)。これに対処するには、別のブラウザーを使用して目的のサイトへアクセスし、別のユーザーを登録してください。一方のユーザーが管理者ロールに追加され、アプリケーションに対する編集権限が与えられます。もう一方のユーザーはそのサイトで非編集メソッドのみを実行できます。匿名ユーザーはホーム ページにのみアクセスできます。
7.  別のプロバイダーを使用して他のユーザーを登録します。
8.  **省略可能**: どのプロバイダーにも関連付けられていないローカル アカウントを作成することもできます。後ほどこのチュートリアルで、ローカル アカウントを作成する機能を削除します。ローカル アカウントを作成するには、**[ログアウト]** リンクをクリックし (現在ログインしている場合)、**[登録]** リンクをクリックします。必要であれば、どの外部認証プロバイダーにも関連付けられていない管理用のローカル アカウントを作成します。

## <a name="mbrDB"></a><span class="short-header">メンバーシップ DB</span>メンバーシップ データベースにロールを追加する

このセクションでは、*canEdit* ロールをメンバーシップ データベースに追加します。canEdit ロールのユーザーのみがデータを編集することができます。実行可能な操作に基づいてロール名を付けるのが望ましいので、この場合は *admin* より *canEdit* が適しています。その後、必要に応じて、("*superAdmin*" ではなく) "*canDeleteMembers*" などの新しいロールを追加します。

1.  **[表示]** メニューの **[サーバー エクスプローラー]** をクリックします。

2.  **サーバー エクスプローラー**で **DefaultConnection** を展開し、さらに **Tables** を展開します。

3.  **UserProfile** を右クリックして **[テーブル データの表示]** をクリックします。

    ![Show table data][Show table data]

4.  canEdit ロールを割り当てるユーザーの **UserId** を記録します。次の図では、**UserId** が "2" であるユーザー *ricka* に canEdit ロールが割り当てられています。

    ![user IDs][user IDs]

5.  **webpages\_Roles** を右クリックして **[テーブル データの表示]** をクリックします。
6.  **RoleName** セルに「**canEdit**」と入力します。ロールを初めて追加する場合、**RoleId** は 1 になります。RoleID を記録しておきます。末尾にスペースを入れないように注意してください。ロール テーブルの "canEdit " がコントローラー コードの "canEdit" と一致しなくなります。

    ![roleID][roleID]

7.  **webpages UsersInRoles** を右クリックして **[テーブル データの表示]** をクリックします。*canEdit* アクセスを付与するユーザーの **UserId** と **RoleId** を入力します。

    ![usr role ID tbl][usr role ID tbl]

**webpages\_OAuthMembership** テーブルには、OAuth プロバイダー、プロバイダーの UserID、および登録されている各 OAuth ユーザーの UserID が格納されます。<!-- Don't replace "-" with "_" or it won't validate -->**webpages-Membership** テーブルには、ASP.NET メンバーシップ テーブルが格納されます。登録リンクを使用して、このテーブルにユーザーを追加できます。*canEdit* ロールが割り当てられており、Facebook や他のサードパーティ認証プロバイダーに関連付けられていないユーザーを追加しておけば、サードパーティ認証プロバイダーに接続できない場合でも *canEdit* アクセスが可能になります。後ほどこのチュートリアルで、ASP.NET メンバーシップ登録を無効にします。

## Authorize 属性を使用してアプリケーションを保護する

このセクションでは、[Authorize][Authorize] 属性を適用してアクション メソッドへのアクセスを制限します。匿名ユーザーが表示できるのはホーム ページだけになります。登録ユーザーは連絡先の詳細ページを閲覧できます。*canEdit* ロールを与えられているユーザーのみがアクション メソッドを実行してデータを変更できます。

1.  [Authorize][Authorize] フィルターと [RequireHttps][RequireHttps] フィルターをアプリケーションに追加します。[Authorize][Authorize] 属性と [RequireHttps][RequireHttps] 属性をコントローラーごとに追加する方法もありますが、セキュリティ上の理由から、通常はこれらをアプリケーション全体に適用します。アプリケーション全体に適用すれば、新しいコントローラーやアクション メソッドを追加したとき、それらが自動的に保護されます。ユーザー自身で適用する必要がありません。詳細については、[ASP.NET MVC 4 アプリケーションの保護と新しい AllowAnonymous 属性に関するページ][ASP.NET MVC 4 アプリケーションの保護と新しい AllowAnonymous 属性に関するページ]を参照してください。*App\_Start\\FilterConfig.cs* ファイルを開き、*RegisterGlobalFilters* メソッドを次のように書き換えます。

        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }

2.  [AllowAnonymous][ASP.NET MVC 4 アプリケーションの保護と新しい AllowAnonymous 属性に関するページ] 属性を **Index** メソッドに追加します。[AllowAnonymous][ASP.NET MVC 4 アプリケーションの保護と新しい AllowAnonymous 属性に関するページ] 属性を使用すれば、特定のメソッドを認証不要として指定できます。
3.  データを変更 (作成、編集、削除) する Get メソッドと Post メソッドに [Authorize(Roles = "canEdit")] を追加します。
4.  *About* メソッドと *Contact* メソッドを追加します。追加後のコードは次のようになります。

        public class HomeController : Controller
        {
            private ContactManagerContext db = new ContactManagerContext();
            [AllowAnonymous]
            public ActionResult Index()
            {
                return View(db.Contacts.ToList());
            }

            public ActionResult About()
            {
                return View();
            }

            public ActionResult Contact()
            {
                return View();
            }

            [Authorize(Roles = "canEdit")]
            public ActionResult Create()
            {
                return View();
            }
            // Methods moved and omitted for clarity.
        }

5.  ASP.NET メンバーシップ登録を削除します。このプロジェクトの現在の ASP.NET メンバーシップ登録はパスワードのリセットをサポートしておらず、人間による登録を検証しません ([CAPTCHA][CAPTCHA] など)。いずれかのサードパーティ プロバイダーを使用してユーザーを認証すると、登録できるようになります。AccountController で、GET および POST Register メソッドから [AllowAnonymous] を削除します。これによって、ボットや匿名ユーザーが登録されるのを防ぎます。
6.  *Views\\Shared\_LoginPartial.cshtml* で登録リンクを削除します。
7.  SSL を有効にします。ソリューション エクスプローラーで **ContactManager** プロジェクトをクリックし、F4 キーを押します。プロパティ ダイアログ ボックスが表示されます。**[SSL Enabled]** を True に変更します。**[SSL URL]** をコピーします。

    ![enable SSL][enable SSL]

8.  ソリューション エクスプローラーで **Contact Manager** プロジェクトを右クリックし、**[プロパティ]** をクリックします。
9.  左側のタブで **[Web]** をクリックします。
10. **[プロジェクト URL]** を変更し、**SSL URL** を使用するようにします。
11. **[仮想ディレクトリの作成]** をクリックします。

    ![enable SSL][6]

12. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。ブラウザーに証明書の警告が表示されます。このアプリケーションでは、**[このサイトの閲覧を続行する]** をクリックしてかまいません。*canEdit* ロールを与えられているユーザーのみがデータを変更できることを確認します。匿名ユーザーが閲覧できるのはホーム ページのみであることを確認します。

    ![cert Warn][cert Warn]

    ![cert Warn][7]

Azure の Web サイトには有効なセキュリティ証明書が備わっているので、Azure にデプロイするときこの警告は表示されません。

## <a name="ppd"></a><span class="short-header">DB の作成</span>データ デプロイ スクリプトを作成する

</p>
メンバーシップ データベースは Entity Framework Code First によって管理されないので、Migrations を使用して展開できません。[dbDacFx][dbDacFx] プロバイダーを使用してデータベース スキーマを展開し、メンバーシップ データをメンバー テーブルに挿入するスクリプトを実行するように発行プロファイルを構成します。

このチュートリアルでは、SQL Server Management Studio (SSMS) を使用してデータ展開スクリプトを作成します。

[Microsoft SQL Server 2012 Express ダウンロード センター][Microsoft SQL Server 2012 Express ダウンロード センター]から SSMS をインストールします。

-   [ENU\x64\SQLManagementStudio\_x64\_ENU.exe][ENU\x64\SQLManagementStudio\_x64\_ENU.exe] (64 ビット システム)
-   [ENU\x86\SQLManagementStudio\_x86\_ENU.exe][ENU\x86\SQLManagementStudio\_x86\_ENU.exe] (32 ビット システム)

お使いのシステムに適したバージョンを選択しないとインストールできません。その場合は、適切なバージョンを選択して、もう一度インストールしてください。

(ダウンロード データは 600 MB あるので、インストールに多少時間がかかります。また、コンピューターの再起動が必要な場合もあります。)

SQL Server インストール センターの最初のページで **[SQL Server の新規スタンドアロン インストールを実行するか、既存のインストールに機能を追加します]** をクリックし、画面の指示に従います。設定は既定値を使用してください。SSMS のインストール画面を次に示します。

![SQL Install][SQL Install]

### 開発データベース スクリプトを作成する

1.  SSMS を実行します。
2.  **[サーバーへの接続]** ダイアログ ボックスで、サーバー名として「*(localdb)\\v11.0*」と入力します。**[認証]** を **[Windows 認証]** に設定して **[接続]** をクリックします。SQL Express をインストールしている場合は、「**.\\SQLEXPRESS**」と 入力します。

    ![con to srvr dlg][con to srvr dlg]

3.  **オブジェクト エクスプローラー**で **[データベース]** を展開し、**[aspnet-ContactManager]** を右クリックして、**[タスク]**、**[スクリプトの生成]** の順にクリックします。

    ![Gen Scripts][Gen Scripts]

4.  **[スクリプトの生成とパブリッシュ]** ダイアログ ボックスで、**[スクリプト作成オプションの設定]** をクリックします。
    **[オブジェクトの選択]** ステップはスキップしてかまいません。既定の設定 ([データベース全体とすべてのデータベース オブジェクトのスクリプトを作成]) をそのまま使用します。

5.  **[詳細設定]** をクリックします。

    ![Set scripting options][Set scripting options]

6.  **[スクリプト作成の詳細オプション]** ダイアログ ボックスで **[スクリプトを作成するデータの種類]** までスクロールし、一覧から **[データのみ]** を選択します (次の図を参照)。

7.  **[USE DATABASE のスクリプトを作成]** を **False** に変更します。Azure SQL データベースでは USE ステートメントを使用しません。また、SQL Server Express をテスト環境に展開する場合、USE ステートメントは必要ありません。

    ![Set scripting options][8]

8.  **[OK]** をクリックします。
9.  **[スクリプトの生成とパブリッシュ]** ダイアログ ボックスで、**[ファイル名]** ボックスにスクリプトの作成場所を指定します。ソリューション フォルダー (*Contacts.sln* ファイルが保存されているフォルダー) のパスを変更し、ファイル名を「*aspnet-data-membership.sql*」に変更します。
10. **[次へ]** をクリックして **[概要]** タブへ進み、もう一度 **[次へ]** をクリックしてスクリプトを作成します。

    ![Save or pub][Save or pub]

11. **[完了]** をクリックします。

## <a name="bkmk_deploytowindowsazure11"></a>Azure にアプリケーションをデプロイする

1.  アプリケーション ルートの *Web.config* ファイルを開きます。*DefaultConnection* マークアップを見つけ、それをコピーして *DefaultConnection* マークアップ行の下に貼り付けます。コピーした要素の名前を「*DefaultConnectionDeploy*」に変更します。ユーザー データをメンバーシップ データベースにデプロイするには、この接続文字列が必要になります。
    ![3 cons str][3 cons str]

2.  アプリケーションをビルドします。
3.  Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

    ![Publish in project context menu][9]

**Web の発行**ウィザードが開きます。

1.  **[設定]** タブをクリックします。**ContactManagerContext** と **DefaultConnectionDeploy** で、**v** アイコンをクリックして **[リモート接続文字列]** を選択します。表示されている 3 つのデータベースでは、すべて同じ接続文字列を使用します。**ContactManagerContext** データベースには連絡先が保存されます。**DefaultConnectionDeploy** は、ユーザー アカウント データをメンバーシップ データベースへ展開するときに使用します。**UsersContext** はメンバーシップ データベースです。

    ![settings][settings]

2.  **ContactManagerContext** で、**[Code First Migrations を実行する]** を選択します。

    ![settings][10]

3.  **DefaultConnectionDeploy** で **[データベースの更新]** チェック ボックスをオンにして、**[データベースの更新の構成]** リンクをクリックします。
4.  **[SQL スクリプトの追加]** リンクをクリックし、*aspnet-data-membership.sql* ファイルへ移動します。この操作を行うのは 1 回だけです。次の展開時にはユーザー データをメンバーシップ テーブルに追加する必要がないので、**[データベースの更新]** チェック ボックスをオフにします。

    ![add sql][add sql]

5.  **[発行]** をクリックします。
6.  [][]<https://developers.facebook.com/apps></a> ページへ移動し、**[App Domains]** と **[Site URL]** の設定を Azure URL に変更します。
7.  アプリケーションをテストします。*canEdit* ロールを与えられているユーザーのみがデータを変更できることを確認します。匿名ユーザーが閲覧できるのはホーム ページのみであることを確認します。認証ユーザーは、データを変更しないすべてのリンク先へ移動できることを確認します。
8.  次にアプリケーションを発行するときは、**DefaultConnectionDeploy** の **[データベースの更新]** チェック ボックスをオフにしてください。

    ![settings][10]

## <a name="ppd2"></a><span class="short-header">DB の更新</span>メンバーシップ データベースを更新する

サイトを Azure に展開した後、さらに別の登録ユーザーを *canEdit* ロールに追加する必要が生じる場合もあります。このセクションでは、Visual Studio を使用して SQL データベースに接続し、*canEdit* ロールにユーザーを追加します。

![settings][10]

1.  **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[発行]** をクリックします。
    ![Publish][Publish]

2.  **[設定]** タブをクリックします。
3.  接続文字列をコピーします。たとえば、このサンプルで使用されている接続文字列は次のとおりです。
    Data Source=tcp:d015leqjqx.database.windows.net,1433;
    Initial Catalog=ContactDB2;User <Id=ricka0@d015lxyze;Password=xyzxyz>
4.  発行ダイアログを閉じます。
5.  **[表示]** メニューの **[サーバー エクスプローラー]** をクリックします。

6.  **[データベースに接続]** アイコンをクリックします。

    ![Publish][11]

7.  データ ソースを選択するためのダイアログ ボックスが表示された場合は、**[Microsoft SQL Server]** をクリックします。
    ![Publish][12]

8.  "*tcp*" で始まるサーバー名をコピーして、**[サーバー名]** ボックスに貼り付けます (下図を参照)。
9.  **[SQL Server 認証を使用する]** をクリックします。
10. **[ユーザー名]** と **[パスワード]** を入力します。これらの情報はコピーした接続文字列に含まれています。
11. データベース名 (ContactDB) を入力します ("ContactDB" 以外の名前を付けた場合は、データベースの "Initial Catalog=" の後の文字列を入力します)。エラー メッセージが表示される場合は、次のセクションを参照してください。
12. **[接続テスト]** をクリックします。エラー メッセージが表示される場合は、次のセクションを参照してください。
    ![add con dlg][add con dlg]

## サーバーに接続できない場合

"サーバーを開けない" ことを知らせるエラー メッセージが表示された場合は、使用できる IP に IP アドレスを追加する必要があります。

![ファイアウォール エラー][ファイアウォール エラー]

1.  Azure ポータルの左側のタブで **[SQL データベース]** を選択します。

    ![Select SQL][Select SQL]

2.  接続するデータベースを選択します。

3.  **[この IP アドレス用に Azure ファイアウォール ルールを設定する]** リンクをクリックします。

    ![firewall rules][firewall rules]

4.  [現在の IP アドレス xxx.xxx.xxx.xxx は既存のファイアウォール ルールに含まれていません。ファイアウォール ルールを更新しますか?" というメッセージが表示されたら、**[はい]** をクリックします。このアドレスだけでは不十分な場合は、IP アドレスの範囲を追加する必要があります。

## 使用できる IP アドレスの範囲を追加する

1.  Azure ポータルで **[SQL データベース]** をクリックします。
2.  データベースをホストする **[サーバー]** をクリックします。

    ![db server][db server]

3.  ページの上部にある **[構成]** をクリックします。
4.  ルール名、開始 IP アドレス、終了 IP アドレスを追加します。
    ![ip range][ip range]

5.  ページの下部にある **[保存]** をクリックします。
6.  ここで、前に説明した手順に従ってメンバーシップ データベースを編集します。

## <a name="nextsteps"></a><span class="short-header">次の手順</span>次の手順

このチュートリアルとサンプル アプリケーションは、Tom Dykstra、Tom FitzMacken、Barry Dorrans (Twitter [@blowdart][@blowdart]) の協力の下、[Rick Anderson][Rick Anderson] (Twitter [@RickAndMSFT][@RickAndMSFT]) が執筆しました。

役に立った内容や改善点など、皆様からのご意見をお寄せください。このチュートリアルに関してだけでなく、ここで紹介した製品に関するご意見やご要望もお待ちしております。お寄せいただいたご意見は、今後の改善に役立たせていただきます。特に、メンバーシップ データベースの構成と展開の自動化に関するご意見をお待ちしております。

Facebook、Google、Yahoo のカラフルなログイン ボタンを取得する方法については、ブログ投稿「[Customizing External Login Buttons in ASP.NET MVC 4 (ASP.NET MVC 4 で外部ログイン ボタンをカスタマイズする)][Customizing External Login Buttons in ASP.NET MVC 4 (ASP.NET MVC 4 で外部ログイン ボタンをカスタマイズする)]」を参照してください。Windows 認証の詳細については、次の Web ページを参照してください。

-   [Azure Authentication (Azure の認証)][Azure Authentication (Azure の認証)]
-   [ASP.NET MVC を使用してイントラネット サイトを作成する方法][ASP.NET MVC を使用してイントラネット サイトを作成する方法]

Azure アプリケーションにデータを保存するには、Azure ストレージを使用する方法もあります。Azure ストレージには、非リレーショナル データを BLOB 形式とテーブル形式で保存できます。ASP.NET MVC および Window Azure の詳細については、次の Web ページを参照してください。

-   [ストレージ テーブル、キュー、BLOB を使用する .NET 多層アプリケーションに関するページ][ストレージ テーブル、キュー、BLOB を使用する .NET 多層アプリケーションに関するページ]
-   [ASP.NET MVC 4 の入門ページ][ASP.NET MVC 4 の入門ページ]
-   [MVC を使用した Entity Framework の概要に関するページ][MVC を使用した Entity Framework の概要に関するページ]
-   [OAuth 2.0 とサインインに関するブログ投稿][OAuth 2.0 とサインインに関するブログ投稿]

Azure の Web サイトに Web アプリケーションをデプロイする方法を確認したところで、Azure の Web サイトを構成、管理、および拡張する方法をさらに確認するには、「[一般的なタスク][一般的なタスク]」ページにある操作方法のトピックを参照してください。

Azure の Web サイトのデバッグ方法については、「[Visual Studio での Azure の Web サイトのトラブルシューティング][Visual Studio での Azure の Web サイトのトラブルシューティング]」を参照してください。

Azure のクラウド サービスにアプリケーションを展開する方法を確認するには、[このチュートリアルのクラウド サービス バージョン][このチュートリアルのクラウド サービス バージョン]および [Azure を使った Web アプリケーションの開発に関するページ][Azure を使った Web アプリケーションの開発に関するページ]を参照してください。Azure の Web サイトではなく Azure のクラウド サービスで ASP.NET Web アプリケーションを実行する場合のいくつかの理由を次に示します。

-   アプリケーションが実行される Web サーバーに対する管理者権限が必要である。
-   アプリケーションが実行される Web サーバーにアクセスするためにリモート デスクトップ接続を使用したい。
-   多層アプリケーションを使用し、複数の仮想サーバー (Web およびワーカー) 間で処理を分散したい。

SQL データベースと Azure ストレージの詳細については、「[Data Storage Offerings on Azure (Azure のデータ ストレージ機能)][Data Storage Offerings on Azure (Azure のデータ ストレージ機能)]」を参照してください。

SQL データベースの使用方法については、[ASP.NET データ アクセス コンテンツ マップでの Azure SQL データベースの操作に関するページ][ASP.NET データ アクセス コンテンツ マップでの Azure SQL データベースの操作に関するページ]を参照してください。

Entity Framework および Code First Migrations の詳細については、次のリソースを参照してください。

-   [MVC を使用した Entity Framework の概要に関するページ][MVC を使用した Entity Framework の概要に関するページ]
-   [Code First Migrations に関するページ][Code First Migrations に関するページ]

<!-- bookmarks -->
<!-- links -->
<!-- links from Tom's hopefully no collisions -->
<!-- images-->

  [login page]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png
  [開発環境を設定する]: #bkmk_setupdevenv
  [Azure 環境を設定する]: #bkmk_setupwindowsazure
  [ASP.NET MVC 4 アプリケーションを作成する]: #bkmk_createmvc4app
  [Azure にアプリケーションを展開する]: #bkmk_deploytowindowsazure1
  [アプリケーションにデータベースを追加する]: #bkmk_addadatabase
  [OAuth プロバイダーを追加する]: #addOauth
  [メンバーシップ データベースにロールを追加する]: #mbrDB
  [データ展開スクリプトを作成する]: #ppd
  [1]: #bkmk_deploytowindowsazure11
  [メンバーシップ データベースを更新する]: #ppd2
  [次のステップ]: #nextsteps
  [Visual Studio 2012 用の Azure SDK]: http://go.microsoft.com/fwlink/?LinkId=254364
  [Web Platform Installer - Azure SDK for .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png
  [Azure の管理ポータル]: https://manage.windowsazure.com
  [New button in Management Portal]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png
  [Create with Database link in Management Portal]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png
  [Database Settings step of New Website - Create with Database wizard]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png
  [New Project dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png
  [New ASP.NET MVC 4 Project dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png
  [\_Layout.cshtml in Solution Explorer]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png
  [To Do List home page]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png
  [4]: http://manage.windowsazure.com "ポータル"
  [Contact manager application in Management Portal Websites tab]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png
  [Quickstart tab and Download Publishing Profile button]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png
  [saving the .publishsettings file]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png
  [Publish in project context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png
  [Import publish settings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png
  [add win az sub]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png
  [download sub]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png
  [download pub file]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png
  [import]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png
  [Import Publish Profile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png
  [To Do List home page running in Azure]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png
  [Add Class in Models folder context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png
  [Add New Item dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png
  [Add Controller in Controllers folder context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png
  [Add Controller dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png
  [Add Controller message box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png
  [Code First Migrations]: http://msdn.microsoft.com/library/hh770484.aspx
  [Package Manager Console in Tools menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png
  [enable-migrations]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png
  [DbContext]: http://msdn.microsoft.com/ja-jp/library/system.data.entity.dbcontext(v=VS.103).aspx
  [Seeding and Debugging Entity Framework (EF) DBs (Entity Framework DB のシード化とデバッグ)]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [Package Manager Console commands]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png
  [MVC view of data]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png
  [OAuth]: http://oauth.net/ "http://oauth.net/"
  [Facebook]: http://developers.facebook.com/
  [Microsoft]: http://go.microsoft.com/fwlink/?LinkID=144070
  [Twitter]: http://dev.twitter.com/
  []: https://developers.facebook.com/apps/
  [Create New FB app]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png
  [New FB app]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png
  [FB testers]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png
  [Show table data]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png
  [user IDs]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png
  [roleID]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png
  [usr role ID tbl]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png
  [Authorize]: http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute(v=vs.100).aspx
  [RequireHttps]: http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx
  [ASP.NET MVC 4 アプリケーションの保護と新しい AllowAnonymous 属性に関するページ]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
  [CAPTCHA]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
  [enable SSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png
  [cert Warn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png
  [dbDacFx]: http://msdn.microsoft.com/ja-jp/library/dd394698.aspx
  [Microsoft SQL Server 2012 Express ダウンロード センター]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29062
  [SQL Install]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png
  [con to srvr dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png
  [Gen Scripts]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png
  [Set scripting options]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png
  [Save or pub]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png
  [3 cons str]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png
  [settings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png
  [add sql]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png
  [Publish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png
  [add con dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png
  [ファイアウォール エラー]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png
  [Select SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png
  [firewall rules]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png
  [db server]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png
  [ip range]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png
  [@blowdart]: http://blogs.msdn.com/b/rickandy/
  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [@RickAndMSFT]: https://twitter.com/blowdart
  [Customizing External Login Buttons in ASP.NET MVC 4 (ASP.NET MVC 4 で外部ログイン ボタンをカスタマイズする)]: http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/
  [Azure Authentication (Azure の認証)]: http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication
  [ASP.NET MVC を使用してイントラネット サイトを作成する方法]: http://msdn.microsoft.com/ja-jp/library/gg703322(v=vs.98).aspx
  [ストレージ テーブル、キュー、BLOB を使用する .NET 多層アプリケーションに関するページ]: http://www.windowsazure.com/ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/
  [ASP.NET MVC 4 の入門ページ]: http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4
  [MVC を使用した Entity Framework の概要に関するページ]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [OAuth 2.0 とサインインに関するブログ投稿]: http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx
  [一般的なタスク]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/
  [Visual Studio での Azure の Web サイトのトラブルシューティング]: /ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [このチュートリアルのクラウド サービス バージョン]: http://www.windowsazure.com/ja-jp/develop/net/tutorials/cloud-service-with-sql-database/
  [Azure を使った Web アプリケーションの開発に関するページ]: http://msdn.microsoft.com/ja-jp/library/Hh674484
  [Data Storage Offerings on Azure (Azure のデータ ストレージ機能)]: http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx
  [ASP.NET データ アクセス コンテンツ マップでの Azure SQL データベースの操作に関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb
  [Code First Migrations に関するページ]: http://msdn.microsoft.com/ja-jp/library/hh770484
