---
title: Azure App Service で PostgreSQL 向けの Python Web アプリを構築する | Microsoft Docs
description: PostgreSQL データベースに接続するデータ駆動型の Python アプリを Azure 上で実行する方法について説明します。
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435788"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Azure で Docker Python と PostgreSQL アプリを構築する

[App Service on Linux](app-service-linux-intro.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、PostgreSQL をデータベース バックエンドとして使用する、データ駆動型の Python Web アプリを作成する方法について説明します。 完了すると、App Service on Linux 上の Docker コンテナー内で実行される Python Flask アプリケーションが完成します。

![App Service on Linux の Docker Python Flask アプリ](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure で PostgreSQL データベースを作成する
> * Python アプリを PostgreSQL に接続する
> * Azure にアプリケーションをデプロイする
> * 診断ログを表示する
> * データ モデルを更新し、アプリを再デプロイする
> * Azure Portal でアプリを管理する

この記事の手順は macOS で実行できます。 Linux および Windows でも手順はほとんど同じですが、このチュートリアルでは相違点について詳しく説明していません。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

1. [Git をインストールする](https://git-scm.com/)
1. [Python をインストールする](https://www.python.org/downloads/)
1. [PostgreSQL をインストールして実行する](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>PostgreSQL のローカル インストールをテストし、データベースを作成する

ローカルのターミナル ウィンドウで、`psql` を実行してローカルの PostgreSQL サーバーに接続します。

```bash
sudo -u postgres psql postgres
```

`unknown user: postgres` のようなメッセージが表示された場合、PostgreSQL インストールがログイン ユーザー名で構成されている可能性があります。 代わりに次のコマンドを試してください。

```bash
psql postgres
```

接続に成功すれば、PostgreSQL データベースは実行されています。 接続されない場合は、「[Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/)」 (ダウンロード - PostgreSQL コア ディストリビューション) にあるお使いの OS の指示に従って、ローカル PostgreSQL データベースが開始されていることを確認します。

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

次のコマンドを実行して、サンプル リポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

このサンプル レポジトリには、[Flask](http://flask.pocoo.org/) アプリケーションが含まれています。

### <a name="run-the-app-locally"></a>アプリをローカルで実行する

必要なパッケージをインストールし、アプリケーションを起動します。

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
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

![ローカルで実行されている Python Flask アプリケーション](./media/tutorial-python-postgresql-app/local-app.png)

Flask サンプル アプリケーションは、ユーザー データをデータベースに格納します。 ユーザーの登録に成功すれば、アプリはローカル PostgreSQL データベースにデータを書き込んでいます。

Flask サーバーを任意のタイミングで停止するには、ターミナルで Ctrl + C キーを押します。

## <a name="create-a-production-postgresql-database"></a>運用 PostgreSQL データベースを作成する

この手順では、Azure に PostgreSQL データベースを作成します。 アプリを Azure にデプロイすると、このクラウド データベースがアプリで使用されます。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

Cloud Shell 内で [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) コマンドを使用して、PostgreSQL サーバーを作成します。

次のコマンドの例では、*\<postgresql_name>* を一意のサーバー名、*\<admin_username>* と *\<admin_password>* を目的のユーザー資格情報に置き換えます。 このユーザー資格情報は、データベース管理者のアカウントのものです。 このサーバー名は、PostgreSQL エンドポイント (`https://<postgresql_name>.postgres.database.azure.com`) の一部として使用されるため、Azure のすべてのサーバーで一意である必要があります。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
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
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> 後のために \<admin_username> と \<admin_password> を覚えておきます。 Postgre サーバーとそのデータベースにサインインするために必要です。


### <a name="create-firewall-rules-for-the-postgresql-server"></a>PostgreSQL サーバー向けのファイアウォール規則を作成する

Cloud Shell 内で次の Azure CLI コマンドを実行し、Azure リソースからデータベースにアクセスできるようにします。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> この設定により、Azure ネットワーク内のすべての IP からネットワーク接続できます。 運用環境で使用する場合、[アプリで使用されている送信 IP アドレスのみを使用する](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)ことで、最も制限の厳しいファイアウォール規則を構成してみてください。

Cloud Shell 内で *\<you_ip_address>* を [ローカル IPv4 IP アドレス](http://www.whatsmyip.org/)に置き換えてコマンドを再び実行し、ローカル コンピューターからアクセスできるようにします。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Python アプリを運用データベースに接続する

この手順では、作成した PostgreSQL サーバー向けの Azure データベースに、Flask サンプル アプリを接続します。

### <a name="create-empty-database-and-user-access"></a>空のデータベースおよびユーザー アクセスを作成する

ローカル ターミナル ウィンドウで次のコマンドを実行し、データベースに接続します。 管理者パスワードの入力を求められたら、「[PostgreSQL サーバー向けの Azure データベースの作成](#create-an-azure-database-for-postgresql-server)」で指定したのと同じパスワードを使用します。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

ローカル Postgres サーバーと同様に、Azure Postgres サーバーでデータベースとユーザーを作成します。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

「`\q`」を入力して PostgreSQL クライアントを終了します。

> [!NOTE]
> ベスト プラクティスとしては、管理者ユーザーを使用する代わりに、特定のアプリケーションに対して作成するデータベース ユーザーのアクセス許可を制限します。 この例では、`manager` ユーザーには `eventregistration` データベースに_のみ_完全な権限が与えられています。

### <a name="test-app-connectivity-to-production-database"></a>アプリから運用データベースへの接続をテストする

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

![ローカルで実行されている Python Flask アプリケーション](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>[Deploy to Azure (Azure へのデプロイ)]

この手順では、Postgre に接続される Python アプリケーションを Azure App Service にデプロイします。

### <a name="configure-repository"></a>リポジトリの構成

App Service の Git デプロイ エンジンにより、リポジトリ ルートに _application.py_ があるとき、`pip` 自動化が呼び出されます。 このチュートリアルで、ユーザーの代わりにデプロイ エンジンに自動化を実行させます。 ローカル ターミナル ウィンドウでリポジトリ ルートに移動し、ダミー _application.py_ を作成し、変更をコミットします。

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>環境変数を構成する

チュートリアルの前半で、PostgreSQL データベースに接続する環境変数を定義しました。

App Service では、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用し、環境変数を_アプリ設定_として設定します。

次の例では、データベース接続の詳細をアプリ設定として指定します。 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

### <a name="configure-entry-point"></a>エントリ ポイントの構成

既定では、組み込みイメージによって、ルート ディレクトリにある _wsgi.py_ または _application.py_ がエントリ ポイントとして検索されますが、エントリ ポイントは _app/app.py_ です。 先に追加した _application.py_ は空であり、何も行われません。

Cloud Shell で [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set) コマンドを実行し、カスタム スタートアップ スクリプトを設定します。

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

`--startup-file` パラメーターはカスタム コマンドを受け取るか、カスタム コマンドを含むファイルのパスを受け取ります。 カスタム コマンドは次の形式にする必要があります。

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

カスタム コマンドでは、エントリ ポイントがルート ディレクトリになく、`<subdirectory>` がサブディレクトリの場合、`--chdir` が必須となります。 `<module>` は _.py_ ファイルの名前で、`<variable>` は Web アプリを表すモジュール内の変数です。

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリの参照

デプロイした Web アプリを参照します。 これには少し時間がかかります。これは、アプリが最初に要求されたときは、コンテナーをダウンロードして実行する必要があるためです。 ページがタイムアウトしたか、エラー メッセージが表示された場合、数分待ってからページを更新してください。

```bash
http://<app_name>.azurewebsites.net
```

前の手順で Azure 運用データベースに保存された登録済みのゲストが表示されます。

![Azure で実行されている Python Flask アプリケーション](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**お疲れさまでした。** App Service for Linux で Python アプリが実行されています。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

Python アプリはコンテナーで実行されているため、App Service on Linux では、コンテナー内から生成されたコンソール ログにアクセスできます。 ログを検索するには、次の URL に移動します。

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

JSON オブジェクトが 2 つ表示されるはずです。いずれにも `href` プロパティがあります。 1 つの `href` は Docker コンソール ログ (`_docker.log` で終わる) を指し、もう 1 つの `href` は Python コンテナー内から生成されたコンソール ログを指します。 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

必要な `href` 値をブラウザー ウィンドウにコピーし、ログに移動します。 ログはストリーム配信されません。そのため、多少の遅延が発生することがあります。 新しいログを表示するには、ブラウザー ページを更新します。

## <a name="update-data-model-and-redeploy"></a>データ モデルを更新し、再デプロイする

この手順では、`Guest` モデルを更新して各イベント登録に参加者数を追加し、Azure に更新プログラムを再デプロイします。

ローカル ターミナル ウィンドウで、次の Git コマンドを使用し、`modelChange` ブランチからファイルをチェックアウトします。

```bash
git checkout origin/modelChange -- .
```

このチェックアウトでモデル、ビュー、コントローラーに必要な変更が行われます。 *alembic* (`flask db migrate`) で生成されるデータベースの移行も含まれています。 次の Git コマンドですべての変更を確認できます。

```bash
git diff master origin/modelChange
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

![ローカルで実行されている Docker コンテナー ベースの Python Flask アプリケーション](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

ローカル ターミナル ウィンドウで、すべての変更を Git にコミットし、コード変更を Azure にプッシュします。

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Azure Web アプリに移動し、新機能をもう一度試します。 必ずページを更新してください。

```bash
http://<app_name>.azurewebsites.net
```

![Azure App Service の Docker Python Flask アプリ](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>Azure Portal で Web アプリを管理する

[Azure Portal](https://portal.azure.com) に移動し、作成した Web アプリを表示します。

左側のメニューで **[App Services (App Services)]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/tutorial-python-postgresql-app/app-resource.png)

既定では、ポータルは Web アプリの **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の [App Service] ページ](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure で PostgreSQL データベースを作成する
> * Python アプリを PostgreSQL に接続する
> * Azure にアプリケーションをデプロイする
> * 診断ログを表示する
> * データ モデルを更新し、アプリを再デプロイする
> * Azure Portal でアプリを管理する

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [組み込み Python イメージを構成する](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure Web Apps にマップする](../app-service-web-tutorial-custom-domain.md)

