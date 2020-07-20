---
title: Windows Node.js アプリを構成する
description: App Service のネイティブ Windows インスタンスで Node.js アプリを構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907994"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Azure App Service 向けの Windows Node.js アプリを構成する

Node.js アプリは、必要なすべての NPM 依存関係と共にデプロイする必要があります。 App Service 展開エンジンは、[Git リポジトリ](deploy-local-git.md)をデプロイするとき、またはビルド自動化が有効になっている [Zip パッケージ](deploy-zip.md)をデプロイするときに、`npm install --production` を自動的に実行します。 ただし、[FTP/S](deploy-ftp.md) を使用してファイルをデプロイする場合、必要なパッケージを手動でアップロードする必要があります。 Linux アプリに関する詳細については、「[Azure App Service 向けの Linux PHP アプリを構成する](containers/configure-language-nodejs.md)」を参照してください。

このガイドでは、App Service にデプロイする Node.js 開発者向けに主要な概念と手順を説明します。 Azure App Service を使用したことがない場合は、まず、[Node.js クイック スタート](app-service-web-get-started-nodejs.md)と [MongoDB を使った Node.js のチュートリアル](app-service-web-tutorial-nodejs-mongodb-app.md)に従ってください。

## <a name="show-nodejs-version"></a>Node.js のバージョンを表示する

現在の Node.js バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

サポートされているすべての Node.js バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Node.js のバージョンを設定する

[サポートされている Node.js バージョン](#show-nodejs-version)にアプリを設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行して、`WEBSITE_NODE_DEFAULT_VERSION` をサポートされているバージョンに設定します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

この設定では、実行時と、App Service のビルド自動化中のパッケージの自動復元時の両方で使用する Node.js のバージョンを指定します。

> [!NOTE]
> プロジェクトの `package.json` で Node.js バージョンを設定する必要があります。 展開エンジンは、サポートされているすべての Node.js バージョンを含む別のプロセスで実行されます。

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](configure-common.md)できます。 その後、標準の Node.js パターンを使用して、それらにアクセスできます。 たとえば、`NODE_ENV` というアプリ設定にアクセスするには、次のコードを使用します。

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp を実行する

既定では、App Service のビルド自動化は、Node.js アプリが Git (またはビルド自動化が有効になっている Zip デプロイ) を介してデプロイされることを認識すると、`npm install --production` を実行します。 アプリで、Grunt、Bower、Gulp など、一般的な自動化ツールのいずれかが必要な場合、それを実行する[カスタム デプロイ スクリプト](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)を提供する必要があります。

リポジトリでこれらのツールを実行できるようにするには、*package.json* での依存関係にこれらを追加する必要があります。 次に例を示します。

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

ローカル ターミナル ウィンドウから、ディレクトリをリポジトリのルートに変更し、次のコマンドを実行します。

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

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されているかどうかをアプリ ロジックが確認する必要がある場合は、`X-Forwarded-Proto` ヘッダーを調べます。

一般的な Web フレームワークでは、標準のアプリ パターンで `X-Forwarded-*` 情報にアクセスできます。 [Express](https://expressjs.com/) で、[trust proxy](https://expressjs.com/guide/behind-proxies.html) を使用できます。 次に例を示します。

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>トラブルシューティング

動作中の Node.js アプリが App Service で異なる動作をしたり、エラーが発生した場合は、次のことを試してください。

- [ログ ストリームにアクセス](#access-diagnostic-logs)します。
- 実稼働モードでローカルにアプリをテストします。 App Service では、実稼働モードで Node.js アプリが実行されるので、プロジェクトがローカルで実稼働モードで予想どおりに動作することを確認する必要があります。 次に例を示します。
    - *package.json* に応じて、実稼働モードに別々のパッケージ (`dependencies` と `devDependencies`) がインストールされる場合があります。
    - 特定の Web フレームワークでは、実稼働モードで静的ファイルを別にデプロイすることがあります。
    - 特定の Web フレームワークでは、実稼働モードで実行しているときにカスタム スタートアップ スクリプトを使用することがあります。
- 開発モードの App Service でアプリを実行します。 たとえば、[MEAN.js](https://meanjs.org/) で、[`NODE_ENV` アプリ設定を指定する](configure-common.md)ことによって、実行時に開発モードにアプリを設定できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Node.js アプリと MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

