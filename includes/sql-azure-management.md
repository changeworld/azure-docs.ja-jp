
#SQL Server Management Studio を使用した Azure SQL データベースの管理

Azure SQL データベースの管理ポータルまたは SQL Server Management Studio (SSMS) クライアント アプリケーションを使用すると、SQL データベースのサブスクリプションを管理できます。また、関連付けられている論理サーバーやデータベースの作成および管理も行うことができます。以下のガイダンスでは、Management Studio を使用して SQL データベースの論理サーバーおよびデータベースを管理する方法について説明します。アプリケーション コードで 
SQL データベース接続を使用する方法については、「[How to use Azure SQL Database (Azure SQL データベースの使用方法)][]」を参照してください。

<div class="dev-callout-new-collapsed">
<strong>注 <span>折りたたむにはクリック</span></strong>
<div class="dev-callout-content">
<p>Management Studio の SQL Server 
2012 バージョンまたは SQL Server 2008 R2 バージョンを使用できます。これより前のバージョンはサポートされていません。</p>
</div>
</div>

このタスクの手順は次のとおりです。

-   [ステップ 1. SQL Server Management Studio を入手する][]
-   [ステップ 2. SQL データベースに接続する][]
-   [ステップ 3. データベースを作成および管理する][]
-   [ステップ 4. ログインを作成および管理する][]
-   [ステップ 5. 動的管理ビューを使用して SQL データベースを監視する][]

<h2><a id="Step1" name="Step1"> </a>ステップ 1. Management Studio を入手する</h2>

Management Studio は、SQL データベースを
管理するための統合環境です。Azure で
データベースを管理するときは、SQL Server と同時にインストールされる Management Studio 
アプリケーションを使用するか、無料の SQL Server 2012 Management Studio Express (SSMSE) バージョンをダウンロードできます。以下の手順で、
SSMSE のインストール方法を紹介します。

1. [Microsoft SQL Server 2012 Express][] のページで、Management Studio の x86 バージョン (32 ビット オペレーティング システムを実行している場合) か x64 バージョン (64 ビット オペレーティング システムを実行している場合) を選択します。**[ダウンロード]** をクリックし、メッセージが表示されたら、セットアップを実行します。

2. **[SQL Server の新規スタンドアロン インストールを実行するか、
    既存のインストールに機能を追加します]** をクリックし、**[OK]** をクリックします。

3. ライセンス条項に同意し、**[OK]** をクリックします。

4. **[インストール]** をクリックして、SQL Server のセットアップに必要なファイルをインストールします。

5. **[機能の選択]** 画面では、**[管理ツール - 基本]** が
    最初から選択されています。今は、Management Studio のインストーラーを実行しているためです。SQL Server Express 全体のセットアップを実行している場合は、**[管理ツール - 基本]** オプションを選択し、**[次へ]** をクリックします。

6. **[エラー報告]** 画面で、エラー レポートを Microsoft に送信するように
    設定することもできます。これを設定しなくても SSMSE は使用できます。**[次へ]** を
    クリックしてインストールを開始します。

7. インストールが完了したら、**[完了]** ページが
    表示されます。**[閉じる]** をクリックします。


<h2><a id="Step2" name="Step2"> </a>ステップ 2. SQL データベースに接続する</h2>

SQL データベースに接続するには、Azure 上のサーバー名が必要になります。この情報を入手するには、ポータルへのサインインが必要になる場合があります

1. [Azure の管理ポータル][]にサインインします。

2. 左のウィンドウで、**[SQL データベース]** をクリックします。

3. SQL データベースのホーム ページで、ページ最上部の **[サーバー]** をクリックし、自分のサブスクリプションに関連付けられている全サーバーの一覧を表示します。接続先のサーバーの名前を探し、その名前をクリップボードにコピーします。

	次に、SQL データベース ファイアウォールを構成して、
ローカル コンピューターからの接続を許可します。それには、ローカル コンピューターの IP アドレスをファイアウォールの例外一覧に追加します。

1. SQL データベースのホーム ページで **[サーバー]** をクリックし、接続するサーバーをクリックします。

2. ページの上部にある **[構成]** をクリックします。

3. [現在のクライアント IP アドレス] の IP アドレスをコピーします。

4. [構成] ページの **[使用できる IP アドレス]** には、ルールの名前、IP アドレス範囲の開始値と終了値を指定できる 3 つのボックスがあります。ルール名としては、コンピューター名などを入力します。範囲の開始値および終了値としてコンピューターの IP アドレスを両方のボックスに入力し、表示されるチェック ボックスをオンにします。

	ルール名は一意である必要があります。開発コンピューターで設定している場合は、[IP 範囲の開始] ボックスと [IP 範囲の終了] ボックスの両方に同じ IP アドレスを入力してもかまいません。それ以外の場合は、組織の他のコンピューターからの接続に対応するために、より広い範囲の IP アドレスの入力が必要になる可能性があります。
 
5. ページの下部にある **[保存]** をクリックします。

    **注:** ファイアウォール設定に対する変更が有効になるまでに 5 分程度
    かかる可能性があります。

	これで Management Studio を使用して SQL データベースに接続する準備ができました。

1. タスク バーで、**[スタート]** をクリックし、**[すべてのプログラム]**、
    **[Microsoft SQL Server 2012]** の順にポイントして、
    **[SQL Server Management Studio]** をクリックします。

2. **[サーバーへの接続]** で、完全修飾サーバー名として 
    *serverName*.database.windows.net と指定します。Azure では、サーバー名は英数字から成る自動生成文字列です。

3. **[SQL Server 認証]** を選択します。

4. サーバーの作成時にポータルで指定した SQL Server 管理者ログインを 
    **[ログイン]** ボックスに *login*@*yourServerName* という形式で
    入力します。

5. サーバーの作成時にポータルで指定したパスワードを 
    **[パスワード]** ボックスに入力します。

8. **[接続]** をクリックして接続を確立します。

Azure では、各 SQL データベース論理サーバーはデータベースのグループを抽象的に定義したものです。各データベースの物理的な場所は、データ センター内の任意のコンピューター上になります。

旧バージョンでは、Management Studio で接続をセットアップするときに **master** に直接接続する必要がありました。この手順はもう必要ではありません。サーバー名、認証の種類、管理者の資格情報に基づいて接続できるようになりました。

SQL Server データベースでのログインとデータベースの
作成や変更などのタスクに使う SSMS ウィザードの多くは Azure 上の 
SQL データベースには利用できないため、代わりに Transact-SQL ステートメントを
使用する必要があります。次のステップでは、
こうしたステートメントの例を紹介します。サポートされているコマンドの
詳細など、SQL データベースでの Transact-SQL の使用方法の詳細については、
「[Transact-SQL リファレンス (Azure SQL データベース)][]」を参照してください。

<h2><a id="Step3" name="Step3"> </a>ステップ 3. データベースを作成および管理する</h2>

**master** データベースに接続している間に、サーバーに
新しいデータベースを作成したり、既存のデータベースを変更または削除したりできます。以下の手順では、
Management Studio を使用して一般的なデータベース管理タスクを
実行する方法について説明します。これらのタスクを実行するには、サーバーのセットアップ時に作成した
サーバー レベルのプリンシパル ログインを使用して **master** 
データベースに接続している必要があります。

Management Studio でクエリ ウィンドウを開くには、データベース フォルダーを開き、**master** を右クリックして、**[新しいクエリ]** をクリックします。

**[実行]** をクリックしてクエリを実行します。

-   新しいデータベースを作成するには、**CREATE DATABASE** ステートメントを使用します。詳細については、
    「[CREATE DATABASE (Azure SQL データベース)][]」を参照してください。以下のステートメントは、**myTestDB** という
    データベースを新たに作成し、最大サイズが 1 GB の Web Edition データベース
    として指定します。

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

-   データベースの名前、最大サイズ、エディション (Business または Web) を
    変更するなど、既存のデータベースを変更するには、**ALTER DATABASE** 
    ステートメントを使用します。詳細については、「[ALTER DATABASE (Azure SQL データベース)][]」を参照してください。以下の
    ステートメントは、前の手順で作成したデータベースの最大サイズを 
    5 GB に変更します。

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

-   既存のデータベースを削除するには、**DROP DATABASE** ステートメントを使用します。
    詳細については、「[DROP DATABASE (Azure SQL データベース)][]」を参照してください。以下のステートメントは、**myTestDB** 
    データベースを削除します。ただし、次のステップでログインを作成するために使用するので、ここでは削除しないでください。

        DROP DATABASE myTestBase;

-   master データベースには、すべてのデータベースに関する詳細を表示する際に
    使用できる **sys.databases** ビューがあります。既存のデータベースをすべて表示するには、
    次のステートメントを実行します。

        SELECT * FROM sys.databases;

-   SQL データベースでは、**USE** ステートメントを使用してデータベースを
    切り替えることはできません。代わりに、ターゲット データベースに
    直接接続する必要があります。

<div class="dev-callout-new">
 <strong>注 <span>折りたたむにはクリック</span></strong>
 <div class="dev-callout-content">
   <p>データベースを作成または変更する Transact-SQL ステートメントの多くは、
それ自体のバッチ内で実行する必要があり、他の Transact-SQL ステートメントと共に
グループ化することはできません。詳細については、上のリンクを使用して
ステートメント固有の情報を参照してください。</p>
</div>
</div>

<h2><a id="Step4" name="Step4"> </a>ステップ 4. ログインを作成および管理する</h2>

master データベースでは、ログインが記録されます。
また、どのログインにデータベースや他のログインを作成するためのアクセス許可があるかも記録されます。ログインを管理するには、
サーバーのセットアップ時に作成したサーバー レベルのプリンシパル ログインを
使用して **master** データベースに接続します。ステートメント 
**CREATE LOGIN**、**ALTER LOGIN**、**DROP LOGIN** を使用すると、
サーバー全体でログインを管理する master データベースに対して
クエリを実行できます。詳細については、「[Azure SQL データベースにおけるデータベースとログインの管理][]」を参照してください。


-   新しいサーバー レベル ログインを作成するには、**CREATE LOGIN** ステートメントを
    使用します。詳細については、「[CREATE LOGIN (Azure SQL データベース)][]」を参照してください。以下のステートメントは、**login1** という
    新しいログインを作成します。**password1** は適切なパスワードに
    置き換えてください。

        CREATE LOGIN login1 WITH password='password1';

-   データベース レベルのアクセス許可を付与するには、
    **CREATE USER** ステートメントを使用します。ログインは必ず **master** データベースに作成する
    必要がありますが、別のデータベースに接続するための
    ログインに対しては、そのデータベースで **CREATE USER** ステートメントを
    使用してデータベース レベルのアクセス許可を付与する必要があります。詳細については、「[CREATE USER (Azure SQL データベース)][]」を参照してください。

-   **myTestDB** という
    データベースにアクセス許可 login1 を付与するには、次のステップに
    従います。

    1. オブジェクト エクスプローラーを最新の状態に更新して、先ほど作成した **myTestDB** データベースを表示します。このデータベースは、**master** が含まれる**システム データベース** フォルダーに表示されます。

     接続を閉じてある場合は、[ファイル] メニューの **[オブジェクト エクスプローラーを接続]** を選択して再接続できます。「[ステップ 2. SQL データベースに接続する][]」の手順を繰り返して、データベースに接続します。

    2. **myTestDB** データベースを右クリックし、**[新しいクエリ]** を選択します。

    3. myTestDB データベースに対して次のステートメントを実行して、
        サーバー レベルのログイン **login1** に対応する **login1User** という
        データベース ユーザーを作成します。

            CREATE USER login1User FROM LOGIN login1;

-   データベースに対する適切なレベルのアクセス許可をユーザー アカウントに
    付与するには、**sp\_addrolemember** ストアド プロシージャを使用します。詳細については、
    「[sp_addrolemember (Transact-SQL)][]」を参照してください。以下のステートメントは、**login1User** を 
    **db\_datareader** ロールに追加することで、データベースに対する
    読み取り専用アクセス許可を **login1User** に付与します。

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   ログインのパスワードなど、既存のログインを必要に応じて変更するには、
    **ALTER LOGIN** ステートメントを使用します。詳細については、
    「[ALTER LOGIN (Azure SQL データベース)][]」を参照してください。**ALTER LOGIN** ステートメントは、**master** データベースに対して
    実行する必要があります。そのデータベースに接続されているクエリ ウィンドウに切り替えてください。

    以下のステートメントは、ログイン **login1** を変更してパスワードをリセットします。
    **newPassword** を適切なパスワードに、**oldPassword** をログインの
    現在のパスワードに置き換えてください。

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   既存のログインを削除するには、**DROP LOGIN** ステートメントを使用します。
    サーバー レベルでログインを削除すると、それに関連付けられている
    データベース ユーザー アカウントも削除されます。詳細については、
    「[DROP DATABASE (Azure SQL データベース)][]」を参照してください。**DROP LOGIN** 
    ステートメントは、**master** データベースに対して実行する必要があります。以下の
    ステートメントは、**login1** というログインを削除します。

        DROP LOGIN login1;

-   master データベースには、ログインを表示するのに使用できる 
    **sys.sql\_logins** ビューがあります。既存のログインをすべて表示するには、
    次のステートメントを実行します。

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>ステップ 5. 動的管理ビューを使用して SQL データベースを監視する</h2>

SQL データベースでは、個々のデータベースを監視するのに使用できる
動的管理ビューがいくつかサポートされています。以下に、これらのビューで取得できる
モニター データの種類の例を示します。すべての
種類と詳しい使用例については、「[動的管理ビューを使用した Azure SQL データベースの監視][]」を参照してください。

-   動的管理ビューに対してクエリを実行するには、アクセス許可 
    **VIEW DATABASE STATE** が必要です。アクセス許可 **VIEW DATABASE STATE** を特定の
    データベース ユーザーに付与するには、サーバー レベルの
    プリンシプル ログインで管理するデータベースに接続し、
    データベースに対して次のステートメントを実行します。

        GRANT VIEW DATABASE STATE TO login1User;

-   **sys.dm\_db\_partition\_stats** ビューを使用すると、
    データベース サイズを計算できます。**sys.dm\_db\_partition\_stats** ビューはデータベース内の
    すべてのパーティションのページと行数情報を返します。
    これらを使ってデータベース サイズを計算できます。次のクエリはデータベース サイズを 
    MB 単位で返します。

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   データベースに関連付けられている内部タスクと現在のユーザー接続に関する情報を
    取得するには、**sys.dm\_exec\_connections** ビューと 
    **sys.dm\_exec\_sessions** ビューを使用します。次のクエリは
    現在の接続に関する情報を返します。

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   キャッシュされたクエリ プランに対する集計パフォーマンス統計を取得するには、
    **sys.dm\_exec\_query\_stats** ビューを使用します。次のクエリは、
    平均 CPU 時間の上位 5 クエリに関する情報を
    返します。

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

<h2>その他のリソース</h2>

* [Azure SQL データベースの概要][]
* [Azure SQL データベースにおけるデータベースとログインの管理][]
* [動的管理ビューを使用した Azure SQL データベースの監視][]
* [Azure SQL データベース プロビジョニング モデル][]
* [SQL データベースへのユーザーの追加に関するページ][]
* [Transact-SQL リファレンス (Azure SQL データベース)][]

  [How to use Azure SQL Database (Azure SQL データベースの使用方法)]: http://www.windowsazure.com/ja-jp/develop/net/how-to-guides/sql-azure/
  [ステップ 1. SQL Server Management Studio を入手する]: #Step1
  [ステップ 2. SQL データベースに接続する]: #Step2
  [ステップ 3. データベースを作成および管理する]: #Step3
  [ステップ 4. ログインを作成および管理する]: #Step4
  [ステップ 5. 動的管理ビューを使用して SQL データベースを監視する]:
    #Step5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29062
  [SSMS Installer - Select installation type (SSMS インストーラー - インストールの種類の選択)]: /media/installer_installation_type.png
  [SSMS Installer - Select features (SSMS インストーラー - 機能の選択)]: /media/installer_feature_selection.png
  [SSMS Installer - Installation complete (SSMS インストーラー - インストールの完了)]: /media/installer_completed.png
  [Azure の管理ポータル]: http://manage.windowsazure.com/
  [Get SQL Database server name from Management Portal (管理ポータルからの SQL データベース サーバー名の取得)]: /media/portal_get_database_name.png
  [Connect to SSMS (SSMS への接続)]: /media/ssms_connect.png
  [Connect to SSMS -- properties (SSMS への接続 -- プロパティ)]: /media/ssms_connect_properties.png
  [Transact-SQL リファレンス (Azure SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336281.aspx
  [CREATE DATABASE (Azure SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (Azure SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff394109.aspx
  [DROP DATABASE (Azure SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336259.aspx
  [Azure SQL データベースにおけるデータベースとログインの管理]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (Azure SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336268.aspx
  [CREATE USER (Azure SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/ee336277.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/ja-jp/library/ms187750.aspx
  [ALTER LOGIN (Azure SQL データベース)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336254.aspx
  [動的管理ビューを使用した Azure SQL データベースの監視]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff394114.aspx
  [Azure SQL データベースの概要]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336230.aspx
  [Azure SQL データベース プロビジョニング モデル]: http://msdn.microsoft.com/ja-jp/library/ee336227.aspx
  [SQL データベースへのユーザーの追加に関するページ]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx

