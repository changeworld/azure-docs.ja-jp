---
title: チュートリアル:Postgres を使用する Python (Django) のデプロイ
description: PostgreSQL データベースを使用する Python アプリを作成し、Azure App Service on Linux にデプロイする方法について説明します。 このチュートリアルでは、Django サンプル アプリを使用してデモンストレーションを行います。
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 504e2f7c07d8d29e4fe4dad52dc008c895517a3d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609784"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>チュートリアル:PostgreSQL を使用した Python (Django) Web アプリを Azure App Service にデプロイする

このチュートリアルでは、データ ドリブンの Python (Django) Web アプリを [Azure App Service](app-service-linux-intro.md) にデプロイし、それを Azure Database for PostgreSQL データベースに接続する方法について説明します。 App Service は、高いスケーラビリティを備えた、パッチを自己適用する Web ホスティング サービスです。

![Python Django Web アプリを Azure App Service にデプロイする](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Database for PostgreSQL データベースを作成する
> * Azure App Service にコードをデプロイして Postgres に接続する
> * コードを更新して再デプロイする
> * 診断ログを表示する
> * Azure portal で Web アプリを管理する

この記事の手順は、macOS、Linux、Windows で実行できます。

## <a name="install-dependencies"></a>依存関係のインストール

このチュートリアルを始める前に:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- [Azure CLI](/cli/azure/install-azure-cli) をインストールします。
- [Git](https://git-scm.com/) のインストール。
- [Python 3](https://www.python.org/downloads/) をインストールします。

## <a name="clone-the-sample-app"></a>サンプル アプリのクローン

ターミナル ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをクローンし、リポジトリのルートに変更します。

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

djangoapp サンプル リポジトリには、データドリブンの [Django](https://www.djangoproject.com/) 投票アプリが含まれます。これを、Django ドキュメントの「[はじめての Django アプリ作成](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)」に従って取得します。 ご参考までに、こちらに記載しています。

## <a name="prepare-app-for-app-service"></a>App Service 向けのアプリを準備する

多くの Python Web フレームワークと同様、Django も、[運用サーバーで実行する前に特定の変更を必要](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)とします。そして、それは App Service でも変わりません。 App Service にデプロイした後もアプリが正しく動作するよう、既定の *azuresite/settings.py* ファイルでいくつかの設定を追加、変更する必要があります。 

App Service に必要な構成を行う *azuresite/production.py* を見てみましょう。 簡単に言えば、このファイルは次のことを行います。

- *azuresite/settings.py* からすべての設定を継承します。
- 許可されるホストに、App Service アプリの完全修飾ドメイン名を追加します。 
- Django では既定では運用環境で静的ファイルが配信されないため、[WhiteNoise](https://whitenoise.evans.io/en/stable/) を使用して、運用環境における静的ファイルの配信を有効にします。 WhiteNoise パッケージは *requirements.txt* に既に含まれています。
- PostgreSQL データベースの構成を追加します。 Django では Sqlite3 が既定のデータベースとして使用されますが、運用アプリには適しません。 [psycopg2-binary](https://pypi.org/project/psycopg2-binary/) パッケージは *requirements.txt* に既に含まれています。
- Postgres の構成には、環境変数が使用されています。 App Service での環境変数の設定方法については後述します。

リポジトリには便宜上 *azuresite/production.py* が含まれていますが、そのままではアプリで使用されません。 その設定が App Service で使用されるようにするには、そのファイルにアクセスするように、*manage.py* と *azuresite/wsgi.py* という 2 つのファイルを構成する必要があります。

- *manage.py* で、次の行を変更します。

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    次のコードにします。

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    環境変数 `DJANGO_ENV` は、後で App Service アプリを構成する際に設定します。

- *azuresite/wsgi.py* にも、上記と同じ変更を加えます。

    App Service では、*manage.py* を使用してデータベースの移行を実行します。また、App Service は *azuresite/wsgi.py* を使用して Django アプリを運用環境で実行します。 この変更を両方のファイルに行うことで、どちらのケースでも確実に運用環境の設定が使用されます。

## <a name="sign-in-to-azure-cli"></a>Azure CLI へのサインイン

Azure CLI をあらかじめインストールしておく必要があります。 [Azure CLI](/cli/azure/what-is-azure-cli) を使用して、コマンド ライン ターミナルから Azure リソースを操作することができます。 

Azure にサインインするには、[`az login`](/cli/azure/reference-index#az-login) コマンドを実行します。

```azurecli
az login
```

ターミナルで手順に従って自分の Azure アカウントにサインインします。 完了すると、ご利用のサブスクリプションが JSON 形式でターミナル出力に表示されます。

## <a name="create-postgres-database-in-azure"></a>Azure で Postgres データベースを作成する

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

このセクションでは、Azure Database for PostgreSQL サーバーとデータベースを作成します。 まず、次のコマンドを使用して `db-up` 拡張機能をインストールします。

```azurecli
az extension add --name db-up
```

次の例に示すように、[`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) コマンドを使用して、Azure に Postgres データベースを作成します。 *\<postgresql-name>* は、"*一意*" の名前に置き換えてください (サーバー エンドポイントは *https://\<postgresql-name>.postgres.database.azure.com*)。 *\<admin-username>* と *\<admin-password>* には、この Postgres サーバーの管理者ユーザーを作成するための資格情報を指定します。

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

このコマンドは、次の処理を行うため、しばらく時間がかかります。

- `myResourceGroup` という[リソース グループ](../../azure-resource-manager/management/overview.md#terminology)がない場合は、作成します。 すべての Azure リソースは、そのいずれか 1 つに存在する必要があります。 `--resource-group` はオプションです。
- 管理ユーザーとして Postgres サーバーを作成します。
- `pollsdb` データベースを作成します。
- ローカル IP アドレスからのアクセスを許可します。
- Azure サービスからのアクセスを許可します。
- `pollsdb` データベースへのアクセス権を持ったデータベース ユーザーを作成します。

すべての手順は、他の `az postgres` コマンドと `psql` を使用して個別に実行することもできますが、`az postgres up` を使用すれば、そのすべての手順を 1 回で実行することができます。

コマンドが完了したら、`Ran Database Query:` で始まる出力行を探してください。 これらは、ユーザー名 `root` およびパスワード `Pollsdb1` で自動的に作成されたデータベース ユーザーを示しています。 後でデータベースにアプリを接続する際に使用することになります。

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>` は、いずれかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)に設定することができます。 ご利用のサブスクリプションから使用できるリージョンは、[`az account list-locations`](/cli/azure/account#az-account-list-locations) コマンドを使用して取得できます。 運用アプリの場合は、データベースとアプリを同じ場所に配置してください。

## <a name="deploy-the-app-service-app"></a>App Service アプリをデプロイする

このセクションでは、App Service アプリを作成します。 作成した Postgres データベースにこのアプリを接続して、コードをデプロイします。

### <a name="create-the-app-service-app"></a>App Service アプリを作成する

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

必ずリポジトリのルート (`djangoapp`) に戻ってください。アプリのデプロイは、このディレクトリから行います。

以下の例のように、[`az webapp up`](/cli/azure/webapp#az-webapp-up) コマンドを使用して App Service アプリを作成します。 *\<app-name>* は、"*一意*" の名前に置き換えてください (サーバー エンドポイントは *https://\<app-name>.azurewebsites.net*)。 *\<app-name>* に使用できる文字は `A`-`Z`、`0`-`9`、`-` です。

```azurecli
az webapp up --plan myAppServicePlan --location westus2 --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

このコマンドは、次の処理を行うため、しばらく時間がかかります。

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- [リソース グループ](../../azure-resource-manager/management/overview.md#terminology)を自動的に生成します。
- Basic 価格レベル (B1) で [App Service プラン](../overview-hosting-plans.md) *myAppServicePlan* を作成します (存在しない場合)。 `--plan` と `--sku` は省略可能ですが、
- App Service アプリが存在しない場合は作成します。
- アプリの既定のログがまだ有効になっていない場合は、有効にします。
- ビルド オートメーションを有効にし、ZIP デプロイを使用してリポジトリをアップロードします。

デプロイが完了すると、次のような JSON 出力が表示されます。

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

*\<app-resource-group>* の値をコピーしてください。 後でアプリを構成する際に必要となります。 

> [!TIP]
> 関連する設定は、リポジトリ内の *.azure* という隠しディレクトリに保存されます。 後で変更があれば簡単なコマンドを使用して再デプロイし、直ちに診断ログを有効にすることができます。
> 
> ```azurecli
> az webapp up
> ```

これでサンプル コードはデプロイされましたが、Azure 内の Postgres データベースにはまだアプリが接続されていません。 その作業を次に行います。

### <a name="configure-environment-variables"></a>環境変数を構成する

アプリをローカルで実行するときは、ターミナル セッションで環境変数を設定できます。 App Service では、"*アプリ設定*" と [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) コマンドを使用して行います。

次のコマンドを実行して、データベース接続の詳細をアプリ設定として指定します。 *\<app-name>* 、 *\<app-resource-group>* 、 *\<postgresql-name>* は、独自の値に置き換えます。 ユーザーの資格情報 `root` と `Pollsdb1` は、`az postgres up` によって自動で作成されたものであることに注意してください。

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

コードでこれらのアプリ設定にアクセスする方法については、「[環境変数へのアクセス](how-to-configure-python.md#access-environment-variables)」をご覧ください。

### <a name="run-database-migrations"></a>データベースの移行を実行する

App Service でデータベースの移行を実行するには、ブラウザーで *https://\<app-name>.scm.azurewebsites.net/webssh/host* にアクセスして SSH セッションを開きます。

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

SSH セッションで、次のコマンドを実行します。

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

デプロイしたアプリには、ブラウザーで *http:\//\<app-name>.azurewebsites.net* という URL を使用してアクセスします。 **No polls are available** (投票は使用できません) というメッセージが表示されます。 

*http:\//\<app-name>.azurewebsites.net/admin* にアクセスし、前の手順で作成した管理者ユーザーを使用してサインインします。 **[Questions]\(質問\)** の横の **[Add]\(追加\)** を選択し、いくつかの選択肢がある投票の質問を作成します。

デプロイしたアプリに *http:\//\<app-name>.azurewebsites.net/admin* という URL を使用してアクセスし、投票の質問をいくつか作成します。 それらの質問は、*http:\//\<app-name>.azurewebsites.net/* で確認できます。 

![App Services の Python Django アプリを Azure で実行する](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

デプロイしたアプリにもう一度 *http:\//\<app-name>.azurewebsites.net* という URL を使用してアクセスし、投票の質問を表示して、質問に回答します。

App Service では、各サブディレクトリ内で *wsgi.py* (`manage.py startproject` によって既定で作成されます) を探すことにより、リポジトリ内の Django プロジェクトが検出されます。 App Service でファイルが見つかると、Django Web アプリが読み込まれます。 App Service による Python アプリのロード方法の詳細については[組み込みの Python イメージの構成](how-to-configure-python.md)に関する記事を参照してください。

**お疲れさまでした。** Azure App Service for Linux で Python (Django) Web アプリが実行されています。

## <a name="develop-app-locally-and-redeploy"></a>アプリをローカルで開発して再デプロイする

このセクションでは、ローカル環境でアプリを開発し、App Service にコードを再デプロイします。

### <a name="set-up-locally-and-run"></a>ローカルでセットアップして実行する

ローカル開発環境をセットアップし、初めてサンプル アプリを実行するには、次のコマンドを実行します。

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Django Web アプリが完全に読み込まれると、次のようなメッセージが表示されます。

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

ブラウザーで *http:\//localhost:8000* にアクセスします。 **No polls are available** (投票は使用できません) というメッセージが表示されます。 

*http:\//localhost:8000/admin* に移動し、前の手順で作成した管理者ユーザーを使用してサインインします。 **[Questions]\(質問\)** の横の **[Add]\(追加\)** を選択し、いくつかの選択肢がある投票の質問を作成します。

![App Services の Python Django アプリをローカルで実行する](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

*http:\//localhost:8000* に再び移動して、投票の質問を確認し、質問に回答します。 ローカルの Django サンプル アプリケーションは、ユーザー データをローカル Sqlite3 データベースに書き込んで格納します。運用データベースが改変されることはありません。 開発環境を Azure 環境に合わせるために、Postgres データベースをローカルで使用することを検討してください。

Django サーバーを停止するには、Ctrl + C キーを押します。

### <a name="update-the-app"></a>アプリの更新

アプリの更新の動作を確認するために、`polls/models.py` に小さな変更を加えます。 次の行を見つけます。

<pre>
choice_text = models.CharField(max_length=200)
</pre>

それを次のように変更します。

```python
choice_text = models.CharField(max_length=100)
```

データ モデルを変更したので、新しい Django 移行を作成する必要があります。 それは次のコマンドで行います。

```
python manage.py makemigrations
```

移行を実行し、開発サーバーを実行して、*http:\//localhost:8000/admin* にアクセスすることによって、変更をローカルでテストできます。

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Azure にコードを再デプロイする

変更を再デプロイするには、リポジトリのルートから次のコマンドを実行します。

```azurecli
az webapp up
```

App Service がアプリの存在を検出して、単にコードをデプロイします。

### <a name="rerun-migrations-in-azure"></a>Azure で移行を再実行する

データ モデルに変更を加えたので、App Service へのデータベースの移行を再実行する必要があります。 ブラウザーで *https://\<app-name>.scm.azurewebsites.net/webssh/host* にアクセスして SSH セッションを開きます。 次のコマンドを実行します。

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>運用環境でアプリを確認する

ブラウザーで *http:\//\<app-name>.azurewebsites.net* にアクセスし、行った変更が運用環境で実行されていることを確認します。 

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

コンテナー内から生成されたコンソール ログにアクセスできます。

> [!TIP]
> `az webapp up` を実行すると、既定のログが自動的にオンになります。 パフォーマンス上の理由から、このログはしばらくすると自動的にオフになりますが、また `az webapp up` を実行すると、その都度オンに戻ります。 これを手動でオンにするには、次のコマンドを実行します。
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

ログ ストリームを確認するには、次の Azure CLI コマンドを実行します。

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

> [!NOTE]
> `https://<app-name>.scm.azurewebsites.net/api/logs/docker` で、ブラウザーからログ ファイルを検査することもできます。

任意のタイミングでログのストリーミングを停止するには、`Ctrl`+`C` と入力します。

## <a name="manage-your-app-in-the-azure-portal"></a>Azure portal でアプリを管理する

[Azure portal](https://portal.azure.com) で、作成したアプリを探して選択します。

![Azure portal で Python Django アプリに移動する](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

既定では、ポータルにはアプリの **[概要]** ページが表示されます。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、再開、削除といった基本的な管理タスクを実行することもできます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure portal の [概要] ページで Python Django アプリを管理する](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後これらのリソースが不要である場合は、次のコマンドを実行してリソース グループを削除します。

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Database for PostgreSQL データベースを作成する
> * Azure App Service にコードをデプロイして Postgres に接続する
> * コードを更新して再デプロイする
> * 診断ログを表示する
> * Azure portal で Web アプリを管理する

次のチュートリアルに進んで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](../app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [Python アプリの構成](how-to-configure-python.md)
