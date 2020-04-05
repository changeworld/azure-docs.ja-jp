---
title: チュートリアル:Postgre を使用する Linux Python アプリ
description: Azure の PostgreSQL データベースに接続して、Linux Python アプリを Azure App Service で動作させる方法について説明します。 このチュートリアルでは Django を使用します。
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523936"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>チュートリアル:Azure App Service で PostgreSQL を使用して Python (Django) Web アプリを実行する

[Azure App Service](app-service-linux-intro.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、データドリブンの Python Django Web アプリを Azure Database for PostgreSQL データベースに接続し、Azure App Service でアプリをデプロイして実行する方法について説明します。

![Azure App Service での Python Django Web アプリ](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Database for PostgreSQL データベースを作成し、Web アプリをそれに接続する
> * Web アプリを Azure App Service にデプロイする
> * 診断ログを表示する
> * Azure portal で Web アプリを管理する

この記事の手順は、macOS、Linux、Windows で実行できます。 ほとんどの場合、手順は似ていますが、このチュートリアルでは違いについては詳しく説明しません。 以下のほとんどの例では、Linux 上の `bash` ターミナル ウィンドウを使います。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- [Git](https://git-scm.com/) のインストール。
- [Python 3](https://www.python.org/downloads/) をインストールします。
- [PostgreSQL](https://www.postgresql.org/download/) をインストールして実行します。

## <a name="test-postgresql-installation-and-create-a-database"></a>PostgreSQL のインストールをテストしてデータベースを作成する

最初に、ローカル環境の PostgreSQL サーバーに接続して、データベースを作成します。 

ローカル ターミナル ウィンドウで `psql` を実行し、組み込みの `postgres` ユーザーとしてローカル環境の PostgreSQL サーバーに接続します。

```bash
sudo su - postgres
psql
```
or
```PowerShell
psql -U postgres
```

接続に成功すれば、PostgreSQL データベースは実行されています。 接続されない場合は、「[Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/)」 (ダウンロード - PostgreSQL コア ディストリビューション) にあるお使いの OS の指示に従って、ローカル PostgreSQL データベースが開始されていることを確認します。

*pollsdb* という名前で新しいデータベースを作成し、名前が *manager* でパスワードが *supersecretpass* のデータベース ユーザーを設定します。

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

「`\q`」を入力して PostgreSQL クライアントを終了します。

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>ローカル Python アプリを作成して実行する

次に、Python Django のサンプル Web アプリを設定して実行します。

[djangoapp](https://github.com/Azure-Samples/djangoapp) サンプル リポジトリには、データドリブンの [Django](https://www.djangoproject.com/) 投票アプリが含まれます。これを、Django ドキュメントの「[はじめての Django アプリ作成](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)」に従って取得します。

### <a name="clone-the-sample-app"></a>サンプル アプリの複製

ターミナル ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリを複製し、新しい作業ディレクトリに変更します。

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Python 仮想環境を構成する

アプリを実行するための Python 仮想環境を作成してアクティブ化します。

```bash
python3 -m venv venv
source venv/bin/activate
```
or
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

`venv` 環境で *env.sh* または *env.ps1* を実行し、*azuresite/settings.py* でデータベース接続の設定に使用される環境変数を設定します。

```bash
source ./env.sh
```
or
```PowerShell
.\env.ps1
```

*requirements.txt* から必要なパッケージをインストールし、[Django の移行](https://docs.djangoproject.com/en/2.1/topics/migrations/)を実行して、[管理者ユーザーを作成](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)します。

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Web アプリの実行

管理者ユーザーを作成した後、Django サーバーを実行します。

```bash
python manage.py runserver
```

Django Web アプリが完全に読み込まれると、次のようなメッセージが表示されます。

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

ブラウザーで *http:\//localhost:8000* にアクセスします。 **No polls are available** (投票は使用できません) というメッセージが表示されます。 

*http:\//localhost:8000/admin* に移動し、前の手順で作成した管理者ユーザーを使用してサインインします。 **[Questions]\(質問\)** の横の **[Add]\(追加\)** を選択し、いくつかの選択肢がある投票の質問を作成します。

![App Services の Python Django アプリをローカルで実行する](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

*http:\//localhost:8000* に再び移動して、投票の質問を確認し、質問に回答します。 ローカルの Django サンプル アプリケーションにより、ユーザー データがローカル環境の PostgreSQL データベースに書き込まれて格納されます。

Django サーバーを停止するには、ターミナルで Ctrl + C キーを押します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

この記事の残りの手順のほとんどでは、Azure Cloud Shell で Azure CLI コマンドを使います。 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Azure Database for PostgreSQL を作成して接続する

このセクションでは、Azure Database for PostgreSQL のサーバーとデータベースを作成し、Web アプリをそれに接続します。 Web アプリを Azure App Service にデプロイすると、アプリではこのクラウド データベースが使用されます。 

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure Database for PostgreSQL サーバー用に新しいリソース グループを作成しても、既存のリソース グループを使用してもかまいません。 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

Cloud Shell で [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) コマンドを使用して、PostgreSQL サーバーを作成します。

> [!NOTE]
> Azure Database for PostgreSQL サーバーを作成する前に、お使いのリージョンで使用できる[コンピューティング世代](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores)を確認します。 リージョンで Gen4 ハードウェアがサポートされていない場合は、次のコマンド ラインの *--sku-name* を、使用しているリージョンでサポートされている値 (Gen5 など) に変更します。 

次のコマンドで、 *\<postgresql-name>* を一意のサーバー名に置き換えます。 サーバー名は、PostgreSQL エンドポイント *https://\<postgresql-name>.postgres.database.azure.com* の一部になるため、Azure のすべてのサーバーで一意である必要があります。 

*\<resourcegroup-name>* および *\<region>* を、使用するリソース グループの名前とリージョンに置き換えます。 *\<admin-username>* および *\<admin-password>* については、データベース管理者アカウントのユーザー資格情報を作成します。 *\<admin-username>* および *\<admin-password>* は、後で PostgreSQL サーバーとデータベースにサインインするときに使うので、憶えておきます。

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Azure Database for PostgreSQL サーバーが作成されると、Azure CLI によって次の例のような JSON コードが返されます。

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーのファイアウォール規則を作成する

[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) コマンドを実行して、Azure リソースからデータベースにアクセスできるようにします。 *\<postgresql-name>* および *\<resourcegroup-name>* プレースホルダーは、実際の値に置き換えます。

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> 前の設定により、Azure ネットワーク内のすべての IP アドレスからネットワーク接続できます。 運用環境で使用する場合、[アプリで使用されている送信 IP アドレスのみを許可する](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)ことで、最も制限の厳しいファイアウォール規則を構成してみてください。

`firewall-rule create` コマンドを再び実行して、ローカル コンピューターからのアクセスを許可します。 *\<your-ip-address>* は、[ローカル環境の IPv4 IP アドレス](https://www.whatsmyip.org/)に置き換えます。 *\<postgresql-name>* および *\<resourcegroup-name>* プレースホルダーは、独自の値に置き換えます。

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Azure Database for PostgreSQL データベースを作成して接続する

次のコマンドを実行して、Azure Database for PostgreSQL サーバーに接続します。 独自の *\<postgresql-name>* および *\<admin-username>* を使用し、作成したパスワードでサインインします。

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

ローカル PostgreSQL サーバーで行ったのと同様にして、Azure Database for PostgreSQL サーバーにデータベースとユーザーを作成します。

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> ベスト プラクティスとしては、管理者ユーザーを使用する代わりに、特定のアプリに対して作成するデータベース ユーザーのアクセス許可を制限します。 `manager` ユーザーは、`pollsdb` データベースに対して "*のみ*"、完全な権限を持っています。

「`\q`」を入力して PostgreSQL クライアントを終了します。

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Azure PostgreSQL データベースへのアプリの接続をテストする

ローカル環境の *env.sh* または *env.ps1* ファイルを編集し、 *\<postgresql-name>* を Azure Database for PostgreSQL サーバーの名前に置き換えることにより、クラウドの PostgreSQL データベースをポイントします。

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
or
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

ローカル ターミナル ウィンドウの `venv` 環境で、編集した *env.sh* または *env.ps1* を実行します。 
```bash
source ./env.sh
```
or
```PowerShell
.\env.ps1
```

Azure データベースへの Django の移行を実行し、管理者ユーザーを作成します。

```bash
python manage.py migrate
python manage.py createsuperuser
```

管理者ユーザーが作成されたら、Django サーバーを実行します。

```bash
python manage.py runserver
```

ブラウザーで *http:\//localhost:8000* にアクセスすると、**No polls are available** (投票は使用できません) というメッセージが再び表示されます。 

*http:\//localhost:8000/admin* に移動し、作成した管理者ユーザーを使ってサインインして、前と同じように投票の質問を作成します。

![App Services の Python Django アプリをローカルで実行する](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

*http:\//localhost:8000* にもう一度移動し、投票の質問が表示されていることを確認します。 アプリで Azure Database for PostgreSQL データベースにデータが書き込まれるようになりました。

Django サーバーを停止するには、ターミナルで Ctrl + C キーを押します。

## <a name="deploy-the-web-app-to-azure-app-service"></a>Web アプリを Azure App Service にデプロイする

このステップでは、Azure Database for PostgreSQL データベースに接続される Python アプリを Azure App Service にデプロイします。

### <a name="configure-repository"></a>リポジトリの構成

このチュートリアルでは Django サンプルを使用するため、Azure App Service で動作させるには、*djangoapp/azuresite/settings.py* ファイルの一部の設定を変更および追加する必要があります。 

1. Django では、受信要求の `HTTP_HOST` ヘッダーが検証されます。 App Service で Django Web アプリを動作させるには、許可されるホストにアプリの完全修飾ドメイン名を追加する必要があります。 
   
   *azuresite/settings.py* を編集し、`ALLOWED_HOSTS` の行を次のように変更します。
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django では、[運用環境で静的ファイルを提供すること](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)はサポートされていません。 このチュートリアルでは、[WhiteNoise](https://whitenoise.evans.io/en/stable/) を使用して、ファイルを提供できるようにします。 WhiteNoise パッケージは、*requirements.txt* で既にインストールされています。 
   
   WhiteNoise を使用するように Django を構成するには、*azuresite/settings.py* で `MIDDLEWARE` の設定を見つけ、一覧の `django.middleware.security.SecurityMiddleware` 行の直後に `whitenoise.middleware.WhiteNoiseMiddleware` を追加します。 `MIDDLEWARE` 設定は次のようになります。
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. *azuresite/settings.py* の末尾に、次の行を追加します。
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   WhiteNoise の構成の詳細については、[WhiteNoise のドキュメント](https://whitenoise.evans.io/en/stable/)を参照してください。

> [!IMPORTANT]
> データベースの設定セクションは、環境変数の使用に関するセキュリティのベスト プラクティスに既に従っています。 完全なデプロイの推奨事項については、[Django ドキュメントのデプロイ チェックリスト](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)を参照してください。

*djangoapp* リポジトリのフォークに変更をコミットします。

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Create App Service plan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>環境変数を構成する

チュートリアルの前半で、PostgreSQL データベースに接続する環境変数を定義しました。

Azure App Service で、[az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用して、環境変数を "*アプリ設定*" として設定します。

Azure Cloud Shell で、次のコマンドを実行して、データベース接続の詳細をアプリ設定として指定します。 *\<app-name>* 、 *\<resourcegroup-name>* 、 *\<postgresql-name>* は、独自の値に置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

コードでこれらのアプリ設定にアクセスする方法については、「[環境変数へのアクセス](how-to-configure-python.md#access-environment-variables)」をご覧ください。

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

App Service デプロイ サーバーによってリポジトリ ルート内の *requirements.txt* が確認され、`git push` 後に Python パッケージ管理が自動的に実行されます。

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

*http:\//\<app-name>.azurewebsites.net* という URL で、デプロイしたアプリを参照します。 これには少し時間がかかります。これは、アプリが最初に要求されたときは、コンテナーをダウンロードして実行する必要があるためです。 ページがタイムアウトしたか、エラー メッセージが表示された場合、数分待ってからページを更新してください。

先ほど作成した投票の質問が表示されます。 

App Service では、各サブディレクトリ内で *wsgi.py* (`manage.py startproject` によって既定で作成されます) を探すことにより、リポジトリ内の Django プロジェクトが検出されます。 App Service でファイルが見つかると、Django Web アプリが読み込まれます。 App Service による Python アプリのロード方法の詳細については[組み込みの Python イメージの構成](how-to-configure-python.md)に関する記事を参照してください。

*http:\//\<app-name>.azurewebsites.net/admin* に移動し、作成した管理者ユーザーを使ってサインインします。 必要に応じて、投票の質問をさらに作成します。

![App Services の Python Django アプリを Azure で実行する](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**お疲れさまでした。** Azure App Service for Linux で Python (Django) Web アプリが実行されています。

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Azure portal でアプリを管理する

[Azure portal](https://portal.azure.com) で、作成したアプリを探して選択します。

![Azure portal で Python Django アプリに移動する](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

既定では、ポータルにはアプリの **[概要]** ページが表示されます。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、再開、削除といった基本的な管理タスクを実行することもできます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure portal の [概要] ページで Python Django アプリを管理する](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

次のチュートリアルに進んで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](../app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [Python アプリの構成](how-to-configure-python.md)
