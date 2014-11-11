<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="nodejs" title="How to Access Azure SQL Database from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Node.js から Azure SQL データベースにアクセスする方法

このガイドでは、Microsoft Driver for Node.JS for SQL Server を使用して Azure SQL データベースにアクセスする方法の基本について説明します。紹介するシナリオは、**SQL データベースの作成**および **SQL データベースへの接続**です。このガイドでは、[Azure 管理ポータル][Azure 管理ポータル]からの SQL データベースの作成を取り上げます。

## 目次

-   [概念][概念]
-   [方法: 環境を設定する][方法: 環境を設定する]
-   [方法: SQL データベースを作成する][方法: SQL データベースを作成する]
-   [方法: SQL データベース接続情報を取得する][方法: SQL データベース接続情報を取得する]
-   [方法: SQL データベース インスタンスに接続する][方法: SQL データベース インスタンスに接続する]
-   [Azure のデプロイに関する考慮事項][Azure のデプロイに関する考慮事項]
-   [次のステップ][次のステップ]

## <span id="Concepts"></span></a>概念

### Azure SQL データベースとは

Azure SQL データベースは、Azure 用のリレーショナル データベース管理システムを提供し、SQL Server テクノロジを基盤としています。SQL データベースを使用すると、リレーショナル データベース ソリューションを簡単に準備してクラウドに展開することができ、分散したデータ センターを活用して、組み込みのデータ保護と自己復旧機能のメリットによるエンタープライズ クラスの可用性、拡張性、およびセキュリティを確保できます。

## Microsoft Drivers for Node.js for SQL Server とは

Microsoft Driver for Node.JS for SQL Server によって、開発者は Node.js アプリケーションから、Microsoft SQL Server または Azure SQL データベースに格納されたデータにアクセスできます。このドライバーは現在プレビュー リリースのみです。完成時には追加の機能がプロジェクトに組み込まれます。このドライバーの詳細については、Microsoft Driver for Node.JS for SQL Server プロジェクトの [Github ページ][Github ページ]および関連する [Wiki][Wiki] を参照してください。

<div class="dev-callout">
<b>注</b>
<p>Microsoft Driver for Node.JS for SQL Server は現在、プレビュー リリースとして利用することができ、Microsoft Windows および Azure オペレーティング システムでのみ利用可能なランタイム コンポーネントに依存しています。</p>
</div>

## <span id="Setup"></span></a>方法: 環境を設定する

### SQL Server Native Client をインストールする

Microsoft SQL Server Driver for Node.js は SQL Server Native Client に依存しています。この Native Client は、アプリケーションを Azure にデプロイしたときに自動的に利用できるようになりますが、ローカルの開発環境には存在していない可能性があります。SQL Server Native Client は、[Microsoft SQL Server 2012 Feature Pack][Microsoft SQL Server 2012 Feature Pack] のダウンロード ページからインストールできます。

<div class="dev-callout">
<b>注</b>
<p>SQL Server Native Client は Microsoft Windows オペレーティング システムでのみ利用できます。このドライバーは Azure でネイティブに利用できますが、Microsoft Windows 以外のオペレーティング システムで開発を行っている場合、この記事の情報に従って、アプリケーションをローカルでテストすることはできません。</p>
</div>

### Node.js をインストールする

Node.js は [][]<http://nodejs.org/#download></a> からインストールできます。お使いのオペレーティング システムのインストール パッケージが利用できない場合は、ソースから Node.js を構築できます。

## <span id="CreateServer"></span></a>方法: SQL データベースの作成

Azure SQL データベースを作成するには、次の手順に従います。

1.  [管理ポータル][Azure 管理ポータル]にログインします。
2.  ポータルの左下にある **[+ 新規]** アイコンをクリックします。

    ![新しい Azure Websites の作成][新しい Azure Websites の作成]

3.  **[SQL データベース]**、**[カスタム作成]** の順にクリックします。

    ![新しい SQL データベースのカスタム作成][新しい SQL データベースのカスタム作成]

4.  **[名前]** ボックスにデータベースの名前を入力し、**[エディション]** (WEB または BUSINESS) を選択して、データベースの **[最大サイズ]** を選択します。**[照合順序]** を選択し、**[新しい SQL データベース サーバー]** を選択します。ダイアログの下部にある矢印をクリックします。(以前に SQL データベースを作成した場合は、**[サーバー]** ボックスの一覧から既存のサーバーを選択できることに注意してください)

    ![SQL データベースの設定の指定][SQL データベースの設定の指定]

5.  管理者の名前とパスワードを入力 (およびパスワードを確認) し、新しい SQL データベースを作成するリージョンを選択して、[`Allow Azure Services to access the server`] チェック ボックスをオンにします。

    ![新しい SQL データベース サーバーの作成][新しい SQL データベース サーバーの作成]

サーバーとデータベースの情報を表示するには、Azure 管理ポータルで **[SQL データベース]** をクリックします。**[データベース]** または **[サーバー]** をクリックすると、該当する情報が表示されます。

![サーバーとデータベースの情報の表示][サーバーとデータベースの情報の表示]

## <span id="ConnectionInfo"></span></a>方法: SQL データベース接続情報を取得する

SQL データベースの接続情報を取得するには、ポータルで **[SQL データベース]** をクリックし、データベースの名前をクリックします。

![データベース情報の表示][データベース情報の表示]

次に、**[接続文字列の表示]** をクリックします。

![Show connection strings][Show connection strings]

表示されたウィンドウの [ODBC] セクションで、接続文字列の値を書き留めます。これは、ノード アプリケーションから SQL データベースに接続するときに使用する接続文字列です。パスワードは SQL データベースの作成時に使用したパスワードになります。

## <span id="Connect"></span></a>方法: SQL データベース インスタンスに接続する

### node-sqlserver をインストールする

Microsoft Driver for Node.JS for SQL Server は node-sqlserver ネイティブ モジュールとして入手できます。このモジュールのバイナリ バージョンは[ダウンロード センター][ダウンロード センター]から入手できます。バイナリ バージョンを使用するには、次のステップに従う必要があります。

1.  バイナリ アーカイブをアプリケーションの **node\_modules** ディレクトリに展開します。
2.  アーカイブから展開された **node-sqlserver-install.cmd** ファイルを実行します。これにより、**node\_modules** の下に **node-sqlserver** サブディレクトリが作成され、ドライバー ファイルがこの新しいディレクトリ構造に移動されます。
3.  **node-sqlserver-install.cmd** ファイルは不要になったため削除します。

<div class="dev-callout">
<b>注</b>
<p>npm ユーティリティを使用して node-sqlserver モジュールをインストールすることもできますが、この場合は node-gyp を呼び出してシステム上にモジュールのバイナリ バージョンを構築します。</p>
</div>

### 接続文字列を指定する

node-sqlserver を使用するには、アプリケーション内にこのモジュールがあり、接続文字列を指定する必要があります。接続文字列は、この記事の「[方法: SQL データベース接続情報を取得する][方法: SQL データベース接続情報を取得する]」セクションで返される ODBC 値です。コードは次のようになります。

    var sql = require('node-sqlserver');
    var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

### データベースのクエリを実行する

クエリを実行するには、Transact-SQL ステートメントを **query** メソッドと共に指定します。次のコードは、HTTP サーバーを作成し、Web ページを表示するときに、**Test** テーブルの **ID**、**Column1**、および **Column2** 行からデータを返します。

    var http = require('http')
    var port = process.env.port||3000;
    http.createServer(function (req, res) {
        sql.query(conn_str, "SELECT * FROM TestTable", function (err, results) {
            if (err) {
                res.writeHead(500, { 'Content-Type': 'text/plain' });
                res.write("Got error :-( " + err);
                res.end("");
                return;
            }
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            for (var i = 0; i < results.length; i++) {
                res.write("ID: " + results[i].ID + " Column1: " + results[i].Column1 + " Column2: " + results[i].Column2);
            }
            res.end("; Done.");
        });
    }).listen(port);

前の例では、結果のコレクションで一度にすべての行を返す方法を示しましたが、イベントをサブスクライブできるようにするステートメント オブジェクトを返すこともできます。これにより、返されたときに個々の行および列を受け取ることができます。次の例に、この方法を示します。

    var stmt = sql.query(conn_str, "SELECT * FROM TestTable");
    stmt.on('meta', function (meta) { console.log("We've received the metadata"); });
    stmt.on('row', function (idx) { console.log("We've started receiving a row"); });
    stmt.on('column', function (idx, data, more) { console.log(idx + ":" + data);});
    stmt.on('done', function () { console.log("All done!"); });
    stmt.on('error', function (err) { console.log("We had an error: " + err); });

## <span id="Deploy"></span></a>Azure のデプロイに関する考慮事項

<div class="dev-callout">
<b>注</b>
<p>次の情報は、Microsoft Driver for Node.JS for SQL Server のプレビュー リリースに基づいています。このセクションの情報は、Azure での node-sqlserver モジュールの使用に関する最新情報ではない可能性があります。最新情報については、Github の <a href="https://github.com/WindowsAzure/node-sqlserver">Microsoft Driver for Node.JS for SQL Server プロジェクトのページ</a>を参照してください。</p>
</div>

Azure では、実行時に node-sqlserver モジュールを動的にインストールしないため、アプリケーションの展開にバイナリ バージョンのモジュールが含まれていることを確認する必要があります。次のディレクトリ構造が存在し、以下に示すファイルが格納されていることを確認することによって、展開にこのモジュールのバイナリ バージョンが含まれていることを確認できます。

    application directory
        node_modules
            node-sqlserver
                lib

**node-sqlserver** ディレクトリに **package.json** ファイルが格納されている必要があります。**lib** ディレクトリに **sql.js** ファイルと **sqlserver.node** ファイル (node-sqlserver モジュールのコンパイル済みの形式) が格納されている必要があります。

Node.js アプリケーションの Azure へのデプロイの詳細については、[Node.js アプリケーションの作成と Azure の Web サイトへのデプロイ][Node.js アプリケーションの作成と Azure の Web サイトへのデプロイ]、および [Node.js クラウド サービスへのデプロイ][Node.js クラウド サービスへのデプロイ]に関するページを参照してください。

## <span id="NextSteps"></span></a>次のステップ

-   [Microsoft Drivers for Node.js for SQL Server の概要][Microsoft Drivers for Node.js for SQL Server の概要]
-   [Github.com 上の Microsoft Driver for Node.js for SQL Server の概要][Github ページ]

  [Azure 管理ポータル]: https://manage.windowsazure.com
  [概念]: #Concepts
  [方法: 環境を設定する]: #Setup
  [方法: SQL データベースを作成する]: #CreateServer
  [方法: SQL データベース接続情報を取得する]: #ConnectionInfo
  [方法: SQL データベース インスタンスに接続する]: #Connect
  [Azure のデプロイに関する考慮事項]: #Deploy
  [次のステップ]: #NextSteps
  [Github ページ]: https://github.com/WindowsAzure/node-sqlserver
  [Wiki]: https://github.com/WindowsAzure/node-sqlserver/wiki
  [Microsoft SQL Server 2012 Feature Pack]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29065
  []: http://nodejs.org/#download
  [新しい Azure Websites の作成]: ./media/sql-database-nodejs-how-to-use/new_website.jpg
  [新しい SQL データベースのカスタム作成]: ./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg
  [SQL データベースの設定の指定]: ./media/sql-database-nodejs-how-to-use/new-sql-db.png
  [新しい SQL データベース サーバーの作成]: ./media/sql-database-nodejs-how-to-use/db-server-settings.png
  [サーバーとデータベースの情報の表示]: ./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png
  [データベース情報の表示]: ./media/sql-database-nodejs-how-to-use/go-to-db-info.png
  [Show connection strings]: ./media/sql-database-nodejs-how-to-use/show-connection-string.png
  [ダウンロード センター]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29995
  [Node.js アプリケーションの作成と Azure の Web サイトへのデプロイ]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js クラウド サービスへのデプロイ]: /ja-jp/develop/nodejs/tutorials/getting-started/
  [Microsoft Drivers for Node.js for SQL Server の概要]: http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx
