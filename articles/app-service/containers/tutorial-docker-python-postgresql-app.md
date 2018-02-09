---
title: "Azure で Docker Python と PostgreSQL アプリを構築する | Microsoft Docs"
description: "PostgreSQL データベースに接続する Python アプリを Azure で動作させる方法について説明します。"
services: app-service\web
documentationcenter: python
author: berndverst
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 01320b93920ae04c72ed80f6a6090232c673f228
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Azure で Docker Python と PostgreSQL アプリを構築する

Web App for Containers では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、Azure で基本的な Docker Python Web アプリを作成する方法を示します。 このアプリを PostgreSQL データベースに接続します。 完了すると、[App Service on Linux](app-service-linux-intro.md) 上の Docker コンテナー内で実行される Python Flask アプリケーションが完成します。

![App Service on Linux の Docker Python Flask アプリ](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure で PostgreSQL データベースを作成する
> * Python アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure Portal でアプリを管理する

以下に示すのは MacOS での手順です。 Linux および Windows でも手順はほとんど同じですが、このチュートリアルでは相違点について詳しく説明していません。
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

1. [Git をインストールする](https://git-scm.com/)
1. [Python をインストールする](https://www.python.org/downloads/)
1. [PostgreSQL をインストールして実行する](https://www.postgresql.org/download/)
1. [Docker Community Edition をインストールする](https://www.docker.com/community-edition)

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

次のコマンドを実行してサンプル レポジトリを複製し、*0.1-initialapp* リリースに移動します。

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

このサンプル レポジトリには、[Flask](http://flask.pocoo.org/) アプリケーションが含まれています。 

### <a name="run-the-application"></a>アプリケーションの実行

> [!NOTE] 
> このプロセスは、運用データベースで使用する Docker コンテナーを作成することで簡略化されます。手順については後述します。

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

![ローカルで実行されている Python Flask アプリケーション](./media/tutorial-docker-python-postgresql-app/local-app.png)

Flask サンプル アプリケーションは、ユーザー データをデータベースに格納します。 ユーザーの登録に成功すれば、アプリはローカル PostgreSQL データベースにデータを書き込んでいます。

Flask サーバーを任意のタイミングで停止するには、ターミナルで Ctrl + C キーを押します。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>運用 PostgreSQL データベースを作成する

この手順では、Azure に PostgreSQL データベースを作成します。 アプリを Azure にデプロイすると、このクラウド データベースがアプリで使用されます。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

[`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) コマンドを使用して、PostgreSQL サーバーを作成します。

次のコマンドの *\<postgresql_name>* プレースホルダーを一意のサーバー名に置き換え、*\<admin_username>* プレースホルダーをユーザー名に置き換えます。 このサーバー名は、PostgreSQL エンドポイント (`https://<postgresql_name>.postgres.database.azure.com`) の一部として使用されるため、Azure のすべてのサーバーで一意である必要があります。 ユーザー名は、最初のデータベース管理者ユーザー アカウントに使用されます。 このユーザーのパスワードを選択することを求められます。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

Azure Database for PostgreSQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーのファイアウォール ルールの作成

次の Azure CLI コマンドを実行して、すべての IP アドレスからデータベースにアクセスできるようにします。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

次の例のような出力で、Azure CLI によってファイアウォール ルールの作成が確認されます。

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>データベースに Python Flask アプリケーションを接続する

この手順では、作成した Azure Database for PostgreSQL サーバーに、Python Flask サンプル アプリケーションを接続します。

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>空のデータベースの作成と新しいデータベース アプリケーション ユーザーの設定

単一のデータベースのみにアクセスできるデータベース ユーザーを作成します。 これらの資格情報は、アプリケーションにサーバーへのフル アクセス許可を与えないようにするために使用します。

データベースに接続します (管理者パスワードの入力を求められます)。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

PostgreSQL CLI からデータベースとユーザーを作成します。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

「`\q`」を入力して PostgreSQL クライアントを終了します。

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Azure PostgreSQL データベースに対してローカルでアプリケーションをテストする

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

![ローカルで実行されている Python Flask アプリケーション](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Docker コンテナーからのアプリケーションの実行

Docker コンテナー イメージを構築します。

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker によって、コンテナーが正常に作成されたことの確認が表示されます。

```bash
Successfully built 7548f983a36b
```

リポジトリ ルートに _db.env_ という名前の環境変数ファイルを追加し、以下のデータベース環境変数をこのファイルに追加します。 アプリは、Azure Database for PostgreSQL の 実稼働データベースに接続します。

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Docker コンテナーの中からアプリを実行します。 次のコマンドでは、環境変数ファイルを指定し、既定の Flask ポート 5000 をローカル ポート 5000 にマップします。

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

出力は前に示したものに似ています。 ただし、最初のデータベースの移行はもう実行する必要がないため、スキップされます。

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

データベースには、先ほど作成した登録が既に含まれています。

![ローカルで実行されている Docker コンテナー ベースの Python Flask アプリケーション](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Docker コンテナーをコンテナー レジストリにアップロードする

この手順では、Docker コンテナーをコンテナー レジストリにアップロードします。 Azure Container Registry を使用しますが、Docker Hub などのその他の一般的なレジストリも使用できます。

### <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する

コンテナー レジストリを作成する次のコマンドの *\<registry_name>* を、選択した一意の Azure Container Registry 名に置き換えます。

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

出力

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Docker イメージをプッシュおよびプルするためのレジストリ資格情報を取得する

レジストリの資格情報を表示するには、まず 管理者モードを有効にします。

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

2 つのパスワードが表示されます。 ユーザー名と 1 つ目のパスワードをメモしておきます。

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Docker コンテナーを Azure Container Registry にアップロードする

レジストリにログインします。 メッセージが表示されたら、取得したパスワードを指定します。

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

レジストリに Docker イメージをプッシュします。

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Docker Python Flask アプリケーションを Azure にデプロイする

この手順では、 Docker コンテナー ベースの Python Flask アプリケーションを Azure App Service にデプロイします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する

[`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) コマンドを使用して、*myAppServicePlan* App Service プラン内に Web アプリを作成します。

Web アプリにより、コードをデプロイするためのホスト領域が取得され、デプロイされたアプリケーションを表示するための URL が提供されます。 Web アプリを作成するには  を使用します。

次のコマンドで、*\<app_name>* プレースホルダーを一意のアプリ名に置き換えます。 この名前は、Web アプリの既定の URL の一部であるため、Azure App Service のすべてのアプリで一意である必要があります。

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

Web アプリが作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>データベースの環境変数を構成する

チュートリアルの前半で、PostgreSQL データベースに接続する環境変数を定義しました。

App Service で、[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) コマンドを使用して、環境変数を "_アプリ設定_" として設定します。

次の例では、データベース接続の詳細をアプリ設定として指定します。 また、*PORT* 変数を使用して、Docker コンテナーのポート 5000 をポート 80 で HTTP トラフィックを受信するようにマップします。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Docker コンテナーのデプロイを構成する

App Service は、Docker コンテナーを自動的にダウンロードして実行できます。

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Docker コンテナーを更新するか、設定を変更した場合は、必ずアプリを再起動してください。 再起動により、すべての設定が適用され、最新のコンテナーがレジストリからプルされるようになります。

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリの参照 

Web ブラウザーを使用して、デプロイされた Web アプリを参照します。 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> コンテナー構成の変更後はコンテナーをダウンロードして起動する必要があるため、Web アプリの読み込みに時間がかかります。

前の手順で Azure 運用データベースに保存された登録済みのゲストが表示されます。

![ローカルで実行されている Docker コンテナー ベースの Python Flask アプリケーション](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**お疲れさまでした。** Docker コンテナー ベースの Python Flask アプリケーションを Azure App Service で実行しています。

## <a name="update-data-model-and-redeploy"></a>データ モデルを更新し、再デプロイする

この手順では、ゲスト モデルを更新して、各イベント登録に参加者数を追加します。

次の git コマンドを使用して、*0.2-migration* リリースをチェックアウトします。

```bash
git checkout tags/0.2-migration
```

このリリースでは、ビュー、コントローラー、およびモデルに必要な変更が既に行われています。 *alembic* (`flask db migrate`) で生成されるデータベースの移行も含まれています。 次の git コマンドを使用して、実行されたすべての変更を確認できます。

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>変更をローカルでテストする

次のコマンドで Flask サーバーを実行して、変更をローカルでテストします。

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

変更を表示するには、ブラウザーで http://localhost:5000 にアクセスします。 テスト登録を作成します。

![ローカルで実行されている Docker コンテナー ベースの Python Flask アプリケーション](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

新しい Docker イメージを構築してコンテナー レジストリにプッシュし、アプリケーションを再起動します。

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Azure Web アプリに移動し、新機能をもう一度試します。 別のイベント登録を作成します。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service の Docker Python Flask アプリ](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Azure Web アプリを管理する

[Azure Portal](https://portal.azure.com) に移動し、作成した Web アプリを表示します。

左側のメニューで **[App Services (App Services)]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/tutorial-docker-python-postgresql-app/app-resource.png)

既定では、ポータルは Web アプリの **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の [App Service] ページ](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>次の手順

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure Web Apps にマップする](../app-service-web-tutorial-custom-domain.md)
