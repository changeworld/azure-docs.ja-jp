<properties linkid="dev-net-how-to-sql-azure" urlDisplayName="SQL データベース" pageTitle="SQL データベースの使用方法 (.NET) - Azure の機能ガイド" metaKeywords="SQL Azure の使用, SQL Azure の概要, SQL Azure データベース接続, SQL Azure ADO.NET, SQL Azure ODBC, SQL Azure EntityClient" description="SQL データベースを使ってみる: SQL データベース インスタンスの作成方法および ADO.NET、ODBC、EntityClient プロバイダーを使用した接続方法について説明します。" metaCanonical="" services="sql-database" documentationCenter=".NET" title=".NET アプリケーションで Azure SQL データベースを使用する方法" authors="" solutions="" manager="" editor="" />







# .NET アプリケーションで Azure SQL データベースを使用する方法

このガイドでは、Azure SQL データベースに論理サーバーとデータベース インスタンスを作成する方法、および ADO.NET、ODBC、EntityClient プロバイダーなどの .NET Framework データ プロバイダー テクノロジを使用してこのデータベースに接続する方法について説明します。


<h2><a name="Whatis"></a>SQL データベースとは</h2>

SQL データベースは、Azure 用のリレーショナル データベース管理システムを提供し、SQL Server テクノロジを基盤としています。SQL データベース インスタンスを使用すると、リレーショナル データベース ソリューションを簡単に準備してクラウドにデプロイすることができ、分散したデータ センターを活用して、組み込みのデータ保護と自己復旧機能のメリットによるエンタープライズ クラスの可用性、拡張性、およびセキュリティを確保できます。

## 目次

- [Azure へのサインイン](#PreReq1)
- [SQL データベースの作成と構成](#PreReq2)
- [SQL データベースに接続する](#connect-db)
- [ADO.NET を使用した接続](#using-sql-server)
- [ODBC を使用した接続](#using-ODBC)
- [EntityClient プロバイダーを使用した接続](#using-entity)
- [次のステップ](#next-steps)

<h2><a name="PreReq1"></a>Azure へのサインイン</h2>

SQL データベースは、Azure におけるリレーショナル データのストレージ サービス、アクセス サービス、および管理サービスを提供します。これを使用するには、Azure サブスクリプションが必要です。

1. Web ブラウザーを開いて、[http://www.windowsazure.com](http://www.windowsazure.com) にアクセスします。無料アカウントを取得するには、右上にある [無料評価版] をクリックして、表示される手順に従います。

2. アカウントが作成されました。これで使用開始する準備が整いました。


<h2><a name="PreReq2"></a><span class="short-header">SQL データベースの作成と構成</span></h2>

次に、データベースとサーバーを作成して構成します。Azure 管理ポータルで、改訂されたワークフローに従ってまずデータベースを作成し、次にサーバー プロビジョニングを行います。

<h3 name="createsrvr">データベース インスタンスと論理サーバーの作成</h3>

1. [Azure 管理ポータル][]にサインインします。

2. ページの下部にある **[+新規]** をクリックします。

3. **[データ サービス]** をクリックします。

4. **[SQL データベース]** をクリックします。

5. **[カスタム作成]** をクリックします。

6. [名前] で、データベース名を入力します。

7. エディション、最大サイズ、および照合順序を選択します。このガイドでは、既定値を使用します。

	SQL データベースには 2 つのデータベース エディションが用意されています。Web Edition は、最大 5 GB までのサイズに対応します。Business Edition は、最大 50 GB までのサイズに対応します。

	データベースを最初に作成するときに MAXSIZE を指定します。この値は、後で ALTER DATABASE を使用して変更できます。MAXSIZE は、データベースのサイズを制限する機能を提供します。

	実際には、Azure 上に作成された SQL データベース 1 つにつき、3 つのレプリカが存在します。これは、高可用性を確保するためです。
フェールオーバーは透過的な機能であり、サービスの一部です。[サービス レベル アグリーメント][]では、SQL データベースの 99.9% のアップタイムが約束されます。

8. [サーバー] で、**[新しい SQL データベース サーバー]** を選択します。

9. 矢印をクリックして、次のページに進みます。

10. [サーバーの設定] で、SQL Server 認証のログイン名を入力します。

	SQL データベースでは、暗号化された接続を経由して SQL 認証を使用します。sysadmin 固定のサーバー ロールに割り当てられた新しい SQL Server 認証のログインが、入力した名前を使用して作成されます。

	ログインには、電子メール アドレス、Windows ユーザー アカウント、または Windows Live ID は使用できません。クレーム認証と Windows 認証のどちらも SQL データベースではサポートされません。

11. 大文字、小文字、および数字か記号を組み合わせた 9 文字以上の強力なパスワードを指定します。

12. リージョンを選択します。リージョンによって、サーバーのジオ (主要地域)が決まります。リージョンは簡単に切り替えることができないので、このサーバーに最適なリージョンを選択してください。また、最も近い場所を選択してください。Azure アプリケーションとデータベースを同じリージョンに置くことで、送信帯域幅コストおよびデータ遅延を削減できます。

13. **[Azure サービスにサーバーへのアクセスを許可します]** オプションを選択したままにしてください。そうすれば、SQL データベース用の管理ポータル、ストレージ サービス、Azure のその他のサービスを使用してこのデータベースに接続できます。

14.  終了したら、ページの下部にあるチェックマークをクリックします。

サーバー名を指定しなかったことに注意してください。SQL データベースでは、DNS エントリが重複しないようにサーバー名は自動的に生成されます。サーバー名は、10 文字の英数字文字列です。SQL データベース サーバーの名前は変更できません。

データベースを作成したら、クリックしてそのダッシュボードを開きます。ダッシュボードに表示される接続文字列をコピーして、アプリケーション コードで使用できます。Management Studio やその他の管理ツールからデータベースに接続する場合に指定する必要がある管理 URL も表示されます。


![image](./media/sql-database-dotnet-how-to-use/SQLDbDashboard.PNG)


次の手順では、ネットワークで実行中のアプリケーションからの接続によるアクセスが許可されるようにファイアウォールを構成します。

<h3 name="configFWLogical">論理サーバーのファイアウォールの構成</h3>

1. **[SQL データベース]** をクリックし、ページの上部にある **[サーバー]** をクリックして、作成したサーバーをクリックします。

	![Image2](./media/sql-database-dotnet-how-to-use/SQLDBFirewall.PNG)

2. **[構成]** をクリックします。

3. 現在のクライアント IP アドレスをコピーします。ネットワークから接続している場合、これは使用するルーターまたはプロキシ サーバーがリッスンしている IP アドレスです。SQL データベースが現在の接続で使用されている IP アドレスを検出すると、このデバイスからの接続要求を受け入れるためのファイアウォール ルールを作成できます。

4. IP アドレスを START IP ADDRESS と END IP ADDRESS の両方に貼り付けて、サーバーへのアクセスが許可されるアドレスの範囲を確立します。その後、この範囲が狭すぎることを示す接続エラーが発生した場合は、このルールを編集して範囲を広げることができます。

	動的に割り当てられた IP アドレスをクライアント コンピューターで使用する場合は、ネットワーク上でコンピューターに割り当てられた IP アドレスを含む範囲を指定する必要があります。狭い範囲から始めて、必要になった場合にのみ範囲を広げます。

5. 使用するコンピューターや会社の名前など、ファイアウォール ルールの名前を入力します。

6. ルールの横にあるチェックマークをクリックして、ルールを保存します。

	![Image3](./media/sql-database-dotnet-how-to-use/SQLDBIPRange.PNG)

7. ページの下部にある **[保存]** をクリックして、手順を完了します。**[保存]** が表示されない場合は、ブラウザーのページを更新します。

これで、データベース インスタンス、論理サーバー、IP アドレスからの受信接続を許可するファイアウォール ルール、および管理者ログインが用意できました。また、プログラムでデータベースに接続する準備ができました。


<h2><a name="Connect-DB"></a><span class="short-header">SQL データベースへの接続</span></h2>

このセクションでは、さまざまに異なる .NET Framework データ プロバイダーを使用して SQL データベース インスタンスに接続する方法について説明します。

Visual Studio を使用することを選択した場合、構成に Azure Web アプリケーションがフロントエンドとして含まれていないときは、開発コンピューターに追加のツールや SDK をインストールする必要はありません。すぐにアプリケーションの開発を開始できます。

Visual Studio では SQL Server で使用するものと同じデザイナー ツールをすべて使用して、SQL データベースを操作できます。サーバー エクスプローラーでは、データベース オブジェクトを表示できますが、編集はできません。Visual Studio Entity Data Model デザイナーのすべての機能を使用して、SQL データベースに対するモデルを作成し、Entity Framework を利用することができます。

### <a name="using-sql-server"></a>SQL Server 用の .NET Framework データ プロバイダーの使用

**System.Data.SqlClient** 名前空間は、SQL Server 用の .NET Framework データ プロバイダーです。

標準の接続文字列は、次のようになります。

    Server=tcp:.database.windows.net;
    Database=;
    User ID=@;
    Password=;
    Trusted_Connection=False;
    Encrypt=True;

**SQLConnectionStringBuilder** クラスを使用すると、次のコード例に示すような接続文字列を作成できます。

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder();
    csBuilder.DataSource = xxxxxxxxxx.database.windows.net;
    csBuilder.InitialCatalog = testDB;
    csBuilder.Encrypt = true;
    csBuilder.TrustServerCertificate = false;
    csBuilder.UserID = MyAdmin;
    csBuilder.Password = pass@word1;

接続文字列の要素が事前にわかっている場合は、それらを構成ファイルに格納しておき、実行時に取得して、接続文字列を作成することができます。構成ファイル内の接続文字列の例を次に示します。

    <connectionStrings>
      <add name="ConnectionString" 
           connectionString ="Server=tcp:xxxxxxxxxx.database.windows.net;Database=testDB;User ID=MyAdmin@xxxxxxxxxx;Password=pass@word1;Trusted_Connection=False;Encrypt=True;" />
    </connectionStrings>

構成ファイル内の接続文字列を取得するには、
**ConfigurationManager** クラスを使用します。

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder(ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString);
    After you have built your connection string, you can use the SQLConnection class to connect the SQL Database server:
    SqlConnection conn = new SqlConnection(csBuilder.ToString());
    conn.Open();

### <a name="using-ODBC"></a>ODBC 用の .NET Framework データ プロバイダーの使用

**System.Data.Odbc** 名前空間は、ODBC 用の .NET Framework データ プロバイダーです。ODBC 接続文字列の例を次に示します。

    Driver={SQL Server Native Client 10.0};
    Server=tcp:.database.windows.net;
    Database=;
    Uid=@;
    Pwd=;
    Encrypt=yes;

**OdbcConnection** クラスは、データ ソースへの開かれた接続を表します。接続を開く方法のコード例を次に示します。

    string cs = "Driver={SQL Server Native Client 10.0};" +
                "Server=tcp:xxxxxxxxxx.database.windows.net;" +
                "Database=testDB;"+
                "Uid=MyAdmin@xxxxxxxxxx;" +
                "Pwd=pass@word1;"+
                "Encrypt=yes;";

    OdbcConnection conn = new OdbcConnection(cs);
    conn.Open();

ランタイムで接続文字列を作成する必要がある場合は、**OdbcConnectionStringBuilder** クラスを使用できます。

### <a name="using-entity"></a>EntityClient プロバイダーの使用

**System.Data.EntityClient** 名前空間は、Entity Framework 用の .NET Framework データ プロバイダーです。

Entity Framework を使用することで、開発者は、リレーショナル ストレージ スキーマに対する直接のプログラミングではなく、概念的なアプリケーション モデルに対するプログラミングによってデータ アクセス アプリケーションを作成できます。Entity Framework は、基になるデータ プロバイダーおよびリレーショナル データベースに **EntityConnection** を提供することにより、ストレージ固有の ADO.NET データ プロバイダーに基づいて構築されます。

**EntityConnection** オブジェクトを構築するには、必要なモデルとマッピング、さらにストレージ固有のデータ プロバイダー名と接続文字列を含んだ一連のメタデータを参照する必要があります。
**EntityConnection** を確立すると、概念モデルから生成されたクラスを使用してエンティティにアクセスできます。

接続文字列の例を次に示します。

    metadata=res://*/SchoolModel.csdl|res://*/SchoolModel.ssdl|res://*/SchoolModel.msl;provider=System.Data.SqlClient;provider connection string="Data Source=xxxxxxxxxx.database.windows.net;Initial Catalog=School;Persist Security Info=True;User ID=MyAdmin;Password=***********"

詳細については、「[Entity Framework 用の EntityClient プロバイダー][]」を参照してください。

## <a name="next-steps"></a>次のステップ

これで、SQL データベースへの接続の基本を学習できました。SQL データベースについてさらに詳細な情報が必要な場合は、次のリソースを参照してください。

-   [開発作業の方法に関するトピック (SQL データベース)][]
-   [SQL データベース][]


  [SQL データベースとは]: #WhatIs
  [Azure へのサインイン]: #PreReq1
  [SQL データベースの作成と構成]: #PreReq2
  [SQL データベースへの接続]: #connect-db
  [ADO.NET を使用した接続]: #using-sql-server
  [ODBC を使用した接続]: #using-ODBC
  [EntityClient プロバイダーを使用した接続]: #using-entity
  [次のステップ]: #next-steps
  [Azure 無料評価版]: {localLink:2187} "Free Trial"
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [方法: SQL データベース サーバーを作成する]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-server.aspx
  [SQL データベースの管理ポータル]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg442309.aspx
  [SQL データベースのファイアウォール]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-firewall.aspx
  [ツールとユーティリティのサポート (SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621784.aspx
  [Azure で SQL データベースを作成する方法]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-database.aspx
  [サービス レベル アグリーメント]: {localLink:1132} "SLA"
  [Entity Framework 用の EntityClient プロバイダー]: http://msdn.microsoft.com/ja-jp/library/bb738561.aspx
  [開発作業の方法に関するトピック (SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621787.aspx
  [SQL データベース]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336279.aspx

