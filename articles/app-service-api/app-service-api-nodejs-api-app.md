<properties
	pageTitle="Azure App Service での Node.js API アプリ | Microsoft Azure"
	description="Azure App Service で Node.js RESTful API を作成して API アプリにデプロイする方法について説明します。"
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="05/06/2016"
	ms.author="bradygaster"/>

# Node.js RESTful API をビルドして Azure で API アプリにデプロイする

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

このチュートリアルでは、簡単な [Node.js](http://nodejs.org) API を作成し、[Git](http://git-scm.com) を使用してそれを [Azure App Service](../app-service/app-service-value-prop-what-is.md) の [API アプリ](app-service-api-apps-why-best-platform.md)にデプロイする方法について説明します。Node.js を実行できる任意のオペレーティング システムを使用でき、作業はすべて cmd.exe や bash などのコマンド ライン ツールを使用して実行します。

## 前提条件

1. [Node.js](http://nodejs.org) のインストール (このサンプルでは Node.js 4.2.2 を想定)
2. [Git](https://git-scm.com/) のインストール
1. [GitHub](https://github.com/) アカウント
1. Microsoft Azure [無料試用版アカウント](https://azure.microsoft.com/pricing/free-trial/)

## サンプル コードの取得

1. Node.js および Git のコマンドを実行できるコマンド ライン インターフェイスを開きます。

1. ローカルの Git リポジトリとして使用できるフォルダーに移動し、[サンプル コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/app-service-api-node-contact-list)を複製します。

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

	サンプル API には 2 つのエンドポイントがあります。`/contacts` に対して Get 要求を発行すると、名前と電子メール アドレスの一覧が JSON 形式で返されます。`/contacts/{id}` では、選択した連絡先のみが返されます。

## Swagger メタデータに基づく Node.js コードのスキャフォールディング

[Swagger](http://swagger.io/) は、RESTful API を記述するメタデータ用のファイル形式です。Azure App Service は、[Swagger メタデータのビルトイン サポート](app-service-api-metadata.md)を提供しています。このチュートリアルは、API 開発ワークフローをモデル化します。最初に Swagger メタデータを作成し、それを使用して API のサーバー コードをスキャフォールディングします。チュートリアルのこのセクションでは、Swagger メタデータ ファイルに基づいて Node.js サーバー コードをスキャフォールディングする方法について説明します。

>[AZURE.NOTE] スキャフォールディングの手順を実行しない場合は、「[Azure での API アプリの作成](#createapiapp)」セクションに直接移動して、新しい API アプリにサンプル コードをデプロイする作業だけを行うことができます。

1. 次のコマンドを実行して、**yo** および **generator-swaggerize** NPM モジュールをグローバルにインストールします。

		npm install -g yo
		npm install -g generator-swaggerize

	Swaggerize は、Swagger メタデータ ファイルで記述された API のサーバー コードを生成するツールです。使用する Swagger ファイルは *api.json* という名前で、複製したリポジトリの *start* フォルダーに格納されています。

2. *start* フォルダーに移動し、`yo swaggerize` コマンドを実行します。Swaggerize によって、いくつかの質問が表示されます。"**what to call this project**" という質問に対しては「contactlist」と入力し、"**path to swagger document**" に対しては「api.json」、"**Express, Hapi, or Restify**" に対しては「express」と入力します。

		yo swaggerize

	![Swaggerize コマンド ライン](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
	**注**: この手順でエラーが発生した場合の対処方法については、次の手順で説明しています。

	Swaggerize によって、アプリケーション フォルダー、スキャフォールディング ハンドラー、および構成ファイルが作成され、**package.json** ファイルが生成されます。Express ビュー エンジンを使用して、Swagger のヘルプ ページが生成されます。

3. `swaggerize` コマンドが "unexpected token" または "invalid escape sequence" エラーによって失敗した場合は、生成された *package.json* ファイルを編集してエラーの原因を修正します。`scripts` の下にある `regenerate` 行で、*api.json* の前にあるバックスラッシュを次の例のようにスラッシュに変更します。

 		"regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. スキャフォールディングされたコードを含むフォルダーに移動します (この場合は *ContactList* サブフォルダー)。

1. `npm install` を実行します。
	
		npm install
		
2. **jsonpath** NPM モジュールをインストールします。

		npm install --save jsonpath
        
    ![Jsonpath のインストール](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. **swaggerize-ui** NPM モジュールをインストールします。

		npm install --save swaggerize-ui
        
    ![Swaggerize UI のインストール](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

## スキャフォールディングされたコードのカスタマイズ

1. **start** フォルダーの **lib** フォルダーを、スキャフォールダーによって作成された **ContactList** フォルダーにコピーします。 

1. **handlers/contacts.js** ファイルのコードを次のコードで置き換えます。

	このコードでは、**lib/contactRepository.js** で生成される **lib/contacts.json** ファイルに保存されている JSON データを使用します。新しい contacts.js コードは HTTP 要求に応答して、すべての contact を取得し、JSON ペイロードとして返します。

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. **handlers/contacts/{id}.js** ファイルのコードを次のコードで置き換えます。

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. **server.js** のコードを次のコードに置き換えます。

	次のコードは、server.js ファイルの変更点がわかるように、コメントで注記を入れています。

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

        server.listen(port, function () { // fifth and final change
        });

## ローカルで実行される API でのテスト

1. Node.js コマンドラインの実行可能ファイルを使用して、サーバーを起動します。 

        node server.js

1. **http://localhost:8000/contacts** を参照すると、contact 一覧の JSON 出力が表示されます (ブラウザーによっては、ダウンロードするように求められます)。

    ![すべての Contacts API の呼び出し](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. **http://localhost:8000/contacts/2** を参照すると、その ID 値が示す contact が表示されます。

    ![特定の Contact API の呼び出し](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Swagger JSON データは **/swagger** エンドポイント経由で提供されます。

    ![Contacts Swagger Json](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. Swagger UI は **/docs** エンドポイント経由で提供されます。Swagger UI では、豊富な HTML クライアント機能を使用して API をテストできます。

    ![Swagger UI](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a> Azure ポータルでの新しい API アプリの作成

このセクションでは、新しい空の API アプリを Azure に作成する手順について説明します。その後、次のセクションで、アプリを Git リポジトリに接続し、コードの変更を継続的に配信できるようにします。

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 

1. **[新規]、[Web + モバイル]、[API アプリ]** の順にクリックします。

    ![New API app in portal](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. *azurewebsites.net* ドメイン内で一意の**アプリ名**を入力します (NodejsAPIApp に番号を付加するなどして一意にします)。

	入力した名前が既に使用されている場合は、右側に緑色のチェック マークではなく赤色の感嘆符が表示されます。この場合、別の名前を入力する必要があります。

	Azure では、この名前が API の URL のプレフィックスとして使用されます。URL 全体は、この名前に *.azurewebsites.net* を追加して構成されます。たとえば、名前が `NodejsAPIApp` である場合、URL は `nodejsapiapp.azurewebsites.net` になります。

6. **[リソース グループ]** ドロップダウンで **[新規作成]** をクリックし、**[新しいリソース グループ名]** に「NodejsAPIAppGroup」(またはお好きな名前) を入力します。

	[リソース グループ](../azure-portal/resource-group-portal.md)は、API アプリ、データベース、VM などの Azure リソースをひとまとめにしたものです。このチュートリアルでは、新しいリソース グループを作成すると便利です。チュートリアルのために作成したすべての Azure リソースを 1 回の手順で簡単に削除できるからです。

4. **[App Service プラン/場所]** をクリックし、**[新規作成]** をクリックします。

	![Create App Service plan](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

	以降の手順では、新しいリソース グループの App Service プランを作成します。App Service プランには、API アプリの実行環境となるコンピューティング リソースを指定します。たとえば、Free レベルを選択した場合、API アプリは共有 VM 上で実行され、一部の有料レベルを選択した場合は専用 VM で実行されます。App Service プランの詳細については、[App Service プランの概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)に関するページを参照してください。

5. **[App Service プラン]** ブレードに、「NodejsAPIAppPlan」(またはお好きな名前) を入力します。

5. **[場所]** ドロップダウン リストで、現在の所在地に最も近い場所を選択します。

	この設定によって、アプリが実行される Azure データ センターが指定されます。このチュートリアルでは、任意のリージョンを選択することができます。任意のリージョンを選択しても、大きな違いはありません。ただし、運用アプリでは、[待機時間](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)を最小限に抑えるために、アクセスするクライアントに最も近いサーバーを選択することをお勧めします。

5. **[価格レベル]、[すべて表示]、[F1 Free]** の順にクリックします。

	無料の価格レベルでこのチュートリアルに十分な性能が提供されます。

	![Select Free pricing tier](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. **[App Service プラン]** ブレードで、**[OK]** をクリックします。

7. **[API アプリ]** ブレードで、**[作成]** をクリックします。

## Git デプロイ用の新しい API アプリの設定

チュートリアルのこのセクションでは、デプロイに使用する資格情報と、Azure App Service の API アプリ用の Git リポジトリを作成します。Azure App Service でこのリポジトリにコミットをプッシュすることによって、API アプリにコードをデプロイします。

1. API アプリが作成されたら、ポータルのホーム ページで **[App Services]** をクリックし、作成した API アプリをクリックします。 

	ポータルに **[API アプリ]** ブレードと **[設定]** ブレードが表示されます。

    ![Portal API app blade](media/app-service-api-nodejs-api-app/portalapiappblade.png)

    ![Portal Settings blade](media/app-service-api-nodejs-api-app/portalsettingsblade.png)

1. **[設定]** ブレードで、下へスクロールして **[発行]** セクションを表示し、**[デプロイ資格情報]** をクリックします。
 
3. **[デプロイ資格情報の設定]** ブレードで、ユーザー名とパスワードを入力し、**[保存]** をクリックします。

	これらの資格情報は、Node.js コードを API アプリに発行するために使用します。次の手順では、API アプリに関連付けられた Azure の Git リポジトリを作成します。

    ![デプロイメント資格情報](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. **[設定]** ブレードで、**[展開元]、[ソースの選択]、[ローカル Git リポジトリ]** の順にクリックし、**[OK]** をクリックします。

    ![Git リポジトリの作成](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Git リポジトリが作成されると、ブレードが更新され、アクティブなデプロイが表示されます。リポジトリは新しいので、一覧にはアクティブなデプロイメントがありません。

    ![アクティブなデプロイメントがない](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Git リポジトリの URL をコピーします。コピーするには、新しい API アプリのブレードを開き、ブレードの **[要点]** セクションを確認します。**[要点]** セクションの **[Git クローン URL]** を見てください。この URL をポイントすると、URL をクリップボードにコピーするためのアイコンが右側に表示されます。このアイコンをクリックして URL をコピーします。

    ![ポータルから Git Url を取得する](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **注**: Git クローン URL は次の手順で必要になるので、どこかの時点で保存しておく必要があります。

これで、Git リポジトリにバックアップされている API アプリができたので、リポジトリにコードをプッシュして、API アプリにコードをデプロイできます。

## Azure への API コードのデプロイ

このセクションでは、次の手順を実行します。

* API のサーバー コードを含むローカル Git リポジトリを作成します。
* そのリポジトリに、Azure で API アプリ用に作成したリポジトリを指すリモートを作成します。
* ローカル リポジトリからリモート リポジトリにコードをプッシュします。 

Azure App Service に組み込まれた継続的な配信機能を使用すると、API アプリに関連付けられた Git リポジトリにコミットをプッシュするだけでコードをデプロイできます。

1. このチュートリアルの最初の部分を実行した場合は、Swaggerize Scaffolder を使用して作成した `start\ContactList` フォルダーを他のフォルダーにコピーします。実行していない場合は、`end\ContactList` フォルダーを他のフォルダーにコピーします。

1. コマンド ライン ツールで、新しいフォルダーに移動し、次のコマンドを実行して新しいローカル Git リポジトリを作成します。

        git init

     ![新しいローカル Git リポジトリ](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. 次のコマンドを実行して、API アプリのリポジトリの Git リモートを追加します。

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **注**: 文字列 "YOUR\_GIT\_CLONE\_URL\_HERE" は、前の手順でコピーした Git クローン URL に置き換えます。

1. 次のコマンドを実行して、すべてのコードを含むコミットを作成します。

        git add .
        git commit -m "initial revision"

    ![Git Commit の出力](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Azure にコードをプッシュするコマンドを実行します。パスワードの入力を求められたら、前に Azure ポータルで作成したパスワードを入力します。

        git push azure master

	API アプリへのデプロイが開始されます。

1. ブラウザーで API アプリの **[デプロイ]** ブレードに戻ると、デプロイが実行されていることが表示されます。

    ![デプロイメントの実行中](media/app-service-api-nodejs-api-app/deployment-happening.png)

    同時に、コマンド ライン インターフェイスにも実行中のデプロイの状態が反映されます。

    ![Node Js デプロイメントの実行中](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

	デプロイが完了すると、**[デプロイ]** ブレードに、コードの変更が API アプリに正常にデプロイされたことが示されます。

## Azure で実行される API でのテスト
 
3. API アプリ ブレードの **[要点]** セクションの **[URL]** をコピーします。 

    ![デプロイメントの完了](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Postman や Fiddler などの REST API クライアント (または Web ブラウザー) を使用して contacts API 呼び出しの URL を指定します。これは、API アプリの `/contacts` エンドポイントです。

    **注:** URL は `https://{your API app name}.azurewebsites.net/contacts` です。

    このエンドポイントに GET 要求を発行すると、API アプリの JSON 出力が取得されます。

    ![Postman に接続する Api](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. ブラウザーで `/docs` エンドポイントに移動し、Azure で実行されている Swagger UI を試します。

継続的な配信の設定が完了しているので、コードを変更した後は、Azure の Git リポジトリにコミットをプッシュするだけで、Azure にデプロイできます。

## 次のステップ

ここでは、Node.js を使用して、初めての API アプリを作成してデプロイしました。次のチュートリアルでは、[CORS を利用し、JavaScript クライアントから API アプリを使用する](app-service-api-cors-consume-javascript.md)方法について学習します。それ以降のチュートリアルでは、認証と承認を実装する方法について説明します。

<!-----HONumber=AcomDC_0518_2016-->