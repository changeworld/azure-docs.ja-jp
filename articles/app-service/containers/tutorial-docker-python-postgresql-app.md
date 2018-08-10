---
title: Azure App Service で Python および PostgreSQL Web アプリを構築する | Microsoft Docs
description: PostgreSQL データベースに接続するデータ ドリブン Python アプリを Azure 内で実行する方法について説明します。
services: app-service\web
documentationcenter: python
author: berndverst
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: ce84498ab89891bd7b96cfcc6b0c7ac029c93cbd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423081"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Azure で Docker Python と PostgreSQL アプリを構築する

Web App for Containers では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、PostgreSQL をデータベース バックエンドとして使用して、データ ドリブン Python Web アプリを作成する方法について説明します。 完了すると、[App Service on Linux](app-service-linux-intro.md) 上の Docker コンテナー内で実行される Python Flask アプリケーションが完成します。

![App Service on Linux の Docker Python Flask アプリ](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure で PostgreSQL データベースを作成する
> * Python アプリを PostgreSQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure Portal でアプリを管理する

この記事の手順は macOS で実行できます。 Linux および Windows でも手順はほとんど同じですが、このチュートリアルでは相違点について詳しく説明していません。
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

1. [Git をインストールする](https://git-scm.com/)
1. [Python をインストールする](https://www.python.org/downloads/)
1. [PostgreSQL をインストールして実行する](https://www.postgresql.org/download/)
1. [Docker Community Edition をインストールする](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>PostgreSQL のローカル インストールをテストし、データベースを作成する

ローカルのターミナル ウィンドウで、`psql` を実行してローカル PostgreSQL サーバーに接続します。

```bash
sudo -u postgres psql
```

接続に成功すれば、PostgreSQL データベースは実行されています。 接続されない場合は、「[Downloads - PostgreSQL Core Distribution (ダウンロード - PostgreSQL コア ディストリビューション)](https://www.postgresql.org/download/)」の手順に従って、ローカル PostgreSQL データベースが開始されていることを確認します。

*eventregistration* という名前のデータベースを作成し、名前が *manager* でパスワードが *supersecretpass* の別のデータベース ユーザーを設定します。

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

「`\q`」を入力して PostgreSQL クライアントを終了します。 

<a name="step2"></a>

## <a name="create-local-python-app"></a>ローカルの Python アプリを作成する

この手順では、ローカル Python Flask プロジェクトを設定します。

### <a name="clone-the-sample-app"></a>サンプル アプリの複製

ターミナル ウィンドウを開き、`CD` コマンドで作業ディレクトリに移動します。

次のコマンドを実行してサンプル レポジトリを複製し、*0.1-initialapp* リリースに移動します。

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

このサンプル レポジトリには、[Flask](http://flask.pocoo.org/) アプリケーションが含まれています。 

### <a name="run-the-app-locally"></a>アプリをローカルで実行する

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

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

Cloud Shell 内で [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) コマンドを使用して、PostgreSQL サーバーを作成します。

次のコマンドの例では、*\<postgresql_name>* を一意のサーバー名、*\<admin_username>* と *\<admin_password>* を目的のユーザー資格情報に置き換えます。 このサーバー名は、PostgreSQL エンドポイント (`https://<postgresql_name>.postgres.database.azure.com`) の一部として使用されるため、Azure のすべてのサーバーで一意である必要があります。 このユーザー資格情報は、データベース管理者のユーザー アカウントのものです。 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name GP_Gen4_2
```

Azure Database for PostgreSQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "<admin_username>",
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

### <a name="create-a-firewall-rule-for-the-postgresql-server"></a>PostgreSQL サーバーのファイアウォール ルールを作成する

Cloud Shell 内で、次の Azure CLI コマンドを実行して、すべての IP アドレスからデータベースにアクセスできるようにします。 開始 IP と終了 IP の両方が `0.0.0.0` に設定されている場合、ファイアウォールは他の Azure リソースに対してのみ開かれます。 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

> [!TIP] 
> [アプリで使用する送信 IP アドレスのみを使用する](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)ことで、ファイアウォール規則による制限をさらに厳しくすることができます。
>

Cloud Shell 内で、*\<you_ip_address>* を [ローカル IPv4 IP アドレス](https://whatismyipaddress.com/)に置き換えてコマンドを再び実行し、ローカル コンピューターからデータベースにアクセスできるようにします。 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<you_ip_address> --end-ip-address=<you_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Python アプリを運用データベースに接続する

この手順では、作成した Azure Database for PostgreSQL サーバーに、Flask サンプル アプリを接続します。

### <a name="create-empty-database-and-user-access"></a>空のデータベースおよびユーザー アクセスを作成する

Cloud Shell 内で `psql` を実行し、データベースに接続します。 管理者パスワードの入力を求められたら、「[Azure Database for PostgreSQL サーバーの作成](#create-an-azure-database-for-postgresql-server)」で指定したのと同じパスワードを使用します。

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

### <a name="test-app-connectivity-to-production-database"></a>運用データベースへのアプリの接続をテストする

ローカル ターミナル ウィンドウで、次のコマンドを実行し、Flask データベースの移行と Flask サーバーを実行します。

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

## <a name="upload-app-to-a-container-registry"></a>アプリをコンテナー レジストリにアップロードする

この手順では、Docker イメージを作成して、Azure Container Registry にアップロードします。 Docker Hub などの人気のあるレジストリを使用することもできます。

### <a name="build-the-docker-image-and-test-it"></a>Docker イメージをビルドしてテストする

ローカル ターミナル ウィンドウで Docker イメージをビルドします。

```bash
cd ..
docker build -t flask-postgresql-sample .
```

イメージが正常に作成されたという確認のメッセージが Docker に表示されます。

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

Docker コンテナー内でイメージをローカルに実行します。 次のコマンドでは、環境変数ファイルを指定し、既定の Flask ポート 5000 をローカル ポート 5000 にマップします。

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

コンテナーがローカルで動作することを確認したので、_db.env_ を削除します。 Azure App Service で、アプリの設定を使用して、環境変数を定義します。  

### <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する

Cloud Shell 内で、次のコマンドを使用して、 Azure Container Registry 内にレジストリを作成します。 *\<registry_name>* は、一意のレジストリー名に置き換えます。

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

### <a name="retrieve-registry-credentials"></a>レジストリ資格情報を取得する

Cloud Shell 内で、次のコマンドを実行して、レジストリの資格情報を取得します。 この情報がないと、イメージをプッシュしたり、プルしたりできません。

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

出力には、2 つのパスワードが表示されています。 ユーザー名 (既定では、レジストリ名) と最初のパスワードをメモしておきます。

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

### <a name="upload-docker-image-to-registry"></a>Docker イメージをレジストリにアップロードする

ローカル ターミナル ウィンドウで、`docker` を使用して、新しいレジストリにサインインします。 メッセージが表示されたら、取得したパスワードを指定します。

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

レジストリに Docker イメージをプッシュします。

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="create-web-app-with-uploaded-image"></a>アップロードされたイメージを使用して Web アプリを作成する

この手順では、Azure App Service でアプリを作成し、Azure Container Registry にアップロードされた Docker イメージを使用するように、このアプリを構成します。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する

Cloud Shell 内で、[`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) コマンドを使用して、*myAppServicePlan* App Service プラン内に Web アプリを作成します。

次のコマンドで、*\<app_name>* プレースホルダーを一意のアプリ名に置き換えます。 この名前は、Web アプリの既定の URL の一部であるため、Azure App Service のすべてのアプリで一意である必要があります。

```azurecli-interactive
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

### <a name="configure-environment-variables"></a>環境変数を構成する

チュートリアルの前半で、PostgreSQL データベースに接続する環境変数を定義しました。

App Service で、[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用して、環境変数を "_アプリ設定_" として設定します。

次の例では、データベース接続の詳細をアプリ設定として指定します。 また、*WEBSITES_PORT* 変数をコンテナー ポート 5000 に設定しています。これにより、コンテナーは、ポート 80 で HTTP トラフィックを受信できます。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" WEBSITES_PORT=5000
```

### <a name="configure-custom-container-deployment"></a>カスタム コンテナー デプロイを構成する

コンテナー イメージの名前を既に指定した場合でも、カスタム レジストリ URL とユーザーの資格情報を指定する必要があります。 Cloud Shell 内で、[az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) コマンドを実行します。

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Cloud Shell 内で、アプリを再起動します。 再起動により、すべての設定が適用され、最新のコンテナーがレジストリからプルされるようになります。

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリの参照 

デプロイした Web アプリを参照します。 

```bash 
http://<app_name>.azurewebsites.net 
```

> [!NOTE]
> Web アプリの起動には少し時間がかかります。これは、アプリが最初に要求されたときは、コンテナーをダウンロードして実行する必要があるためです。 だいぶ時間が経ってから最初にエラーが表示された場合は、ページを更新してください。

前の手順で Azure 運用データベースに保存された登録済みのゲストが表示されます。

![ローカルで実行されている Docker コンテナー ベースの Python Flask アプリケーション](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**お疲れさまでした。** Web App for Containers 内で Python アプリが実行されています。

## <a name="update-data-model-and-redeploy"></a>データ モデルを更新し、再デプロイする

この手順では、`Guest` モデルを更新して、各イベント登録に参加者数を追加します。

ローカル ターミナル ウィンドウで、次の git コマンドを使用して、*0.2-migration* リリースをチェックアウトします。

```bash
git checkout tags/0.2-migration
```

このリリースでは、モデル、ビュー、およびコントローラーに必要な変更が既に行われています。 *alembic* (`flask db migrate`) で生成されるデータベースの移行も含まれています。 次の git コマンドを使用して、実行されたすべての変更を確認できます。

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>変更をローカルでテストする

ローカル ターミナル ウィンドウで、次のコマンドで Flask サーバーを実行して、変更をローカルでテストします。

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

変更を表示するには、ブラウザーで http://localhost:5000 に移動します。 テスト登録を作成します。

![ローカルで実行されている Docker コンテナー ベースの Python Flask アプリケーション](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

ローカル ターミナル ウィンドウで、新しい docker イメージをビルドし、レジストリにプッシュします。

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

Cloud Shell 内でアプリを再起動し、最新のコンテナーがレジストリから取得されることを確認します。

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
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
