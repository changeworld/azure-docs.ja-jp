<properties
	pageTitle="Mobile Apps 用 Node.js バックエンド サーバー SDK を操作する方法 | Azure App Service"
	description="Azure App Service Mobile Apps 用の Node.js バックエンド サーバー SDK を操作する方法について説明します。"
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="adrianhall"/>

# Azure Mobile Apps Node.js SDK の使用方法

この記事では、Azure App Service Mobile Apps で Node.js バックエンドを使用する方法についての詳細な情報と例を提供します。

> [AZURE.NOTE]この SDK はプレビュー段階です。そのため、運用環境でこの SDK を使用することは推奨されません。このドキュメントの例では、v2.0.0-beta1 の [azure-mobile-apps] を使用しています。

## <a name="Introduction"></a>はじめに

Azure App Service Mobile Apps は、Web アプリケーションにモバイルに最適化されたデータ アクセス Web API を追加する機能を提供します。Azure App Service Mobile Apps SDK は、ASP.NET と Node.js の Web アプリケーション向けに用意されています。この SDK を使用すると、次の処理を実行できます。

- データ アクセスのためのテーブル操作 (読み取り、挿入、更新、削除)
- カスタム API 操作

両方の操作では、エンタープライズ ID の Azure Active Directory だけでなく、Facebook、Twitter、Google および Microsoft などのソーシャル ID プロバイダーを含む、Azure App Service で許可されているすべての ID プロバイダーでの認証が提供されます。

各ユース ケースのサンプルは、[GitHub の samples ディレクトリ]にあります。

### <a name="howto-cmdline-basicapp"></a>コマンド ラインを使用する基本的な Node.js バックエンドの作成

Azure App Service Mobile App の Node.js バックエンドはすべて ExpressJS アプリケーションとして開始されます。ExpressJS は、Node.js で使用可能な最も人気のある Web サービス フレームワークです。次のように、基本的な [Express] アプリケーションを作成できます。

1. コマンドまたは PowerShell ウィンドウで、プロジェクト用の新しいディレクトリを作成します。

        mkdir basicapp

2. npm init を実行して、パッケージの構造を初期化します。

        cd basicapp
        npm init

    npm init コマンドでは、プロジェクトを初期化するための一連の質問が示されます。以下の出力例を参照してください。

    ![npm init の出力][0]

3. npm リポジトリから express および azure-mobile-apps ライブラリをインストールします。

        npm install --save express azure-mobile-apps

4. app.js ファイルを作成して、基本的なモバイル サーバーを実装します。

		var express = require('express'),
			azureMobileApps = require('azure-mobile-apps');

		var app = express(),
			mobile = azureMobileApps();

		// Define a TodoItem table
		mobile.tables.add('TodoItem');

		// Add the mobile API so it is accessible as a Web API
		app.use(mobile);

		// Start listening on HTTP
		app.listen(process.env.PORT || 3000);

このアプリケーションでは、単一のエンドポイント tables/TodoItem でモバイルに最適化された WebAPI が作成され、動的スキーマを使用する基になる SQL データ ストアへの非認証アクセスが可能になります。次のクライアント ライブラリのクイック スタートに従う場合に適しています。

- [iOS クライアントのクイック スタート]
- [Xamarin.iOS クライアントのクイック スタート]
- [Xamarin.Android クライアントのクイック スタート]
- [Xamarin.Forms クライアントのクイック スタート]
- [Windows Store クライアントのクイック スタート]
- [HTML/JavaScript クライアントのクイック スタート]

この基本的なアプリケーションのコードは、[GitHub の basicapp サンプル]にあります。

### <a name="howto-vs2015-basicapp"></a>Visual Studio 2015 での Node バックエンドの作成

Visual Studio 2015 には、IDE 内で Node.js アプリケーションを開発するための拡張機能が必要です。作業を開始するには、[Node.js Tools 1.1 for Visual Studio] をダウンロードしてインストールします。Node.js Tools for Visual Studio をインストールしたら、Express 4.x アプリケーションを作成します。

1. **[新しいプロジェクト]** ダイアログを開きます (**[ファイル]**、**[新規作成]**、**[プロジェクト...]** の順にクリック)。

2. **[テンプレート]**、**[JavaScript]**、**[Node.js]** の順に展開します。

3. **[Basic Azure Node.js Express 4 Application]** を選択します。

4. プロジェクト名を入力します。*[OK]* をクリックします。

	![Visual Studio 2015 の新しいプロジェクト][1]

5. **npm** ノードを右クリックし、**[新しい npm パッケージのインストール...]** を選択します。

6. 最初の Node.js アプリケーションの作成時に、npm カタログを更新することが必要な場合があります。更新が必要な場合は、更新するよう求められるので、**[更新]** をクリックします。

7. 検索ボックスに「_azure-mobile-apps_」と入力します。**azure-mobile-apps 2.0.0** パッケージをクリックしてから、**[パッケージのインストール]** をクリックします。

	![新しい npm パッケージのインストール][2]

8. **[閉じる]** をクリックします。

9. _app.js_ ファイルを開き、Azure Mobile Apps SDK のサポートを追加します。ライブラリの require ステートメントの下の 6 行目に、次のコードを追加します。

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    他の app.use ステートメントの後の約 27 行目に、次のコードを追加します。

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use('mobile');

    ファイルを保存します。

10. アプリケーションをローカルで実行するか (API は http://localhost:3000 で動作します)、Azure に発行します。

### <a name="howto-publish-to-azure"></a>Azure への Node.js バックエンドの発行

Microsoft Azure では、Azure サービスに Azure App Service Mobile Apps Node.js バックエンドを発行するための多数のメカニズムが提供されます。Visual Studio に統合されたデプロイメント ツール、コマンドライン ツールおよびソース管理に基づく継続的なデプロイメント オプションも利用します。このトピックの詳細については、[Azure App Service のデプロイメントに関するガイド]を参照してください。

Azure App Service には、デプロイ前に確認する必要がある Node.js アプリケーションに関する以下の特定のアドバイスがあります。

- [Node バージョンの指定]方法
- [Node モジュールの使用]方法

## <a name="TableOperations"></a>テーブル操作

azure-mobile-apps Node.js Server SDK では、WebAPI として SQL Azure に格納されるデータ テーブルを公開するためのメカニズムが提供されます。以下の 5 つの操作が提供されます。

| 操作 | 説明 |
| --------- | ----------- |
| GET /tables/\_tablename\_ | テーブルのすべてのレコードを取得します。 |
| GET /tables/\_tablename\_/:id | テーブルの特定のレコードを取得します。 |
| POST /tables/\_tablename\_ | テーブルに新しいレコードを作成します。 |
| PATCH /tables/\_tablename\_/:id | テーブルの既存のレコードを更新します。 |
| DELETE /tables/\_tablename\_/:id | テーブルのレコードを削除します。 |

この WebAPI は [OData] をサポートし、テーブル スキーマを拡張して[オフライン データ同期]をサポートします。

### <a name="howto-dynamicschema"></a>動的スキーマを使用するテーブルの定義

テーブルを使用する前に定義する必要があります。テーブルは、静的スキーマで (開発者がスキーマ内の列を定義する場合)、または動的に (SDK で受信要求に基づいてスキーマを制御する場合) 定義できます。さらに、開発者は、定義に Javascript コードを追加することで、WebAPI の特定の側面を制御できます。

ベスト プラクティスとして、テーブル ディレクトリ内の Javascript ファイルに各テーブルを定義し、tables.import() メソッドを使用してテーブルをインポートする必要があります。basic-app を拡張すると、app.js ファイルが次のように調整されます。

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
	    mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

次のように ./tables/TodoItem.js にテーブルを定義します。

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

テーブルでは、既定で動的スキーマが使用されます。動的スキーマをグローバルに無効にするには、Azure ポータル内のアプリ設定の **MS\_DynamicSchema** を false に設定します。

完全な例は [GitHub の todo サンプル]にあります。

### <a name="howto-staticschema"></a>静的スキーマを使用するテーブルの定義

WebAPI を使用して公開する列を明示的に定義することができます。azure-mobile-apps Node.js SDK では、指定した一覧にオフライン データ同期に必要な他の列が自動的に追加されます。たとえば、クイック スタート クライアント アプリケーションには、text (文字列) と complete (ブール値) という 2 つの列を持つテーブルが必要になります。これは、テーブル定義 JavaScript ファイル (テーブル ディレクトリにある) に次のように定義できます。

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

静的にテーブルを定義する場合は、tables.initialize() メソッドを呼び出して起動時にデータベース スキーマを作成する必要もあります。tables.initialize() メソッドは [Promise] を返します。これは、Web サービスがデータベースの初期化前に要求を処理しないようにするために使用されます。

### <a name="howto-sqlexpress-setup"></a>ローカル コンピューター上での開発データストアとしての SQL Express の使用

Azure Mobile Apps Node SDK には、データをすぐに使用できる 3 つのオプションが用意されています。

- **メモリ** ドライバーを使用して、非永続ストアの例を提供します。
- **mssql** ドライバーを使用して、開発用の SQL Express データ ストアを提供します。
- **mssql** ドライバーを使用して、実稼働用の SQL Azure データ ストアを提供します。

Azure Mobile Apps Node.js SDK では [mssql Node.js パッケージ]を使用して、SQL Express と SQL Azure への両方の接続を確立および使用します。このパッケージでは、SQL Express インスタンスで TCP 接続を有効にする必要があります。

> [AZURE.NOTE]メモリ ドライバーでは、テスト用の完全な機能セットは提供されません。ローカルでバックエンドをテストする場合は、SQL Express データ ストアと sql ドライバーを使用することをお勧めします。

1. [Microsoft SQL Server 2014 Express] をダウンロードしてインストールします。必ず、SQL Server 2014 Express with Tools エディションをインストールしてください。明示的に 64 ビットのサポートが要求された場合を除き、32 ビット バージョンを使用することで実行時のメモリ使用量が少なくなります。

2. SQL Server 2014 構成マネージャーを実行します。

  1. 左側のツリー メニューにある **[SQL Server ネットワークの構成]** ノードを展開します。
  2. **[SQLEXPRESS のプロトコル]** をクリックします。
  3. **[TCP/IP]** を右クリックし、**[有効化]** を選択します。ポップアップ ダイアログで **[OK]** をクリックします。
  4. **[TCP/IP]** を右クリックし、**[プロパティ]** を選択します。
  5. **[IP アドレス]** タブをクリックします。
  6. **[IPAll]** ノードを見つけます。**[TCP ポート]** フィールドに、「**1433**」と入力します。
  
	 	 ![Configure SQL Express for TCP/IP][3]
  7. **[OK]** をクリックします。ポップアップ ダイアログで **[OK]** をクリックします。
  8. 左側のツリー メニューにある **[SQL Server のサービス]** をクリックします。
  9. **[SQL Server (SQLEXPRESS)]** を右クリックし、**[再起動]** を選択します。
  10. SQL Server 2014 構成マネージャーを閉じます。

3. SQL Server 2014 Management Studio を実行して、ローカルの SQL Express インスタンスに接続します。

  1. オブジェクト エクスプローラーでインスタンスを右クリックし、**[プロパティ]** を選択します。
  2. **[セキュリティ]** ページを選択します。
  3. **[SQL Server 認証モードと Windows 認証モード]** が選択されていることを確認します。
  4. **[OK]** をクリックします。

  		![Configure SQL Express Authentication][4]

  5. オブジェクト エクスプローラーで、**[セキュリティ]**、**[ログイン]** の順に展開します。
  6. **[ログイン]** を右クリックし、**[新しいログイン...]** を選択します。
  7. ログイン名を入力します。**[SQL Server 認証]** を選択します。パスワードを入力し、**[パスワードの確認入力]** に同じパスワードを入力します。パスワードは、Windows の複雑さの要件を満たしている必要があります。
  8. **[OK]** をクリックします。

  		![Add a new user to SQL Express][5]

  9. 新しいログインを右クリックし、**[プロパティ]** を選択します。
  10. **[サーバーの役割]** ページを選択します。
  11. **[dbcreator]** サーバーの役割の横にあるチェック ボックスをオンにします。
  12. **[OK]** をクリックします。
  13. SQL Server 2015 Management Studio を閉じます。

選択したユーザー名とパスワードは必ず記録してください。特定のデータベース要件に応じて、他のサーバーの役割またはアクセス許可の割り当てが必要になる場合があります。

Node.js アプリケーションは、**SQLCONNSTR\_MS\_TableConnectionString** 環境変数を読み取って、このデータベースの接続文字列を確認します。ご使用の環境内にこれを設定することができます。たとえば、PowerShell を使用して、以下のようにこの環境変数を設定することができます。

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

TCP/IP 接続を介して、データベースにアクセスし、接続用のユーザー名とパスワードを入力する必要があることに注意してください。

### <a name="howto-config-localdev"></a>ローカル開発用のプロジェクトの構成

Azure Mobile Apps は、ローカル ファイルシステムから _azureMobile.js_ という JavaScript ファイルを読み取ります。実稼働環境で Azure Mobile Apps SDK を構成するときは、このファイルを使用しないでください。代わりに、[Azure ポータル]内の [アプリ設定] を使用します。_azureMobile.js_ ファイルでは、構成オブジェクトをエクスポートする必要があります。最も一般的な設定は次のとおりです。

- データベース設定
- 診断ログ設定
- 代替 CORS 設定

上記のデータベース設定を実装する _azureMobile.js_ ファイルの例を次に示します。

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

パスワードがクラウドに保存されないように、_azureMobile.js_ を _.gitignore_ ファイル (または他のソース コード管理の無視ファイル) に追加することをお勧めします。必ず、[Azure ポータル]内の [アプリ設定] で実稼働設定を構成してください。

### <a name="howto-use-sqlazure"></a>実稼働データストアとしての SQL Azure の使用

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Azure App Service アプリケーションのすべての種類でデータ ストアとして SQL Azure を使用します。モバイル アプリ バックエンドをまだ作成していない場合は、次の手順に従って新しいものを作成します。

1. [Azure ポータル]にログインします。

2. ウィンドウの左上で、**[+ 新規]**、**[Web + モバイル]**、**[モバイル アプリ]** の順にクリックし、モバイル アプリ バックエンドの名前を指定します。

3. **[リソース グループ]** ボックスで、アプリと同じ名前を入力します。

4. 既定の App Service プランが選択されます。App Service プランを変更するには、[App Service プラン]、**[+ 新規作成]** の順にクリックします。新しい App Service プランの名前を指定し、適切な場所を選択します。[価格レベル] をクリックし、サービスに適切な価格レベルを選択します。**[すべて表示]** を選択して、**Free** や **Shared** などの価格オプションをさらに表示します。価格レベルを選択したら、**[選択]** をクリックします。**[App Service プラン]** ブレードに戻り、**[OK]** をクリックします。

5. **[作成]** をクリックします。これにより、モバイル アプリ バックエンドが作成され、後でサーバー プロジェクトをデプロイすることができます。モバイル アプリ バックエンドのプロビジョニングには数分かかる場合があります。モバイル アプリ バックエンドのプロビジョニングが完了すると、ポータルでモバイル アプリ バックエンドの **[設定]** ブレードが開きます。

モバイル アプリ バックエンドを作成したら、既存の SQL Azure データベースをモバイル アプリ バックエンドに接続するか、新しい SQL Azure データベースを作成するかを選択できます。この方法では、新しい SQL データベースを作成します。

> [AZURE.NOTE]新しいモバイル アプリ バックエンドと同じ場所に、既にデータベースがある場合は、**[既存のデータベースを使用する]** を選ぶと、そのデータベースを選択できます。別の場所にあるデータベースを使用することは、帯域幅コストと待機時間が増加するため、お勧めしません。

6. 新しいモバイル アプリ バックエンドで、**[設定]**、**[モバイル アプリ]**、**[データ]**、**[+ 追加]** の順にクリックします。

7. **[データ接続の追加]** ブレードで、**[SQL Database - 必要な設定の構成]**、**[新しいデータベースの作成]** の順にクリックします。**[名前]** フィールドに新しいデータベースの名前を入力します。

8. **[サーバー]** をクリックします。**[新しいサーバー]** ブレードで、**[サーバー名]** フィールドに一意のサーバー名を入力し、**[サーバー管理ログイン]** と **[パスワード]** に適切な内容を指定します。**[Azure サービスにサーバーへのアクセスを許可する]** チェック ボックスがオンになっていることを確認します。**[OK]** をクリックします。

	![SQL Azure データベースの作成][6]

9. **[新しいデータベース]** ブレードで、**[OK]** をクリックします。

10. **[データ接続の追加]** ブレードに戻り、**[接続文字列]** を選択して、データベースの作成時に指定したログイン名とパスワードを入力します。既存のデータベースを使用する場合は、そのデータベースのログイン資格情報を入力します。入力したら、**[OK]** をクリックします。

11. もう一度 **[データ接続の追加]** ブレードに戻り、**[OK]** をクリックしてデータベースを作成します。

<!--- END OF ALTERNATE INCLUDE -->

データベースの作成には数分かかる場合があります。**[通知]** 領域を使用して、デプロイの進行状況を監視します。データベースのデプロイが正常に完了するまでは、先に進まないでください。正常にデプロイされると、モバイル バックエンドの [アプリ設定] で、SQL Azure データベース インスタンスの接続文字列が作成されます。このアプリ設定は、**[設定]** > **[アプリケーション設定]** > **[接続文字列]** で確認できます。

### <a name="howto-tables-auth"></a>テーブルへのアクセスに対する認証の要求

テーブル エンドポイントで App Service 認証を使用する場合は、まず、[Azure ポータル]で App Service 認証を構成する必要があります。Azure App Service での認証構成の詳細については、以下の使用する ID プロバイダーの構成ガイドを参照してください。

- [Azure Active Directory 認証の構成方法]
- [Facebook 認証の構成方法]
- [Google 認証の構成方法]
- [Microsoft 認証の構成方法]
- [Twitter 認証の構成方法]

各テーブルには、テーブルへのアクセスを制御するために使用できる access プロパティがあります。次のサンプルでは、認証を必要とする静的に定義されたテーブルを示します。

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

access プロパティには、次の 3 つの値を使用できます。

  - *anonymous* は、認証なしでデータを読み取る許可をクライアント アプリケーションに与えることを示します。
  - *authenticated* は、クライアント アプリケーションが要求で有効な認証トークンを送信する必要があることを示します。
  - *disabled* は、このテーブルが現在無効になっていることを示します。

access プロパティが定義されていない場合は、非認証アクセスが許可されます。

### <a name="howto-tables-disabled"></a>特定のテーブル操作へのアクセスの無効化

テーブルでの表示だけでなく、個々の操作を制御するために access プロパティを使用できます。操作には以下の 4 つがあります。

  - *read* は、テーブルに対する RESTful GET 操作です。
  - *insert* は、テーブルに対する RESTful POST 操作です。
  - *update* は、テーブルに対する RESTful PATCH 操作です。
  - *delete* は、テーブルに対する RESTful DELETE 操作です。

たとえば、読み取り専用の非認証テーブルを指定するとします。これは、次のようなテーブル定義で指定することができます。

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>テーブル操作で使用されるクエリの調整

テーブル操作の一般的な要件は、データの制限付きビューを提供することです。たとえば、ユーザーが独自のレコードの読み取りまたは更新のみができるように、認証済みユーザー ID がタグ付けされているテーブルを提供できます。次のようなテーブル定義では、この機能が提供されます。

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
		context.query.where({ userId: context.user.id });
		return context.execute();
	});

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
	    context.item.userId = context.user.id;
	    return context.execute();
    }

    module.exports = table;

通常はクエリを実行する操作には、where 句で調整できる query プロパティがあります。query プロパティは、OData クエリをデータ バックエンドが処理できるものに変換する際に使用する [QueryJS] オブジェクトです。(上記のような) 単純な等式の場合、マップを使用できます。特定の SQL 句の追加も比較的容易です。

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>テーブルでの論理削除の構成

論理削除では実際にレコードは削除されません。代わりに、削除列を true に設定して、データベース内のレコードを削除済みとしてマークします。Mobile Client SDK で IncludeDeleted() が使用されない限り、Azure Mobile Apps SDK によって結果から論理削除レコードが自動的に削除されます。論理削除のテーブルを構成するには、テーブル定義ファイルで softDelete プロパティを設定します。例を以下に示します。

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
		"complete": "boolean"
	};

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Turn on Soft Delete
	table.softDelete = true;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

クライアント アプリケーションから、WebJob またはカスタム メカニズムを使用して、レコードを消去するためのメカニズムを確立する必要があります。

### <a name="howto-tables-seeding"></a>データでのデータベースのシード処理

新しいアプリケーションを作成する場合、データでのテーブルのシード処理が必要になることがあります。これは、以下のようにテーブル定義 JavaScript ファイル内で行うことができます。

	var azureMobileApps = require('azure-mobile-apps');

	var table = azureMobileApps.table();

	// Define the columns within the table
	table.columns = {
		"text": "string",
		"complete": "boolean"
	};
	table.seed = [
		{ text: 'Example 1', complete: false },
		{ text: 'Example 2', complete: true }
	];

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

データのシード処理は、テーブルが Azure Mobile Apps SDK で作成されている場合にのみ行われることに注意してください。データベース内にテーブルが既に存在する場合、テーブルにデータは挿入されません。動的スキーマが有効な場合、スキーマはシード処理されたデータから推論されます。

initialize() メソッドを明示的に呼び出して、サービスの実行開始時にテーブルを作成することをお勧めします。

## <a name="CustomAPI"></a>カスタム API

/tables エンドポイント経由のデータ アクセス API に加え、Azure Mobile Apps ではカスタム API も提供できます。カスタム API はテーブル定義と同じような方法で定義され、認証を含む、すべての同じ機能にアクセスできます。

カスタム API で App Service 認証を使用する場合は、まず、[Azure ポータル]で App Service 認証を構成する必要があります。Azure App Service での認証構成の詳細については、以下の使用する ID プロバイダーの構成ガイドを参照してください。

- [Azure Active Directory 認証の構成方法]
- [Facebook 認証の構成方法]
- [Google 認証の構成方法]
- [Microsoft 認証の構成方法]
- [Twitter 認証の構成方法]

### <a name="howto-customapi-basic"></a>単純なカスタム API の定義

カスタム API は、テーブル API とほぼ同じ方法で定義されます。

1. **api** ディレクトリを作成します。
2. **api** ディレクトリに API 定義 JavaScript ファイルを作成します。
3. import メソッドを使用して、**api** ディレクトリをインポートします。

前に使用した basic-app サンプルに基づくプロトタイプの api 定義を以下に示します。

	var express = require('express'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

	// Import the Custom API
	mobile.api.import('./api');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);

ここでは、_Date.now()_ メソッドを使用してサーバーの日付を返す単純な API を使用します。api/date.js ファイルを以下に示します。

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};

	module.exports = api;

各パラメーターは、標準的な RESTful 動詞 (GET、POST、PATCH または DELETE) のいずれかです。メソッドは、必要な出力を送信する標準的な [ExpressJS ミドルウェア]関数です。

### <a name="howto-customapi-auth"></a>カスタム API へのアクセスに対する認証の要求

Azure Mobile Apps SDK では、テーブル エンドポイントとカスタム API の両方に対して同じ方法で認証を実装します。前のセクションで開発した API に認証を追加するには、**access** プロパティを追加します。

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};
	// All methods must be authenticated.
	api.access = 'authenticated';

	module.exports = api;

以下のように、特定の操作で認証を指定することもできます。

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};
	// The GET methods must be authenticated.
	api.get.access = 'authenticated';

	module.exports = api;

認証を必要とするカスタム API には、テーブル エンドポイントで使用されるものと同じトークンを使用する必要があります。

## <a name="Debugging"></a>デバッグとトラブルシューティング

Azure App Service では、Node.js アプリケーションに関するいくつかのデバッグとトラブルシューティングの手法が提供されます。これらの手法をすべて使用できます。

- [Azure App Service の監視]
- [Azure App Service での診断ログの有効化]
- [Visual Studio での Azure App Service のトラブルシューティング]

### <a name="howto-diagnostic-logs"></a>Azure Mobile Apps の診断ログへの書き込み

Node.js アプリケーションは、広範囲の診断ログ ツールにアクセスできます。Azure Mobile Apps Node.js SDK は、内部で診断ログに [Winston] を使用します。これを自動的に有効にするには、デバッグ モードを有効にするか、[Azure ポータル]で **MS\_DebugMode** アプリ設定を true に設定します。生成されたログは、[Azure ポータル]の [診断ログ] に表示されます。

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[iOS クライアントのクイック スタート]: app-service-mobile-ios-get-started.md
[Xamarin.iOS クライアントのクイック スタート]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android クライアントのクイック スタート]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms クライアントのクイック スタート]: app-service-mobile-xamarin-forms-get-started.md
[Windows Store クライアントのクイック スタート]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/JavaScript クライアントのクイック スタート]: app-service-html-get-started.md
[オフライン データ同期]: app-service-mobile-offline-data-sync.md
[Azure Active Directory 認証の構成方法]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook 認証の構成方法]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google 認証の構成方法]: app-service-mobile-how-to-configure-google-authentication.md
[Microsoft 認証の構成方法]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter 認証の構成方法]: app-service-mobile-how-to-configure-twitter-authentication.md
[Azure App Service のデプロイメントに関するガイド]: ../app-service-web/web-site-deploy.md
[Azure App Service の監視]: ../app-service-web/web-sites-monitor.md
[Azure App Service での診断ログの有効化]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Visual Studio での Azure App Service のトラブルシューティング]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[Node バージョンの指定]: ../nodejs-specify-node-version-azure-apps.md
[Node モジュールの使用]: ../nodejs-use-node-mobiles-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/

[Azure ポータル]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/ja-JP/docs/Web/JavaScript/Reference/Global_Objects/Promise
[GitHub の basicapp サンプル]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[GitHub の todo サンプル]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[GitHub の samples ディレクトリ]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js パッケージ]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/ja-JP/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS ミドルウェア]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_1203_2015-->