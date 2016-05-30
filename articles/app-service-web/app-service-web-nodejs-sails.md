<properties
	pageTitle="Sails.js Web アプリを Azure App Service にデプロイする"
	description="Node.js アプリケーションを Azure App Service にデプロイする方法を学習します。このチュートリアルは、Sails.js Web アプリをデプロイする方法を説明します。"
	services="app-service\web"
	documentationCenter="nodejs"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="03/31/2016"
	ms.author="cephalin"/>

# Sails.js Web アプリを Azure App Service にデプロイする

このチュートリアルでは、Sails.js アプリを Azure App Service にデプロイする方法について説明します。その過程で、App Service で実行する Node.js アプリを構成する方法の一般的な知識を得ることができます。

## 前提条件

- Node.js。インストール用のバイナリは[こちら](https://nodejs.org/)にあります。
- Sails.js。インストールの手順は[こちら](http://sailsjs.org/get-started)にあります。
- Sails.js の実用的な知識。このチュートリアルは、一般的な Sail.js の実行に関する問題解決を支援するものではありません。
- Gitインストール用のバイナリは[こちら](http://www.git-scm.com/downloads)にあります。
- Azure CLI。インストールの手順は[こちら](../xplat-cli-install.md)にあります。
- Microsoft Azure アカウント。アカウントを持っていない場合は、[無料試用版にサインアップする](/pricing/free-trial/?WT.mc_id=A261C142F)か、または [Visual Studio サブスクライバー特典を有効にする](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)ことができます。

>[AZURE.NOTE] Azure App Service が動作するところを見てから Azure アカウントにサインアップするには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)をご覧ください。有効期間が短いスターター アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードや契約は必要ありません。

## 手順 1: 開発環境内で Sails.js アプリを作成する

最初に、次の手順に従って既定の Sails.js アプリをすばやく作成します。

1. 任意のコマンド ライン ターミナルと `CD` を作業ディレクトリに開きます。

2. 新しい Sails.js アプリを作成して実行します。

        sails new <appname>
        cd <appname>
        sails lift

    http://localhost:1377 で既定のホーム ページに移動できることを確認します。

## 手順 2: Azure で App Service アプリのリソースを作成する

次に App Service アプリのリソースを作成します。そのリソースに Sails.js アプリを後でデプロイします。

1. 同じターミナルで Azure にそのようにログインします。

        azure login

    画面の指示に従い、Azure サブスクリプションのある Microsoft アカウントを使用してブラウザーでログイン操作を進めます。

2. まだ Sails.js プロジェクトのルート ディレクトリにいることを確認します。次のコマンドを使用し、一意のアプリ名で Azure に App Service アプリ リソースを作成します。Web アプリの URL は http://&lt;appname>.azurewebsites.net になります。

        azure site create --git <appname>

    プロンプトに従って、デプロイ先の Azure リージョンを選択します。Azure サブスクリプションの Git/FTP デプロイの資格情報を設定したことがない場合は、資格情報の作成も求められます。

    App Service アプリのリソースの作成後:

    - Sails.js アプリは Git 初期化されます。
    - Git 初期化されたローカル リポジトリは Git リモートとして ("azure" という適切な名前が付けられた) 新しい App Service アプリに接続されます。
    - iisnode.yml ファイルがルート ディレクトリに作成されます。このファイルを使用して [iisnode](https://github.com/tjanczuk/iisnode) を構成できます。iisnode は Node.js アプリを実行するために App Service で使用されます。

## 手順 3: Sails.js アプリを構成およびデプロイする

 App Service での Sails.js アプリの使用は、次の 3 つの主要な手順で構成されます。

 - App Service で実行するようにアプリを構成する
 - アプリを App Service にデプロイする
 - デプロイに関するトラブルシューティングのために、stderr と stdout のログを読み取る

次の手順に従います。

1. ルート ディレクトリに新しい iisnode.yml ファイルを開いて、次の 2 行を追加します。

        loggingEnabled: true
        logDirectory: iisnode

    これで iisnode のログが有効になりました。この仕組みの詳細については、「[Get stdout and stderr logs from iisnode (iisnode から stdout ログと stderr ログを取得する)](app-service-web-nodejs-sails.md#iisnodelog)」をご覧ください。

2. config/env/production.js を開いて、運用環境を構成して、`port` と `hookTimeout` を設定します。

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    これらの構成設定のドキュメントは [Sails.js ドキュメント](http://sailsjs.org/documentation/reference/configuration/sails-config)にあります。

    次に、[Grunt](https://www.npmjs.com/package/grunt) に Azure のネットワーク ドライブとの互換性があることを確認する必要があります。この記事が書かれた時点では、ネットワーク ドライブをサポートしない古い [glob](https://www.npmjs.com/package/glob) パッケージ (v3.1.21) が Grunt で使用されているため、Grunt で ["ENOTSUP: operation not supported on socket" エラー](https://github.com/isaacs/node-glob/issues/205)が発生する可能性があります。以下は Grunt で [glob v5.0.14 以上](https://github.com/isaacs/node-glob/commit/bf3381e90e283624fbd652835e1aefa55d45e2c7)が使用されるように設定する手順です。

3. アプリの作成時に `npm install` はすでに実行されていることから、プロジェクトのルートに npm-shrinkwrap.json を生成します。

        npm shrinkwrap

4. npm-shrinkwrap.json を開いて、`"grunt":` の json を探して、必要な glob バージョンの依存関係を追加します。完成した json は次のようになります。

        "grunt": {
            "version": "0.4.5",
            "from": "grunt@0.4.5",
            "resolved": "https://registry.npmjs.org/grunt/-/grunt-0.4.5.tgz",
            "dependencies": {
                "glob": {
                    "version": "5.0.14",
                    "from": "glob@5.0.14",
                    "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
                }
            }
        },

5. `"glob":` を検索して、glob へのすべての参照を探します。参照が v3.1.21 以下である場合は、json を次のように変更します。

        "glob": {
            "version": "5.0.14",
            "from": "glob@5.0.14",
            "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
        }

6. 変更内容を保存して、アプリがまだローカルで実行されるかどうかを確認するために変更をテストします。

        npm install
        sails lift

4. ここで git を使用して、アプリを Azure にデプロイします。

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. 最後に、ブラウザーでライブ Azure アプリを起動します。

        azure site browse

    同じ Sails.js ホーム ページが表示されます。
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## デプロイのトラブルシューティング

App Service で何らかの理由により Sails.js アプリケーションのエラーが発生した場合は、そのトラブルシューティングを行うのに役立つ stderr ログを探します。詳細については、「[Get stdout and stderr logs from iisnode (iisnode から stdout ログと stderr ログを取得する)](app-service-web-nodejs-sails.md#iisnodelog)」をご覧ください。Sails.js アプリケーションが正常に開始した場合は、見慣れたメッセージが stdout ログに表示されます。

                .-..-.

    Sails              <|    .-..-.
    v0.12.1             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\a76e8111-663e-449d-956e-5c5deff2d304
    To shut down Sails, press <CTRL> + C at any time.

## Azure のデータベースへの接続

データベース Azure に接続するには、Azure SQL Database、MySQL、MongoDB、Azure (Redis) のキャッシュなど、Azure で任意のデータベースを作成して、対応する[データストア アダプター](https://github.com/balderdashy/sails#compatibility)を使用してそのデータベースに接続します。このセクションの手順は、Azure SQL Database に接続する方法を説明しています。

1. [ここ](../sql-database/sql-database-get-started.md)のチュートリアルに従って、新しい SQL Server に空の Azure SQL Database を作成します。既定のファイアウォール設定では、Azure サービス (たとえば App Service) に接続できます。

2. コマンド ライン ターミナルから、SQL Server のアダプターをインストールします。

        npm install sails-sqlserver --save

    package.json を変更したので、npm-shrinkwrap.json を再生成する必要があります。これを次に実行します。
    
3. node\_modules/ ディレクトリを削除します。

4. `npm shrinkwrap` を実行します。

5. npm-shrinkwrap.json をもう一度開いて、前のセクションで行ったように `glob` パッケージのバージョンを更新します。

    ここで、主なタスクに戻ります。
        
3. config/connections.js を開いて、次の json をアダプターの一覧に追加します。

        sqlserver: {
            adapter: 'sails-sqlserver',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.sqlserver, 
            database: process.env.dbname,
            options: {
                encrypt: true   // use this for Azure databases
            }
        },

4. 環境変数 (`process.env.*`) ごとに、App Service で設定する必要があります。それには、ターミナルから次のコマンドを実行します。

        azure site appsetting add dbuser="<database server administrator>"
        azure site appsetting add dbpassword="<database server password>"
        azure site appsetting add sqlserver="<database server name>.database.windows.net"
        azure site appsetting add dbname="<database name>"
        
4. config/env/production.js を開いて運用環境を構成して、`models` JSON オブジェクトに `connection` と `migrate` を設定します。

        models: {
            connection: 'sqlserver',
            migrate: 'alter'
        },

4. ターミナルから通常と同じように Sails.js [ブループリント API](http://sailsjs.org/documentation/concepts/blueprints)を[生成](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate)します。次に例を示します。

         sails generate api mywidget
     
5. すべての変更内容を保存してから Azure にプッシュします。アプリに移動して、アプリがまだ動作しているかどうかを確認します。

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. ブラウザーに作成したばかりのブループリント API にアクセスします。次に例を示します。

        http://<appname>.azurewebsites.net/widget/create
    
    この API は、作成されたエントリをブラウザー ウィンドウに返します。
    
        {"id":1,"createdAt":"2016-03-28T23:08:01.000Z","updatedAt":"2016-03-28T23:08:01.000Z"}

## その他のリソース

- [Get started with Node.js web apps in Azure App Service (Azure App Service で Node.js Web アプリの使用を開始する)](app-service-web-nodejs-get-started.md)
- [Azure アプリケーションでの Node.js モジュールの使用](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0518_2016-->