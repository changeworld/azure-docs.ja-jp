---
title: "Azure での Node.js とMongoDB Web アプリの構築 | Microsoft Docs"
description: "MongoDB 接続文字列を使用して DocumentDB データベースに接続する Node.js アプリを Azure で動作させる方法について説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/30/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 8dcb006a8cf167cdbfb67de5a11dabf0edbbe41c
ms.lasthandoff: 04/22/2017


---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Azure での Node.js とMongoDB Web アプリの構築
このチュートリアルでは、Azure で Node.js Web アプリを作成し、MongoDB データベースに接続する方法について説明します。 完了すると、MEAN アプリケーション (MongoDB、Express、AngularJS、および Node.js) が [Azure App Service Web Apps](app-service-web-overview.md) で実行されます。

![Azure App Service で実行されている MEAN.js アプリ](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

## <a name="before-you-begin"></a>開始する前に

このサンプルを実行する前に、前提条件となる以下をローカルにインストールします。

1. [Git をダウンロードし、インストールします](https://git-scm.com/)
1. [Node.js と NPM をダウンロードし、インストールします](https://nodejs.org/)
1. [MongoDB Community Edition をダウンロードし、インストールして、実行します](https://docs.mongodb.com/manual/administration/install-community/) 
1. [Azure CLI 2.0 をダウンロードし、インストールします](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb-database"></a>ローカルの MongoDB データベースをテストする
この手順では、ローカルの MongoDB データベースが稼働していることを確認します。

ターミナル ウィンドウを開き、`CD` コマンドで MongoDB インストールの `bin` ディレクトリに移動します。 

ターミナルで `mongo` を実行して、ローカルの MongoDB サーバーに接続します。

```bash
mongo
```

接続に成功した場合、MongoDB データベースは既に稼働しています。 成功しない場合は、[MongoDB Community Edition をダウンロードし、インストールして、実行](https://docs.mongodb.com/manual/administration/install-community/)する手順に従って、ローカルの MongoDB データベースが開始されていることを確認します。

MongoDB データベースのテストが完了したら、ターミナルで `Ctrl` + `C` キーを押します。 

<a name="step2"></a>

## <a name="create-local-nodejs-application"></a>ローカル Node.js アプリケーションを作成する
この手順では、ローカル Node.js プロジェクトを設定します。

### <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

ターミナル ウィンドウを開き、`CD` コマンドで作業ディレクトリに移動します。  

次のコマンドを実行して、サンプル リポジトリを複製します。 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

このサンプル リポジトリには、[MEAN.js](http://meanjs.org/) アプリケーションが含まれています。 

### <a name="run-the-application"></a>アプリケーションの実行

必要なパッケージをインストールし、アプリケーションを起動します。

```bash
cd meanjs
npm install
npm start
```

アプリが完全に読み込まれると、次のようなメッセージが表示されます。

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

ブラウザーで `http://localhost:3000` にアクセスします。 上部のメニューの **[Sign Up (サインアップ)]** をクリックし、ダミーのユーザーを作成します。 

MEAN.js サンプル アプリケーションでは、ユーザー データをデータベースに格納します。 ユーザーを作成し、MEAN.js が作成されたユーザーに自動的にサインインすると、データがローカルの MongoDB データベースに書き込まれます。

![MongoDB に正常に接続されている MEAN.js](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

**[Admin (管理)]** > **[Manage Articles (記事の管理)]** の順にクリックして、いくつかの記事を追加します。

任意のタイミングで Node.js を停止するには、ターミナルで `Ctrl` + `C` キーを押します。 

## <a name="create-a-production-mongodb-database"></a>運用 MongoDB データベースを作成する

この手順では、Azure で MongoDB データベースを作成します。 アプリを Azure にデプロイすると、このデータベースは運用ワークロードに使用されます。

MongoDB のために、このチュートリアルでは、[Azure DocumentDB](/azure/documentdb/) を使用します。これにより、MongoDB のクライアント接続をサポートできます。 言い換えると、Node.js アプリケーションのみが MongoDB データベースに接続していることを認識します。 接続が DocumentDB データベースによって提供されるという事実は、アプリケーションに対して透過的です。

### <a name="log-in-to-azure"></a>Azure へのログイン

ターミナル ウィンドウで Azure CLI 2.0 を使用して、Azure App Service で Node.js アプリケーションをホストするために必要なリソースを作成します。  [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) で[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 Azure リソース グループとは、Web アプリ、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、西ヨーロッパ リージョンにリソース グループを作成します。

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

`--location` に使用できる値を確認するには、Azure CLI コマンド `az appservice list-locations` を使用してください。

### <a name="create-a-documentdb-account"></a>DocumentDB アカウントを作成する

[az documentdb create](/cli/azure/documentdb#create) コマンドで DocumentDB アカウントを作成します。

次のコマンドで、`<documentdb_name>` プレースホルダーを独自の一意の DocumentDB 名に置き換えます。 この一意の名前は、DocumentDB エンドポイント (`https://<documentdb_name>.documents.azure.com/`) の一部として使用されます。そのため、この名前は Azure のすべての DocumentDB アカウントで一意である必要があります。 

```azurecli
az documentdb create --name <documentdb_name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` パラメーターにより、MongoDB のクライアント接続が有効になります。

DocumentDB アカウントが作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<documentdb_name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<documentdb_name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<documentdb_name>",
  "readLocations": [
    ...
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    ...
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>データベースに Node.js アプリケーションを接続する

この手順では、MongoDB 接続文字列を使用して、MEAN.js サンプル アプリケーションを、先ほど作成した DocumentDB データベースに接続します。 

### <a name="retrieve-the-database-key"></a>データベース キーの取得

DocumentDB データベースに接続するには、データベース キーが必要です。 [az documentdb list-keys](/cli/azure/documentdb#list-keys) コマンドを使用して、プライマリ キーを取得します。

```azurecli
az documentdb list-keys --name <documentdb_name> --resource-group myResourceGroup
```

Azure CLI によって次の例のような情報が出力されます。

```json
{
  "primaryMasterKey": "RUayjYjixJDWG5xTqIiXjC...",
  "primaryReadonlyMasterKey": "...",
  "secondaryMasterKey": "...",
  "secondaryReadonlyMasterKey": "..."
}
```

`primaryMasterKey` の値をテキスト エディターにコピーします。 この情報は、次の手順に必要です。

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Node.js アプリケーションでの接続文字列の構成

MEAN.js リポジトリにある `config/env/production.js` を開きます。

次の例に示すように、`db` オブジェクトの `uri` の値を置き換えます。 2 つの `<documentdb_name>` プレースホルダーを DocumentDB データベース名に、`<primary_maste_key>` プレースホルダーを前の手順でコピーしたキーに置き換えます。

```javascript
db: {
  uri: 'mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> [Azure DocumentDB では SSL が必須](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements)なので、`ssl=true` オプションは重要です。 
>
>

変更を保存します。

### <a name="test-the-application-in-production-mode"></a>運用モードでのアプリケーションのテスト 

他の Node.js アプリケーションと同様、MEAN.js では `gulp prod` を使用して、運用環境用のスクリプトを小さくしてバンドルします。 これにより、運用環境に必要なファイルが生成されます。 

`gulp prod` を今すぐ実行します。

```bash
gulp prod
```

次に、次のコマンドを実行して、`config/env/production.js` で構成した接続文字列を使用します。

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` により、運用環境で実行するよう Node.js に指示する環境変数を設定し、`node server.js` により、リポジトリ ルートにある `server.js` を使用して Node.js サーバーを起動します。 このようにして、Node.js アプリケーションを Azure に読み込みます。 

アプリが読み込まれたら、運用環境で実行されていることを確認します。

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

ブラウザーで `http://localhost:8443` にアクセスします。 前と同じように、上部のメニューの **[Sign Up (サインアップ)]** をクリックし、ダミーのユーザーを作成します。 作成すると、データが Azure の DocumentDB データベースに書き込まれます。 

## <a name="deploy-the-nodejs-application-to-azure"></a>Azure に Node.js アプリケーションをデプロイする
この手順では、MongoDB に接続している Node.js アプリケーションを Azure App Service にデプロイします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[az appservice plan create](/cli/azure/appservice/plan#create) コマンドで、App Service プランを作成します。 

> [!NOTE] 
> App Service プランは、アプリをホストするために使用される物理リソースのコレクションを表しています。 App Service プランに割り当てられたすべてのアプリケーションは、プランで定義されたリソースを共有します。これにより、複数のアプリをホストする際にコストを節約できます。 
> 
> App Service プランには、次の定義があります。 
> 
> * リージョン (北ヨーロッパ、米国東部、東南アジア) 
> * インスタンス サイズ (Small、Medium、Large) 
> * スケール カウント (インスタンス数 1、2、3 など) 
> * SKU (Free、Shared、Basic、Standard、Premium) 
> 

次の例では、**Free** 価格レベルを使用して、`myAppServicePlan` という名前の App Service プランを作成します。

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 0, 
    "family": "F", 
    "name": "F1", 
    "tier": "Free" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

### <a name="create-a-web-app"></a>Web アプリを作成する

App Service プランを作成したので、`myAppServicePlan` App Service プラン内に Web アプリを作成します。 Web アプリにより、コードをデプロイするためのホスト領域が取得され、デプロイされたアプリケーションを表示するための URL が提供されます。 Web アプリを作成するには、[az appservice web create](/cli/azure/appservice/web#create) コマンドを使用します。 

次のコマンドで、`<app_name>` プレースホルダーを独自の一意のアプリ名に置き換えます。 この一意の名前は、Web アプリの既定のドメイン名の一部として使用されます。そのため、この名前は Azure のすべてのアプリで一意である必要があります。 後で、Web アプリをユーザーに公開する前に、任意のカスタム DNS エントリを Web アプリにマップできます。 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Web アプリが作成されると、Azure CLI によって次の例のような情報が表示されます。 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-an-environment-variable"></a>環境変数の構成

チュートリアルの前半では、`config/env/production.js` でデータベース接続文字列をハードコードしました。 セキュリティのベスト プラクティスに従って、この機密データを Git リポジトリに保存しないようにする必要があります。 Azure で実行されるアプリでは、代わりに環境変数を使用します。

App Service では、[az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) コマンドを使用して、環境変数を_アプリ設定_として設定します。 

次の例を使用すると、Azure Web アプリの `MONGODB_URI` アプリ設定を構成できます。 前と同じように、必ずプレースホルダーを置き換えます。

```azurecli
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true"
```

Node.js コードでは、任意の環境変数にアクセスする場合と同じように、`process.env.MONGODB_URI` を使用して、このアプリ設定にアクセスします。 

次のコマンドを使用して、`config/env/production.js` への変更を元に戻します。

```bash
git checkout -- .
```

`config/env/production.js` を再度開きます。 既定の MEAN.js アプリは、作成した `MONGODB_URI` 環境変数を使用するように既に構成されています。

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイの構成 

アプリケーションを Azure App Service にデプロイするには、FTP、ローカル Git、GitHub、Visual Studio Team Services、BitBucket など、さまざまな方法があります。 FTP とローカル Git の場合、デプロイを認証するには、サーバー上で構成されたデプロイ ユーザーが必要です。 

アカウント レベルの資格情報を作成するには、[az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) コマンドを使用します。 

> [!NOTE] 
> App Service への FTP とローカル Git のデプロイには、デプロイ ユーザーが必要です。 このデプロイ ユーザーは、アカウント レベルです。 そのため、Azure サブスクリプション アカウントとは異なります。 このデプロイ ユーザーは、1 回だけ構成する必要があります。

```azurecli
az appservice web deployment user set --user-name <specify-a-username> --password <mininum-8-char-captital-lowercase-number>
```

Azure Web アプリへのローカル Git アクセスを構成するには、[az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) コマンドを使用します。 

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup
```

デプロイ ユーザーが構成されると、Azure CLI によって、次の形式の Azure Web アプリのデプロイ URL が表示されます。

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

次の手順で使用するため、ターミナルからの出力をコピーしておきます。 

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

ローカル Git リポジトリに Azure リモートを追加します。 

```bash
git remote add azure <paste_copied_url_here> 
```

Node.js アプリケーションをデプロイするために、Azure リモートにプッシュします。 デプロイ ユーザーの作成時に指定したパスワードを入力するように求めるメッセージが表示されます。 

```bash
git push azure master
```

デプロイ中、Azure App Service は進行状況について Git と通信します。

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

> [!NOTE]
> デプロイ プロセスにより、`npm install` の後、[Gulp](http://gulpjs.com/) が実行されます。 App Service では、デプロイ時に Gulp および Grunt タスクが実行されません。そのため、このサンプル リポジトリには、それを有効にする追加の 2 つのファイルがルート ディレクトリにあります。 
>
> - `.deployment` - このファイルは、`bash deploy.sh` をカスタム デプロイ スクリプトとして実行するよう App Service に指示します。
> - `deploy.sh` - カスタム デプロイ スクリプト。 ファイルを確認すると、`npm install` と `bower install` の後に `gulp prod` が実行されることがわかります。 
>
> この方法を使用して、Git ベースのデプロイに任意の手順を追加できます。
>
> Azure Web アプリを再起動する場合は、これらの自動タスクが App Service によって再実行されないことに注意してください。
>
>

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリの参照 
Web ブラウザーを使用して、デプロイされた Web アプリを参照します。 

```bash 
http://<app_name>.azurewebsites.net 
``` 

上部のメニューの **[Sign Up (サインアップ)]** をクリックし、ダミーのユーザーを作成します。 

作成し、アプリが作成されたユーザーに自動的にサインインすると、Azure の MEAN.js アプリが MongoDB (DocumentDB) データベースに接続します。 

![Azure App Service で実行されている MEAN.js アプリ](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

**[Admin (管理)]** > **[Manage Articles (記事の管理)]** の順にクリックして、いくつかの記事を追加します。 

**お疲れさまでした。** データ主導型の Node.js アプリが Azure App Service で実行されています。

## <a name="update-data-model-and-redeploy"></a>データ モデルを更新し、再デプロイする

この手順では、`article` データ モデルに変更をいくつか加え、変更内容を Azure に発行します。

### <a name="update-the-data-model"></a>データ モデルの更新

`modules/articles/server/models/articles.server.controller.js`を開きます。

`ArticleSchema` に `comment` という `String` 型を追加します。 完了すると、スキーマ コードは次のようになります。

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

モデルの変更は、これで終了です。 

### <a name="update-the-articles-code"></a>記事のコードの更新

次に、`comment` を使用するように、`articles` コードの残りの部分を更新します。

全部で 5 つのファイルを変更する必要があります (1 つのサーバー コントローラーと 4 つのクライアント ビュー)。 

最初に、`modules/articles/server/controllers/articles.server.controller.js` を開きます。

`update` 関数に `article.comment` の割り当てを追加します。 完了すると、`update` 関数は次のようになります。

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

次に、`modules/client/views/view-article.client.view.js` を開きます。

`</section>` 終了タグのすぐ上に、`comment` と残りの記事データを表示する次の行を追加します。

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

次に、`modules/client/views/list-articles.client.view.js` を開きます。

`</a>` 終了タグのすぐ上に、`comment` と残りの記事データを表示する次の行を追加します。

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

次に、`modules/client/views/admin/list-articles.client.view.js` を開きます。

`<div class="list-group">` タグ内の `</a>` 終了タグのすぐ上に、`comment` と残りの記事データを表示する次の行を追加します。

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

最後に、`modules/client/views/admin/list-articles.client.view.js` を開きます。

次のような送信ボタンを含む `<div class="form-group">` タグを探します。

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

そのタグのすぐ上に、もう 1 つ `<div class="form-group">` タグを追加して、ユーザーが `comment` フィールドを編集できるようにします。 新しいタグは次のようになります。

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>ローカルでの変更のテスト

すべての変更を保存します。

再度運用モードで変更をテストします。

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> `config/env/production.js` が元に戻っており、`MONGODB_URI` 環境変数がローカル コンピューターではなく、Azure Web アプリでのみ設定されていることに注意してください。 構成ファイルを確認すると、運用構成が既定でローカルの MongoDB データベースを使用するようになっていることがわかります。 そのため、コードの変更をローカルでテストする際に、運用データを操作することはありません。
>
>

ブラウザーで `http://localhost:8443` にアクセスし、サインインしていることを確認します。

**[Admin (管理)]** > **[Manage Articles (記事の管理)]** の順にクリックし、**[+]** ボタンをクリックして、記事を追加します。

新しい `Comment` テキスト ボックスが表示されます。

![記事に追加されたコメント フィールド](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

### <a name="publish-changes-to-azure"></a>Azure への変更の発行

変更を Git にコミットし、コード変更を Azure にプッシュします。

```bash
git commit -am "added article comment"
git push azure master
```

`git push` が完了したら、Azure Web アプリに移動し、新機能をもう一度試します。

![Azure に発行されたモデルとデータベースの変更](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

> [!NOTE]
> 以前に追加した記事は引き続き表示されます。 DocumentDB の既存のデータは失われません。 また、データ スキーマは更新され、既存のデータはそのまま残ります。
>
>

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする 

Azure App Service で Node.js アプリケーションを実行している場合、コンソール ログをターミナルに直接パイプできます。 このようにすると、アプリケーション エラーのデバッグに役立つ同じ診断メッセージを取得できます。

ログのストリーミングを開始するには、[az appservice web log tail](/cli/azure/appservice/web/log#tail) コマンドを使用します。

```azurecli 
az appservice web log tail --name <app_name> --resource-group myResourceGroup 
``` 

ログのストリーミングが開始されたら、ブラウザーで Azure Web アプリを最新の情報に更新して、Web トラフィックを取得します。 ターミナルにパイプされたコンソール ログが表示されます。

任意のタイミングでログのストリーミングを停止するには、`Ctrl` + `C` キーを押します。 

## <a name="manage-your-azure-web-app"></a>Azure Web アプリを管理する

Azure Portal に移動し、作成した Web アプリを表示します。

そのためには、[https://portal.azure.com](https://portal.azure.com) にサインインします。

左側のメニューで **[App Service]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Web アプリの "_ブレード_" (水平方向に開かれるポータル ページ) が表示されます。

既定では、Web アプリのブレードは **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ブレードの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の App Service ブレード](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

ブレードのこれらのタブは、Web アプリに追加することができるさまざまな優れた機能を示しています。 次の一覧では、ほんの一部の例を示しています。

* カスタム DNS 名をマップする
* カスタム SSL 証明書をバインドする
* 継続的なデプロイを構成する
* スケールアップとスケールアウトを行う
* ユーザー認証を追加する

<!--

## Step 4 - Download server logs
In this step, you turn on monitoring of your web app with web server logs, and then download these logs. 

### Enable logging
Enable all logging options for your web app.

```azurecli
az appservice web log config --name <app_name> --resource-group myResourceGroup --application-logging true --detailed-error-messages true --failed-request-tracing true --web-server-logging filesystem
```

### Generate errors

To generate some error entries, navigate to a nonexistent page in your web app. For example: `http://<app_name>.azurewebsites.net/404`. 

### Download log files
Download the log files for review.

```azurecli
az appservice web log download --name <app_name> --resource-group myResourceGroup
```

## Step 5 - Scale to another region
In this step, you scale your Node.js app to serve your customers in a new region. That way, you can tailor your web app to customers in different regions, and also put your web app closer to them to improve performance. When you're done with this step, you will have a [Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/) profile with two endpoints, which route traffic to two web apps which reside in different geographical regions.

1. Create a Traffic Manager profile with a unique name and add it to your resource group.

    ```azurecli
    az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name <unique-dns-name>
    ```

    > [!NOTE]
    > `--routing-method Performance` specifies that this profile [routes user traffic to the closest endpoint](../traffic-manager/traffic-manager-routing-methods.md).

2. Get the resource ID of your existing Node.js web app.

    ```azurecli
    az appservice web show --name <app_name> --resource-group myResourceGroup --query id --output tsv
    ```

3. Add an endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-1-resource-id>`:

    ```azurecli
    az network traffic-manager endpoint create --name <app_name>-westeurope --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id <web-app-1-resource-id>
    ```

4. Your Traffic Manager profile now has an endpoint that points to your web app. Query for its URL to try it out.

    ```azurecli
    az network traffic-manager profile show --name cephalin-express --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
    ```

    Copy the output into your browser. You should get the default Express page again, with data from your database.

5. Let's add some identifying characteristic to your West Europe app. Add an environment variable.

    ```azurecli
    az appservice web config appsettings update --settings region="Europe" --name <app_name> --resource-group myResourceGroup    
    ```

6. Open `routes/index.js` and change the `router.get()` to use the environment variable.

    ```javascript
    router.get('/', function(req, res, next) {
      res.render('index', { title: 'Express ' + process.env.region, data: output });
    });
    ```

7. Save your changes and push them to Azure.

    ```
    git add .
    git commit -m "added region string."
    git push azure master
    ```

8. Refresh your browser on your Traffic Manager profile's URL. You should now see `Express Europe` in the homepage. 

    Since your Traffic Manager profile only has one endpoint which points to your West Europe web app, this is the only page you'll see. Next, you create a new web app in Southeast Asia and add a new endpoint to the profile.

4. Create an App Service plan and web app in the Southeast Asia region, and deploy the same code to it just like you did in [Step 2]<#step2>.

    ```azurecli
    az appservice plan create --name my-expressjs-appservice-plan-asia --resource-group myResourceGroup --location "Southeast Asia" --sku FREE
    az appservice web create --name <app_name>-asia --plan my-expressjs-appservice-plan-asia --resource-group myResourceGroup
    url=$(az appservice web source-control config-local-git --name <app_name>-asia --resource-group myResourceGroup --query url --output tsv)
    git remote add azureasia $url
    git push azureasia master
    ```

5. Add the same application settings to the new web app. Set the region to `"Asia"`.

    ```azurecli
    az appservice web config appsettings update --settings dbconnstring="mongodb://$accountname:$password@$accountname.documents.azure.com:10250/tutorial?ssl=true&sslverifycertificate=false" --name <app_name>-asia --resource-group myResourceGroup    
    az appservice web config appsettings update --settings region="Asia" --name <app_name>-asia --resource-group myResourceGroup    
    ```

    Since DocumentDB is a [geographically distributed](../documentdb/documentdb-distribute-data-globally.md) NoSQL service, you can use the same MongoDB connection string in the Southeast Asia web app. When the MongoDB client driver connects to your DocumentDB account, Azure automatically figures out where is the closest place to route the connection. No code change is necessary. You only need to add the regions you want to support to your DocumentDB account, which you will do next.

6. Add `Southeast Asia` as a region to your DocumentDB account.

    ```azurecli
    az documentdb update --locations "West Europe"=0 "Southeast Asia"=1 --name $accountname --resource-group myResourceGroup
    ```

3. To finish, add a second endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-2-resource-id>`:

    ```azurecli
    resourceid=$(az appservice web show --name <app_name>-asia --resource-group myResourceGroup --query id --output tsv)
    az network traffic-manager endpoint create -n <app_name>-southeastasia --profile-name myTrafficManagerProfile -g myResourceGroup --type azureEndpoints --target-resource-id resourceid
    ```
  
Now, try to access the URL of your Traffic Manager profile. If you access the URL from the Europe region, you should see "Express Europe", but from the Asia region, you should see "Express Asia".

-->
## <a name="more-resources"></a>その他のリソース

