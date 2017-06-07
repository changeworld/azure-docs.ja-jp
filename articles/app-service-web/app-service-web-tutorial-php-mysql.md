---
title: "Azure で PHP と MySQL Web アプリを構築する | Microsoft Docs"
description: "Azure で動作し、MySQL データベースに接続する PHP アプリの入手方法を説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3ad716fab4f5084c38c83f4bc90a616856666b38
ms.contentlocale: ja-jp
ms.lasthandoff: 06/01/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Azure で PHP と MySQL Web アプリを構築する
このチュートリアルでは、Azure で PHP Web アプリを作成し、MySQL データベースに接続する方法について説明します。 このチュートリアルを終了すると、[Azure App Service Web Apps](app-service-web-overview.md) で実行される [Laravel](https://laravel.com/) アプリケーションが完成します。

![Azure App Service で実行される PHP アプリ](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * PHP アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure ポータルでアプリを管理する

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、前提条件となる以下をローカルにインストールします。

1. [Git をダウンロードし、インストールします](https://git-scm.com/)
1. [PHP 5.6.4 をダウンロードし、インストールします](http://php.net/downloads.php)
1. [Composer をダウンロードし、インストールします](https://getcomposer.org/doc/00-intro.md)
1. Laravel で必要な PHP 拡張機能 (OpenSSL、PDO-MySQL、Mbstring、Tokenizer、XML) を有効にする
1. [MySQL をダウンロード、インストール、および起動します](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Azure CLI 2.0 をダウンロードし、インストールします](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>ローカル MySQL を準備する

この手順では、このチュートリアルで使用するデータベースをローカル MySQL サーバーに作成します。

### <a name="connect-to-mysql-server"></a>MySQL サーバーに接続する
ターミナル ウィンドウで、ローカル MySQL サーバーに接続します。

```bash
mysql -u root -p
```

パスワードの入力を求められたら、`root` アカウントのパスワードを入力します。 ルート アカウントのパスワードを思い出せない場合は、「[MySQL: root のパスワードをリセットする方法](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)」を参照してください。

コマンドが正常に実行されれば、MySQL サーバーは既に実行されています。 正常に実行されない場合は、[MySQL のインストール後の手順](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)に従って、MySQL サーバーが起動されたことを確認してください。

### <a name="create-a-database"></a>データベースを作成する

`mysql` プロンプトで、データベースを作成します。

```sql
CREATE DATABASE sampledb;
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

<a name="step2"></a>

## <a name="create-local-php-app"></a>ローカル PHP アプリを作成する
この手順では、Laravel サンプル アプリケーションを取得し、データベース接続を構成してローカルで実行します。 

### <a name="clone-the-sample"></a>サンプルを複製する

ターミナル ウィンドウを開き、`cd` コマンドで作業ディレクトリに移動します。  

次のコマンドを実行して、サンプル リポジトリを複製します。 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

複製したディレクトリに `cd` し、必要なパッケージをインストールします。

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL 接続を構成する

レポジトリのルートに _.env_ ファイルを作成し、その中に次の変数をコピーします。 _&lt;root_password >_ プレース ホルダーを、ルート ユーザーのパスワードに置き換えます。

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

> [!NOTE]
> この _.env_ ファイルを Laravel でどのように使用するかの詳細については、[Laravel 環境の構成](https://laravel.com/docs/5.4/configuration#environment-configuration)に関するページを参照してください。
>
>

### <a name="run-the-sample"></a>サンプルを実行する

[Laravel データベースの移行](https://laravel.com/docs/5.4/migrations)を実行して、アプリケーションで必要なテーブルを作成する必要があります。 移行で作成されるテーブルを確認するには、Git レポジトリの _database/migrations_ ディレクトリを調べます。

```bash
php artisan migrate
```

新しい Laravel アプリケーション キーを生成します。

```bash
php artisan key:generate 
```

アプリケーションを実行します。

```bash
php artisan serve
```

ブラウザーで `http://localhost:8000` にアクセスします。 ページで、いくつかのタスクを追加します。

![MySQL に正常に接続されている PHP](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

任意のタイミングで PHP を停止するには、ターミナルで `Ctrl`+`C` キーを押します。 

## <a name="create-production-mysql-in-azure"></a>Azure に 運用 MySQL を作成する

この手順では、MySQL データベースを[Azure Database for MySQL (Preview)](/azure/mysql) に作成します。 その後、このデータベースに接続するように PHP アプリケーションを構成します。

### <a name="log-in-to-azure"></a>Azure にログインする

ターミナル ウィンドウで Azure CLI 2.0 を使用して、Azure App Service で PHP アプリケーションをホストするために必要なリソースを作成します。 [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。 

```azurecli-interactive 
az login 
``` 

### <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドを使用して、[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 Azure リソース グループとは、Web アプリ、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例は、北ヨーロッパ リージョンにリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```

`--location` に使用できる値を確認するには、[az appservice list-locations](/cli/azure/appservice#list-locations) コマンドを使用します。

### <a name="create-a-mysql-server"></a>MySQL サーバーを作成する

[az mysql server create](/cli/azure/mysql/server#create) コマンドを使用して、Azure Database for MySQL (Preview) にサーバーを作成します。

次のコマンドの _&lt;mysql_server_name>_ プレースホルダーを独自の一意の MySQL サーバー名に置き換えます。 この名前は、MySQL サーバーのホスト名 (`<mysql_server_name>.database.windows.net`) の一部であるため、グローバルに一意である必要があります。 同様に、_&lt;admin_user>_ と _&lt;admin_password>_ を独自の値に置き換えます。

```azurecli-interactive
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --user <admin_user> \
    --password <admin_password>
```

MySQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "<admin_user>",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>サーバーのファイアウォールを構成する

[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) コマンドを使用して、MySQL サーバーのクライアントへの接続を許可するファイアウォール ルールを作成します。 

```azurecli-interactive
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> 現時点では、Azure Database for MySQL (Preview) は、Azure Services からの接続のみを有効にすることはできません。 Azure の IP アドレスは動的に割り当てられるため、当面はすべての IP アドレスを有効にしておくことをお勧めします。 サービスはプレビュー段階であるため、データベースを保護するためのより優れた方法はまもなく利用可能になる予定です。
>
>

### <a name="connect-to-production-mysql-server"></a>運用 MySQL サーバーに接続する

ターミナル ウィンドウで、Azure の MySQL サーバーに接続します。 _&lt;admin_user>_ と _&lt;mysql_server_name>_ に指定した値を使用します。

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>運用データベースを作成する

`mysql` プロンプトで、データベースを作成します。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>アクセス許可を持つユーザーを作成する

データベース ユーザーを作成し、`sampledb` データベースのすべての特権を付与します。 プレースホルダーの _&lt;phpapp_user>_ と _&lt;phpapp_password>_ を独自の一意のアプリ名に置き換えます。

```sql
CREATE USER '<phpapp_user>' IDENTIFIED BY '<phpapp_password>'; 
GRANT ALL PRIVILEGES ON sampledb.* TO '<phpapp_user>';
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

## <a name="connect-app-to-production-mysql"></a>アプリを運用 MySQL サーバーに接続する

この手順では、PHP アプリケーションを Azure Database for MySQL (Preview) に作成したばかりの MySQL データベースに接続します。 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>接続を構成する 

レポジトリのルートに _.env.production_ ファイルを作成し、その中に次の変数をコピーします。 プレースホルダーの _&lt;mysql_server_name>__&lt;phpapp_user>_ と _&lt;phpapp_password>_ を置き換えます。

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=<phpapp_user>@<mysql_server_name>
DB_PASSWORD=<phpapp_password>
```

変更を保存します。

### <a name="test-the-application"></a>アプリケーションをテストする

環境ファイルとして _.env.production_ を使用して Laravel データベースの移行を実行して、Azure Database for MySQL (Preview) の MySQL データベース内にテーブルを作成します。

```bash
php artisan migrate --env=production --force
```

この時点では、_.env.production_ には有効なアプリケーション キーはありません。 ターミナルで、新しいものを生成します。 

```bash
php artisan key:generate --env=production --force
```

環境ファイルとして _.env.production_ を使用してサンプル アプリケーションを実行します。

```bash
php artisan serve --env=production
```

ブラウザーで `http://localhost:8000` にアクセスします。 エラーの発生なしでページが読み込まれれば、PHP アプリケーションは Azure の MySQL データベースに接続しています。 

ページにいくつかのタスクを追加します。

![PHP が Azure Database for MySQL (Preview) に正常にデータベースに接続されている](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

任意のタイミングで PHP を停止するには、ターミナルで `Ctrl`+`C` キーを押します。 

### <a name="secure-sensitive-data"></a>機微なデータをセキュリティで保護する

_.env.production_ の機微なデータが Git にコミットされないことを確認する必要があります。

これを行うには、レポジトリのルートから_.gitignore_ を開き、新しい行にファイル名を追加します。

```
.env.production
```

変更を保存した後、変更を Git にコミットします。

```bash
git add .gitignore
git commit -m "keep sensitive data out of git"
```

後で App Service の環境変数を構成して Azure Database for MySQL (Preview) のデータベースに接続する方法を学習するため、App Service には `.env` ファイルは必要ありません。 

## <a name="deploy-php-app-to-azure"></a>PHP アプリを Azure にデプロイする
この手順では、MySQL に接続される MySQL アプリケーションを Azure App Service にデプロイします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[az appservice plan create](/cli/azure/appservice/plan#create) コマンドで、App Service プランを作成します。 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

次の例では、**Free** 価格レベルを使用して、_myAppServicePlan_ という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-a-web-app"></a>Web アプリを作成する

App Service プランを作成したので、_myAppServicePlan_ App Service プラン内に Web アプリを作成します。 Web アプリにより、コードをデプロイするためのホスト領域が取得され、デプロイされたアプリケーションを表示するための URL が提供されます。 Web アプリを作成するには、[az appservice web create](/cli/azure/appservice/web#create) コマンドを使用します。 

次のコマンドの _&lt;appname>_ プレースホルダーを独自の一意のアプリ名に置き換えます。 この一意の名前は、Web アプリの既定のドメイン名の一部として使用されます。そのため、この名前は Azure のすべてのアプリで一意である必要があります。 後で、Web アプリをユーザーに公開する前に、任意のカスタム DNS エントリを Web アプリにマップできます。 

```azurecli-interactive
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
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

### <a name="set-the-php-version"></a>PHP のバージョンを設定する

[az appservice web config update](/cli/azure/appservice/web/config#update) コマンドを使用して、アプリケーションで必要な PHP のバージョンを設定します。

次のコマンドは、PHP のバージョンを _7.0_ に設定します。

```azurecli-interactive
az appservice web config update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>データベース設定を構成する

既に指摘したように、App Service の環境変数を使用して、Azure MySQL データベースに接続できます。

App Service では、[az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) コマンドを使用して、環境変数を_アプリ設定_として設定します。 

次のコマンドを使用して、アプリの `DB_HOST`、`DB_DATABASE`、`DB_USERNAME`、および`DB_PASSWORD`の各設定を構成できます。 プレースホルダーの _&lt;appname>_、_&lt;mysql_server_name>_、_&lt;phpapp_user>_、および _&lt;phpapp_password>_ を置き換えます。

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="<phpapp_user>@<mysql_server_name>" DB_PASSWORD="<phpapp_password>"
```

PHP [getenv()](http://www.php.net/manual/function.getenv.php) メソッドを使用して、これらの設定にアクセスできます。 Laravel コードでは、PHP `getenv()` に対して [env()](https://laravel.com/docs/5.4/helpers#method-env) ラッパーが使用されます。 たとえば、_config/database.php_の MySQL 構成は次のようになります。

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel の環境変数を構成する

ローカル コンピューターと同じように、Laravel には App Service のアプリケーション キーが必要です。 それもアプリ設定を使用して構成できます。

`php artisan` を使用して新しいアプリケーションキーを生成します (_.env_ には保存されません)。

```bash
php artisan key:generate --show
```

[az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) コマンドを使用して、App Service Web アプリにアプリケーション キーを設定します。 プレースホルダーの _&lt;appname>_ と _&lt;outputofphpartisankey:generate>_ を置き換えます。

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

> [!NOTE]
> `APP_DEBUG="true"` は、デプロイした Web アプリでエラーが発生した場合にデバッグ情報を返すように Laravel に指示します。 実稼働アプリケーションを実行するときは、`false` を設定してセキュリティを強化する必要があります。
>
>

### <a name="set-the-virtual-application-path"></a>仮想アプリケーション パスを設定する

Web アプリの仮想アプリケーション パスを設定します。 この手順が必要なのは、[Laravel アプリケーション のライフサイクル](https://laravel.com/docs/5.4/lifecycle)がアプリケーションのルート ディレクトリではなく_パブリック_ ディレクトリから始まる場合のみです。 ライフ サイクルがルート ディレクトリから始まる PHP フレームワークは、仮想アプリケーション パスの手動での構成なしで動作できます。

[az resource update](/cli/azure/resource#update) コマンドを使用して、仮想アプリケーション パスを設定します。 _&lt;appname>_ プレースホルダーを置き換えます。

```bash
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

> [!NOTE]
> 既定では、Azure App Service は、デプロイされたアプリケーション ファイルのルート ディレクトリ (_sites\wwwroot_) に対して仮想アプリケーションのルート パス (_/_) をポイントします。 
>
>

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

FTP とローカル Git の場合、デプロイを認証するには、サーバー上で構成されたデプロイ ユーザーが必要です。 

> [!NOTE] 
> FTP と、Web アプリへのローカル Git のデプロイには、デプロイ ユーザーが必要です。 ユーザー名とパスワードはアカウント レベルです。そのため、Azure サブスクリプションの資格情報とは異なります。

デプロイ ユーザー名とパスワードを作成している場合は、次のコマンドを使用してユーザー名を表示できます。

```azurecli-interactive
az appservice web deployment user show
```

デプロイ ユーザーを構成していない場合は、[az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) コマンドを実行してデプロイ資格情報を作成します。 

```azurecli-interactive
az appservice web deployment user set \
    --user-name <username> \
    --password <minimum-8-char-capital-lowercase-number>
```

ユーザー名は一意であり、パスワードは強力なパスワードにする必要があります。 `'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 `'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。

このデプロイ ユーザーの作成は 1 回だけ実行する必要があります。すべての Azure デプロイでこのユーザーを使用できます。

後でアプリケーションをデプロイするときに使用するため、ユーザー名とパスワードを記録しておきます。

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイを構成する 

アプリケーションを Azure App Service にデプロイするには、FTP、ローカル Git、GitHub、Visual Studio Team Services、BitBucket など、さまざまな方法があります。 

Azure Web アプリへのローカル Git アクセスを構成するには、[az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) コマンドを使用します。 

```azurecli-interactive
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
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

Azure Remote にプッシュして、PHP アプリケーションをデプロイします。 デプロイ ユーザーの作成時に指定したパスワードを入力するように求めるメッセージが表示されます。 

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

> [!NOTE]
> デプロイ プロセスの最後に [Composer](https://getcomposer.org/) パッケージがインストールされることに気付くかもしれません。 App Service では既定のデプロイ中にこれらの自動化が実行されないため、このサンプル レポジトリには、有効化するための3 つのファイルがルート ディレクトリに追加されます。 
>
> - `.deployment` - このファイルは、`bash deploy.sh` をカスタム デプロイ スクリプトとして実行するよう App Service に指示します。
> - `deploy.sh` - カスタム デプロイ スクリプト。 このファイルを確認すると、`npm install` の後で `php composer.phar install` が実行されることがわかります。 
> - `composer.phar` - Composer パッケージ マネージャー。
>
> この方法を使用して、App Service に対する Git ベースのデプロイに対して任意の手順を追加できます。 詳細については、「[Custom Deployment Script (カスタム デプロイ スクリプト)](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)」を参照してください。
>
>

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリを参照する

`http://<app_name>.azurewebsites.net` を参照し、一覧にいくつかのタスクを追加します。 

![Azure App Service で実行される PHP アプリ](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

**お疲れさまでした。** データ主導型の PHP アプリが Azure App Service で実行されています。

## <a name="update-data-model-and-redeploy"></a>データ モデルを更新し、再デプロイする

この手順では、`task` データ モデルに変更をいくつか加え、変更内容を Azure に発行します。

このタスク シナリオでは、タスクを完了としてマークできるようにアプリケーションを変更します。 

### <a name="add-a-column"></a>列を追加する

ターミナルで、Git レポジトリのルートにいることを確認した後、`tasks` テーブル用の新しいデータベースの移行を生成します。

```bash
php artisan make:migration add_complete_column --table=tasks
```

このコマンドは、生成される移行ファイルの名前を表示します。 このファイルを _database/migrations_ で探し、テキスト エディターで開きます。

up() メソッドを次のコードに置き換えます。

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

このコードは、`tasks` テーブルに `complete` と呼ばれるブール値の列を追加します。

down() メソッドを、ロールバック アクション用の次のコードに置き換えます。

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

ターミナルで、Laravel データベースの移行をローカルに実行して、ローカル データベースを変更します。

```bash
php artisan migrate
```

[Laravel の命名規則](https://laravel.com/docs/5.4/eloquent#defining-models)に基づいて、モデル `Task` (_app/Task.php_ 参照) を `tasks` テーブルに既定でマップして、データ モデルの更新を終了します。

### <a name="update-application-logic"></a>アプリケーション ロジックを更新する

_routes/web.php_ を開きます。 サンプル アプリケーションは、そのルートとビジネス ロジックをここに定義します。

ファイルの末尾に、次のコードを使用してルートを追加します。

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

このコードは、データ モデルに対して `complete` 値の切り替えによる単純な更新を行います。

### <a name="update-the-view"></a>ビューを更新する

_resources/views/tasks.blade.php_を開きます。 `<tr>` 開始タグを探し、次のコードに置き換えます。

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

これは、タスクが完了しているかどうかに応じて行の色を変更します。

次の行には、次のコードがあります。

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

この行全体を次のコードに置き換えます。

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

このコードは、前に定義したルートを参照する送信ボタンを追加します。

### <a name="test-your-changes-locally"></a>ローカルで変更をテストする

Git レポジトリのルート ディレクトリから、開発サーバーを再実行します。

```bash
php artisan serve
```

ブラウザーで `http://localhost:8000` に移動します。チェック ボックスをクリックし、それに応じてタスクの状態が変わることを確認します。

![タスクに追加されたチェック ボックス](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

ターミナルで、運用環境の接続文字列を使用して Laravel データベースの移行を実行して、Azure の運用データベースを変更します。

```bash
php artisan migrate --env=production --force
```

すべての変更を Git にコミットした後、コードの変更を Azure にプッシュします。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push` が完了したら、Azure Web アプリに移動し、新機能をもう一度試します。

![Azure に発行されたモデルとデータベースの変更](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

> [!NOTE]
> 追加したタスクがある場合は、それらも引き続き表示されます。 データ スキーマに対する更新では、既存のデータはそのまま残ります。
>
>

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする 

Azure App Service で PHP アプリケーションを実行している間、コンソール ログをターミナルに直接パイプできます。 このようにすると、アプリケーション エラーのデバッグに役立つ同じ診断メッセージを取得できます。

ログのストリーミングを開始するには、[az appservice web log tail](/cli/azure/appservice/web/log#tail) コマンドを使用します。

```azurecli-interactive 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

ログのストリーミングが開始されたら、ブラウザーで Azure Web アプリを最新の情報に更新して、Web トラフィックを取得します。 ターミナルにパイプされたコンソール ログが表示されます。

任意のタイミングでログのストリーミングを停止するには、`Ctrl` + `C` キーを押します。 

> [!TIP]
> PHP アプリケーションは、標準の [error_log()](http://php.net/manual/function.error-log.php) を使用してコンソールに出力できます。 サンプル アプリケーションでは、_app/Http/routes.php_ でこの方法を使用しています。
>
> Web フレームワークとして、[Laravel では、ログ記録プロバイダーとして Monolog を使用](https://laravel.com/docs/5.4/errors)しています。 Monolog でメッセージをコンソールに出力する方法については、「[PHP: How to use monolog to log to console (php://out) (PHP: monolog を使用してコンソールにログを出力する方法 (php://out)) ](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out)」を参照してください。
>
>

## <a name="manage-your-azure-web-app"></a>Azure Web アプリを管理する

Azure Portal に移動し、作成した Web アプリを表示します。

そのためには、[https://portal.azure.com](https://portal.azure.com) にサインインします。

左側のメニューで **[App Service]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Web アプリの "_ブレード_" (水平方向に開かれるポータル ページ) が表示されます。

既定では、Web アプリのブレードは **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ブレードの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の App Service ブレード](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

ブレードのこれらのタブは、Web アプリに追加することができるさまざまな優れた機能を示しています。 次の一覧では、ほんの一部の例を示しています。

* カスタム DNS 名をマップする
* カスタム SSL 証明書をバインドする
* 継続的なデプロイを構成する
* スケールアップとスケールアウトを行う
* ユーザー認証を追加する

## <a name="clean-up-resources"></a>リソースをクリーンアップする
 
これらのリソースが別のチュートリアルで不要である場合 (「[次のステップ](#next)」を参照)、次のコマンドを実行して削除することができます。 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * PHP アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure ポータルでアプリを管理する

次のチュートリアルに進み、カスタム DNS 名をマップする方法を学習してください。

> [!div class="nextstepaction"] 
> [既存のカスタム DNS 名を Azure Web Apps にマップする](app-service-web-tutorial-custom-domain.md)

