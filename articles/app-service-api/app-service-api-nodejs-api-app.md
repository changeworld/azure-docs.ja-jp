<properties
	pageTitle="Azure App Service での Node.js API アプリの構築とデプロイ"
	description="Node.js API アプリ パッケージを作成して Azure App Service にデプロイする方法について説明します。"
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra "/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="11/27/2015"
	ms.author="bradygaster"/>

# Azure App Service での Node.js API アプリの構築とデプロイ

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 前提条件
1. [Node.js](nodejs.org) が開発用コンピューターで実行されている (このサンプルでは、Node.js バージョン 4.2.2 がインストールされていることを前提としています)。
1. [GitHub](https://github.com/) アカウント
1. Microsoft Azure [無料試用版アカウント](https://azure.microsoft.com/pricing/free-trial/)
1. ローカルの開発用ワークステーションに Git がインストールされている

## セットアップ手順
Node.js コマンド ラインを使用して次のコマンドを実行します。Swaggerize Yo generator を使用すると、Swagger JSON ファイルに定義されている HTTP 要求の処理に必要になるベースライン Node.js コードをスキャフォールディングすることができます。
 
1. **yo** と **generator-swaggerize** NPM モジュールをグローバルにインストールします。

        npm install -g yo
	    npm install -g generator-swaggerize
		
1. [サンプル コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/app-service-api-node-contact-list)を複製します。

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git
				
1. ソース コードに含まれる **api.json** ファイルに基づいて、API をスキャフォールディングするコマンドを実行します。**api.json** ファイルは、実際の API を示す Swagger ファイルです。次の手順で、"yo swaggerize" コマンドを使用してスキャフォールディングします。

        yo swaggerize
        
    **注:** API.json は、API Apps プレビュー期間の *apiapp.json* ファイルとは異なる内容です。

1. Swaggerize はハンドラーをスキャフォールディングし、**api.json** に含まれる Swagger メタデータに合わせて構成します。スキャフォールディング プロセス中は、GitHub ユーザー名や電子メール アドレスなど、さまざまな質問に回答します。この情報は、アプリケーションのフォルダーに **package.json** ファイルを生成するために使用されます。スキャフォールディング プロセス中の質問で最も重要な点は、質問されたときに **express** を選択することです。このサンプルでは、後で Azure で API アプリを実行するときに express ビュー エンジンを利用して Swagger ヘルプ ページを生成するためです。

	![Swaggerize コマンド ライン](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
1. スキャフォールディングされたコードを含むフォルダーに移動します (この場合は *ContactList* サブフォルダー)。次に **jsonpath** NPM モジュールをインストールします。

        npm install --save jsonpath
        
    コマンドライン エクスペリエンスにインストール結果が表示されます。

    ![Jsonpath のインストール](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. **swaggerize-ui** NPM モジュールをインストールします。

        npm install --save swaggerize-ui
        
    コマンドライン エクスペリエンスにインストール結果が表示されます。

    ![Swaggerize UI のインストール](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

1. **start** フォルダーの **lib** フォルダーを、スキャフォールダーによって作成された **ContactList** フォルダーにコピーします。

1. **handlers/contacts.js** ファイルのコードを次のコードで置き換えます。このコードでは、**lib/contactRepository.js** で生成される **lib/contacts.json** ファイルに保存されている JSON データを使用します。次の新しい contacts.js コードは HTTP 要求に応答して、このコードを使用するすべての contact を取得します。

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. **handlers/contacts/{id}.js** ファイルのコードを次のコードで置き換えます。このコードでは、**lib/contactRepository.js** を使用して HTTP 要求で要求される contact を取得し、JSON ペイロードとして返します。

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. **server.js** のコードを次のコードに置き換えます。次のコードは、server.js ファイルの変更点がわかるように、コメントで注記を入れています。

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth change
            app.setHost(undefined); // sixth and final change
        });

1. Node.js コマンドラインの実行可能ファイルを使用して、サーバーを起動します。

        node server.js

    このコマンドを実行すると、Node.js HTTP サーバーが起動し、API のサービスが開始されます。

1. ****http://localhost:8000/contacts** を参照すると、contact 一覧の JSON 出力が表示されます (ブラウザーによっては、ダウンロードするように求められます)。

    ![すべての Contacts API の呼び出し](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. ****http://localhost:8000/contacts/2** を参照すると、その ID 値が示す contact が表示されます。

    ![特定の Contact API の呼び出し](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Swagger JSON データは **/swagger** エンドポイント経由で提供されます。

    ![Contacts Swagger Json](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. Swagger UI は **/docs** エンドポイント経由で提供されます。Swagger UI では、豊富な HTML クライアント機能を使用して API をテストできます。

    ![Swagger UI](media/app-service-api-nodejs-api-app/swagger-ui.png)

## Azure ポータルでの新しい API アプリの作成
このセクションでは、新しい空の API アプリを Azure に作成するプロセスの手順について説明します。次に、アプリを Git リポジトリに接続し、コードの変更を継続的に配信できるようにします。

ソース コードを複製した GitHub リポジトリは、デプロイメントのためにコードをプッシュするリポジトリと同じではありません。サンプル GitHub リポジトリにはコードの "Start" 状態が含まれていましたが、現在では、コードの "end" 状態をスキャフォールディングするようになりました。API アプリに関連付けられた Git リポジトリにのみ、そのコードをプッシュする必要があります。最初の手順は、Azure ポータルを使用して API アプリを作成することです。次に、

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 

1. 新しい API アプリを作成します。

    ![New Api App Portal](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

1. 新しい API アプリを既存のリソース グループまたは App Service プランに追加するか、スクリーンショットのように、新しいリソース グループと App Service プランを作成することができます。

    ![Api App Creation Blade](media/app-service-api-nodejs-api-app/api-app-creation-blade.png)

1. ポータルに API アプリが作成されたら、次のように API アプリの設定を含むブレードを参照します。

    ![ポータルの設定へのナビゲーション](media/app-service-api-nodejs-api-app/portal-nav-to-settings.png)

1. [設定] メニューの **[デプロイメント資格情報]** ナビゲーション項目をクリックします。ブレードが開いたら、Node.js を API アプリに発行するときに使用するユーザー名とパスワードを追加します。**[デプロイメント資格情報の設定]** ブレードの **[保存]** をクリックします。

    ![デプロイメント資格情報](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. デプロイメント資格情報を設定したら、App Service に関連付けられている Git リポジトリを作成できます。このリポジトリにコードをプッシュするたびに、Azure App Service は変更を選択し、API アプリ インスタンスに直接デプロイします。Git リポジトリを作成してサイトに関連付けるには、次のように [設定] メニュー ブレードの **[継続的なデプロイ]** メニュー項目をクリックします。次に、**[ソースの選択]** ブレードの **[ローカル Git リポジトリ]** オプションを選択します。次に、[OK] をクリックして Git リポジトリを作成します。

    ![Git リポジトリの作成](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Git リポジトリが作成されると、ブレードが更新され、アクティブなデプロイメントが表示されます。リポジトリは新しいので、一覧にはアクティブなデプロイメントがありません。

    ![アクティブなデプロイメントがない](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. 最後に、ポータルから Git リポジトリの URL をコピーします。コピーするには、新しい API アプリのブレードを開き、ブレードの **[要点]** セクションを確認します。[要点] セクションに **[Git クローン URL]** が表示されます。その横に、クリップボードに URL をコピーするアイコンがあります。アイコンをクリックして URL をコピーするか (URL にマウス ポイントを移動するとボタンが表示されます)、URL 全体を選択してクリップボードにコピーします。

    ![ポータルから Git Url を取得する](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **注**: Git クローン URL は次の手順で必要になるので、どこかの時点で保存しておく必要があります。

以上の手順で Git リポジトリに新しい API アプリがバックアップされました。リポジトリにコードをプッシュし、Azure の継続的なデプロイメント機能を利用して変更を自動的にデプロイできるようになります。

## API アプリの複製を Azure にデプロイする
Azure App Service が提供する組み込みの継続的な配信機能を使用すると、App Service に関連付けられた Git リポジトリにコードをコミットするだけで、Azure でソース コードが選択され、API アプリにデプロイされます。

1. 入門用コードを含め、GitHub から複製したメイン リポジトリの外部で実行するコードの新しいローカル Git リポジトリを作成するときに、Swaggerize スキャフォールダーで作成された **src/end/ContactList** をデスクトップまたは他のフォルダーにコピーします。 

1. Node.js コマンド ライン エクスペリエンスを使用して新しいフォルダーに移動します。そのフォルダーで、次のコマンドを実行して新しいローカル Git リポジトリを作成します。

        git init

    このコマンドでローカル Git リポジトリが作成され、新しいリポジトリが初期化されたという確認メッセージが表示されます。

    ![新しいローカル Git リポジトリ](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Node.js コマンド ライン エクスペリエンスを使用して次のコマンドを実行します。Git リモートがローカル リポジトリに追加されます。リモート リポジトリは、先ほど作成し、Azure で実行されている API アプリと関連付けられているものです。

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **注**: 上の文字列 "YOUR\_GIT\_CLONE\_URL\_HERE" を、前の手順でコピーした Git クローン URL に置き換えます。

1. 次に、Node.js コマンド ライン エクスペリエンスから以下の 2 つのコマンドを実行します。

        git add .
        git commit -m "initial revision"

    2 つのコマンドを完了すると、コマンド ライン ウィンドウに次のスクリーンショットのような内容が表示されます。

    ![Git Commit の出力](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. コードを Azure にプッシュして、API アプリへのデプロイメントをトリガーするには、Node.js コマンドラインで次のコマンドを実行します。パスワードの入力を求められたら、Azure ポータルでデプロイメント資格情報を作成するときに使用したパスワードを入力します。

        git push azure master

1. API アプリの **[継続的なデプロイ]** ブレードに戻ると、実行中のデプロイメントが表示されます。

    ![デプロイメントの実行中](media/app-service-api-nodejs-api-app/deployment-happening.png)

    同時に、Node.js コマンド ラインにも実行中のデプロイメントの状態が反映されます。

    ![Node Js デプロイメントの実行中](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

1. デプロイメントが完了すると、**[継続的なデプロイ]** ブレードに、コードの変更のデプロイメントが API アプリに反映されます。API アプリ ブレードの **[要点]** セクションの **[URL]** をコピーします。

    ![デプロイメントの完了](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Postman や Fiddler などの REST API クライアント (または Web ブラウザー) を使用して contacts API 呼び出しの URL を指定します。これは、API アプリの **/contacts** エンドポイントです。

    **注:** http://myapiapp.azurewebsites.net/contacts のような URL です。

    このエンドポイントに GET 要求を発行すると、API アプリの JSON 出力が表示されます。

    ![Postman に接続する Api](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

## 次のステップ

ここでは、Node.js を使用して、初めての API アプリを作成してデプロイしました。API Apps 入門シリーズの次のチュートリアルでは、[CORS を利用し、JavaScript クライアントから API アプリを使用する](app-service-api-cors-consume-javascript.md)方法について学習します。

このサンプルを応用すれば、ハンドラーにコードを追加して、データベースや、API アプリ インスタンスのディスクにデータを格納できるようになります。[継続的なデプロイ] を有効にしたので、API アプリの機能を変更し、拡張する操作は、コードを変更して Git リポジトリにプッシュする操作と同じくらい簡単になります。

<!---HONumber=AcomDC_0128_2016-->