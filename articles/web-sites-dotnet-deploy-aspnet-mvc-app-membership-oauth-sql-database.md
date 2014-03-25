<properties linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="SQL データベースを使用する Web サイト" pageTitle="メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Windows Azure の Web サイトに展開する" metaKeywords="Azure Hello World チュートリアル, Azure 概要チュートリアル, SQL データベース チュートリアル, Azure .NET Hello World チュートリアル, Azure C# Hello World チュートリアル, SQL Azure C# チュートリアル" description="SQL データベース バックエンドを使用する ASP.NET MVC 5 Web サイトを開発し、Windows Azure に展開する方法について説明します。" metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="OAuth" authors=""  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />



# メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションを Windows Azure の Web サイトに展開する

***執筆: [Rick Anderson](https://twitter.com/RickAndMSFT) および Tom Dykstra。更新日: 2013 年 10 月 18 日。***


<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/net/tutorials/web-site-with-sql-database/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/ja-jp/develop/net/tutorials/web-site-with-sql-database-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

このチュートリアルでは、ユーザーが Facebook や Google の資格情報を使用してログインできる、安全な ASP.NET MVC 5 Web アプリケーションを構築する方法を説明します。さらに、作成したアプリケーションを Windows Azure に展開する方法も学習します。

Windows Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2013 を持っていない場合は、SDK によって Visual Studio 2013 for Web Express が自動的にインストールされます。これで、Windows Azure 向けのアプリケーションを無料で開発できます。(Visual Studio 2012 を使用する場合は、[このチュートリアルの前のバージョン](/ja-jp/develop/net/tutorials/web-site-with-sql-database-vs2012/) を参照してください。)このバージョンのチュートリアルは、前のバージョンと比べてはるかに単純です。

このチュートリアルは、Windows Azure を使用した経験がない読者を対象に作成されています。このチュートリアルでは、安全なデータ主導型 Web アプリケーションを作成し、クラウド データベースを使用してクラウド上で実行します。

学習内容: 

* 安全な ASP.NET MVC 5 プロジェクトを作成して Windows Azure の Web サイトに発行する方法
* [OAuth](http://oauth.net/ "http://oauth.net/")、[OpenID](http://openid.net/)、および ASP.NET メンバーシップ データベースを使用してアプリケーションを保護する方法
* 新しいメンバーシップ API を使用してユーザーおよびロールを追加する方法
* SQL データベースを使用して Windows Azure にデータを保存する方法

ASP.NET MVC 5 に基づく、データベース アクセスに ADO.NET Entity Framework を使用する、簡単な連絡先リスト Web アプリケーションをビルドします。次の図は、完成したアプリケーションのログイン ページです。

![ログイン ページ][rxb]

<div class="dev-callout"><p><strong>注</strong> このチュートリアルを完了するには、Windows Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">Windows Azure の無料評価版サイト</a>を参照してください。</p></div>


このチュートリアルの内容: 

- [開発環境を設定する](#setupdevenv)
- [Windows Azure 環境を設定する][setupwindowsazureenv]
- [ASP.NET MVC 5 アプリケーションを作成する][createapplication]
- [Windows Azure にアプリケーションを展開する][deployapp1]
- [アプリケーションにデータベースを追加する][adddb]
- [OAuth プロバイダーを追加する][]
- [メンバーシップ API を使用する][]
- [Windows Azure にアプリケーションを展開する][deployapp11]
- [次のステップ][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]


<h2><a name="bkmk_setupwindowsazure"></a>Windows Azure 環境を設定する</h2>

次のステップでは、Windows Azure の Web サイトと SQL データベースを作成することで Windows Azure 環境をセットアップします。

### Windows Azure で Web サイトと SQL データベースを作成する

Windows Azure の Web サイトは、共有ホスティング環境で実行されます。つまり、他の Windows Azure クライアントと共有する仮想マシン (VM) 上で実行されます。共有ホスティング環境は、低コストでクラウドの利用を開始できる方法です。後で Web トラフィックが増加したら、アプリケーションの規模を変更して専用 VM 上で実行するように設定してニーズを満たすことができます。もっと複雑なアーキテクチャが必要な場合は、Windows Azure のクラウド サービスに移行できます。クラウド サービスは専用 VM 上で実行され、ユーザーのニーズに応じて構成できます。

Windows Azure SQL データベースは、SQL Server テクノロジに基づいて構築されたクラウドベースのリレーショナル データベース サービスです。SQL Server で動作するツールおよびアプリケーションは、SQL データベースでも動作します。

1. [Windows Azure の管理ポータル](https://manage.windowsazure.com)で、左側のタブにある **[Web サイト]** をクリックし、**[新規]** をクリックします。

	![管理ポータルの新しいボタン][rxWSnew]

1. **[Web サイト]**、**[カスタム作成]** の順にクリックします。

	![管理ポータルの [データベースとともに作成] リンク][rxCreateWSwithDB]

	**新しい Web サイト - カスタム作成**ウィザードが開きます。

1. ウィザードの **[Web サイトの作成]** 手順で、アプリケーションの一意の URL として使用する文字列を **[URL]** ボックスに入力します。ここに入力した文字列と、このテキスト ボックスの右側に表示されている文字列を組み合わせたものが実際の URL になります。図には URL が表示されますが、その URL は既に取得されている可能性が高いため、別の URL の選択が必要になります。

	![管理ポータルの [データベースとともに作成] リンク][rr1]

1.* *[データベース]** ボックスの一覧の **[無料の 20 MB SQL データベースの作成]** を選択します。

1. **[リージョン]** ボックスの一覧で、Web サイトに選択したリージョンと同じリージョンを選択します。
この設定によって、使用する VM が実行されるデータ センターが指定されます。
1. **[DB 接続文字列名]** ボックスは、既定値の *DefaultConnection* をそのまま使用します。
1. ボックスの下部にある右矢印をクリックします。
ウィザードの **[データベースの設定の指定]** 手順に進みます。

1. **[名前]** ボックスに「*ContactDB*」と入力します (下図を参照)。
1. **[サーバー]** ボックスで、**[新しい SQL データベース サーバー]** を選択します。(下図を参照)。または、以前に SQL Server データベースを作成した場合は、ボックスの一覧からその SQL Server を選択できます。
1.**[リージョン]** は、作成した Web サイトと同じエリアに設定します。
1. 管理者の**ログイン名**と**パスワード**を入力します。**[新しい SQL データベース サーバー]** を選択した場合は、既存の名前とパスワードではなく、このデータベースへのアクセス時に使用する新しい名前とパスワードを入力してください。以前に作成した SQL Server を選択した場合は、その SQL Server の作成時に設定したパスワードを入力します。このチュートリアルでは、**[データベースの詳細設定を構成します]** チェック ボックスをオンにしません。無料の DB の場合、設定できるのは照合順序のみです。
1. 終了したら、ダイアログ ボックスの右下にあるチェック マークをクリックします。

	![新しい Web サイト - データベースとともに作成ウィザードの [データベースの設定] 手順][setup007]
	
	次の画像では、既存の SQL Server を選択した場合のログインを示しています。

	![新しい Web サイト - データベースとともに作成ウィザードの [データベースの設定] 手順][rxPrevDB]

	管理ポータルが [Web サイト] ページに戻り、**[状態]** 列にサイトが作成中であることが示されます。しばらくすると (通常は 1 分未満)、サイトの作成に成功したことが **[状態]** 列に示されます。左側にあるナビゲーション バーでは、アカウントで所有するサイト数が **[Web サイト]** アイコンの横に表示され、データベース数が **[SQL データベース]** アイコンの横に表示されます。

<h2><a name="bkmk_createmvc4app"></a>ASP.NET MVC 5 アプリケーションを作成する</h2>

Windows Azure の Web サイトを作成しましたが、まだその中にコンテンツがありません。次のステップでは、Windows Azure に発行する Visual Studio Web アプリケーションを作成します。

### プロジェクトを作成する

2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。

   ![[ファイル] メニューの [新しいプロジェクト]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

3. **[新しいプロジェクト]** ダイアログ ボックスで、**[インストールされているテンプレート]** の下にある **C#** を展開して **[Web]** を選択し、**[ASP.NET Web アプリケーション]** を選択します。


4. アプリケーションに「**ContactManager**」という名前を付けて、**[OK]** をクリックします。

   ![[新しいプロジェクト] ダイアログ ボックス](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
   **注:** この画像では、名前が "MyExample" となっていますが、必ず「ContactManager」と入力してください。これからコピーするコード ブロックは、ContactManager というプロジェクト名が前提となっています。

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** テンプレートを選択し、**[認証の変更]** をクリックします。

   ![[新しい ASP.NET プロジェクト] ダイアログ ボックス](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13changeauth.png)

6. **[認証の変更]** ダイアログ ボックスは、既定値の **[個々のユーザー アカウント]** をそのまま使用します。

   ダイアログには、**[個々のユーザー アカウント]** は、ユーザー プロファイルを SQL データベースに保存するアプリケーション用であると説明されています。この場合ユーザーは、Facebook、Twitter、Google の既存のアカウントを使って登録することができます。他の認証オプションについては、[Visual Studio 2013 の ASP.NET Web プロジェクトの認証方法に関するページ](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#auth)を参照してください。

7. **[OK]** をクリックします。

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで **[OK]** をクリックします。

     ![[新しい ASP.NET プロジェクト] ダイアログ ボックス](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13changeauth.png)


### ページのヘッダーとフッターを設定する


1. **ソリューション エクスプローラー**の *Views\Shared* フォルダーにある *Layout.cshtml* ファイルを開きます。
	![ソリューション エクスプローラーに表示されている _Layout.cshtml ファイル][newapp004]
1. "My ASP.NET MVC Application" となっている箇所 (2 か所) を「Contact Manager」に書き換えます。
1. "Application name" を「CM Demo」に書き換えます。
2. *Cm* コントローラーを使用するため、1 つ目のアクション リンクを更新します。*Home* を *Cm* に書き換えてください。

![コードの変更](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### ローカルでアプリケーションを実行する

1. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

	アプリケーションのホーム ページが既定のブラウザーに表示されます。

![ローカルで動作中の Web サイト](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

これで、Windows Azure に展開するアプリケーションを作成するために必要な操作が完了しました。データベース機能は後で追加します。

<h2><a name="bkmk_deploytowindowsazure1"></a>Windows Azure にアプリケーションを展開する</h2>

5. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

   ![プロジェクトのコンテキスト メニューの [発行]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   **Web の発行**ウィザードが開きます。

6. **Web の発行**ウィザードの **[プロファイル]** タブで、**[インポート]** をクリックします。

   ![発行設定のインポート][ImportPublishSettings]

   **[発行プロファイルのインポート]** ダイアログ ボックスが表示されます。

5. 次のいずれかの方法で、Visual Studio から Windows Azure アカウントへの接続を有効にします。

   
	***[サインイン]** をクリックし、Windows Azure アカウントの資格情報を入力します。

		これはすばやく簡単に実行できる方法ですが、この方法を使用した場合、**[サーバー エクスプローラー]** ウィンドウで Windows Azure SQL データベースやモバイル サービスを確認することができません。

	* アカウントへのアクセスを可能にする管理証明書をインストールするには、**[サブスクリプションの管理]** をクリックします。

		**[Windows Azure サブスクリプションの管理]** ダイアログ ボックスで、**[証明書]** タブをクリックし、**[インポート]** をクリックします。操作手順に従い、Windows Azure アカウント用のサブスクリプション ファイル (*.publishsettings* ファイル) をダウンロードしてインポートします。

		> [WACOM.NOTE]
		> サブスクリプション ファイルをソース コード ディレクトリの外にあるフォルダー (Downloads フォルダーなど) にダウンロードし、インポートが完了したらそのファイルを削除します。これは、悪意のあるユーザーがサブスクリプション ファイルへのアクセス許可を取得すると、Windows Azure サービスを編集、作成、削除できるためです。

		詳細については、[Visual Studio から Windows Azure にアクセスする方法](http://go.microsoft.com/fwlink/?LinkId=324796)に関するページを参照してください。

7. **[発行プロファイルのインポート]** ダイアログ ボックスで、ドロップダウン リストから Web サイトを選択し、**[OK]** をクリックします。

![発行プロファイルのインポート](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs4.png)

1. **[Web の発行]** ダイアログ ボックスの **[発行]** をクリックします。

	![発行](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	これで、作成したアプリケーションはクラウドで実行されています。このアプリケーションを次に展開するときは、変更したファイル (または新しいファイル) のみが展開されます。

<h2><a name="bkmk_addadatabase"></a>アプリケーションにデータベースを追加する</h2>

次に、MVC アプリケーションを更新して、連絡先を表示および更新してデータをデータベースに保存する機能を追加します。アプリケーションでは、データベースの作成およびデータベース内のデータの読み取りと更新に Entity Framework を使用します。

### 連絡先のデータ モデル クラスを追加する

まず、コードで単純なデータ モデルを作成します。

1. **ソリューション エクスプローラー**で、Models フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。

![Models フォルダーのコンテキスト メニューの [クラスの追加]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. **[新しい項目の追加]** ダイアログ ボックスで、新しいクラス ファイルに「*Contact.cs*」という名前を付け、[**追加**] をクリックします。

![[新しい項目の追加] ダイアログ ボックス][adddb002]

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

<h2><a name="bkmk_addcontroller"></a>データのコントローラーとビューを追加する</h2>

1. プロジェクトをビルドします ****(Ctrl + Shift + B)。(スキャフォールディング機能の使用前にプロジェクトをビルドする必要があります。)
1. **ソリューション エクスプローラー**で、Controllers フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。

	![Controllers フォルダーのコンテキスト メニューの [コントローラーの追加]][addcode001]

5. **[スキャフォールディングの追加]** ダイアログ ボックスで、**[MVC 5 コントローラーとビュー、EF を使用]** を選択し、**[追加]** をクリックします。
	
![[スキャフォールディングの追加] ダイアログ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)

5. **[コントローラーの追加]** ダイアログ ボックスで、コントローラー名として「CmController」と入力します (下図を参照)。
1. **[モデル クラス]** ボックスで **Contact (ContactManager.Models)** を選択します。
1. **[データ コンテキスト クラス]** で **ApplicationDbContext (ContactManager.Models)** を選択します。この **ApplicationDbContext** が、メンバーシップ DB と連絡先データの両方に使用されます。

![[新しいデータ コンテキスト] ダイアログ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrCtx.png)

1. **[追加]** をクリックします。

   Visual Studio によって、**Contact** オブジェクトの CRUD データベース操作に対応したコントローラー メソッドとビューが作成されます。

## Migrations の有効化、データベースの作成、サンプル データとデータ初期化子の追加 ##

次の作業では、作成したデータ モデルに基づいてデータベースを作成するために、[Code First Migrations](http://msdn.microsoft.com/library/hh770484.aspx) 機能を有効にします。

1. **[ツール]** メニューの **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。
	![[ツール] メニューの [パッケージ マネージャー コンソール]][addcode008]
2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		enable-migrations
	**enable-migrations** コマンドによって *Migrations* フォルダーが作成され、そのフォルダーに *Configuration.cs* ファイルが保存されます。このファイルを編集して、データベースの初期データ投入を行い、Migration を構成できます。

2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		add-migration Initial


	 **add-migration Initial** コマンドは、データベースを作成する **&lt;date_stamp&gt;Initial** ファイルを *Migrations* フォルダーに生成します。最初のパラメーター (**Initial**) は任意であり、このファイルの名前の作成に使用されます。新しいクラス ファイルは**ソリューション エクスプローラー**で表示できます。
	**Initial** クラスでは、**Up** メソッドを使用して Contacts テーブルを作成し、**Down** メソッドを使用してそのテーブルを削除します (前の状態に戻します)。
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

	![パッケージ マネージャー コンソールのコマンド][addcode009]

	**update-database** によって、データベースを作成する最初の Migration が実行されます。既定では、データベースは SQL Server Express LocalDB データベースとして作成されます 

7. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行し、[CM Demo] リンクをクリックします (または、http://localhost:(port#)/Cm に移動します)。

アプリケーションでは、登録されたデータが表示され、編集、詳細、削除のリンクが示されます。データの作成、編集、削除、表示を行うことができます。

![データの MVC ビュー][rx2]

<h2><a name="addOauth"></a><span class="short-header">OAuth</span>OAuth2 および OpenID プロバイダーを追加する</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") は、Web、モバイル、およびデスクトップのアプリケーションからシンプルで標準的な方法で安全に認証するためのオープン プロトコルです。ASP.NET MVC インターネット テンプレートは OAuth および [OpenID](http://openid.net/) を使用して、Facebook、Twitter、Google、Microsoft を認証プロバイダーとしてサポートします。このチュートリアルでは Google のみを認証プロバイダーとして使用しますが、コードを少し変更すれば他のプロバイダーも使用できます。他のプロバイダーを実装する手順は、このチュートリアルで説明する手順とほとんど同じです。Facebook を認証プロバイダーとして使用する方法については、Facebook や Google の OAuth2/OpenID サインオンを使用した ASP.NET MVC 5 アプリケーションの作成について執筆した[拙著のチュートリアル](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)を参照してください。

このチュートリアルでは、認証の他にロールを使用して権限を付与します。*canEdit* ロールに追加したユーザーのみがデータを変更 (連絡先を作成、編集、削除) できます。

1. *App_Start\Startup.Auth.cs* ファイルを開きます。*app.UseGoogleAuthentication()* メソッドからコメント文字を削除します。

1. アプリケーションを実行し、**[ログイン]** リンクをクリックします。
1. **[Use another service to log in]** の **[Google]** をクリックします。
1. 資格情報を入力します。
1. **[Accept]** をクリックして、電子メールや基本的な情報へのアクセスをアプリケーションに許可します。
1. 登録ページにリダイレクトされます。**ユーザー名**は必要に応じて変更できます。**[登録]** をクリックします。

![登録](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr8.png)

<h2><a name="mbrDB"></a><span class="short-header">メンバーシップ DB</span>メンバーシップ API を使用する</h2>
このセクションでは、ローカル ユーザーと *canEdit* ロールをメンバーシップ データベースに追加します。*canEdit* ロールのユーザーのみがデータを編集することができます。実行可能な操作に基づいてロール名を付けるのが望ましいので、この場合は *admin* より *canEdit* が適しています。その後、必要に応じて、(わかりにくい *superAdmin* ではなく) *canDeleteMembers* などの新しいロールを追加します。

1. *migrations\configuration.cs* ファイルを開き、次の 'using' ステートメントを追加します。

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
               UserName = "user1",
            };
            ir = um.Create(user, "Passw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

2. 新しいメソッドを **Seed** メソッドから呼び出します。

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            AddUserAndRole(context);
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }

   このコードでは、*canEdit* という名前の新しいロールを作成し、新しいローカル ユーザー *user1* を作成して、*user1* を *canEdit* ロールに追加します。

## 一時的なコードを使用して新しいソーシャル ログイン ユーザーを canEdit ロールに追加する##
このセクションでは、Account コントローラーの **ExternalLoginConfirmation** メソッドを一時的に変更して、*canEdit* ロールに新しいユーザーを追加し、OAuth または OpenID プロバイダーに登録します。ここで一時的に **ExternalLoginConfirmation** メソッドを変更し、新しいユーザーが管理者ロールに自動的に追加されるようにします。ロールの追加と管理に必要なツールの準備が整うまでの間、以下に示した一時的な自動登録コードを使用することになります。将来的には、ユーザー アカウントとロールを作成したり編集したりするための、[WSAT](http://msdn.microsoft.com/ja-jp/library/ms228053(v=vs.90) と同様のツールを提供できればと思います。**サーバー エクスプローラー**を使用してユーザーをロールに追加する方法については、このチュートリアルの中で紹介します。

1. **Controllers\AccountController.cs** ファイルを開き、**ExternalLoginConfirmation** メソッドに移動します。
1. 次の **AddToRoleAsync** への呼び出しを **SignInAsync** の呼び出しの直前に追加します。

                await UserManager.AddToRoleAsync(user.Id, "CanEdit");

   このコードは、新しく登録したユーザーを "CanEdit" ロールに追加します。これにより、データの変更 (編集) を伴うアクション メソッドへのアクセス権がユーザーに割り当てられます。以下の図は、変更後のコードを示しています。

   ![コード](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr9.png)

このチュートリアルの中で、アプリケーションを Windows Azure に展開します。そこでのログオンには、Google などサード パーティの認証プロバイダーが使用されます。新しく登録されたアカウントは、*canEdit* ロールに追加されます。サイトの URL と Google ID さえあればだれでも登録し、データベースを更新することができます。そのような操作が第三者によって行われるのを防ぐためには、サイトを停止する必要があります。だれが *canEdit* ロールに追加されているかは、データベースを調べることによって確認できます。

**[パッケージ マネージャー コンソール]** で上方向キーを押し、次のコマンドを呼び出します。

		Update-Database

**Update-Database** コマンドを実行すると、**Seed** メソッドが実行され、先ほど追加した **AddUserAndRole** が実行されます。**AddUserAndRole** は *user1* というユーザーを作成し、*canEdit* ロールに追加します。

## SSL と Authorize 属性を使用してアプリケーションを保護する##

このセクションでは、[Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) 属性を適用してアクション メソッドへのアクセスを制限します。匿名ユーザーが表示できるのは、home コントローラーの **Index** アクション メソッドだけになります。登録ユーザーは、連絡先データ (Cm コントローラーの **[Index]** ページと **[Details]** ページ)、[About] ページ、[Contact] ページを表示することができます。*canEdit* ロールを与えられているユーザーのみがアクション メソッドを実行してデータを変更できます。

1. [Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) フィルターと [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) フィルターをアプリケーションに追加します。[Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) 属性と [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) 属性をコントローラーごとに追加する方法もありますが、セキュリティ上の理由から、通常はこれらをアプリケーション全体に適用します。アプリケーション全体に適用すれば、新しいコントローラーやアクション メソッドを追加したとき、それらが自動的に保護されます。ユーザー自身で適用する必要がありません。詳細については、[ASP.NET MVC 4 アプリケーションの保護と新しい AllowAnonymous 属性に関するページ](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)を参照してください。*App_Start\FilterConfig.cs* ファイルを開き、*RegisterGlobalFilters* メソッドを次のように書き換えます (2 つのフィルターを追加)。

        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }

   このコードで適用した [Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) フィルターによって、匿名ユーザーは、アプリケーション内のメソッドに一切アクセスできなくなります。2 つのメソッドについては、[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を使用して承認要件を免除し、匿名ユーザーがログインしてホーム ページを表示できるようにします。[RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) により、Web アプリケーションに対するすべてのアクセスは HTTPS に限定されます。

1. [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)AllowAnonymous 属性を Home コントローラーの **Index** メソッドに追加します。[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を使用すれば、特定のメソッドを認証不要として指定できます。以下に示したのは、HomeController からのコードの抜粋です。	

         namespace ContactManager.Controllers
         {
            public class HomeController : Controller
            {
               [AllowAnonymous]
               public ActionResult Index()
               {
                  return View();
               }

2. グローバルに検索すると、Account コントローラーのログイン メソッドや登録メソッドで *AllowAnonymous* が使われていることがわかります。
1. *CmController.cs* で、*Cm* コントローラーでデータの変更 (Create、Edit、Delete、つまり、Index と Details を除くすべてのアクション メソッド) を伴う HttpGet メソッドと HttpPost メソッドに '[Authorize(Roles = "canEdit")]`' を追加します。追加後のコードは次のようになります。

   ![コードの画像](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)

## プロジェクトに対して SSL を有効にする##

1. SSL を有効にします。ソリューション エクスプローラーで **ContactManager** プロジェクトをクリックし、F4 キーを押します。プロパティ ダイアログ ボックスが表示されます。**[SSL Enabled]** を True に変更します。**[SSL URL]** をコピーします。過去に SSL Web サイトを作成したことがなければ、SSL URL は https://localhost:44300/ になります。

	![SSL を有効にする][rxSSL]
 
1. ソリューション エクスプローラーで **Contact Manager** プロジェクトを右クリックし、**[プロパティ]** をクリックします。
1. 左側のタブで **[Web]** をクリックします。
1. **SSL URL** を使用するように **[プロジェクト URL]** を変更し、ページを保存します (Ctrl + S)。

	![SSL を有効にする](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。ブラウザーに証明書の警告が表示されます。このアプリケーションでは、**[このサイトの閲覧を続行する]** をクリックしてかまいません。
 
	![証明書の警告][rxNOT]


	![証明書の警告][rxNOT2]
 
   既定のブラウザーに、home コントローラーの **Index** ページが表示されます。

1. まだ前のセッションにログインしている場合は、**ログアウト** リンクをクリックします。
1. **[About]** または **[Contact]** リンクをクリックします。匿名ユーザーはこれらのページを表示できないため、ログイン ページにリダイレクトされます。
1. **[Register]** リンクをクリックして、*Joe* というローカル ユーザーを追加します。*Joe* であれば Home ページ、About ページ、Contact ページを表示できることを確認します。
1. *[CM Demo]* リンクをクリックし、データが表示されることを確認します。
1. このページ上の編集リンクをクリックすると、ログイン ページにリダイレクトされます (新しいローカル ユーザーが *canEdit* ロールに追加されていないため)。
1. ユーザー名 *user1*、パスワード "Passw0rd1" ("word" の "0" はゼロ) でログインします。先ほど選択した編集ページにリダイレクトされます。

   このアカウントとパスワードでログインできない場合は、ソース コードからパスワードをコピーして貼り付けてみてください。それでもログインできない場合は、**AspNetUsers** テーブルを見て、*user1* が追加されていることを確認します。**AspNetUsers** テーブルの確認方法については、後でチュートリアルの中で紹介します。
1. データを変更できることを確認します。

<h2><a name="bkmk_deploytowindowsazure11"></a>Windows Azure にアプリケーションを展開する</h2>

1. アプリケーションをビルドします。
1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![プロジェクトのコンテキスト メニューの [発行]][firsdeploy003]

**Web の発行**ウィザードが開きます。

1. **[設定]** タブをクリックします。**v** アイコンをクリックして **ApplicationDbContext** の **[リモート接続文字列]** を選択し、**[ContactDB]** を選択します。

   (発行プロファイルの作成後、Visual Studio を再起動した場合、接続文字列がドロップダウン リストに表示されない場合があります。その場合は、先ほど作成した発行プロファイルを編集する代わりに、先ほどと同じ方法で新しい発行プロファイルを作成し、以降の **[設定]** タブの手順に従ってください。)

	![設定](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. **ContactManagerContext** で、**[Code First Migrations を実行する]** を選択します。

![設定](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. **[発行]** をクリックします。

1. *user1* でログインし、データを編集できることを確認します。

1. ログアウトします。

2. Google または Facebook を使用してログインします。これにより、Google または Facebook のアカウントが **canEdit** ロールに追加されます。

### Web サイトを停止して第三者の登録を防ぐ

1. **サーバー エクスプローラー**で **[Web サイト]** に移動します。
4. 各 Web サイトのインスタンスを右クリックし、**[Web サイトの停止]** を選択します。

	![Web サイトの停止](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png)

Windows Azure の管理ポータルから Web サイトを選択し、ページ下部の **[停止]** アイコンをクリックする方法もあります。

![Web サイトの停止](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### AddToRoleAsync の削除、発行、テスト

1. Account コントローラーの **ExternalLoginConfirmation** メソッドから次のコードを削除します。
                `await UserManager.AddToRoleAsync(user.Id, "CanEdit");`
1. プロジェクトをビルドします。これにより、ファイルの変更が保存され、コンパイル エラーがないか確認が行われます。
5. **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[発行]** をクリックします。

	   ![プロジェクトのコンテキスト メニューの [発行]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. **[プレビューの開始]** をクリックします。更新する必要のあるファイルだけが展開されます。
5. Web サイトを開始します。これはポータルから行うのが最も簡単です。**Web サイトが停止した状態で発行することはできません**。
5. Visual Studio に戻り、**[発行]** をクリックします。
3. Windows Azure アプリケーションが既定のブラウザーで起動します。匿名ユーザーとしてホーム ページが表示されます。
4. **[About]** リンクをクリックします。ログイン ページにリダイレクトされます。
5. ログイン ページの **[登録]** リンクをクリックし、ローカル アカウントを作成します。読み取り専用ページにはアクセスできるが、データの変更を伴うページにはアクセスできない (*canEdit* ロールによって保護されている) ことをこのローカル アカウントを使用して確認します。ローカル アカウント アクセスは、後でこのチュートリアルの中で削除します。
<!--
1. Log out of the local user account and log in with the Google account you previously registered with. Verify you can edit data. 
-->

![ログオフ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr6.png)

1. *About* ページと *Contact* ページにアクセスできることを確認します。

![ログオフ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr7.png)

1. **[CM Demo]** リンクをクリックして **Cm** コントローラーに移動します。URL に *Cm* を付加することによって移動してもかまいません。

![CM ページ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. [編集] リンクをクリックします。ログイン ページにリダイレクトされます。**[Use another service to log in]** で [Google] または [Facebook] をクリックし、過去に登録したアカウントでログインします。
2. そのアカウントにログインした状態でデータを編集できることを確認します。
 	**注:** このアプリケーションで Google からログアウトし、同じブラウザーで別の google アカウントにログインすることはできません。1 つのブラウザーを使用している場合は、Google に移動してログアウトする必要があります。同じサード パーティの認証システム (Google など) に属している別のアカウントでログオンするためには、異なるブラウザーを使用する必要があります。


## SQL Azure DB を調べる##

1. **サーバー エクスプローラー**で **[ContactDB]** に移動します。
2. **[ContactDB]** を右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** を選択します。
 
![[SQL Server オブジェクト エクスプローラーで開く]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)

 
**注:** **[SQL データベース]** を展開できず、**ContactDB** を Visual Studio から表示*できない*場合は、以下の手順に従い、特定のファイアウォール ポート (またはポート範囲) を開放する必要があります。**使用できる IP アドレスの範囲を追加する手順**と **SSOX から SQL Azure データベースに接続する手順**に従ってください。ファイアウォール ルールの追加後、データベースに接続できるようになるまでに数分かかる場合があります。
 
1. **AspNetUsers** テーブルを右クリックし、**[データの表示]** を選択します。

![CM ページ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. **canEdit** ロールへの追加用として登録した Google アカウントの ID と *user1* の ID をメモします。それ以外のユーザーは **canEdit** ロールに含めないようにする必要があります。この点については、次のステップで確認します。

![CM ページ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. **SQL Server オブジェクト エクスプローラー**で **[AspNetUserRoles]** を右クリックし、**[データの表示]** を選択します。

![CM ページ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
CM ページ **UserId** が、登録した Google アカウントおよび *user1* のものであることを確認します。


## サーバーに接続できない場合##

このセクションの手順は、"サーバーを開けません" という内容のエラー ダイアログが表示された場合にのみ適用します。
	![ファイアウォール エラー][rx5]

使用できる IP アドレスに、目的の IP アドレスを追加する必要があります。

1. Windows Azure ポータルの左側のタブで **[SQL データベース]** を選択します。
	![[SQL] を選択します。][rx6]

1. 接続するデータベースを選択します。
1. **[この IP アドレス用に Windows Azure ファイアウォール ルールを設定する]** リンクをクリックします。

	![ファイアウォール ルール][rx7]

1. [現在の IP アドレス xxx.xxx.xxx.xxx は既存のファイアウォール ルールに含まれていません。ファイアウォール ルールを更新しますか?] というメッセージが表示されたら、**[はい]** をクリックします。企業ファイアウォールによっては、このアドレスだけでは不十分な場合もあります。そのような場合は、IP アドレスの範囲を追加する必要があります。

次に、使用できる一連の IP アドレスを追加します。

1. Windows Azure ポータルで **[SQL データベース]** をクリックします。
1. データベースをホストする **[サーバー]** をクリックします。

	![データベース サーバー][rx8]

1. ページの上部にある **[構成]** リンクをクリックします。
1. ルール名、開始 IP アドレス、終了 IP アドレスを追加します。

	![IP 範囲][rx9]

1. ページの下部にある **[保存]** をクリックします。
1. ご意見やご感想、また接続先となる一連の IP アドレスを追加する必要がある場合は、お知らせください。

最後に、SQL データベース インスタンスに SSOX から接続します。

1. [表示] メニューの **[SQL Server オブジェクト エクスプローラー]** をクリックします。
1. **[SQL Server]** を右クリックし、**[SQL Server の追加]** を選択します。
1. **[サーバーへの接続]** ダイアログ ボックスで、**[認証]** を **[SQL Server 認証]** に設定します。**[サーバー名]** と **[ログイン]** は Windows Azure ポータルから取得されます。
1. ブラウザーでポータルに移動し、**[SQL データベース]** を選択します。
1. **[ContactDB]** を選択し、**[SQL データベース接続文字列を表示する]** をクリックします。
1. **[接続文字列]** ページから **[サーバー]** と **[ユーザー ID]** をコピーします。
1. **[サーバー]** と **[ユーザー ID]** の値を Visual Studio の **[サーバーへの接続]** ダイアログに貼り付けます。**[ユーザー ID]** の値が **[ログイン]** エントリに入ります。SQL DB の作成時に使用したパスワードを入力します。

![[サーバーへの接続] ダイアログ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

これで、先ほど説明した手順に従い、Contact データベースに移動できます。


## データベース テーブルを編集して canEdit ロールにユーザーを追加するには

先ほどは、コードを使用してユーザーを canEdit ロールに追加しました。それ以外にも、メンバーシップ テーブル内のデータを直接操作する方法もあります。以下の手順では、その方法を使用してロールにユーザーを追加します。

2. **SQL Server オブジェクト エクスプローラー**で **[AspNetUserRoles]** を右クリックし、**[データの表示]** を選択します。

![CM ページ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. *RoleId* をコピーして空の (新しい) 行に貼り付けます。
	
![CM ページ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. **AspNetUsers** テーブルから、ロールに追加するユーザーを探してその *Id* をコピーし、**AspNetUserRoles** テーブルの **[UserId]** 列に貼り付けます。

もっと簡単にユーザーとロールを管理できるように現在ツールを作成しています。

## ローカル登録の考慮事項##

このプロジェクトの現在の ASP.NET メンバーシップ登録はパスワードのリセットをサポートしておらず、人間による登録を検証しません ([CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership) など)。いずれかのサードパーティ プロバイダーを使用してユーザーを認証すると、登録できるようになります。ローカル登録を無効にする場合は、次のステップを実行します。


1. AccountController で、GET および POST *Register* メソッドから *[[AllowAnonymous]]* 属性を削除します。これによって、ボットや匿名ユーザーが登録されるのを防ぎます。
1. *Views\Shared* フォルダーの *_LoginPartial.cshtml* ファイルから登録リンクを削除します。
2. *Views\Account\Login.cshtml* ファイルから登録リンクを削除します。
2. アプリケーションを展開します。


<h2><a name="nextsteps"></a><span class="short-header">次のステップ</span>次のステップ</h2>

ユーザー登録データベースにプロファイル データを追加する方法や、Facebook を認証プロバイダーとして使用する詳細な手順については、[Facebook や Google の OAuth2/OpenID サインオンを使用した ASP.NET MVC 5 アプリケーションの作成について執筆した拙著のチュートリアル](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on )を参考にしてください。


ASP.NET MVC については、[ASP.NET MVC 5 の基本について執筆したチュートリアル](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)をぜひご覧ください。Entity Framework のプログラミングについては、Tom Dykstra が [Entity Framework と MVC について取り上げたこちらのページ](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)に、さらに詳しく解説されています。

このチュートリアルとサンプル アプリケーションは、Tom Dykstra と Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) の協力の下、[Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) が執筆しました。

役に立った内容や改善点など、皆様からのご意見をお寄せください。このチュートリアルに関してだけでなく、ここで紹介した製品に関するご意見やご要望もお待ちしております。お寄せいただいたご意見は、今後の改善に役立たせていただきます。

<!--
To get the colorful Facebook, Google and Yahoo log on buttons, see the blog post [Customizing External Login Buttons in ASP.NET MVC 5](http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/). 
 -->
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




<!-- links -->






















<!-- links from Tom's hopefully no collisions -->





















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









[Windows Azure の Web サイトでの ASP.NET に関する重要な情報]: #aspnetwindowsazureinfo
[次のステップ]: #nextsteps



[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
























