---
title: "Azure App Service on Linux での Ruby および MySQL の Web アプリの構築 | Microsoft Docs"
description: "Azure で動作して Azure の MySQL データベースに接続する Ruby アプリを作成する方法を説明します。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 951e66e47cf8fbe9d2cdf1606a8d63054bcada13
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Azure App Service on Linux での Ruby および MySQL の Web アプリの構築

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、Ruby Web アプリを作成し、MySQL データベースに接続する方法について説明します。 このチュートリアルを終了すると、App Service on Linux で実行される [Ruby on Rails](http://rubyonrails.org/) アプリが完成します。

![Azure App Service で動作している Ruby on Rails アプリ](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * Ruby on Rails アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Git をインストールする](https://git-scm.com/)
* [Ruby 2.3 をインストールする](https://www.ruby-lang.org/documentation/installation/)
* [Ruby on Rails 5.1 をインストールする](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [MySQL をインストールして起動する](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>ローカル MySQL を準備する

この手順では、このチュートリアルで使用するデータベースをローカル MySQL サーバーに作成します。

### <a name="connect-to-local-mysql-server"></a>ローカル MySQL サーバーに接続する

ターミナル ウィンドウで、ローカル MySQL サーバーに接続します。 このチュートリアルでは、ターミナル ウィンドウを使ってすべてのコマンドを実行します。

```bash
mysql -u root -p
```

パスワードの入力を求められたら、`root` アカウントのパスワードを入力します。 ルート アカウントのパスワードを思い出せない場合は、「[MySQL: root のパスワードをリセットする方法](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)」を参照してください。

コマンドが正常に実行されれば、MySQL サーバーは実行されています。 正常に実行されない場合は、[MySQL のインストール後の手順](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)に従って、MySQL サーバーが起動されたことを確認してください。

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Ruby on Rails アプリをローカルに作成する
このステップでは、Ruby on Rails サンプル アプリケーションを作成し、データベース接続を構成して、ローカルで実行します。 

### <a name="clone-the-sample"></a>サンプルを複製する

ターミナル ウィンドウから、`cd` コマンドで作業ディレクトリに移動します。

次のコマンドを実行して、サンプル レポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` コマンドで複製したディレクトリに移動します。 必要なパッケージをインストールします。

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>MySQL 接続を構成する

リポジトリで _config/database.yml_ を開き、ローカルな MySQL ルート ユーザーの名前とパスワードを指定します (13 行目)。 [Homebrew](https://brew.sh/) などのツールを使って MySQL をインストールした場合、ファイル内の資格情報は既に既定値に設定されています (`root` と空のパスワード)。

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>ローカルでサンプルを実行する

[Rails の移行](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations)を実行して、アプリケーションで必要なテーブルを作成します。 移行で作成されるテーブルを確認するには、Git レポジトリの _db/migrate_ ディレクトリを調べます。

```bash
rake db:create
rake db:migrate
```

アプリケーションを実行します。

```bash
rails server
```

ブラウザーで `http://localhost:3000` にアクセスします。 ページにいくつかのタスクを追加します。

![MySQL に正常に接続された Ruby on Rails](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Rails サーバーを停止するには、ターミナルで「`Ctrl + C`」と入力します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Azure に MySQL を作成する

この手順では、MySQL データベースを[Azure Database for MySQL (Preview)](/azure/mysql) に作成します。 後で、このデータベースに接続するように Ruby on Rails アプリケーションを構成します。

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL サーバーを作成する

[`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) コマンドを使用して、Azure Database for MySQL (プレビュー) でサーバーを作成します。

次のコマンドで、_&lt;mysql_server_name>_ プレースホルダーを MySQL サーバー名に置き換えます (有効な文字は `a-z`、`0-9`、および `-` です)。 この名前は、MySQL サーバーのホスト名 (`<mysql_server_name>.mysql.database.azure.com`) の一部であるため、グローバルに一意である必要があります。

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

MySQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>サーバーのファイアウォールを構成する

[`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) コマンドを使用して、MySQL サーバーでクライアント接続を許可するためのファイアウォール規則を作成します。

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> 現在のところ、Azure Database for MySQL (Preview) では、接続を Azure Services のみに制限することはできません。 Azure の IP アドレスは動的に割り当てられるため、すべての IP アドレスを有効にしておくことをお勧めします。 このサービスはプレビューの段階です。 データベースを保護するためのより優れた方法が提供される予定です。
>

### <a name="connect-to-production-mysql-server-locally"></a>ローカルに運用 MySQL サーバーに接続する

ターミナル ウィンドウで、Azure の MySQL サーバーに接続します。 _&lt;mysql_server_name>_ に指定した値を使用します。

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

パスワードの入力を求められたら、データベース サーバーの作成時に指定した _My5up3r$tr0ngPa$w0rd!_ を使います。

### <a name="create-a-production-database"></a>運用データベースを作成する

`mysql` プロンプトで、データベースを作成します。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>アクセス許可を持つユーザーを作成する

_railsappuser_ というデータベース ユーザーを作成し、このユーザーに `sampledb` データベースのすべての特権を付与します。

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>アプリを Azure MySQL に接続する

この手順では、Azure Database for MySQL (プレビュー) に作成した MySQL データベースに Ruby on Rails アプリケーションを接続します。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>データベース接続を構成する

リポジトリで _config/database.yml_ を開きます。 ファイルの下部にある運用環境変数を次のコードに置き換えます。 _&lt;mysql_server_name>_ プレースホルダーには、作成した MySQL サーバーの名前を使います。

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

変更を保存します。

> [!NOTE]
> `sslca` が追加され、サンプル リポジトリ内の既存の _.pem_ ファイルを指し示します。 既定では、Azure Database for MySQL はクライアントからの SSL 接続を強制します。 この `.pem` 証明書を使って、Azure Database for MySQL への SSL 接続を行います。 詳細については、「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](../../mysql/howto-configure-ssl.md)」を参照してください。
>

### <a name="test-the-application-locally"></a>ローカルでアプリケーションをテストする

ローカル ターミナルで、次の環境変数を設定します。

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

構成した運用環境の値で Rails データベース移行を実行して、Azure Database for MySQL (プレビュー) 内の MySQL データベースにテーブルを作成します。 

```bash
rake db:migrate RAILS_ENV=production
```

Rails アプリケーションを運用環境で実行するときは、プリコンパイル済みのアセットが必要です。 次のコマンドで必要なアセットを生成します。

```bash
rake assets:precompile
```

Rails の運用環境では、セキュリティを管理するためにシークレットも使われます。 秘密鍵を生成します。

```bash
rails secret
```

Rails の運用環境で使われる対応する変数に、秘密鍵を保存します。 便宜上、両方の変数に同じ鍵を使います。

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Rails 運用環境で JavaScript ファイルと CSS ファイルを提供できるようにします。

```bash
export RAILS_SERVE_STATIC_FILES=true
```

運用環境でサンプル アプリケーションを実行します。

```bash
rails server -e production
```

`http://localhost:3000` に移動します。 エラーなしでページが読み込まれれば、Ruby on Rails アプリケーションは Azure の MySQL データベースに接続しています。

ページにいくつかのタスクを追加します。

![Azure Database for MySQL (プレビュー) に正常に接続された Ruby on Rails](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Rails サーバーを停止するには、ターミナルで「`Ctrl + C`」と入力します。

### <a name="commit-your-changes"></a>変更をコミットする

次の Git コマンドを実行して、変更をコミットします。

```bash
git add .
git commit -m "database.yml updates"
```

アプリをデプロイする準備ができました。

## <a name="deploy-to-azure"></a>[Deploy to Azure (Azure へのデプロイ)]

このステップでは、MySQL に接続された Rails アプリケーションを Azure App Service にデプロイします。

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する

Cloud Shell で [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) コマンドを使用して、`myAppServicePlan` App Service プランに Web アプリを作成します。 

次の例では、`<app_name>` をグローバルに一意のアプリ名に置き換えてください (有効な文字は `a-z`、`0-9`、`-`)。 ランタイムは `RUBY|2.3` に設定されており、[既定の Ruby イメージ](https://hub.docker.com/r/appsvc/ruby/)がデプロイされます。 サポートされているすべてのランタイムを確認するには、[`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes) を実行します。 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "RUBY|2.3" --deployment-local-git
```

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Git デプロイが有効な、空の新しい Web アプリが作成されました。

> [!NOTE]
> Git リモートの URL は `deploymentLocalGitUrl` プロパティに `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git` 形式で出力されます。 この URL は後で必要になるので保存しておいてください。
>

### <a name="configure-database-settings"></a>データベース設定を構成する

App Service では、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) コマンドを使用して、環境変数を "_アプリ設定_" として設定します。

次の Cloud Shell コマンドでは、アプリ設定 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME`、および `DB_PASSWORD` を構成します。 プレースホルダーの _&lt;appname>_ と _&lt;mysql_server_name>_ を置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Rails 環境変数を構成する

ローカル ターミナルで、Azure の Rails 運用環境用の新しい秘密鍵を生成します。

```bash
rails secret
```

Rails 運用環境で必要な変数を構成します。

次の Cloud Shell では、2 つの _&lt;output_of_rails_secret>_ プレースホルダーをローカル ターミナルで生成した新しい秘密鍵に置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` は、Git の各デプロイでアセットをプリコンパイルするように既定の Ruby コンテナーに指示します。

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

ローカル ターミナルで、ローカル Git リポジトリに Azure リモートを追加します。

```bash
git remote add azure <paste_copied_url_here>
```

Azure リモートにプッシュして、Ruby on Rails アプリケーションをデプロイします。 デプロイ ユーザーの作成時に指定したパスワードを入力するように求めるメッセージが表示されます。

```bash
git push azure master
```

デプロイ中、Azure App Service は進行状況について Git と通信します。

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリの参照

`http://<app_name>.azurewebsites.net` を参照し、一覧にいくつかのタスクを追加します。

![Azure App Service で動作している Ruby on Rails アプリ](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

データ主導型の Ruby on Rails アプリが Azure App Service で実行されるようになりました。

## <a name="update-model-locally-and-redeploy"></a>ローカルにモデルを更新し、再デプロイする

この手順では、`task` データ モデルと Web アプリに単純な変更を加え、変更内容を Azure に発行します。

このタスク シナリオでは、タスクを完了としてマークできるようにアプリケーションを変更します。

### <a name="add-a-column"></a>列を追加する

ターミナルで、Git リポジトリのルートに移動します。

`Done` というブール型の列を `Tasks` テーブルに追加する新しい移行を生成します。

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

このコマンドは、_db/migrate_ ディレクトリに新しい移行ファイルを生成します。


ターミナルで、Rails データベースの移行を実行して、ローカル データベースを変更します。

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>アプリケーション ロジックを更新する

*app/controllers/tasks_controller.rb* ファイルを開きます。 ファイルの末尾にある次の行を探します。

```rb
params.require(:task).permit(:Description)
```

この行を変更して、新しい `Done` パラメーターを追加します。

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>ビューを更新する

編集フォームである *app/views/tasks/_form.html.erb* ファイルを開きます。

`<%=f.error_span(:Description) %>` という行を探し、その直後に次のコードを挿入します。

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

単一レコードのビュー ページである *app/views/tasks/show.html.erb* ファイルを開きます。 

`<dd><%= @task.Description %></dd>` という行を探し、その直後に次のコードを挿入します。

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

すべてのレコードのインデックス ページである *app/views/tasks/index.html.erb* ファイルを開きます。

`<th><%= model_class.human_attribute_name(:Description) %></th>` という行を探し、その直後に次のコードを挿入します。

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

同じファイルで、`<td><%= task.Description %></td>` という行を探し、その直後に次のコードを挿入します。

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>変更をローカルでテストする

ローカル ターミナルで、Rails サーバーを実行します。

```bash
rails server
```

タスクの状態の変化を確認するには、`http://localhost:3000` に移動して、項目を追加または編集します。

![タスクに追加されたチェック ボックス](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Rails サーバーを停止するには、ターミナルで「`Ctrl + C`」と入力します。

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

ターミナルで、運用環境に対して Rails データベース移行を実行し、Azure データベースで変更を行います。

```bash
rake db:migrate RAILS_ENV=production
```

すべての変更を Git にコミットした後、コードの変更を Azure にプッシュします。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push` が完了したら、Azure Web アプリに移動し、新機能を試します。

![Azure に発行されたモデルとデータベースの変更](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

タスクを追加した場合は、そのタスクがデータベースに保持されます。 データ スキーマに対する更新では、既存のデータはそのまま残ります。

## <a name="manage-the-azure-web-app"></a>Azure Web アプリを管理する

[Azure Portal](https://portal.azure.com) に移動し、作成した Web アプリを管理します。

左側のメニューで **[App Services]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/tutorial-php-mysql-app/access-portal.png)

Web アプリの [概要] ページを確認します。 ここでは、停止、開始、再開、参照、削除のような基本的な管理タスクを行うことができます。

左側のメニューは、アプリを構成するためのページを示しています。

![Azure Portal の [App Service] ページ](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * Ruby on Rails アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure Web Apps にマップする](../app-service-web-tutorial-custom-domain.md)
