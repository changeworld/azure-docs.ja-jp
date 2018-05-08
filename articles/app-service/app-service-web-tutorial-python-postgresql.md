---
title: Azure で Python と PostgreSQL Web アプリを構築する | Microsoft Docs
description: PostgreSQL データベースに接続する Python アプリを Azure で動作させる方法について説明します。
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 49ec67d06446d6c48e45aef90e2bd528a1b541a9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
---
# <a name="tutorial-build-a-python-and-postgresql-web-app-in-azure"></a>チュートリアル: Azure で Python と PostgreSQL Web アプリを構築する

> [!NOTE]
> この記事では、Windows 上の App Service にアプリをデプロイします。 _Linux_ 上の App Service にデプロイするには、[Azure で Docker Python と PostgreSQL アプリを構築する](./containers/tutorial-docker-python-postgresql-app.md)に関するページを参照してください。
>

[Azure App Service](app-service-web-overview.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、Azure で基本的な Python Web アプリを作成する方法を示します。 このアプリを PostgreSQL データベースに接続します。 完了すると、App Service 上で実行される Python Flask アプリケーションが完成します。

![Linux 上の App Service の Python Flask アプリ](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure で PostgreSQL データベースを作成する
> * Python アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure Portal でアプリを管理する

このチュートリアルの手順は macOS で実行できます。 Linux および Windows でも手順はほとんど同じですが、このチュートリアルでは相違点について詳しく説明していません。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

1. [Git をインストールする](https://git-scm.com/)
1. [Python をインストールする](https://www.python.org/downloads/)
1. [PostgreSQL をインストールして実行する](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>PostgreSQL のローカル インストールをテストし、データベースを作成する

ターミナル ウィンドウを開き、`psql` を実行してローカル PostgreSQL サーバーに接続します。

```bash
sudo -u postgres psql
```

接続に成功すれば、PostgreSQL データベースは実行されています。 接続されない場合は、「[Downloads - PostgreSQL Core Distribution (ダウンロード - PostgreSQL コア ディストリビューション)](https://www.postgresql.org/download/)」の手順に従って、ローカル PostgreSQL データベースが開始されていることを確認します。

*eventregistration* という名前のデータベースを作成し、名前が *manager* でパスワードが *supersecretpass* の別のデータベース ユーザーを設定します。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
「`\q`」を入力して PostgreSQL クライアントを終了します。 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>ローカル Python Flask アプリケーションを作成します。

この手順では、ローカル Python Flask プロジェクトを設定します。

### <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

ターミナル ウィンドウを開き、`CD` コマンドで作業ディレクトリに移動します。

次のコマンドを実行してサンプル リポジトリを複製した後、最初のアプリ (`modelChange` の前) のコミットに戻します。

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

このサンプル レポジトリには、[Flask](http://flask.pocoo.org/) アプリケーションが含まれています。 

### <a name="run-the-application"></a>アプリケーションの実行

必要なパッケージをインストールし、アプリケーションを起動します。

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

アプリが完全に読み込まれると、次のようなメッセージが表示されます。

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

ブラウザーで `http://localhost:5000` にアクセスします。 **[Register! (登録する)]** をクリックし、 テスト ユーザーを作成します。

![ローカルで実行されている Python Flask アプリケーション](./media/app-service-web-tutorial-python-postgresql/local-app.png)

Flask サンプル アプリケーションは、ユーザー データをデータベースに格納します。 ユーザーの登録に成功すれば、アプリはローカル PostgreSQL データベースにデータを書き込んでいます。

Flask サーバーを任意のタイミングで停止するには、ターミナルで Ctrl + C キーを押します。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Azure で PostgreSQL を作成する

この手順では、Azure に PostgreSQL データベースを作成します。 アプリを Azure にデプロイすると、このクラウド データベースがアプリで使用されます。

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>PostgreSQL サーバーを作成する

[`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) コマンドを使用して、PostgreSQL サーバーを作成します。

次のコマンドの *\<postgresql_name>* プレースホルダーを一意のサーバー名に、*\<admin_username>* プレースホルダーをユーザー名に、*\<admin_password>* プレースホルダーをパスワードに置き換えます。 このサーバー名は、PostgreSQL エンドポイント (`https://<postgresql_name>.postgres.database.azure.com`) の一部として使用されるため、Azure のすべてのサーバーで一意である必要があります。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

Azure Database for PostgreSQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "location": "westeurope",
  "name": "<postgresql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>サーバーのファイアウォールを構成する

次の Azure CLI コマンドを実行して、すべての IP アドレスからデータベースにアクセスできるようにします。 開始 IP と終了 IP の両方が 0.0.0.0 に設定されている場合、ファイアウォールは他の Azure リソースに対してのみ開かれます。 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0 --name AllowAzureIPs
```

次の例のような出力で、Azure CLI によってファイアウォール ルールの作成が確認されます。

```json
{
  "additionalProperties": {},
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
 "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

> [!TIP] 
> [アプリで使用する送信 IP アドレスのみを使用する](app-service-ip-addresses.md#find-outbound-ips)ことで、ファイアウォール規則による制限をさらに厳しくすることができます。
>

### <a name="create-a-production-database-and-user"></a>運用データベースとユーザーを作成する

単一のデータベースのみにアクセスできるデータベース ユーザーを作成します。 これらの資格情報は、アプリケーションにサーバーへのフル アクセス許可を与えないようにするために使用します。

データベースに接続します (管理者パスワードの入力を求められます)。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <admin_username>@<postgresql_name> postgres
```

PostgreSQL CLI からデータベースとユーザーを作成します。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

「`\q`」を入力して PostgreSQL クライアントを終了します。

### <a name="test-app-locally-with-azure-postgresql"></a>Azure PostgreSQL を使用してローカルでアプリをテストする

複製した Github リポジトリの *app* フォルダーに戻り、データベース環境変数を更新することで、Python Flask アプリケーションを実行できます。

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

アプリが完全に読み込まれると、次のようなメッセージが表示されます。

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

ブラウザーで http://localhost:5000 にアクセスします。 **[Register! (登録する)]** をクリックし、 テスト登録を作成します。 Azure のデータベースにデータが書き込まれます。

![ローカルで実行されている Python Flask アプリケーション](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>[Deploy to Azure (Azure へのデプロイ)]

この手順では、PostgreSQL に接続される Python アプリケーションを Azure App Service にデプロイします。

Git リポジトリには、Flask Web アプリを App Service で実行するために必要な次のファイルが既に含まれています。

- `.deployment`: 実行するカスタム デプロイ スクリプトを指定します。
- `deploy.cmd`: デプロイ スクリプト。 `pip install` が実行される場所です。
- `web.config`: IIS の `httpPlatformHandler` で実行するエントリ ポイント スクリプトを指定します。
- `run_waitress_server.py`: エントリ ポイント スクリプト。 [`waitress`](https://docs.pylonsproject.org/projects/waitress) サーバーで Flask Web アプリを起動します。

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Python のインストール

この手順では、Python 3.6.2 を[サイト拡張機能](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)と共に App Service にインストールします。 「[デプロイ ユーザーを構成する](#configure-a-deployment-user)」で構成した資格証明を使用して、REST エンドポイントに対して認証を行います。

Cloud Shell で次のコマンドを実行して、Python 3.6.2 パッケージ情報を取得します。 *\<deployment_user>* を構成したデプロイ ユーザー名に置き換え、*\<app_name>* をアプリ名に置き換えます。 メッセージが表示されたら、構成したデプロイ パスワードを使用します。

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

Cloud Shell で次のコマンドを実行して、Python パッケージをインストールします。 *\<deployment_user>* を構成したデプロイ ユーザー名に置き換え、*\<app_name>* をアプリ名に置き換えます。 メッセージが表示されたら、構成したデプロイ パスワードを使用します。

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

コマンド出力から、Python がパス `D:\home\python362x86\python.exe` にインストールされていることがわかります。

### <a name="configure-database-settings"></a>データベース設定を構成する

チュートリアルの前半で、PostgreSQL データベースに接続する環境変数を定義しました。

App Service では、[az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) コマンドを使用して、環境変数を "_アプリ設定_" として設定します。

次の例では、データベース接続の詳細をアプリ設定として指定します。 *\<app_name>* をアプリ名に置き換え、*\<postgresql_name>* を PostgreSQL サーバー名に置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリの参照 

Web ブラウザーを使用して、デプロイされた Web アプリを参照します。 

```bash 
http://<app_name>.azurewebsites.net 
```

前の手順で Azure 運用データベースに保存された登録済みのゲストが表示されます。

![ローカルで実行されている Python Flask アプリケーション](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**お疲れさまでした。** Azure App Service で Python Flask アプリが実行されています。

## <a name="update-data-model-and-redeploy"></a>データ モデルを更新し、再デプロイする

この手順では、`Guest` モデルを更新して、各イベント登録に参加者数を追加します。

`modelChange` コミットでタグ付けされたファイルをチェックアウトします。

```bash
git checkout modelChange -- *
```

このリリースでは、ビュー、コントローラー、およびモデルに必要な変更が既に行われています。 *alembic* (`flask db migrate`) で生成されるデータベースの移行も含まれています。 [GitHub コミット ビュー](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e)ですべてのファイルの変更を確認することができます。

### <a name="test-your-changes-locally"></a>変更をローカルでテストする

次のコマンドで Flask サーバーを実行して、変更をローカルでテストします。

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

変更を表示するには、ブラウザーで http://localhost:5000 に移動します。 テスト登録を作成します。

![ローカルで実行されている Python Flask アプリケーション](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

ローカル ターミナル ウィンドウで、すべての変更を Git にコミットし、コード変更を Azure にプッシュします。

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Azure Web アプリに移動し、新機能をもう一度試します。 別のイベント登録を作成します。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service の Python Flask アプリ](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Azure Web アプリを管理する

[Azure Portal](https://portal.azure.com) に移動し、作成した Web アプリを表示します。

左側のメニューで **[App Services (App Services)]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

既定では、ポータルは Web アプリの **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の [App Service] ページ](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次の手順

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure Web Apps にマップする](app-service-web-tutorial-custom-domain.md)
