---
title: Linux Python アプリを構成する
description: Azure portal と Azure CLI の両方を使用して、Web アプリが実行される Python コンテナーを構成する方法について説明します。
ms.topic: quickstart
ms.date: 10/06/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 935baef209811146d0b60f4fc02986818fd103a7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743784"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Azure App Service 向けの Linux Python アプリを構成する

この記事では、[Azure App Service](overview.md) で Python アプリが実行される方法と、必要に応じて App Service の動作をカスタマイズする方法について説明します。 Python アプリは、必要なすべての [pip](https://pypi.org/project/pip/) モジュールと共にデプロイする必要があります。

App Service デプロイ エンジンでは、[Git リポジトリ](deploy-local-git.md) (または [ZIP パッケージ](deploy-zip.md)) のデプロイ時に自動的に仮想環境をアクティブ化し、`pip install -r requirements.txt` を実行します。

このガイドでは、App Service の組み込み Linux コンテナーを使用する Python 開発者のために、主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、まず [Python クイックスタート](quickstart-python.md)と [PostgreSQL を使った Python のチュートリアル](tutorial-python-postgresql-app.md)に従ってください。

構成には [Azure portal](https://portal.azure.com) と Azure CLI のいずれかを使用できます。

- **Azure portal** : 「 [Azure portal で App Service アプリを構成する](configure-common.md)」で説明されているとおり、アプリの **[設定]**  >  **[構成]** ページを使用します。

- **Azure CLI** : 2 つのオプションがあります。

    - [Azure Cloud Shell](../cloud-shell/overview.md) でコマンドを実行します。これは、コード ブロックの右上隅にある **[試してみる]** ボタンを使用して開くことができます。
    - 最新バージョンの [Azure CLI](/cli/azure/install-azure-cli) をインストールしてコマンドをローカルで実行してから、[az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。
    
> [!NOTE]
> Linux は現在、App Service で Python アプリを実行するための推奨されるオプションです。 Windows オプションについては、[Windows フレーバーの App Service での Python](/visualstudio/python/managing-python-on-azure-app-service) に関するページを参照してください。

## <a name="configure-python-version"></a>Python バージョンの構成

- **Azure portal** : Linux コンテナー向けに「 [全般設定を構成する](configure-common.md#configure-general-settings)」で説明されているとおり、 **[構成]** ページで **[全般設定]** を使用します。

- **Azure CLI** :

    -  [az webapp config show](/cli/azure/webapp/config#az_webapp_config_show) を使用して現在の Python バージョンを表示します。
    
        ```azurecli-interactive
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        `<resource-group-name>` と `<app-name>` は、Web アプリに適した名前に置き換えます。
    
    - [az webapp config set](/cli/azure/webapp/config#az_webapp_config_set) を使用して Python バージョンを設定します
        
        ```azurecli-interactive
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - [az webapp list-runtimes](/cli/azure/webapp#az_webapp_list_runtimes) を使用して、Azure App Service でサポートされている Python バージョンをすべて表示します。
    
        ```azurecli-interactive
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
サポートされない Python バージョンを実行するには、代わりに独自のコンテナー イメージを作成します。 詳細については、[カスタム Docker イメージの使用](tutorial-custom-container.md?pivots=container-linux)に関するページを参照してください。

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>ビルドの自動化のカスタマイズ

Oryx と呼ばれる App Service のビルド システムでは、Git または ZIP パッケージを使用してアプリをデプロイする際に、次のステップを実行します。

1. `PRE_BUILD_COMMAND` 設定を指定した場合は、カスタムのビルド前スクリプトを実行します。
1. `pip install -r requirements.txt` を実行します。 *requirements.txt* ファイルがプロジェクトのルート フォルダーに存在していなければなりません。 そうでないと、ビルド プロセスでエラーがレポートされます: "Could not find setup.py or requirements.txt; Not running pip install." (setup.py または requirements.txt が見つかりませんでした; pip install が実行されていません。)
1. (Django アプリを示す) リポジトリのルートに *manage.py* がある場合は、 *manage.py collectstatic* を実行します。 ただし、`DISABLE_COLLECTSTATIC` 設定が `true` の場合、この設定はスキップされます。
1. `POST_BUILD_COMMAND` 設定を指定した場合は、カスタムのビルド後スクリプトを実行します。

既定では、`PRE_BUILD_COMMAND`、`POST_BUILD_COMMAND`、`DISABLE_COLLECTSTATIC` の設定は空です。 

- Django アプリをビルドするときに collectstatic の実行を無効にするには、`DISABLE_COLLECTSTATIC` 設定を true にします。

- ビルド前コマンドを実行するには、コマンド (`echo Pre-build command` など)、または自分のプロジェクトのルートからスクリプト ファイルへの相対パス (`scripts/prebuild.sh`) が含まれるように `PRE_BUILD_COMMAND` の設定を行います。 どのコマンドでも、プロジェクトのルート フォルダーへの相対パスを使用する必要があります。

- ビルド後コマンドを実行するには、コマンド (`echo Post-build command` など)、または自分のプロジェクトのルートからスクリプト ファイルへの相対パス (`scripts/postbuild.sh`) が含まれるように `POST_BUILD_COMMAND` の設定を行います。 どのコマンドでも、プロジェクトのルート フォルダーへの相対パスを使用する必要があります。

ビルドの自動化をカスタマイズする設定の詳細については、「[Oryx 構成](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)」を参照してください。 

App Service で Linux の Python アプリを実行、ビルドする方法の詳細については、[Oryx による Python アプリの検出とビルド](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)に関するページを参照してください。

> [!NOTE]
> `PRE_BUILD_SCRIPT_PATH` および `POST_BUILD_SCRIPT_PATH` の設定は `PRE_BUILD_COMMAND` および `POST_BUILD_COMMAND` と同一で、従来の目的でサポートされています。
> 
> `SCM_DO_BUILD_DURING_DEPLOYMENT` という名前の設定に `true` または 1 が含まれている場合、デプロイ中に Oryx によってビルドが行われます。 この設定は、Git、Azure CLI コマンド `az webapp up`、Visual Studio Code を使用してデプロイする場合に true になります。

> [!NOTE]
> すべてのビルド前後のスクリプトで常に相対パスを使用してください。Oryx が動作するビルド コンテナーは、アプリが動作するランタイム コンテナーとは異なるためです。 コンテナー内のアプリ プロジェクト フォルダーの正確な配置場所 (たとえば、 *site/wwwroot* といった配置場所) は指定しないようにします。

## <a name="production-settings-for-django-apps"></a>Django アプリの運用設定

Azure App Service などの運用環境の場合、Django アプリは Django の[デプロイ チェックリスト](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) (djangoproject.com) に準拠する必要があります。

次の表では、Azure に関連する運用設定について説明しています。 これらの設定は、アプリの *setting.py* ファイルで定義されます。

| Django 設定 | Azure での手順 |
| --- | --- |
| `SECRET_KEY` | 「[環境変数としてのアプリ設定へのアクセス](#access-app-settings-as-environment-variables)」の説明のとおりに、App Service 設定の値を格納します。 また、[この値は "シークレット" として Azure Key Vault に格納](/azure/key-vault/secrets/quick-create-python)することもできます。 |
| `DEBUG` | 値を 0 (false) にして App Service で `DEBUG` 設定を作成してから、その値を環境変数として読み込みます。 実際の開発環境では、値を 1 (true) にして `DEBUG` 環境変数を作成してください。 |
| `ALLOWED_HOSTS` | 運用環境の Django では、 *settings.py* の `ALLOWED_HOSTS` 配列にアプリの URL が含まれている必要があります。 この URL は、`os.environ['WEBSITE_HOSTNAME']` というコードを使用して実行時に取得できます。 App Service によって、`WEBSITE_HOSTNAME` 環境変数がアプリの URL に自動的に設定されます。 |
| `DATABASES` | データベースに接続するための App Service の設定を定義し、それらを環境変数として読み込んで [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) ディクショナリを設定します。 または、値 (特にユーザー名とパスワード) を [Azure Key Vault シークレット](/azure/key-vault/secrets/quick-create-python)として格納することもできます。 |

## <a name="container-characteristics"></a>コンテナーの特性

Python アプリは App Service にデプロイされると、[App Service Python GitHub リポジトリ](https://github.com/Azure-App-Service/python)で定義された Linux Docker コンテナー内で動作します。 イメージの構成は、バージョン固有のディレクトリ内で見つけることができます。

このコンテナーには次の特性があります。

- アプリは、[Gunicorn WSGI HTTP サーバー](https://gunicorn.org/)を使用して実行されます。このとき、追加の引数 `--bind=0.0.0.0 --timeout 600` が使用されます。
    - [Gunicorn の構成の概要](https://docs.gunicorn.org/en/stable/configure.html#configuration-file)に関するページ (docs.gunicorn.org) で説明されているとおり、プロジェクトのルートにある *gunicorn.conf.py* ファイルを使用して Gunicorn の構成設定を指定できます。 また、[スタートアップ コマンドをカスタマイズ](#customize-startup-command)することもできます。

    - 「[Gunicorn のデプロイ](https://docs.gunicorn.org/en/latest/deploy.html)」(docs.gunicorn.org) で説明されているとおり、偶発的または意図的な DDoS 攻撃から Web アプリを防御するために、Gunicorn は Nginx リバース プロキシの背後で実行されます。

- 既定では、基本のコンテナー イメージには Flask Web フレームワークのみが含まれています。ただし、コンテナーは、WSGI に準拠していて Python 3.6 以上と互換性のある他のフレームワーク (Django など) をサポートしています。

- Django など、追加のパッケージをインストールするには、直接的な依存関係を指定したプロジェクトのルートに [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) ファイルを作成します。 そうすることで、App Service によって、それらの依存関係がプロジェクトのデプロイ時に自動的にインストールされます。

    依存関係がインストールされるには、 *requirements.txt* ファイルがプロジェクトのルートに " *なければなりません* "。 そうでないと、ビルド プロセスでエラーがレポートされます: "Could not find setup.py or requirements.txt; Not running pip install." (setup.py または requirements.txt が見つかりませんでした; pip install が実行されていません。) このエラーが発生した場合は、自分の requirements ファイルの場所を確認してください。

- App Service では、Web アプリの URL (`msdocs-hello-world.azurewebsites.net` など) を使用して、`WEBSITE_HOSTNAME` という名前の環境変数が自動的に定義されます。 また、アプリの名前 (`msdocs-hello-world` など) を使用して `WEBSITE_SITE_NAME` も定義されます。 
   
## <a name="container-startup-process"></a>コンテナーのスタートアップ プロセス

App Service on Linux コンテナーでは、起動中に次の手順が実行されます。

1. [カスタム スタートアップ コマンド](#customize-startup-command)が提供されている場合は、これを使用します。
2. [Django アプリ](#django-app)の存在を確認し、検出された場合はそれための Gunicorn を起動します。
3. [Flask アプリ](#flask-app)の存在を確認し、検出された場合はそれための Gunicorn を起動します。
4. 他のアプリが見つからない場合は、コンテナーに組み込まれている既定のアプリを起動します。

次のセクションでは、各オプションについてさらに詳しく説明します。

### <a name="django-app"></a>Django アプリ

Django アプリの場合、App Service によってお客様のアプリ コード内で `wsgi.py` という名前のファイルが検索され、次のコマンドを使用して Gunicorn が実行されます。

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

スタートアップ コマンドをより細かくコントロールしたい場合、 [カスタム スタートアップ コマンド](#customize-startup-command)を使用し、`<module>` を *wsgi.py* が含まれているフォルダーの名前に置き換えます。そのモジュールがプロジェクトのルートにない場合は `--chdir` 引数を追加してください。 たとえば、自分のプロジェクトのルートを基準として *knboard/backend/config* に *wsgi.py* が配置されている場合は、引数 `--chdir knboard/backend config.wsgi` を使用します。

運用ログを有効にするには、[カスタム スタートアップ コマンド](#customize-startup-command)の例で示しているとおり、`--access-logfile` および `--error-logfile` パラメーターを追加します。

### <a name="flask-app"></a>Flask アプリ

Flask の場合、App Service によって *application.py* または *app.py* という名前のファイルが検索され、Gunicorn が次のように起動されます。

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

お客様のメイン アプリ モジュールが別のファイルに含まれている場合は、アプリ オブジェクトに別の名前を使用します。また、Gunicorn に追加の引数を指定したい場合は、[カスタム スタートアップ コマンド](#customize-startup-command)を使用します。

### <a name="default-behavior"></a>既定の動作

カスタム コマンド、Django アプリ、または Flask アプリが App Service によって検出されない場合は、 _opt/defaultsite_ フォルダーにある既定の読み取り専用アプリが実行されます。 既定のアプリは次のように表示されます。

![App Service on Linux の既定の Web ページ](media/configure-language-python/default-python-app.png)

## <a name="customize-startup-command"></a>スタートアップ コマンドのカスタマイズ

この記事で前に述べたように、 [Gunicorn の構成の概要](https://docs.gunicorn.org/en/stable/configure.html#configuration-file)に関するページで説明されているとおり、プロジェクトのルートにある *gunicorn.conf.py* ファイルを使用して Gunicorn の構成設定を指定できます。

このような構成では十分でない場合は、スタートアップ コマンド ファイルでカスタム スタートアップ コマンドまたは複数のコマンドを指定して、コンテナーのスタートアップ動作をコントロールできます。 スタートアップ コマンド ファイルでは、 *startup.sh* 、 *startup.cmd* 、 *startup.txt* など、任意の名前を使用することができます。

どのコマンドでも、プロジェクトのルート フォルダーへの相対パスを使用する必要があります。

スタートアップ コマンドまたはコマンド ファイルを指定するには:

- **Azure portal** : アプリの **[構成]** ページを選択してから、 **[全般設定]** を選択します。 **[スタートアップ コマンド]** フィールドで、自分のスタートアップ コマンドの完全なテキスト、または自分のスタートアップ コマンド ファイルの名前を入力します。 次に、 **[保存]** を選択して変更を適用します。 Linux コンテナー用に「[全般設定を構成する](configure-common.md#configure-general-settings)」を参照してください。

- **Azure CLI** : `--startup-file` パラメーターをスタートアップ コマンドまたはファイルに設定して、 [az webapp config set](/cli/azure/webapp/config#az_webapp_config_set) コマンドを使用します。

    ```azurecli-interactive
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    `<custom-command>` は、自分のスタートアップ コマンドの完全なテキスト、または自分のスタートアップ コマンド ファイルの名前に置き換えます。
        
App Service では、カスタム スタートアップ コマンドまたはファイルの処理中に発生したエラーが無視されて、Django および Flask アプリが検索されることでスタートアップ プロセスが続行されます。 想定どおりの動作にならない場合は、自分のスタートアップ コマンドまたはファイルに問題がないこと、スタートアップ コマンド ファイルがアプリのコードと共に App Service にデプロイされていることを確認してください。 [診断ログ](#access-diagnostic-logs)で詳細な情報を確認することもできます。 また、 [Azure portal](https://portal.azure.com) でアプリの **[問題の診断と解決]** ページも確認してください。

### <a name="example-startup-commands"></a>スタートアップ コマンドの例

- **Gunicorn 引数の追加** : 次の例では、Django アプリを起動するための Gunicorn コマンド ラインに `--workers=4` を追加します。 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    詳細については、「[Running Gunicorn (Gunicorn の実行)」](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org) を参照してください。

- **Django での運用ログの有効化** : `--access-logfile '-'` および `--error-logfile '-'` 引数をコマンド ラインに追加します。

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    これらのログは [App Service ログ ストリーム](#access-diagnostic-logs)に表示されます。

    詳細については、[Gunicorn のログ](https://docs.gunicorn.org/en/stable/settings.html#logging)に関するページ (docs.gunicorn.org) を参照してください。
    
- **カスタムの Flask メイン モジュール** : 既定では、Flask アプリのメイン モジュールは *application.py* か *app.py* であると App Service によって想定されています。 メイン モジュールに別の名前を使用する場合は、スタートアップ コマンドをカスタマイズする必要があります。 たとえば、メイン モジュールが *hello.py* で、そのファイルにおける Flask アプリ オブジェクトの名前が `myapp` である Flask アプリがある場合、コマンドは次のようになります。

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    メイン モジュールがサブフォルダー (`website` など) に存在する場合、そのフォルダーを `--chdir` 引数で指定します。
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Gunicorn 以外のサーバーの使用** : 別の Web サーバー ( [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html) など) を使用する場合は、スタートアップ コマンドとして、またはスタートアップ コマンド ファイルで、適切なコマンドを使用します。

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>環境変数としてのアプリ設定へのアクセス

「[アプリケーションの設定の構成](configure-common.md#configure-app-settings)」で説明されているとおり、アプリの設定は、お客様のアプリのためにクラウドに格納された値です。 これらの設定は、環境変数として自分のアプリのコードで利用できます。アクセスには標準の [os.environ](https://docs.python.org/3/library/os.html#os.environ) パターンを使用します。

たとえば、`DATABASE_SERVER` というアプリ設定を作成したら、その設定の値は次のコードで取得します。

```python
db_server = os.environ['DATABASE_SERVER']
```
    
## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されているかどうかをアプリ ロジックが確認する必要がある場合は、`X-Forwarded-Proto` ヘッダーを調べます。

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

一般的な Web フレームワークでは、標準のアプリ パターンで `X-Forwarded-*` 情報にアクセスできます。 [CodeIgniter](https://codeigniter.com/) では、[is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) は既定で `X_FORWARDED_PROTO` の値をチェックします。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Azure portal を使用してログにアクセスするには、アプリの左側のメニューにある **[監視]**  >  **[ログ ストリーム]** を選択します。

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>トラブルシューティング

- **自分のアプリ コードをデプロイした後に既定のアプリが表示される。** 既定のアプリは、アプリ コードが App Service にデプロイされていない場合、またはアプリ コードが App Service によって検出されず、代わりに既定のアプリが実行された場合に表示されます。

    - App Service を再起動し、15 から 20 秒待って、アプリをもう一度確認します。
    
    - Windows ベースのインスタンスではなく、App Service for Linux が使用されていることを確認してください。 Azure CLI から `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind` コマンドを実行します。`<resource-group-name>` と `<app-service-name>` は適宜置き換えてください。 出力として `app,linux` が表示されるはずです。それ以外の場合は、App Service を再作成し、Linux を選択してください。
    
    - SSH または Kudu コンソールを使用して App Service に直接接続し、お客様のファイルが *site/wwwroot* に存在することを確認します。 ファイルが存在しない場合は、デプロイ プロセスを見直してアプリを再デプロイします。
    
    - ファイルが存在する場合は、お客様固有のスタートアップ ファイルを App Service が識別できていません。 [Django](#django-app) または [Flask](#flask-app) に関して App Service で想定されているとおりにお客様のアプリが構造化されていることをチェックします。または、[カスタム スタートアップ コマンド](#customize-startup-command)を使用します。

- **ブラウザーに "サービスは利用できません" というメッセージが表示される。** ブラウザーは App Service からの応答を待ってタイムアウトしました。これは、App Service によって Gunicorn サーバーが起動されたもののアプリ コードを指定する引数が正しくないことを示しています。

    - ブラウザーを最新の情報に更新します (特に、お客様が App Service プランの最も低い価格レベルを使用している場合)。 たとえば、無料のレベルを使用しているときは、アプリの起動にかかる時間が長くなることがあります。その場合、ブラウザーを最新の情報に更新すると、応答が速くなります。

    - [Django](#django-app) または [Flask](#flask-app) に関して App Service で想定されているとおりにお客様のアプリが構造化されていることをチェックします。または、[カスタム スタートアップ コマンド](#customize-startup-command)を使用します。

    - [ログ ストリーム](#access-diagnostic-logs)にエラー メッセージがないか調べます。

- **ログ ストリームに "Could not find setup.py or requirements.txt; Not running pip install." (setup.py または requirements.txt が見つかりませんでした; pip install が実行されていません。) と表示される。** Oryx のビルド プロセスで、 *requirements.txt* ファイルの検出が失敗しました。

    - SSH または Kudu コンソールを使用して App Service に直接接続し、 *requirements.txt* が *site/wwwroot* の直下に存在することを確認します。 存在しない場合は、ファイルが自分のリポジトリに存在していて、自分のデプロイに含まれている場所を作ります。 別のフォルダーに存在する場合は、それをルートに移動させてください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:PostgreSQL を使った Python アプリ](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [チュートリアル:プライベート コンテナー リポジトリからデプロイする](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](faq-app-service-linux.md)
