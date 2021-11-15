---
title: 'チュートリアル: Service Connector を使用して、Azure App Service 上に Postgres を使用した Django アプリをビルドする'
description: PostgreSQL データベースを使用する Python Web アプリを作成し、Azure にデプロイします。 このチュートリアルでは Django フレームワークを使用し、アプリは Azure App Service on Linux でホストされ、App Service とデータベースは Service Connector に接続されています。
ms.devlang: python
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
zone_pivot_groups: postgres-server-options
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2a3307cfea56d18cbbe0186cd3c927fafdd4074
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853035"
---
# <a name="tutorial-using-service-connector-preview-to-build-a-django-app-with-postgres-on-azure-app-service"></a>チュートリアル: Service Connector (プレビュー) を使用して、Azure App Service 上に Postgres を使用した Django アプリをビルドする

> [!NOTE]
> このチュートリアルでは、Web アプリをデータベース サービスに簡単に接続できる Service Connector (プレビュー) を使用しています。 このチュートリアルは、このプレビュー機能を使用するために、[App Service チュートリアル](../app-service/tutorial-python-postgresql-app.md)を変更したものであるため、類似点があります。 このチュートリアルのセクション「[4.2 データベースに接続するための環境変数を構成する](#42-configure-environment-variables-to-connect-the-database)」を参照して、どのような場合にサービス コネクタを使用するのかと、App Service チュートリアルで説明されている接続プロセスがどのように簡略化されるかを確認します。

::: zone pivot="postgres-single-server"

このチュートリアルでは、データ ドリブンの Python [Django](https://www.djangoproject.com/) Web アプリを [Azure App Service](overview.md) にデプロイし、それを Azure Database for Postgres データベースに接続する方法について説明します。 上記のオプションを選択して、PostgresSQL フレキシブル サーバー (プレビュー) を試すこともできます。 フレキシブル サーバーには、よりシンプルなデプロイ メカニズムが用意されており、継続的なコスト削減が可能になります。

このチュートリアルでは、Azure CLI を使用して以下のタスクを実了します。

> [!div class="checklist"]
> * Python と Azure CLI を使用して初期環境を設定する
> * Azure Database for PostgreSQL データベースを作成する
> * Azure App Service にコードをデプロイして PostgreSQL に接続する
> * コードを更新して再デプロイする
> * 診断ログを表示する
> * Azure portal で Web アプリを管理する

:::zone-end

::: zone pivot="postgres-flexible-server"

このチュートリアルでは、データ ドリブンの Python [Django](https://www.djangoproject.com/) Web アプリを [Azure App Service](overview.md) にデプロイし、それを [Azure Database for PostgreSQL フレキシブル サーバー (プレビュー)](../postgresql/flexible-server/index.yml) データベースに接続する方法について説明します。 PostgreSQL フレキシブル サーバー (プレビュー) を使用できない場合は、上記の [単一サーバー] オプションを選択します。 

このチュートリアルでは、Azure CLI を使用して以下のタスクを実了します。

> [!div class="checklist"]
> * Python と Azure CLI を使用して初期環境を設定する
> * Azure Database for PostgreSQL フレキシブル サーバー データベースを作成する
> * Azure App Service にコードをデプロイして PostgreSQL フレキシブル サーバーに接続する
> * コードを更新して再デプロイする
> * 診断ログを表示する
> * Azure portal で Web アプリを管理する


:::zone-end

## <a name="1-set-up-your-initial-environment"></a>1.初期環境を設定する

1. アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
1. <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 以降</a>をインストールします。
1. <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.30.0 以降をインストールします。これを任意のシェルで使用してコマンドを実行し、Azure リソースのプロビジョニングと構成を行います。

ターミナル ウィンドウを開き、Python のバージョンが 3.6 以降であることを確認します。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Azure CLI のバージョンが 2.30.0 以降であることを確認します。

```azurecli
az --version
```

アップグレードする必要がある場合は、`az upgrade` コマンド (バージョン 2.30.0 以降が必要) を試すか、「<a href="/cli/azure/install-azure-cli" target="_blank"> Azure CLI のインストール</a>」を参照してください。

次に CLI から Azure にサインインします。

```azurecli
az login
```

このコマンドを実行すると、お客様の資格情報を収集するためにブラウザーが開かれます。 コマンドが終了すると、ご利用のサブスクリプションに関する情報を含んだ JSON 出力が表示されます。

サインイン後は、Azure CLI を使用して Azure コマンドを実行して、サブスクリプション内のリソースを操作することができます。

問題がある場合は、 [お知らせください](https://aka.ms/DjangoCLITutorialHelp)。

## <a name="2-clone-or-download-the-sample-app"></a>2.サンプル アプリをクローンまたはダウンロードする

# <a name="git-clone"></a>[git clone](#tab/clone)

サンプル リポジトリをクローンします。

```terminal
git clone https://github.com/Azure-Samples/serviceconnector-webapp-postgresql-django.git
```

次に、そのフォルダーに移動します。

```terminal
cd serviceconnector-webapp-postgresql-django
```

::: zone pivot="postgres-flexible-server"

フレキシブル サーバー (プレビュー) の場合は、サンプルのフレキシブル サーバー ブランチを使用します。このブランチでは、データベース サーバーの URL の設定方法や、Azure PostgreSQL フレキシブル サーバーの必要に応じて Django データベース構成に `'OPTIONS': {'sslmode': 'require'}` を追加することなど、いくつかの変更が必要となります。

```terminal
git checkout flexible-server
```

::: zone-end

# <a name="download"></a>[ダウンロード](#tab/download)

[https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) にアクセスします。

::: zone pivot="postgres-flexible-server"
フレキシブル サーバー (プレビュー) の場合は、"master" というブランチ コントロールを選択し、代わりにフレキシブル サーバー ブランチを選択します。
::: zone-end

**[クローン]** を選択し、 **[ZIP のダウンロード]** を選択します。 

その ZIP ファイルを、*djangoapp* という名前のフォルダーに展開します。 

次に、その *djangoapp* フォルダー内でターミナル ウィンドウを開きます。

---

djangoapp サンプルには、データ ドリブンの Django 投票アプリが含まれます。これを、Django ドキュメントの「[はじめての Django アプリ作成](https://docs.djangoproject.com/en/3.1/intro/tutorial01/)」に従って取得します。 参考までに、完成したアプリをここに記載しています。

このサンプルは、App Service のような運用環境で実行するために変更もされています。

- 運用環境の設定は、*azuresite/production.py* ファイルにあります。 開発環境の設定は、*azuresite/settings.py* にあります。
- `WEBSITE_HOSTNAME` 環境変数を設定すると、アプリで運用環境の設定が使用されます。 この変数は、Azure App Service によって Web アプリの URL (`msdocs-django.azurewebsites.net` など) に自動的に設定され ます。

運用環境の設定は、任意の運用環境で実行するために Django を構成する場合に固有であり、App Service に固有なものではありません。 詳細については、[Django デプロイ チェックリスト](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/)に関するページを参照してください。 一部の変更点の詳細については、[Azure 上での Django の運用設定](../app-service/configure-language-python.md#production-settings-for-django-apps)に関するセクションを参照してください。

問題がある場合は、 [お知らせください](https://aka.ms/DjangoCLITutorialHelp)。

## <a name="3-create-postgres-database-in-azure"></a>3.Azure で Postgres データベースを作成する

::: zone pivot="postgres-single-server"
<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

すべてのコマンドでパラメーターを指定する必要がないように、Azure CLI でのパラメーターのキャッシュを有効にします。 (キャッシュされた値は *.azure* フォルダーに保存されます。)

```azurecli
az config param-persist on 
```

Azure CLI 用 `db-up` 拡張機能をインストールします。

```azurecli
az extension add --name db-up
```

`az` コマンドが認識されない場合は、「[初期環境を設定する](#1-set-up-your-initial-environment)」の説明に従って Azure CLI がインストールされていることを確認してください。

次に、[`az postgres up`](/cli/azure/postgres#az_postgres_up) コマンドを使用して Azure に Postgres データベースを作成します。

```azurecli
az postgres up --resource-group ServiceConnector-tutorial-rg --location eastus --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

-  *\<postgres-server-name>* を **Azure 全体で一意** である名前に置き換えます (サーバー エンドポイントは `https://<postgres-server-name>.postgres.database.azure.com` になります)。 会社名と別の一意の値を組み合わせて使用すると、適切なパターンになります。
- *\<admin-username>* と *\<admin-password>* には、この Postgres サーバーの管理者ユーザーを作成するための資格情報を指定します。 管理者のユーザー名に *azure_superuser*、*azure_pg_admin*、*admin*、*administrator*、*root*、*guest*、または *public* は使用できません。 *pg_* で始めることはできません。 パスワードには、次のうちの 3 つのカテゴリの、**8 から 128 文字** が含まれている必要があります: 英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、#、% など)。 パスワードにユーザー名を含めることはできません。
- ユーザー名とパスワードに `$` 文字は使用しないでください。 後で、これらの値を使用して環境変数を作成しますが、Python アプリの実行に使用する Linux コンテナー内では、環境変数内の `$` 文字に特殊な意味があります。
- ここで使用している B_Gen5_1 (Basic、Gen5、1 コア) の[価格レベル](../postgresql/concepts-pricing-tiers.md)は、コストが最も低いものです。 運用データベースの場合は、`--sku-name` 引数を省略して、代わりに GP_Gen5_2 (General Purpose、Gen 5、2 コア) レベルを使用します。

このコマンドによって次の操作が実行されます。これには数分かかる場合があります。

- `ServiceConnector-tutorial-rg` という[リソース グループ](../azure-resource-manager/management/overview.md#terminology)がまだない場合は、作成します。
- `--server-name` 引数によって指定された Postgres サーバーを作成します。
- `--admin-user` および `--admin-password` 引数を使用して、管理者アカウントを作成します。 これらの引数を省略すると、コマンドによって一意の資格情報が自動的に生成されるようになります。
- `--database-name` 引数で指定されたとおりに `pollsdb` データベースを作成します。
- ローカル IP アドレスからのアクセスを有効にします。
- Azure サービスからのアクセスを有効にします。
- `pollsdb` データベースへのアクセス権を持ったデータベース ユーザーを作成します。

すべての手順は、他の `az postgres` および `psql` コマンドを使用して個別に実行することもできますが、`az postgres up` を使用すれば、そのすべての手順をまとめて実行できます。

コマンドが完了すると、サーバーの URL、生成されたユーザー名 ("joyfulKoala@msdocs-djangodb-12345" など)、GUID パスワードと共に、データベースのさまざまな接続文字列を含む JSON オブジェクトが出力されます。 このチュートリアルの後半で必要になるため、**ユーザー名とパスワードを一時的なテキスト ファイルにコピー** します。

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>` は、いずれかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)に設定することができます。 ご利用のサブスクリプションから使用できるリージョンは、[`az account list-locations`](/cli/azure/account#az_account_list_locations) コマンドを使用して取得できます。 運用アプリの場合は、データベースとアプリを同じ場所に配置してください。

::: zone-end

::: zone pivot="postgres-flexible-server"

1. すべてのコマンドでパラメーターを指定する必要がないように、Azure CLI でのパラメーターのキャッシュを有効にします。 (キャッシュされた値は *.azure* フォルダーに保存されます。)

    ```azurecli
    az config param-persist on 
    ```

1. [リソース グループ](../azure-resource-manager/management/overview.md#terminology)を作成します (必要に応じて名前を変更できます)。 リソース グループ名はキャッシュされ、後続のコマンドに自動的に適用されます。

    ```azurecli
    az group create --name ServiceConnector-tutorial-rg --location eastus
    ```

1. データベース サーバーを作成します (この処理には数分かかります)。

    ```azurecli
    az postgres flexible-server create --sku-name Standard_B1ms --public-access all
    ```
    
    `az` コマンドが認識されない場合は、「[初期環境を設定する](#1-set-up-your-initial-environment)」の説明に従って Azure CLI がインストールされていることを確認してください。
    
    [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) コマンドでは、次のアクションが実行されます (数分かかります)。
    
    - キャッシュされた名前がまだ存在していない場合は、既定のリソース グループを作成します。
    - PostgreSQL フレキシブル サーバーを作成します:
        - 既定では、このコマンドでは `server383813186` のような生成された名前が使用されます。 `--name` パラメーターを使用して、独自の名前を指定できます。 Azure 全体で重複しない、一意の名前にしてください。
        - このコマンドでは、最も低コストの価格レベル `Standard_B1ms` が使用されます。 既定の `Standard_D2s_v3` レベルを使用するには、`--sku-name` 引数を省略します。
        - このコマンドでは、前の `az group create` コマンドからキャッシュされたリソース グループと場所が使用されます。この例では、`eastus` リージョン内のリソース グループ `ServiceConnector-tutorial-rg` です。
    - ユーザー名とパスワードを使用して管理者アカウントを作成します。 これらの値は、`--admin-user` および `--admin-password` パラメーターを使用して直接指定できます。
    - 既定の `flexibleserverdb` という名前のデータベースを作成します。 `--database-name` パラメーターを使用すると、データベース名を指定できます。
    - 完全なパブリック アクセスを有効にして、`--public-access` パラメーターを使用して制御できます。
    
1. コマンドが完了したら、**コマンドの JSON 出力をファイルにコピー** します。このチュートリアルで後ほど、出力された値 (特にホスト、ユーザー名、パスワード) とデータベース名が必要となります。

::: zone-end

問題がある場合は、 [お知らせください](https://aka.ms/DjangoCLITutorialHelp)。

## <a name="4-deploy-the-code-to-azure-app-service"></a>4.Azure App Service にコードをデプロイする

このセクションでは App Service アプリでアプリ ホストを作成し、このアプリを Postgres データベースに接続して、そのホストにコードをデプロイします。

### <a name="41-create-the-app-service-app"></a>4.1 App Service アプリを作成する

::: zone pivot="postgres-single-server"

ターミナルで、現在の場所がアプリ コードを含む *djangoapp* リポジトリ フォルダーであることを確認します。

[`az webapp up`](/cli/azure/webapp#az_webapp_up) コマンドを使用して App Service アプリ (ホスト プロセス) を作成します。

```azurecli
az webapp up --resource-group ServiceConnector-tutorial-rg --location eastus --plan ServiceConnector-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- 引数 `--location` には、[Service Connector でサポートされている](concept-region-support.md)場所を使用してください。
-  *\<app-name>* を Azure 全体で一意である名前に置き換えます (サーバー エンドポイントは `https://<app-name>.azurewebsites.net`)。 *\<app-name>* に使用できる文字は `A`-`Z`、`0`-`9`、`-` です。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。

このコマンドによって次の操作が実行されます。これには数分かかる場合があります。

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- [リソース グループ](../azure-resource-manager/management/overview.md#terminology)がまだ存在していない場合は作成します (このコマンドでは、先ほどデータベースを作成したのと同じリソース グループを使用します)。
- Basic 価格レベル (B1) で [App Service プラン](../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* を作成します (存在しない場合)。 `--plan` と `--sku` は省略可能ですが、
- App Service アプリが存在しない場合は作成します。
- アプリの既定のログがまだ有効になっていない場合は、有効にします。
- ビルド オートメーションを有効にし、ZIP デプロイを使用してリポジトリをアップロードします。
- リソース グループの名前や App Service プランなどの一般的なパラメーターをファイル *.azure/config* にキャッシュします。このため、後のコマンドで同じパラメーターをすべて指定する必要はありません。 たとえば、変更を加えた後でアプリを再デプロイするには、パラメーターを指定せずに `az webapp up` を再実行するだけです。 ただし、CLI 拡張機能に含まれるコマンド (`az postgres up` など) では、現時点でキャッシュが使用されません。そのため、ここでは、リソース グループと場所を `az webapp up` の初回使用時に指定する必要がありました。

::: zone-end

::: zone pivot="postgres-flexible-server"

1. ターミナルで、現在の場所がアプリ コードを含む *djangoapp* リポジトリ フォルダーであることを確認します。

1. サンプル アプリの `flexible-server` ブランチに切り替えます。 このブランチには、PostgreSQL フレキシブル サーバーに必要な特定の構成が含まれています。

    ```cmd
    git checkout flexible-server
    ```

1. 次の [`az webapp up`](/cli/azure/webapp#az_webapp_up) コマンドを実行して、アプリの App Service ホストを作成します。

    ```azurecli
    az webapp up --name <app-name> --sku B1 
    ```
    <!-- without --sku creates PremiumV2 plan -->
        
    このコマンドでは、前の `az group create` コマンドからキャッシュされたリソース グループと場所を使用して (この例では `Python-Django-PGFlex-rg` リージョン内のグループ `eastus`)、次のアクションが実行されます。これには数分かかる場合があります。
    
    <!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
    <!-- No it doesn't. az webapp up doesn't respect --resource-group -->
    - Basic 価格レベル (B1) で [App Service プラン](../app-service/overview-hosting-plans.md)を作成します。 `--sku` を省略すると、既定値を使用できます。
    - App Service アプリを作成します。
    - アプリの既定のログを有効にします。
    - ビルド オートメーションを有効にし、ZIP デプロイを使用してリポジトリをアップロードします。

::: zone-end

デプロイが成功すると、コマンドによって次の例のような JSON 出力が生成されます。

![az webapp up コマンド出力の例](../app-service/media/tutorial-python-postgresql-app/az-webapp-up-output.png)

問題がある場合は、 まず、[トラブルシューティング ガイド](../app-service/configure-language-python.md#troubleshooting)を参照し、それでも解決しない場合は[お知らせください](https://aka.ms/DjangoCLITutorialHelp)。

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2 データベースに接続するための環境変数を構成する

コードを App Service にデプロイしたので、次の手順として、アプリを Azure の Postgres データベースに接続します。

アプリ コードでは、`AZURE_POSTGRESQL_HOST`、`AZURE_POSTGRESQL_NAME`、`AZURE_POSTGRESQL_USER`、および `AZURE_POSTGRESQL_PASS` という 4 つの環境変数でデータベース情報を検索することを想定しています。

App Service で環境変数を設定するには、次の [az connection create]() コマンドを使用して "アプリ設定" を作成します。

::: zone pivot="postgres-single-server"

```azurecli
az webapp connection create postgres --client-type django
```

リソース グループ、アプリ名、db 名は、キャッシュされた値から取得されます。 このコマンドの実行中に、postgres データベースの管理者パスワードを指定する必要があります。

- このコマンドは、アプリ コードで想定されている "AZURE_POSTGRESQL_HOST"、"AZURE_POSTGRESQL_NAME"、"AZURE_POSTGRESQL_USER"、"AZURE_POSTGRESQL_PASS" という名前の設定を作成します。
- 管理者の資格情報を忘れた場合は、コマンドを使用してリセットします。


::: zone-end

::: zone pivot="postgres-flexible-server"
```azurecli
az webapp connection create postgres --client-type django
```

リソース グループ、アプリ名、db 名は、キャッシュされた値から取得されます。 このコマンドの実行中に、postgres データベースの管理者パスワードを指定する必要があります。

- このコマンドは、アプリ コードで想定されている "AZURE_POSTGRESQL_HOST"、"AZURE_POSTGRESQL_NAME"、"AZURE_POSTGRESQL_USER"、"AZURE_POSTGRESQL_PASS" という名前の設定を作成します。
- 管理者の資格情報を忘れた場合は、コマンドを使用してリセットします。

::: zone-end

> [!NOTE]
> "The subscription is not registered to use Microsoft.ServiceLinker (サブスクリプションが Microsoft.ServiceLinker を使用するように登録されていません)" というエラー メッセージが表示される場合は、`az provider register -n Microsoft.ServiceLinker` を実行して Service Connector リソース プロバイダーを登録し、接続コマンドを再度実行してください。 

Python コードでは、`os.environ.get('AZURE_POSTGRESQL_HOST')` のようなステートメントを使用して、環境変数としてこれらの設定にアクセスします。 詳細については、「[環境変数へのアクセス](../app-service/configure-language-python.md#access-environment-variables)」を参照してください。

問題がある場合は、 まず、[トラブルシューティング ガイド](../app-service/configure-language-python.md#troubleshooting)を参照し、それでも解決しない場合は[お知らせください](https://aka.ms/DjangoCLITutorialHelp)。

### <a name="43-run-django-database-migrations"></a>4.3 Django データベースの移行を実行する

Django データベースの移行によって、Azure データベース上の PostgreSQL のスキーマが、コードに記述されているスキーマと一致していることが確認されます。

1. `az webpp ssh` を実行して、ブラウザーで Web アプリの SSH セッションを開きます。

    ```azurecli
    az webapp ssh
    ```


1. SSH セッションで次のコマンドを実行します (**Ctrl**+**Shift**+**V** キーを使用してコマンドを貼り付けることができます)。

    ```bash
    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    データベースへの接続に関するエラーが発生した場合は、前のセクションで作成したアプリケーション設定の値を確認してください。

1. `createsuperuser` コマンドを使用すると、スーパーユーザーの資格情報の入力を求められます。 このチュートリアルの目的では、既定のユーザー名である `root` を使用し、**Enter** キーを押してメール アドレスを空白のままにして、パスワードを「`Pollsdb1`」と入力します。

1. データベースがロックされているというエラーが表示された場合は、前のセクションで `az webapp settings` コマンドを実行したことを確認してください。 それらの設定を行わないと、migrate コマンドがデータベースと通信できずにエラーが発生します。

問題がある場合は、 まず、[トラブルシューティング ガイド](../app-service/configure-language-python.md#troubleshooting)を参照し、それでも解決しない場合は[お知らせください](https://aka.ms/DjangoCLITutorialHelp)。
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4 アプリで投票の質問を作成する

1. アプリの Web サイトを開きます。 データベース内に特定の投票がまだないため、アプリには "Polls app (投票アプリ)" および "No polls are available (投票は利用できません)" というメッセージが表示されます。

    ```azurecli
    az webapp browse
    ```

    "アプリケーション エラー" が表示される場合は、前の手順「[データベースに接続するための環境変数を構成する](#42-configure-environment-variables-to-connect-the-database)」で必須の設定を作成していないか、またはそれらの値にエラーが含まれていることが考えられます。 コマンド `az webapp config appsettings list` を実行して、設定を確認します。

    設定を更新してエラーを修正したら、アプリが再起動するまで少し待ってから、ブラウザーを最新の状態に更新します。

1. URL に `/admin` を追加して、Web アプリの管理ページを参照します (例: `http://<app-name>.azurewebsites.net/admin`)。 前のセクションで用いた Django のスーパーユーザー資格情報 (`root` と `Pollsdb1`) を使用してサインインします。 **[Polls]\(投票\)** で、 **[Questions]\(質問\)** の横の **[Add]\(追加\)** を選択し、いくつかの選択肢がある投票の質問を作成します。

1. メインの Web サイト (`http://<app-name>.azurewebsites.net`) に戻り、質問がユーザーに表示されるようになったことを確認します。 質問に自由に回答してデータベースにデータを生成します。

**お疲れさまでした。** アクティブな Postgres データベースを使用して、Azure App Service for Linux で Python Django Web アプリが実行されています。

> [!NOTE]
> App Service では、各サブフォルダー内で *wsgi.py* ファイル (`manage.py startproject` によって既定で作成されます) を探すことにより、Django プロジェクトが検出されます。 App Service でそのファイルが見つかると、Django Web アプリが読み込まれます。 詳細については、[組み込みの Python イメージの構成](../app-service/configure-language-python.md)に関するページを参照してください。


## <a name="5-clean-up-resources"></a>5.リソースをクリーンアップする

アプリを残しておく場合、またはその他のチュートリアルに進む場合は、「[次のステップ](#next-steps)」に進んでください。 それ以外の場合は、継続して料金が発生しないように、このチュートリアルで作成したリソース グループを削除できます。

```azurecli
az group delete --name ServiceConnector-tutorial-rg --no-wait
```

リソース グループを削除することによって、その中に含まれるすべてのリソースの割り当て解除と削除も行われます。 このコマンドを使用する前に、グループ内のリソースが不要になったことを確認してください。

すべてのリソースが削除されるまでには多少時間がかかります。 `--no-wait` 引数を指定すると、コマンドからすぐに制御が戻されます。

問題がある場合は、 [お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
