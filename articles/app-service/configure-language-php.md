---
title: Windows PHP アプリを構成する
description: App Service のネイティブ Windows インスタンスで PHP アプリを構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1eb4e9d349fdd0097cbde4e4cef3d5c61a167193
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907987"
---
# <a name="configure-a-windows-php-app-for-azure-app-service"></a>Azure App Service 向けの Windows PHP アプリを構成する

このガイドでは、Azure App Service の PHP Web アプリ、モバイル バックエンド、API アプリを構成する方法を示します。 このガイドは、Windows プラットフォームでホストされているアプリに適用されます。 Linux アプリに関する詳細については、「[Azure App Service 向けの Linux PHP アプリを構成する](containers/configure-language-php.md)」を参照してください。

このガイドでは、App Service にアプリをデプロイする PHP 開発者向けに主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、最初に [PHP クイック スタート](app-service-web-get-started-php.md)と [PHP と MySQL のチュートリアル](app-service-web-tutorial-php-mysql.md)に従ってください。

## <a name="show-php-version"></a>PHP バージョンを表示する

現在の PHP バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

サポートされているすべての PHP バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes | grep php
```

## <a name="set-php-version"></a>PHP バージョンを設定する

PHP バージョンを 7.4 に設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

## <a name="run-composer"></a>Composer を実行する

デプロイ時に App Service で [Composer](https://getcomposer.org/) を実行する場合、最も簡単な方法は、Composer をリポジトリに含めることです。

ローカルのターミナル ウィンドウから、リポジトリのルートにディレクトリを変更し、[Download Composer](https://getcomposer.org/download/) (Composer のダウンロード) の指示に従い、ディレクトリ ルートに *composer.phar* をダウンロードします。

次のコマンドを実行します ([npm](https://www.npmjs.com/get-npm) をインストールしておく必要があります)。

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

リポジトリのルートには現在、 *.deployment* と *deploy.sh* の 2 つの追加ファイルがあります。

*deploy.sh* を開き、次のような `Deployment` セクションを見つけます。

```bash
##################################################################################################################################
# Deployment
# ----------
```

`Deployment` セクションの*末尾*に必要なツールの実行に必要なコード セクションを追加します。

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Git か、ビルド自動化を有効にした Zip デプロイを利用してすべての変更をコミットし、コードをデプロイします。 これで Composer はデプロイの自動化の一部として実行しているはずです。

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp を実行する

Grunt、Bower、Gulp など、一般的な自動化ツールを App Service でデプロイ時に実行する場合、[カスタム デプロイ スクリプト](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)を提供する必要があります。 App Service では、Git か、ビルド自動化を有効にした [Zip デプロイ](deploy-zip.md)を利用してデプロイするとき、このスクリプトが実行されます。 

リポジトリでこれらのツールを実行できるようにするには、*package.json* での依存関係にこれらを追加する必要があります。 次に例を示します。

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

ローカル ターミナル ウィンドウから、ディレクトリをリポジトリのルートに変更し、次のコマンドを実行します ([npm](https://www.npmjs.com/get-npm) をインストールしておく必要があります)。

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

リポジトリのルートには現在、 *.deployment* と *deploy.sh* の 2 つの追加ファイルがあります。

*deploy.sh* を開き、次のような `Deployment` セクションを見つけます。

```bash
##################################################################################################################################
# Deployment
# ----------
```

このセクションは、`npm install --production` の実行で終わります。 `Deployment` セクションの*末尾*に必要なツールの実行に必要なコード セクションを追加します。

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

[MEAN.js サンプルでの例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)を参照してください。ここでは、デプロイ スクリプトはカスタム `npm install` コマンドも実行します。

### <a name="bower"></a>Bower

このスニペットは `bower install` を実行します。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

このスニペットは `gulp imagemin` を実行します。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

このスニペットは `grunt` を実行します。

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](configure-common.md#configure-app-settings)できます。 その後、標準の [getenv()](https://secure.php.net/manual/function.getenv.php) パターンを使用して、それらにアクセスできます。 たとえば、`DB_HOST` というアプリ設定にアクセスするには、次のコードを使用します。

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>サイト ルートを変更する

選択した Web フレームワークでは、サイト ルートとしてサブディレクトリを使用できます。 たとえば、[Laravel](https://laravel.com/) では、サイト ルートとして *public/* サブディレクトリが使用されます。

サイト ルートをカスタマイズするには、[`az resource update`](/cli/azure/resource#az-resource-update) コマンドを利用し、アプリの仮想アプリケーション パスを設定します。 次の例では、リポジトリの *public/* サブディレクトリにサイト ルートが設定されます。 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

既定では、Azure App Service は、デプロイされたアプリケーション ファイルのルート ディレクトリ (_sites\wwwroot_) に対して仮想アプリケーションのルート パス ( _/_ ) をポイントします。

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
> PHP のバージョンと現在の *php.ini* 構成を確認する最善の方法は、アプリで [phpinfo()](https://php.net/manual/function.phpinfo.php) を呼び出すことです。
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a> 非 PHP_INI_SYSTEM ディレクティブをカスタマイズする

PHP_INI_USER、PHP_INI_PERDIR、PHP_INI_ALL ディレクティブ ([php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を参照) をカスタマイズするには、`.user.ini` ファイルをアプリのルート ディレクトリに追加します。

`php.ini` ファイルで使用するものと同じ構文を使用して、構成設定を `.user.ini` ファイルに追加します。 たとえば、`display_errors` 設定をオンにして `upload_max_filesize` を 10 M に設定する場合は、`.user.ini` ファイルに次のテキストを含めます。

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

変更内容でアプリを再デプロイし、再起動します。

`.user.ini` ファイルを使用する代わりに、アプリで [ini_set()](https://www.php.net/manual/function.ini-set.php) を使用し、これらの非 PHP_INI_SYSTEM ディレクティブをカスタマイズできます。

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM ディレクティブをカスタマイズする

PHP_INI_SYSTEM ディレクティブをカスタマイズするには ([php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を参照)、 *.htaccess* アプローチは使用できません。 App Service は、`PHP_INI_SCAN_DIR` アプリ設定を使用して、別のメカニズムを提供します。

最初に、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行して、`PHP_INI_SCAN_DIR` というアプリ設定を追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Kudu コンソール (`https://<app-name>.scm.azurewebsites.net/DebugConsole`) に移動し、`d:\home\site` に移動します。

`d:\home\site` に `ini` というディレクトリを作成し、続いてカスタマイズするディレクティブを使用した *.ini* ファイル (たとえば、*settings.ini*) を `d:\home\site\ini` ディレクトリに作成します。 *php.ini* ファイルで使用するものと同じ構文を使用します。 

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

```
extension=d:\home\site\wwwroot\bin\mongodb.so
zend_extension=d:\home\site\wwwroot\bin\xdebug.so
```

変更内容を有効にするには、アプリを再起動します。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

標準の [error_log()](https://php.net/manual/function.error-log.php) ユーティリティを使用し、Azure App Service に診断ログを表示させます。

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>トラブルシューティング

動作中の PHP アプリが App Service で異なる動作をしたり、エラーが発生した場合は、次のことを試してください。

- [ログ ストリームにアクセス](#access-diagnostic-logs)します。
- 実稼働モードでローカルにアプリをテストします。 App Service では、アプリが実稼働モードで実行されるので、プロジェクトがローカルで実稼働モードで予想どおりに動作することを確認する必要があります。 次に例を示します。
    - 特定の Web フレームワークでは、実稼働モードで静的ファイルを別にデプロイすることがあります。
    - 特定の Web フレームワークでは、実稼働モードで実行しているときにカスタム スタートアップ スクリプトを使用することがあります。
- デバッグ モードで Azure App Service でアプリを実行します。 たとえば、[Laravel](https://meanjs.org/) で、[`APP_DEBUG` アプリ設定を `true` に指定](configure-common.md#configure-app-settings)することにより、実稼働環境でのデバッグ メッセージを出力するようにアプリを構成できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:PHP アプリと MySQL](app-service-web-tutorial-php-mysql.md)
