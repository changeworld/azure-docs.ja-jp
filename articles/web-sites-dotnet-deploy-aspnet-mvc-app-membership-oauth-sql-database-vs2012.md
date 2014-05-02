<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />




# メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションを Azure の Web サイトに展開する

***執筆: [Rick Anderson](https://twitter.com/RickAndMSFT) および Tom Dykstra。更新日: 2013 年 10 月 15 日。***


<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/net/tutorials/web-site-with-sql-database/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/ja-jp/develop/net/tutorials/web-site-with-sql-database-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>注</strong><p><a href="/ja-jp/develop/net/tutorials/web-site-with-sql-database/">このチュートリアルの新しいバージョン</a>が提供されています。Visual Studio 2012 を使用する場合はこのバージョンを引き続き利用できますが、新しいバージョンの方が格段に取り組みやすい内容となっています。</p></div>

このチュートリアルでは、ユーザーが Facebook、Yahoo、Google の資格情報を使用してログインできる、安全な ASP.NET MVC 4 Web アプリケーションを構築する方法について説明します。さらに、作成したアプリケーションを Azure に展開する方法についても学習します。

Azure アカウントは無料で開くことができます。また、まだ Visual Studio 2012 を持っていない場合は、SDK によって Visual Studio 2012 for Web Express が自動的にインストールされます。これで、Azure 向けのアプリケーションを無料で開発できます。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルでは、安全なデータ主導型 Web アプリケーションを作成し、クラウド データベースを使用してクラウド上で実行します。

学習内容: 

* Azure SDK をインストールして、Azure 向け開発用にコンピューターを準備する方法
* 安全な ASP.NET MVC 4 プロジェクトを作成して Azure の Web サイトに発行する方法
* OAuth および ASP.NET メンバーシップ データベースを使用してアプリケーションを保護する方法
* メンバーシップ データベースを Azure に展開する方法
* SQL データベースを使用して Azure にデータを保存する方法
* Visual Studio を使用して、メンバーシップ データベースを更新および管理する方法

ASP.NET MVC 4 に基づく、データベース アクセスに ADO.NET Entity Framework を使用する、簡単な連絡先リスト Web アプリケーションをビルドします。次の図は、完成したアプリケーションのログイン ページです。

![ログイン ページ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

このチュートリアルの内容: 

- [開発環境を設定する][setupdbenv]
- [Azure 環境を設定する][setupwindowsazureenv]
- [ASP.NET MVC 4 アプリケーションを作成する][createapplication]
- [Azure にアプリケーションを展開する][deployapp1]
- [アプリケーションにデータベースを追加する][adddb]
- [OAuth プロバイダーを追加する][]
- [メンバーシップ データベースにロールを追加する][]
- [データ展開スクリプトを作成する][]
- [Azure にアプリケーションを展開する][deployapp11]
- [メンバーシップ データベースを更新する][]
- [次のステップ][]

<h2><a name="bkmk_setupdevenv"></a>開発環境を設定する</h2>

最初に、.NET Framework 対応の Azure SDK をインストールして、開発環境を設定します 

1. Azure SDK for .NET をインストールするには、次のリンクをクリックします。Visual Studio 2012 をまだインストールしていない場合は、次のリンクをクリックするとインストールされます。このチュートリアルには、Visual Studio 2012 が必要です。
[Azure SDK for Visual Studio 2012]( http://go.microsoft.com/fwlink/?LinkId=254364)
1. インストール プログラムの実行または保存を求めるメッセージが表示されたら、**[実行]** をクリックします。
1. Web Platform Installer のウィンドウで、**[インストール]** をクリックし、インストールの手順を進めます。

![Web Platform Installer -  Azure SDK for .NET][WebPIAzureSdk20NetVS12]


インストールが完了すると、開発に必要なツールがすべて揃います。



<h2><a name="bkmk_setupwindowsazure"></a>Azure 環境を設定する</h2>

次のステップでは、Azure の Web サイトと SQL データベースを作成することで Azure 環境をセットアップします。

### Azure で Web サイトと SQL データベースを作成する

Azure の Web サイトは、共有ホスティング環境で実行されます。つまり、他の Azure クライアントと共有する仮想マシン (VM) 上で実行されます。共有ホスティング環境は、低コストでクラウドの利用を開始できる方法です。後で Web トラフィックが増加したら、アプリケーションの規模を変更して専用 VM 上で実行するように設定してニーズを満たすことができます。もっと複雑なアーキテクチャが必要な場合は、Azure のクラウド サービスに移行できます。クラウド サービスは専用 VM 上で実行され、ユーザーのニーズに応じて構成できます。

Azure SQL データベースは、SQL Server テクノロジに基づいて構築されたクラウドベースのリレーショナル データベース サービスです。SQL Server で動作するツールおよびアプリケーションは、SQL データベースでも動作します。

1. [Azure 管理ポータル](https://manage.windowsazure.com)で、左側のタブにある **[Web サイト]** をクリックし、**[新規]** をクリックします。

![管理ポータルの新しいボタン][rxWSnew]

2. **[カスタム作成]** をクリックします。

	![管理ポータルの [データベースとともに作成] リンク][rxCreateWSwithDB]

**新しい Web サイト - カスタム作成**ウィザードが開きます。

3. ウィザードの **[新しい Web サイトを作成する]** 手順で、アプリケーションの一意の URL として使用する文字列を **[URL]** ボックスに入力します。ここに入力した文字列と、このテキスト ボックスの右側に表示されている文字列を組み合わせたものが実際の URL になります。図には "contactmgr2" と表示されていますが、その URL は既に取得されている可能性が高いため、別の URL の選択が必要になります。

	![管理ポータルの [データベースとともに作成] リンク][rxCreateWSwithDB_2]

4. **[データベース]** ドロップダウン リストで、**[新しい SQL データベースを作成します]** を選択します。

5. **[リージョン]** ボックスの一覧で、Web サイトで選択したリージョンと同じリージョンを選択します。この設定では、どのデータ センターで VM を実行するかを指定します。**[DB 接続文字列名]** に「*connectionString1*」と入力します。

	![新しい Web サイト - データベースとともに作成ウィザードの [新しい Web サイトを作成する] 手順][rxCreateWSwithDB_2]

6. ボックスの下部にある右矢印をクリックします。ウィザードの **[データベースの設定]** 手順に進みます。

7. **[名前]** ボックスに「*ContactDB*」と入力します。

8. **[サーバー]** ボックスで、**[新しい SQL データベース サーバー]** を選択します。または、以前に SQL Server データベースを作成した場合は、ボックスの一覧からその SQL Server を選択できます。

9. 管理者の**ログイン名**と**パスワード**を入力します。**[新しい SQL データベース サーバー]** を選択した場合は、既存の名前とパスワードではなく、このデータベースへのアクセス時に使用する新しい名前とパスワードを入力してください。以前に作成した SQL Server を選択した場合は、その SQL Server の作成時に設定したパスワードを入力します。このチュートリアルでは、**[データベースの詳細設定を構成します]** チェック ボックスをオンにしません。**詳細設定**では、DB サイズ (既定値は 1 GB ですが 150 GB まで拡張可能) と照合順序を指定できます。

10. 終了したら、ダイアログ ボックスの下部にあるチェック マークをクリックします。
	
	![新しい Web サイト - データベースとともに作成ウィザードの [データベースの設定] 手順][setup007]
	
	次の画像では、既存の SQL Server を選択した場合のログインを示しています。
	![新しい Web サイト - データベースとともに作成ウィザードの [データベースの設定] 手順][rxPrevDB]

	管理ポータルが [Web サイト] ページに戻り、**[状態]** 列にサイトが作成中であることが示されます。しばらくすると (通常は 1 分未満)、サイトの作成に成功したことが **[状態]** 列に示されます。左側にあるナビゲーション バーでは、アカウントで所有するサイト数が **[Web サイト]** アイコンの横に表示され、データベース数が **[SQL データベース]** アイコンの横に表示されます。


<h2><a name="bkmk_createmvc4app"></a>ASP.NET MVC 4 アプリケーションを作成する</h2>

Azure の Web サイトを作成しましたが、まだその中にコンテンツがありません。次の手順では、Azure に発行する Visual Studio Web アプリケーション プロジェクトを作成します。

### プロジェクトを作成する

1. Visual Studio 2012 を起動します。
1. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3. **[新しいプロジェクト]** ダイアログ ボックスで、**[インストールされているテンプレート]** の下にある **[Visual C#]** を展開して **[Web]** を選択し、**[ASP.NET MVC 4 Web アプリケーション]** を選択します。既定の **[.NET Framework 4.5]** をそのまま使用します。アプリケーションに「**ContactManager**」という名前を付けて、**[OK]** をクリックします。

	![[新しいプロジェクト] ダイアログ ボックス][newapp002]

6. **[新しい ASP.NET MVC 4 プロジェクト]** ダイアログ ボックスで、**[インターネット アプリケーション]** テンプレートを選択します。**[ビュー エンジン]** ボックスの一覧で既定の [Razor] をそのまま使用し、**[OK]** をクリックします。

	![[新しい ASP.NET MVC 4 プロジェクト] ダイアログ ボックス][rxb2]

### ページのヘッダーとフッターを設定する


1. **ソリューション エクスプローラー**で、Views\Shared フォルダーを展開し、*_Layout.cshtml* ファイルを開きます。

	![_ソリューション エクスプローラーに表示されている Layout.cshtml ファイル][newapp004]

1. "My ASP.NET MVC Application" となっている箇所をすべて「Contact Manager」に書き換えます。
1. "your logo here" を「CM Demo」に書き換えます。



### ローカルでアプリケーションを実行する

1. Ctrl + F5 キーを押して、アプリケーションを実行します。アプリケーションのホーム ページが既定のブラウザーに表示されます。
![To Do List のホーム ページ][rxa]

これで、Azure に展開するアプリケーションを作成するために必要な操作が完了しました。データベース機能は後で追加します。

<h2><a name="bkmk_deploytowindowsazure1"></a>Azure にアプリケーションを展開する</h2>

1. ブラウザーで、[Azure 管理ポータル](http://manage.windowsazure.com "portal")を開きます。

2. **[Web サイト]** タブで、前の手順で作成したサイトの名前をクリックします。

	![管理ポータルの [Web サイト] タブにある Contact Manager アプリケーション][setup009]

3. ウィンドウの右側で、**[発行プロファイルのダウンロード]** をクリックします。

	![[クイック スタート] タブと [発行プロファイルのダウンロード] ボタン][firsdeploy001]

	この手順では、アプリケーションを Web サイトに展開するために必要な設定をすべて含むファイルがダウンロードされます。このファイルを Visual Studio にインポートすると、この情報を手動で入力する必要はありません。

4. Visual Studio からアクセスできるフォルダーに .*publishsettings* ファイルを保存します。

	![.publishsettings ファイルの保存][firsdeploy002]


	[WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

5. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![プロジェクトのコンテキスト メニューの [発行]][PublishVSSolution]
	
	**Web の発行**ウィザードが開きます。

6. **Web の発行**ウィザードの **[プロファイル]** タブで、**[インポート]** をクリックします。

	![発行設定のインポート][ImportPublishSettings]

	**[発行プロファイルのインポート]** ダイアログ ボックスが表示されます。

1. 以前に Visual Studio で Azure サブスクリプションを追加していない場合は、次の手順に従います。これらの手順で、**[Azure の Web サイトからインポート]** の下のドロップダウン リストに Web サイトが含まれるようにサブスクリプションを追加します。
    
	a. **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure サブスクリプションの追加]** をクリックします。

	![Azure サブスクリプションの追加](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png)
	
	b. **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[サブスクリプション ファイルのダウンロード]** をクリックします。
    
	![サブスクリプションのダウンロード](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)
	
	c. ブラウザー ウィンドウで、*.publishsettings* ファイルを保存します。
    
	![発行ファイルのダウンロード](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png)

	> [WACOM.NOTE]
	> publishsettings ファイルには、Azure のサブスクリプションとサービスの管理に使用される資格情報 (デコード済み) が保存されています。このファイルのセキュリティに関するベスト プラクティスは、このファイルをソース ディレクトリの外 (Libraries\Documents フォルダーなど) に一時的に保存し、インポートが完了したらそのファイルを削除することです。悪意のあるユーザーが .publishsettings ファイルへのアクセス許可を取得すると、Azure サービスを編集、作成、削除できるためです。
	
    
	d. **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[参照]** をクリックし、*.publishsettings* ファイルに移動します。
    
	![サブスクリプションのダウンロード](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png)
	
	e. **[インポート]** をクリックします。
    
	![インポート](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png)

7. **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure の Web サイトからインポート]** をクリックし、ドロップダウン リストから Web サイトを選択して **[OK]** をクリックします。

	![発行プロファイルのインポート][ImportPublishProfile]

	これで、作成したアプリケーションはクラウドで実行されています。このアプリケーションを次に展開するときは、変更したファイル (または新しいファイル) のみが展開されます。
	
	![Azure で実行されている連絡先リストのホーム ページ][newapp005]


<h2><a name="bkmk_addadatabase"></a>アプリケーションにデータベースを追加する</h2>

次に、MVC アプリケーションを更新して、連絡先を表示および更新してデータをデータベースに保存する機能を追加します。アプリケーションでは、データベースの作成およびデータベース内のデータの読み取りと更新に Entity Framework を使用します。

### 連絡先のデータ モデル クラスを追加する

まず、コードで単純なデータ モデルを作成します。

1. **ソリューション エクスプローラー**で、Models フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。

![Models フォルダーのコンテキスト メニューの [クラスの追加]][adddb001]

2. **[新しい項目の追加]** ダイアログ ボックスで、新しいクラス ファイルに「*Contact.cs*」という名前を付け、**[追加]** をクリックします。

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

1.プロジェクトをビルドします **(Ctrl + Shift + B)**。(スキャフォールディング機能の使用前にプロジェクトをビルドする必要があります。)
1. **ソリューション エクスプローラー**で、Controllers フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。

	[Controllers フォルダーのコンテキスト メニューの [コントローラーの追加]][addcode001]

5.**[コントローラーの追加]** ダイアログ ボックスで、コントローラー名として「HomeController」と入力します。
1. **[スキャフォールディングのオプション]** の [テンプレート] で **[Entity Framework を使用した、読み取り/書き込み操作とビューのある MVC コントローラー]** を選択します。
6. モデル クラスとして **Contact** を、データ コンテキスト クラスとして [**&lt;新しいデータ コンテキスト...&gt;**] を選択します。

	[[コントローラーの追加] ダイアログ ボックス][addcode002]

7. **[新しいデータ コンテキスト]** ダイアログ ボックスで、既定値の *ContactManager.Models.ContactManagerContext* をそのまま使用します。
	[[コントローラーの追加] ダイアログ ボックス][rxNewCtx]

8.**[OK]** をクリックし、**[コントローラーの追加]** ダイアログ ボックスで **[追加]** をクリックします。
9. **[コントローラーの追加]** の上書き確認のダイアログ ボックスで、すべてのチェック ボックスがオンになっていることを確認し、**[OK]** をクリックします。

	[[コントローラーの追加] メッセージ ボックス][rxOverwrite]

Visual Studio によって、**Contact** オブジェクトの CRUD データベース操作に対応したコントローラー メソッドとビューが作成されます。

## Migrations の有効化、データベースの作成、サンプル データとデータ初期化子の追加##

次の作業では、作成したデータ モデルに基づいてデータベースを作成するために、[Code First Migrations](http://msdn.microsoft.com/library/hh770484.aspx) 機能を有効にします。

1.**[ツール]** メニューの **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。

	[[ツール] メニューの [パッケージ マネージャー コンソール]][addcode008]

2.**[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		enable-migrations -ContextTypeName ContactManagerContext

	[enable-migrations][rxE]
	コンテキストの型名 (**ContactManagerContext**) を指定する必要があります。プロジェクトに [DbContext](http://msdn.microsoft.com/ja-jp/library/system.data.entity.dbcontext(v=VS.103).aspx) の 2 つの派生クラスがあるためです。前の手順で追加した **ContactManagerContext** と、メンバーシップ データベースに使用される **UsersContext** です。**ContactManagerContext** クラスは Visual Studio のスキャフォールディング ウィザードによって追加されました。

	**enable-migrations** コマンドによって *Migrations* フォルダーが作成され、そのフォルダーに *Configuration.cs* ファイルが保存されます。このファイルを編集して Migration を構成できます。

2. **[パッケージ マネージャー コンソール]** ウィンドウで、次のコマンドを入力します。

		add-migration Initial


	**add-migration Initial** コマンドは、データベースを作成する **&lt;date_stamp&gt;Initial** ファイルを *Migrations* フォルダーに生成します。最初のパラメーター ( **Initial** ) は省略可能で、ファイルの名前を作成するときに使用されます。新しいクラス ファイルは**ソリューション エクスプローラー**で表示できます。

	**Initial** クラスでは、**Up** メソッドを使用して Contacts テーブルを作成し、**Down** メソッドを使用してそのテーブルを削除します (前の状態に戻します)。

3. *Migrations\Configuration.cs* ファイルを開きます。
4. 次の名前空間を追加します。

    	 using ContactManager.Models;



5.*Seed* メソッドを次のコードに置き換えます。

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

	このコードでは、連絡先情報を使用してデータベースを初期化します。シード データベースの生成の詳細については、「[Seeding and Debugging Entity Framework (EF) DBs (Entity Framework DB のシード化とデバッグ)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)」を参照してください。


6. **[パッケージ マネージャー コンソール]**で、次のコマンドを入力します。

		update-database

	[パッケージ マネージャー コンソールのコマンド][addcode009]

	**update-database** によって、データベースを作成する最初の Migration が実行されます。既定では、データベースは SQL Server Express LocalDB データベースとして作成されます (SQL Server Express をインストールしている場合を除き、この例では、SQL Server Express インスタンスを使用してデータベースが作成されます)。

7.Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。

アプリケーションでは、登録されたデータが表示され、編集、詳細、削除のリンクが示されます。

![データの MVC ビュー][rx2]

<h2><a name="addOauth"></a><span class="short-header">OAuth</span>OAuth プロバイダーを追加する</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") は、Web、モバイル、およびデスクトップのアプリケーションからシンプルで標準的な方法で安全に認証するためのオープン プロトコルです。ASP.NET MVC インターネット テンプレートは OAuth を使用して、Facebook、Twitter、Google、Yahoo、Microsoft を認証プロバイダーとしてサポートします。このチュートリアルでは Facebook、Google、Yahoo のみを認証プロバイダーとして使用しますが、コードを少し変更すれば他のプロバイダーも使用できます。他のプロバイダーを実装する手順は、このチュートリアルで説明する手順とほとんど同じです。

このチュートリアルでは、認証の他にロールを使用して権限を付与します。canEdit ロールに追加したユーザーのみが連絡先を作成、編集、削除できます。

## 外部プロバイダーを登録する##

外部プロバイダーから取得した資格情報を使用してユーザーを認証するには、そのプロバイダーに Web サイトを登録し、鍵と接続シークレットを取得する必要があります。Google と Yahoo の場合は、登録して鍵を取得する必要がありません。

このチュートリアルでは、これらのプロバイダーに登録するための手順を詳しく説明しません。複雑な手順ではないので、プロバイダーの指示に従ってサイトを登録してください。サイトの登録については、次の開発者向けサイトを参照してください。

- [Facebook](http://developers.facebook.com/)
- [Microsoft](http://go.microsoft.com/fwlink/?LinkID=144070)
- [Twitter](http://dev.twitter.com/)

[https://developers.facebook.com/apps](https://developers.facebook.com/apps/) ページへ移動し、必要であればログインします。**[Register as a Developer]** ボタンをクリックして登録手続きを行います。登録が完了したら、**[新しいアプリケーションを作成]** をクリックします。アプリケーションの名前を入力します。アプリケーションの名前空間を入力する必要はありません。

![新しい FB アプリケーションの作成](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png)


**[App Domain]** に「localhost」と入力し、**[Site URL]** に「http://localhost/」 と入力します。**[Sandbox Mode]** で **[有効]** をクリックし、**[変更を保存]** をクリックします。


このアプリケーションに OAuth を実装するには、**[App ID]** と **[App Secret]** が必要です。
	[新しい FB アプリケーション][rxFB]

## テスト ユーザーを作成する ##
画面左側の **[設定]** で **[開発者の役割]** をクリックします。(**テスト担当者**行ではなく) **テスト ユーザー**行の **[作成]** リンクをクリックします。

![FB テスター](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png)

**[変更]** リンクをクリックし、テスト ユーザーの電子メールを取得します (アプリケーションにログインするとき使用します)。**[もっと見る]** リンクをクリックし、次に **[編集]** をクリックしてテスト ユーザーのパスワードを設定します。

## プロバイダーのアプリケーション ID と鍵を追加する##

*App_Start\AuthConfig.cs* ファイルを開きます。*RegisterFacebookClient* メソッドからコメント文字を削除し、App ID と App Secret を追加します。実際に取得した値を使用してください。ここに表示されている値は使用できません。*OAuthWebSecurity.RegisterGoogleClient* 呼び出しからコメント文字を削除し、次のように *AuthWebSecurity.RegisterYahooClient* を追加します。Google プロバイダーと Yahoo プロバイダーの場合、登録して鍵を取得する必要がありません。
警告: アプリケーション ID とシークレットを安全な場所に保管してください。アプリケーション ID とシークレットが悪意のあるユーザーに漏れると、アプリケーションに不正アクセスされる可能性があります。

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "enter numeric key here",
                appSecret: "enter numeric secret here");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }


1. アプリケーションを実行し、**[ログイン]** リンクをクリックします。
1. **[Facebook]** ボタンをクリックします。
1. Facebook の資格情報、またはいずれかのテスト ユーザーの資格情報を入力します。
1. **[OK]** をクリックします。アプリケーションから Facebook のリソースへアクセスできるようになります。
1. 登録ページにリダイレクトされます。テスト アカウントを使用してログインした場合は、もう少し短い**ユーザー名**に変更できます ("Bill FB test" など)。**[登録]** ボタンをクリックします。ユーザー名と電子メール エイリアスがメンバーシップ データベースに保存されます。
1. 別のユーザーを登録します。現在、ログイン システムのバグにより、システムからログオフした後、同じプロバイダーを使用して別のユーザーとしてログインできません (つまり、Facebook アカウントをログオフした後、別の Facebook アカウントで再びログインすることはできません)。これに対処するには、別のブラウザーを使用して目的のサイトへアクセスし、別のユーザーを登録してください。一方のユーザーが管理者ロールに追加され、アプリケーションに対する編集権限が与えられます。もう一方のユーザーはそのサイトで非編集メソッドのみを実行できます。匿名ユーザーはホーム ページにのみアクセスできます。
1. 別のプロバイダーを使用して他のユーザーを登録します。
1. **省略可能**: どのプロバイダーにも関連付けられていないローカル アカウントを作成することもできます。後ほどこのチュートリアルで、ローカル アカウントを作成する機能を削除します。ローカル アカウントを作成するには、**[ログアウト]** リンクをクリックし (現在ログインしている場合)、**[登録]** リンクをクリックします。必要であれば、どの外部認証プロバイダーにも関連付けられていない管理用のローカル アカウントを作成します。

<h2><a name="mbrDB"></a><span class="short-header">メンバーシップ DB</span>メンバーシップ データベースにロールを追加する</h2>
このセクションでは、*canEdit* ロールをメンバーシップ データベースに追加します。canEdit ロールのユーザーのみがデータを編集することができます。実行可能な操作に基づいてロール名を付けるのが望ましいので、この場合は *admin* より *canEdit* が適しています。その後、必要に応じて、("*superAdmin*" ではなく) "*canDeleteMembers*" などの新しいロールを追加します。

1. **[表示]** メニューで **[サーバー エクスプローラー]** をクリックします。

1. **サーバー エクスプローラー**で **DefaultConnection** を展開し、さらに **Tables** を展開します。

1. **UserProfile** を右クリックして **[テーブル データの表示]** をクリックします。
 
	![テーブル データの表示][rxSTD]
 
1. canEdit ロールを割り当てるユーザーの **UserId** を記録します。次の図では、**UserId** が "2" であるユーザー *ricka* に canEdit ロールが割り当てられています。

	![ユーザー ID][rxUid]
 
1. **webpages_Roles** を右クリックして **[テーブル データの表示]** をクリックします。
1. **RoleName** セルに「**canEdit**」と入力します。ロールを初めて追加する場合、**RoleId** は 1 になります。RoleID を記録しておきます。末尾にスペースを入れないように注意してください。ロール テーブルの "canEdit " がコントローラー コードの "canEdit" と一致しなくなります。

	![roleID][rxRoleID]

1. **webpages UsersInRoles** を右クリックして、**[テーブル データの表示]** をクリックします。*canEdit* アクセスを付与するユーザーの **UserId** と **RoleId** を入力します。

	![ユーザーのロール ID テーブル][rxUR]

**webpages_OAuthMembership** テーブルには、OAuth プロバイダー、プロバイダーの UserID、および登録されている各 OAuth ユーザーの UserID が格納されます。<!-- Don't replace "-" with "_" or it won't validate -->**webpages-Membership** テーブルには、ASP.NET メンバーシップ テーブルが格納されます。登録リンクを使用して、このテーブルにユーザーを追加できます。*canEdit* ロールが割り当てられており、Facebook や他のサードパーティ認証プロバイダーに関連付けられていないユーザーを追加しておけば、サードパーティ認証プロバイダーに接続できない場合でも *canEdit* アクセスが可能になります。後ほどこのチュートリアルで、ASP.NET メンバーシップ登録を無効にします。

## Authorize 属性を使用してアプリケーションを保護する##

このセクションでは、[Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute(v=vs.100) 属性を適用してアクション メソッドへのアクセスを制限します。匿名ユーザーが表示できるのはホーム ページだけになります。登録ユーザーは連絡先の詳細ページを閲覧できます。*canEdit* ロールを与えられているユーザーのみがアクション メソッドを実行してデータを変更できます。

1. [Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) フィルターと [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) フィルターをアプリケーションに追加します。[Authorize](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.authorizeattribute(v=vs.100).aspx) 属性と [RequireHttps](http://msdn.microsoft.com/ja-jp/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx) 属性をコントローラーごとに追加する方法もありますが、セキュリティ上の理由から、通常はこれらをアプリケーション全体に適用します。アプリケーション全体に適用すれば、新しいコントローラーやアクション メソッドを追加したとき、それらが自動的に保護されます。ユーザー自身で適用する必要がありません。詳細については、[ASP.NET MVC 4 アプリケーションの保護と新しい AllowAnonymous 属性に関するページ](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)を参照してください。*App_Start\FilterConfig.cs* ファイルを開き、*RegisterGlobalFilters* メソッドを次のように書き換えます。

        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }

1. [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を **Index** メソッドに追加します。[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 属性を使用すれば、特定のメソッドを認証不要として指定できます。
1. データを変更 (作成、編集、削除) する Get メソッドと Post メソッドに [Authorize(Roles = "canEdit")] を追加します。
1. *About* メソッドと *Contact* メソッドを追加します。追加後のコードは次のようになります。

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
	        // メソッドはわかりやすいように省略されています。
	    }

1. ASP.NET メンバーシップ登録を削除します。このプロジェクトの現在の ASP.NET メンバーシップ登録はパスワードのリセットをサポートしておらず、人間による登録を検証しません ([CAPTCHA](http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership) など)。いずれかのサードパーティ プロバイダーを使用してユーザーを認証すると、登録できるようになります。AccountController で、GET および POST *Register* メソッドから *[AllowAnonymous]* を削除します。これによって、ボットや匿名ユーザーが登録されるのを防ぎます。
1. *Views\Shared\_LoginPartial.cshtml* で登録リンクを削除します。
1. SSL を有効にします。ソリューション エクスプローラーで **ContactManager** プロジェクトをクリックし、F4 キーを押します。プロパティ ダイアログ ボックスが表示されます。**[SSL Enabled]** を True に変更します。**[SSL URL]** をコピーします。

	![SSL を有効にする][rxSSL]
 
1. ソリューション エクスプローラーで **Contact Manager** プロジェクトを右クリックし、**[プロパティ]** をクリックします。
1. 左側のタブで **[Web]** をクリックします。
1. **[プロジェクト URL]** を変更し、**SSL URL** を使用するようにします。
1. **[仮想ディレクトリの作成]** をクリックします。
	
	![SSL を有効にする][rxS2]
 
1. Ctrl + F5 キーを押して、アプリケーションを実行します。ブラウザーに証明書の警告が表示されます。このアプリケーションでは、**[このサイトの閲覧を続行する]** をクリックしてかまいません。*canEdit* ロールを与えられているユーザーのみがデータを変更できることを確認します。匿名ユーザーが閲覧できるのはホーム ページのみであることを確認します。

	![証明書の警告][rxNOT]

	![証明書の警告][rxNOT2]
 
Azure の Web サイトには有効なセキュリティ証明書が備わっているので、Azure に展開するときこの警告は表示されません。
<h2><a name="ppd"></a><span class="short-header">DB の作成</span>データ展開スクリプトを作成する</h2>


メンバーシップ データベースは Entity Framework Code First によって管理されないので、Migrations を使用して展開できません。[dbDacFx](http://msdn.microsoft.com/ja-jp/library/dd394698.aspx) プロバイダーを使用してデータベース スキーマを展開し、メンバーシップ データをメンバー テーブルに挿入するスクリプトを実行するように発行プロファイルを構成します。

このチュートリアルでは、SQL Server Management Studio (SSMS) を使用してデータ展開スクリプトを作成します。

[Microsoft SQL Server 2012 Express ダウンロード センター](http://www.microsoft.com/ja-jp/download/details.aspx?id=29062)から SSMS をインストールします。

- [ENU\x64\SQLManagementStudio_x64_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe) (64 ビット システム)
- [ENU\x86\SQLManagementStudio_x86_ENU.exe](http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe) (32 ビット システム)

 お使いのシステムに適したバージョンを選択しないとインストールできません。その場合は、適切なバージョンを選択して、もう一度インストールしてください。

(ダウンロード データは 600 MB あるので、インストールに多少時間がかかります。また、コンピューターの再起動が必要な場合もあります。)

SQL Server インストール センターの最初のページで **[SQL Server の新規スタンドアロン インストールを実行するか、既存のインストールに機能を追加します]** をクリックし、画面の指示に従います。設定は既定値を使用してください。SSMS のインストール画面を次に示します。

![SQL インストール][rxSS]

### 開発データベース スクリプトを作成する###


1. SSMS を実行します。
1. **[サーバーへの接続]** ダイアログ ボックスで、サーバー名として「*(localdb)\v11.0*」と入力します。**[認証]** を **[Windows 認証]** に設定して **[接続]** をクリックします。SQL Express をインストールしている場合は、「**.\SQLEXPRESS**」と 入力します。
	
	![[サーバーへの接続] ダイアログ ボックス][rxC2S]

1. **オブジェクト エクスプローラー**で **[データベース]** を展開し、**[aspnet-ContactManager]** を右クリックして、**[タスク]**、**[スクリプトの生成]** の順にクリックします。
	
	![スクリプトの生成][rxGenScripts]

1. **[スクリプトの生成とパブリッシュ]** ダイアログ ボックスで、**[スクリプト作成オプションの設定]** をクリックします。
**[オブジェクトの選択]** ステップはスキップしてかまいません。既定の設定 ([データベース全体とすべてのデータベース オブジェクトのスクリプトを作成]) をそのまま使用します。

1. **[詳細設定]** をクリックします。

	![スクリプト作成オプションの設定][rx11]

1. **[スクリプト作成の詳細オプション]** ダイアログ ボックスで **[スクリプトを作成するデータの種類]** までスクロールし、一覧から **[データのみ]** を選択します (次の図を参照)。

1. **[USE DATABASE のスクリプトを作成]** を **False** に変更します。Azure SQL データベースでは USE ステートメントを使用しません。また、SQL Server Express をテスト環境に展開する場合、USE ステートメントは必要ありません。
	
	![スクリプト作成オプションの設定][rxAdv]

1. **[OK]** をクリックします。
1. **[スクリプトの生成とパブリッシュ]** ダイアログ ボックスで、**[ファイル名]** ボックスにスクリプトの作成場所を指定します。ソリューション フォルダー (*Contacts.sln* ファイルが保存されているフォルダー) のパスを変更し、ファイル名を「*aspnet-data-membership.sql*」に変更します。
1. **[次へ]** をクリックして **[概要]** タブへ進み、もう一度 **[次へ]** をクリックしてスクリプトを作成します。

	![保存または発行][rx1]

1. **[完了]** をクリックします。

<h2><a name="bkmk_deploytowindowsazure11"></a>Azure にアプリケーションを展開する</h2>

1. アプリケーション ルートの *Web.config* ファイルを開きます。*DefaultConnection* マークアップを見つけ、それをコピーして *DefaultConnection* マークアップ行の下に貼り付けます。コピーした要素の名前を「*DefaultConnectionDeploy*」に変更します。ユーザー データをメンバーシップ データベースにデプロイするには、この接続文字列が必要になります。
	![3 つの接続文字列][rxD]

1. アプリケーションをビルドします。
1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![プロジェクトのコンテキスト メニューの [発行]][firsdeploy003]

**Web の発行**ウィザードが開きます。

1. **[設定]** タブをクリックします。**ContactManagerContext** と **DefaultConnectionDeploy** で、**v** アイコンをクリックして **[リモート接続文字列]** を選択します。表示されている 3 つのデータベースでは、すべて同じ接続文字列を使用します。**ContactManagerContext** データベースには連絡先が保存されます。**DefaultConnectionDeploy** は、ユーザー アカウント データをメンバーシップ データベースへ展開するときに使用します。**UsersContext** はメンバーシップ データベースです。
	
	![設定][rxD2]

1. **ContactManagerContext** で、**[Code First Migrations を実行する]** チェック ボックスをオンにします。
	
	![設定][rxSettings]

1. **DefaultConnectionDeploy** で **[データベースの更新]** チェック ボックスをオンにして、**[データベースの更新の構成]** リンクをクリックします。
1. **[SQL スクリプトの追加]** リンクをクリックし、*aspnet-data-membership.sql* ファイルへ移動します。この操作を行うのは 1 回だけです。次の展開時にはユーザー データをメンバーシップ テーブルに追加する必要がないので、**[データベースの更新]** チェック ボックスをオフにします。

	![SQL の追加][rxAddSQL2]

1. **[発行]** をクリックします。
1. [https://developers.facebook.com/apps](https://developers.facebook.com/apps/) ページへ移動し、**[App Domains]** と **[Site URL]** の設定を Azure URL に変更します。
1. アプリケーションをテストします。*canEdit* ロールを与えられているユーザーのみがデータを変更できることを確認します。匿名ユーザーが閲覧できるのはホーム ページのみであることを確認します。認証ユーザーは、データを変更しないすべてのリンク先へ移動できることを確認します。
1. 次にアプリケーションを発行するときは、**DefaultConnectionDeploy** の **[データベースの更新]** チェック ボックスをオフにしてください。
	
	![設定][rxSettings]

<h2><a name="ppd2"></a><span class="short-header">DB の更新</span>メンバーシップ データベースを更新する</h2>

サイトを Azure に展開した後、さらに別の登録ユーザーを *canEdit* ロールに追加する必要が生じる場合もあります。このセクションでは、Visual Studio を使用して SQL データベースに接続し、*canEdit* ロールにユーザーを追加します。

![設定][rxSettings]

1. **ソリューション エクスプローラー**で目的のプロジェクトを右クリックし、**[発行]** をクリックします。
	![発行][rxP]

1. **[設定]** タブをクリックします。
2. 接続文字列をコピーします。たとえば、このサンプルで使用されている接続文字列は 
	Data Source=tcp:d015leqjqx.database.windows.net,1433;
	Initial Catalog=ContactDB2;User Id=ricka0@d015lxyze;Password=xyzxyz
1. 発行ダイアログ ボックスを閉じます。
1. **[表示]** メニューで **[サーバー エクスプローラー]** をクリックします。

1. **[データベースに接続]** アイコンをクリックします。
	
	![発行][rxc]

1. データ ソースを選択するためのダイアログ ボックスが表示された場合は、**[Microsoft SQL Server]** をクリックします。
	![発行][rx3]

1. "*tcp*" で始まるサーバー名をコピーして、**[サーバー名]** ボックスに貼り付けます (次の図を参照)。
1. **[SQL Server 認証を使用する]** をクリックします。
1. **[ユーザー名]** と **[パスワード]** を入力します。これらの情報はコピーした接続文字列に含まれています。
1. データベース名 (ContactDB) を入力します ("ContactDB" 以外の名前を付けた場合は、データベースの "Initial Catalog=" の後の文字列を入力します)。エラー メッセージが表示される場合は、次のセクションを参照してください。
1. **[接続テスト]** をクリックします。エラー メッセージが表示される場合は、次のセクションを参照してください。
	![接続の追加ダイアログ][rx4]

## サーバーに接続できない場合 ##
"サーバーを開けない" ことを知らせるエラー メッセージが表示された場合は、使用できる IP に IP アドレスを追加する必要があります。

![ファイアウォール エラー][rx5]

1. Azure ポータルの左側のタブで **[SQL データベース]** を選択します。

	![[SQL] を選択します。][rx6]

1. 接続するデータベースを選択します。

1. **[この IP アドレス用に Azure ファイアウォール ルールを設定する]** リンクをクリックします。

	![ファイアウォール ルール][rx7]

1. "現在の IP アドレス xxx.xxx.xxx.xxx は既存のファイアウォール ルールに含まれていません。ファイアウォール ルールを更新しますか?" というメッセージが表示されたら、**[はい]** をクリックします。このアドレスだけでは不十分な場合は、IP アドレスの範囲を追加する必要があります。

## 使用できる IP アドレスの範囲を追加する##

1. Azure ポータルで **[SQL データベース]** をクリックします。
1. データベースをホストする **[サーバー]** をクリックします。

	![データベース サーバー][rx8]

1. ページの上部にある **[構成]** をクリックします。
1. ルール名、開始 IP アドレス、終了 IP アドレスを追加します。
	![IP 範囲][rx9]

1. ページの下部にある **[保存]** をクリックします。
1. ここで、前に説明した手順に従ってメンバーシップ データベースを編集します。

<h2><a name="nextsteps"></a><span class="short-header">次のステップ</span>次のステップ</h2>

このチュートリアルとサンプル アプリケーションは、Tom Dykstra、Tom FitzMacken、Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) の協力の下、[Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) が執筆しました。

役に立った内容や改善点など、皆様からのご意見をお寄せください。このチュートリアルに関してだけでなく、ここで紹介した製品に関するご意見やご要望もお待ちしております。お寄せいただいたご意見は、今後の改善に役立たせていただきます。特に、メンバーシップ データベースの構成と展開の自動化に関するご意見をお待ちしております。

Facebook、Google、Yahoo のカラフルなログイン ボタンを取得する方法については、ブログ投稿「[Customizing External Login Buttons in ASP.NET MVC 4 (ASP.NET MVC 4 で外部ログイン ボタンをカスタマイズする)](http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/)」を参照してください。Windows 認証の詳細については、次の Web ページを参照してください。

- [Azure Authentication (Azure の認証)](http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication)
- [ASP.NET MVC を使用してイントラネット サイトを作成する方法](http://msdn.microsoft.com/ja-jp/library/gg703322(v=vs.98).aspx)

Azure アプリケーションにデータを保存するには、Azure ストレージを使用する方法もあります。Azure ストレージには、非リレーショナル データを BLOB 形式とテーブル形式で保存できます。ASP.NET MVC および Window Azure の詳細については、次の Web ページを参照してください。

- [ストレージ テーブル、キュー、BLOB を使用する .NET 多層アプリケーションに関するページ](http://www.windowsazure.com/ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/)
- [ASP.NET MVC 4 の入門ページ](http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4)
- [MVC を使用した Entity Framework の概要に関するページ][EFCodeFirstMVCTutorial]
- [OAuth 2.0 とサインインに関するブログ投稿](http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx)


Azure の Web サイトに Web アプリケーションを展開する方法を確認したところで、Azure の Web サイトを構成、管理、および拡張する方法をさらに確認するには、「[一般的なタスク][CommonTasks]」ページにある操作方法のトピックを参照してください。

Azure の Web サイトのデバッグ方法については、「[Visual Studio での Azure の Web サイトのトラブルシューティング](/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)」を参照してください。

Azure のクラウド サービスにアプリケーションを展開する方法を確認するには、[このチュートリアルのクラウド サービス バージョン][NetAppWithSqlAzure]および [Azure を使った Web アプリケーションの開発に関するページ][DevelopingWebAppsWithWindowsAzure]を参照してください。Azure の Web サイトではなく Azure のクラウド サービスで ASP.NET Web アプリケーションを実行する場合のいくつかの理由を次に示します。

* アプリケーションが実行される Web サーバーに対する管理者権限が必要である。
* アプリケーションが実行される Web サーバーにアクセスするためにリモート デスクトップ接続を使用したい。
* 多層アプリケーションを使用し、複数の仮想サーバー (Web および worker) 間で処理を分散したい。

SQL データベースと Azure ストレージの詳細については、「[Data Storage Offerings on Azure (Azure のデータ ストレージ機能)][WindowsAzureDataStorageOfferings]」を参照してください。

SQL データベースの使用方法については、[ASP.NET データ アクセス コンテンツ マップでの Azure SQL データベースの操作に関するページ](http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb)を参照してください。

Entity Framework および Code First Migrations の詳細については、次のリソースを参照してください。

* [MVC を使用した Entity Framework の概要に関するページ][EFCodeFirstMVCTutorial]
* [Code First Migrations に関するページ][EFCFMigrations]




<!-- bookmarks -->
[OAuth プロバイダーを追加する]: #addOauth
[メンバーシップ データベースにロールを追加する]:#mbrDB
[データ展開スクリプトを作成する]:#ppd
[メンバーシップ データベースを更新する]:#ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase




<!-- links -->




[WindowsAzureDataStorageOfferings]: http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx

[NetAppWithSQLAzure]: http://www.windowsazure.com/ja-jp/develop/net/net-app-with-sql-azure





[CommonTasks]: http://windowsazure.com/develop/net/common-tasks/





[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[EFCFMigrations]: http://msdn.microsoft.com/ja-jp/library/hh770484


<!-- links from Tom's hopefully no collisions -->

[WindowsAzureDataStorageOfferings]: http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx

[NetAppWithSQLAzure]: http://www.windowsazure.com/ja-jp/develop/net/tutorials/cloud-service-with-sql-database/





[CommonTasks]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/







[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[EFCFMigrations]: http://msdn.microsoft.com/ja-jp/library/hh770484
[DevelopingWebAppsWithWindowsAzure]: http://msdn.microsoft.com/ja-jp/library/Hh674484

<!-- images-->
[rxE]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png
[rxP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png
[rxFB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png
[rxSTD]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png
[rxUid]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png
[rxRoleID]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png
[rxUR]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png
[rxC2S]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png
[rxGenScripts]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png
[rx11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png
[rxAdv]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png
[rx1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png
[rxd]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png
[rxSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png
[rxD2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png
[rxAddSQL2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png
[rxc]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png
[rx3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png
[rx4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png
[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png
[rxa]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png
[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png
[rxSS]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png
[rxp2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxp2.png
[rxp3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxp3.png
[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png
[rxS2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png
[rxb2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
[rxCreateWSwithDB_2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png
[rxNewCtx]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png
[rxCreateWSwithDB_2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png 
[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png
[rxNewCtx]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png
[rxSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png
[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png
[setup009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png
[newapp005]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png
[firsdeploy001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png
[firsdeploy002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png
[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png
[adddb001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png
[Important information about ASP.NET in Azure Web Sites]: #aspnetwindowsazureinfo
[次のステップ]: #nextsteps
[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png

