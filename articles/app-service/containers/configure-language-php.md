---
title: PHP アプリの構成
description: アプリ用に事前構築済みの PHP コンテナーを構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9933205095587d9e8e0d8a5641d213f159512450
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234953"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Azure App Service 向けの Linux PHP アプリを構成する

このガイドでは、ビルトインの PHP ランタイムを Azure App Service の Web アプリ、モバイル バックエンド、および API アプリで構成する方法を示します。

このガイドでは、App Service のビルトイン Linux コンテナーを使用する PHP 開発者のために、主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、最初に [PHP クイック スタート](quickstart-php.md)と [PHP と MySQL のチュートリアル](tutorial-php-mysql-app.md)に従ってください。

## <a name="show-php-version"></a>PHP バージョンを表示する

現在の PHP バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

サポートされているすべての PHP バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>PHP バージョンを設定する

PHP バージョンを 7.2 に設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>ビルドの自動化のカスタマイズ

ビルドの自動化を有効にして Git または zip パッケージを使用してアプリをデプロイする場合、App Service のビルドの自動化によって、次の手順が実行されます。

1. `PRE_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。
1. `php composer.phar install` を実行します。
1. `POST_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。

`PRE_BUILD_COMMAND` および `POST_BUILD_COMMAND` は、既定では空の環境変数です。 ビルド前のコマンドを実行するには、`PRE_BUILD_COMMAND` を定義します。 ビルド後のコマンドを実行するには、`POST_BUILD_COMMAND` を定義します。

次の例では、一連のコマンドに対して 2 つの変数をコンマ区切りで指定しています。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

ビルドの自動化をカスタマイズするためのその他の環境変数については、「[Oryx の構成](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)」を参照してください。

Linux 上で App Service によって PHP アプリが実行されビルドされる方法に関する詳細については、[Oryx ドキュメントの PHP アプリが検出されビルドされる方法](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)に関するページを参照してください。

## <a name="customize-start-up"></a>スタートアップのカスタマイズ

既定では、組み込みの PHP コンテナーによって Apache サーバーが実行されます。 起動時に `apache2ctl -D FOREGROUND"` を実行します。 必要に応じて、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行することにより、起動時に別のコマンドを実行できます。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)できます。 その後、標準の [getenv()](https://secure.php.net/manual/function.getenv.php) パターンを使用して、それらにアクセスできます。 たとえば、`DB_HOST` というアプリ設定にアクセスするには、次のコードを使用します。

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>サイト ルートを変更する

選択した Web フレームワークでは、サイト ルートとしてサブディレクトリを使用できます。 たとえば、[Laravel](https://laravel.com/) は、サイト ルートとして `public/` サブディレクトリを使用します。

App Service の既定の PHP イメージでは Apache が使用されていて、アプリ用にサイト ルートをカスタマイズすることはできません。 この制限を回避するには、次の内容で *.htaccess* ファイルをリポジトリ ルートに追加します。

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

*.htaccess* の書き換えを行わない場合は、代わりに [カスタム Docker イメージ](quickstart-docker-go.md)を使用して Laravel アプリケーションをデプロイできます。

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されているかどうかをアプリ ロジックが確認する必要がある場合は、`X-Forwarded-Proto` ヘッダーを調べます。

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

一般的な Web フレームワークでは、標準のアプリ パターンで `X-Forwarded-*` 情報にアクセスできます。 [CodeIgniter](https://codeigniter.com/) では、[is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) は既定で `X_FORWARDED_PROTO` の値をチェックします。

## <a name="customize-phpini-settings"></a>php.ini 設定をカスタマイズする

PHP のインストールを変更する必要がある場合は、以下の手順に従って、いずれかの [php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を変更できます。

> [!NOTE]
> PHP のバージョンと現在の *php.ini* 構成を確認する最善の方法は、アプリで [phpinfo()](https://www.php.net/manual/function.phpinfo.php) を呼び出すことです。
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a> 非 PHP_INI_SYSTEM ディレクティブをカスタマイズする

PHP_INI_USER、PHP_INI_PERDIR、および PHP_INI_ALL ディレクティブ ([php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を参照) をカスタマイズするには、 *.htaccess* ファイルをアプリのルート ディレクトリに追加します。

*.htaccess* ファイルで、`php_value <directive-name> <value>` 構文を使用してディレクティブを追加します。 次に例を示します。

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

変更内容でアプリを再デプロイし、再起動します。 Kudu でデプロイする場合 (たとえば [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) を使用)、デプロイ後に自動的に再起動します。

*.htaccess* を使用する代わりに、アプリで [ini_set()](https://www.php.net/manual/function.ini-set.php) を使用して、これらの非 PHP_INI_SYSTEM ディレクティブをカスタマイズできます。

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM ディレクティブをカスタマイズする

PHP_INI_SYSTEM ディレクティブをカスタマイズするには ([php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を参照)、 *.htaccess* アプローチは使用できません。 App Service は、`PHP_INI_SCAN_DIR` アプリ設定を使用して、別のメカニズムを提供します。

最初に、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行して、`PHP_INI_SCAN_DIR` というアプリ設定を追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` は、*php.ini* が存在している既定のディレクトリです。 `/home/site/ini` は、カスタム *.ini* ファイルを追加するカスタム ディレクトリです。 `:` で値を区切ります。

Linux コンテナーを含む Web SSH セッションに移動します (`https://<app-name>.scm.azurewebsites.net/webssh/host`)。

`/home/site` に `ini` というディレクトリを作成し、続いてカスタマイズするディレクティブを使用した *.ini* ファイル (たとえば、*settings.ini*) を `/home/site/ini` ディレクトリに作成します。 *php.ini* ファイルで使用するものと同じ構文を使用します。 

> [!TIP]
> App Service でのビルトイン Linux コンテナーで、 */home* は永続化された共有ストレージとして使用されます。 
>

たとえば、[expose_php](https://php.net/manual/ini.core.php#ini.expose-php) の値を変更するには、次のコマンドを実行します。

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

変更内容を有効にするには、アプリを再起動します。

## <a name="enable-php-extensions"></a>PHP 拡張機能を有効にする

ビルトイン PHP のインストールには、最もよく使用される拡張機能が含まれています。 [php.ini ディレクティブのカスタマイズ](#customize-php_ini_system-directives)と同じ方法で、追加の拡張機能を有効にすることができます。

> [!NOTE]
> PHP のバージョンと現在の *php.ini* 構成を確認する最善の方法は、アプリで [phpinfo()](https://php.net/manual/function.phpinfo.php) を呼び出すことです。
>

追加の拡張機能を有効にするには、次の手順に従います。

アプリのルート ディレクトリに `bin` ディレクトリを追加して、その中に `.so` 拡張ファイル (たとえば、*mongodb.so*) を配置します。 拡張機能が Azure の PHP バージョンと互換性があり、VC9 および非スレッドセーフ (nts) 互換であることを確認してください。

変更をデプロイします。

[PHP_INI_SYSTEM ディレクティブのカスタマイズ](#customize-php_ini_system-directives)の手順に従って、[extension](https://www.php.net/manual/ini.core.php#ini.extension) または [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) ディレクティブを使用したカスタム *.ini* ファイルに拡張機能を追加します。

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

変更内容を有効にするには、アプリを再起動します。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>トラブルシューティング

動作中の PHP アプリが App Service で異なる動作をしたり、エラーが発生した場合は、次のことを試してください。

- [ログ ストリームにアクセス](#access-diagnostic-logs)します。
- 実稼働モードでローカルにアプリをテストします。 App Service では、実稼働モードで Node.js アプリが実行されるので、プロジェクトがローカルで実稼働モードで予想どおりに動作することを確認する必要があります。 次に例を示します。
    - *composer.json* に応じて、実稼働モードに別々のパッケージ (`require` と `require-dev`) がインストールされる場合があります。
    - 特定の Web フレームワークでは、実稼働モードで静的ファイルを別にデプロイすることがあります。
    - 特定の Web フレームワークでは、実稼働モードで実行しているときにカスタム スタートアップ スクリプトを使用することがあります。
- デバッグ モードで Azure App Service でアプリを実行します。 たとえば、[Laravel](https://meanjs.org/) で、[`APP_DEBUG` アプリ設定を `true` に指定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)することにより、実稼働環境でのデバッグ メッセージを出力するようにアプリを構成できます。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:PHP アプリと MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](app-service-linux-faq.md)
