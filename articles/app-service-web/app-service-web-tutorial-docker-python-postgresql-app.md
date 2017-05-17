---
title: "Azure で Docker Python と PostgreSQL アプリを構築する | Microsoft Docs"
description: "PostgreSQL データベースに接続する Python アプリを Azure で動作させる方法について説明します。"
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/03/2017
ms.author: beverst
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 554843c5f3161a45ff984b000944f546b44dd472
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Azure で Docker Python と PostgreSQL アプリを構築する
このチュートリアルでは、Azure で基本的な Docker Python Web アプリを作成する方法を示します。 さらに、このアプリを PostgreSQL データベースに接続します。 このチュートリアルを完了すると、[Azure App Service Web Apps](app-service-web-overview.md) で Docker コンテナー内で実行される Python Flask アプリケーションが完成します。

![Azure App Service の Docker Python Flask アプリ](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="before-you-begin"></a>開始する前に

このサンプルを実行する前に、前提条件となる以下をローカルにインストールします。

1. [Git をダウンロードし、インストールします](https://git-scm.com/)
1. [Python をダウンロードし、インストールします](https://www.python.org/downloads/)
1. [PostgreSQL をダウンロード、インストール、および実行します](https://www.postgresql.org/download/)
1. [Docker Community Edition をダウンロードし、インストールします](https://www.docker.com/community-edition)
1. [Azure CLI 2.0 をダウンロードし、インストールします](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-postgresql-installation-and-create-a-database"></a>PostgreSQL のローカル インストールをテストし、データベースを作成する
この手順では、ローカル PostgreSQL データベースが実行されていることを確認します。

ターミナル ウィンドウを開き、`psql postgres` を実行してローカル PostgreSQL サーバーに接続します。

```bash
psql postgres
```

接続に成功すれば、PostgreSQL データベースは既に実行されています。 接続されない場合は、[PostgreSQL をダウンロードし、インストールして、実行](https://www.postgresql.org/download/)する手順に従って、ローカル PostgreSQL データベースが開始されていることを確認します。

`eventregistration` という名前のデータベースを作成し、名前が `manager` でパスワードが `supersecretpass` のデータベース ユーザーを設定します。

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

次のコマンドを実行してサンプル レポジトリを複製し、`0.1-initialapp` リリースに移動します。

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.gi
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

このサンプル レポジトリには、[Flask](http://http://flask.pocoo.org/) アプリケーションが含まれています。 

### <a name="run-the-application"></a>アプリケーションを実行する

> [!NOTE] 
> このプロセスは、運用データベースで使用する Docker コンテナーを作成することで簡略化されます。手順については後述します。

必要なパッケージをインストールし、アプリケーションを起動します。

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

アプリが完全に読み込まれると、次のようなメッセージが表示されます。

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

ブラウザーで `http://127.0.0.1:5000` にアクセスします。 **[Register! (登録する)]** をクリックし、 ダミー登録を作成します。

![ローカルで実行されている Python Flask アプリケーション](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

Flask サンプル アプリケーションは、ユーザー データをデータベースに格納します。 操作が成功し、登録がアプリに表示されれば、アプリはローカル PostgreSQL データベースにデータを書き込んでいます。

Flask を任意のタイミングで停止するには、ターミナルで `Ctrl`+`C` キーを押します。 

## <a name="create-a-production-postgresql-database"></a>運用 PostgreSQL データベースを作成する

この手順では、Azure に PostgreSQL データベースを作成します。 アプリを Azure にデプロイするときに、運用ワークロードに対してこのデータベースを指定します。

### <a name="log-in-to-azure"></a>Azure にログインする

ターミナル ウィンドウで Azure CLI 2.0 を使用して、Azure App Service で Python アプリケーションをホストするために必要なリソースを作成します。  [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) で[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 Azure リソース グループとは、Web アプリ、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例は、米国西部リージョンにリソース グループを作成します。

```azurecli
az group create --name myResourceGroup --location "West US"
```

`--location` に使用できる値を確認するには、Azure CLI コマンド `az appservice list-locations` を使用してください。

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーを作成する

[az postgres server create](/cli/azure/documentdb#create) コマンドを使用して、PostgreSQL サーバーを作成します。

次のコマンドの `<postgresql_name>` プレースホルダーを独自の一意の PostgreSQL サーバー名に置き換えます。 この一意の名前は、PostgreSQL エンドポイント (`https://<postgresql_name>.postgres.database.azure.com`) の一部として使用されます。そのため、この名前は Azure のすべてのサーバーで一意である必要があります。 

```azurecli
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <my_admin_username>
```

`--admin-user` は、最初のデータベース管理者のユーザー アカウントを作成するために必要です。 このユーザーのパスワードを選択することを求められます。

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

### <a name="creating-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーのファイアウォール ルールの作成

データベースにアクセスする前に、すべての IP アドレスからデータベースにアクセスできるようにする必要があります。 これは、次の Azure CLI コマンドを使用して行うことができます。

```azurecli
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

ファイアウォールが作成されると、Azure CLI によって、次のようにルールの存在が確認されます。

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

### <a name="creating-an-empty-database-and-setting-up-a-new-database-application-user"></a>空のデータベースの作成と新しいデータベース アプリケーション ユーザーの設定

単一のデータベースのみにアクセスできる新しいデータベース ユーザーを作成します。 この手順は、管理者の資格情報を使用したサーバーへのフル アクセス許可がアプリケーションに与えられることを回避します。

データベースに接続します (管理者パスワードの入力を求められます)。
```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

PostgreSQL CLI からデータベースとユーザーを作成します。
```
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

「`\q`」を入力して PostgreSQL クライアントを終了します。

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Azure PostgreSQL データベースに対してローカルでアプリケーションをテストする 

複製した Github レポジトリの `app` フォルダーに戻り、データベース環境変数を更新するだけで Python Flask アプリケーションを実行できます。

```bash
FLASK_APP=app.py;DBHOST="<postgresql_name>.postgres.database.azure.com";DBUSER="manager@<postgresql_name>";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

アプリが完全に読み込まれると、次のようなメッセージが再び表示されます。

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

ブラウザーで `http://127.0.0.1:5000` にアクセスします。 **[Register! (登録する)]** をクリックし、 ダミー登録を作成します。 今回は、Azure の運用データベースにデータが書き込まれます。

![ローカルで実行されている Python Flask アプリケーション](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Docker コンテナーからのアプリケーションの実行

次に、Docker コンテナー イメージを構築し、引き続き Azure の PostgreSQL 運用データベースに接続して Docker コンテナーの中からアプリケーションをローカルで実行します。

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker によって、コンテナーが正常に作成されたことの確認が表示されます。

```
Successfully built 7548f983a36b
```

環境変数ファイル `db.env` にデータベース環境変数を追加してみましょう。

```
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

次に、Docker コンテナーの中からアプリを実行します。 環境変数ファイルを指定し、既定の Flask ポート 5000 をローカル ポート 5000 にマップします。

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

当然ながら、出力は前のものに似ています。 ただし、最初のデータベースの移行はもう実行する必要がないため、スキップされます。
```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 ```

 データベースには、先ほど作成した登録が既に含まれています。

 ![ローカルで実行されている Docker コンテナー ベースの Python Flask アプリケーション](./media/app-service-web-tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Docker コンテナーをコンテナー レジストリにアップロードする
この手順では、作成した Docker コンテナーをコンテナー レジストリにアップロードします。 Azure Container Registry を使用しますが、Docker Hub などのその他の一般的なレジストリも使用できます。

### <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する

コンテナー レジストリを作成する次のコマンドの `<registry_name>` を、選択した一意の Azure Container Registry 名に置き換えます。

```azurecli
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

資格情報にアクセスする前に、管理者モードにする必要があります。

```azurecli
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

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Docker Python Flask アプリケーションを Azure にデプロイする
この手順では、 Docker コンテナー ベースの Python Flask アプリケーションを Azure App Service にデプロイします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[az appservice plan create](/cli/azure/appservice/plan#create) コマンドで、App Service プランを作成します。 

> [!NOTE] 
> App Service プランは、アプリをホストするために使用される物理リソースのコレクションを表しています。 App Service プランに割り当てられたすべてのアプリケーションは、プランで定義されたリソースを共有します。これにより、複数のアプリをホストする際にコストを節約できます。 
> 
> App Service プランには、次の定義があります。 
> 
> * リージョン (北ヨーロッパ、米国東部、東南アジアなど) 
> * インスタンス サイズ (Small、Medium、Large) 
> * スケール カウント (インスタンス数 1、2、3 など) 
> * SKU (Free、Shared、Basic、Standard、Premium) 
> 

次の例では、S1 価格レベルを使用して、`myAppServicePlan` という名前の Linux ベースの App Service プランを作成します。

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json 
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
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

### <a name="configure-the-database-environment-variables"></a>データベースの環境変数を構成する

チュートリアルの前半で、PostgreSQL データベースに接続する環境変数を手動で定義しました。

App Service では、[az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) コマンドを使用して、環境変数を_アプリ設定_として設定します。 

次のコマンドを使用して、データベース接続の詳細をアプリ設定として指定できます。 さらに、`PORT` 変数を使用して、Docker コンテナーのポート 5000 をポート 80 で HTTP トラフィックを受信するようにマップすることを指定します。

```azurecli
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Docker コンテナーのデプロイを構成する 

App Service は、Docker コンテナーを自動的にダウンロードして実行できます。

アカウント レベルの資格情報を作成するには、[az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) コマンドを使用します。 

```azurecli
az appservice web config container update --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Docker コンテナーの更新または上記の設定の変更を行った場合は、常にアプリを再起動して、すべての設定が適用され、最新のコンテナーがレジストリからプルされるようにします。

```azurecli
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリを参照する 
Web ブラウザーを使用して、デプロイされた Web アプリを参照します。 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> コンテナー構成を変更した後の初回の Web アプリへのアクセスでは、コンテナーのダウンロードと起動が実行されるために多少時間がかかります。

前の手順で Azure 運用データベースに保存された登録済みのゲストが表示されます。

 ![ローカルで実行されている Docker コンテナー ベースの Python Flask アプリケーション](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**お疲れさまでした。** Docker コンテナー ベースの Python Flask アプリケーションを Azure App Service で実行しています。


## <a name="update-data-model-and-redeploy"></a>データ モデルを更新し、再デプロイする

この手順では、ゲスト モデルを更新して、各イベント登録に参加者数を追加します。

次の git コマンドを使用して、`0.2-migration` リリースをチェック アウトします。
```bash
git checkout tags/0.2-migration
```

このリリースでは、ビュー、コントローラー、およびモデルに必要な変更が既に行われています。 *alembic* (`flask db migrate`) で生成されるデータベースの移行も含まれています。 次の git コマンドを使用して、実行されたすべての変更を確認できます。

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>変更をローカルでテストする

次のコマンドで Flask サーバーを実行して、変更をローカルでテストします。

Mac/Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

変更を表示するには、ブラウザーで `http://127.0.0.1:5000` に移動します。 新しいダミー登録を作成します。

![ローカルで実行されている Docker コンテナー ベースの Python Flask アプリケーション](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

新しい Docker イメージを構築してコンテナー レジストリにプッシュし、アプリケーションを再起動します。

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Azure Web アプリに移動し、新機能をもう一度試します。 別のイベント登録を作成します。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service の Docker Python Flask アプリ](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Azure Web アプリを管理する

Azure Portal に移動し、作成した Web アプリを表示します。

そのためには、[https://portal.azure.com](https://portal.azure.com) にサインインします。

左側のメニューで **[App Service]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-docker-python-postgresql-app/app-resource.png)

Web アプリの "_ブレード_" (水平方向に開かれるポータル ページ) が表示されます。

既定では、Web アプリのブレードは **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ブレードの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の App Service ブレード](./media/app-service-web-tutorial-docker-python-postgresql-app/app-mgmt.png)

ブレードのこれらのタブは、Web アプリに追加することができるさまざまな優れた機能を示しています。 次の一覧では、ほんの一部の例を示しています。

* カスタム DNS 名をマップする
* カスタム SSL 証明書をバインドする
* 継続的なデプロイを構成する
* スケールアップとスケールアウトを行う
* ユーザー認証を追加する


