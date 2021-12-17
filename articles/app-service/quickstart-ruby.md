---
title: クイック スタート:Ruby アプリを作成する
description: Azure App Service で Linux コンテナーに初めての Ruby アプリをデプロイして、App Service の使用を開始します。
keywords: Azure App Service、Linux、OSS、Ruby、Rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 04/27/2021
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: dffb2634b59c54632364d8469244edb6840d7b9d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435422"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>App Service で Ruby on Rails アプリを作成する

[Azure App Service on Linux](overview.md#app-service-on-linux) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイックスタート チュートリアルでは、[Cloud Shell](../cloud-shell/overview.md) を使用して App Service on Linux に Ruby on Rails アプリをデプロイする方法を示します。

> [!NOTE]
> この時点で、Ruby 開発スタックは Ruby on Rails のみをサポートしています。 別のプラットフォーム (Sinatra など) を使用する場合や、サポートされていない Ruby バージョンを使用する場合は、[カスタム コンテナーで実行](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux)する必要があります。

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Ruby 2.6 以降をインストールする</a>
* <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>

## <a name="download-the-sample"></a>サンプルのダウンロード

1. ターミナル ウィンドウで、お使いのローカル コンピューターにサンプル アプリケーションを複製し、サンプル コードを含むディレクトリに移動します。 

    ```bash
    git clone https://github.com/Azure-Samples/ruby-docs-hello-world
    cd ruby-docs-hello-world
    ```

1. 既定のブランチが `main` であることを確認します。

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > App Service では、ブランチ名の変更は必要ありません。 ただし、多くのリポジトリで既定のブランチが `main` に変更されているため、このチュートリアルでは、`main` からリポジトリをデプロイする方法も示します。 詳細については、「[デプロイ ブランチを変更する](deploy-local-git.md#change-deployment-branch)」を参照してください。

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する

1. 必要な gem をインストールします。 このサンプルには `Gemfile` が含まれているので、次のコマンドを実行するだけです。

    ```bash
    bundle install
    ```

1. gem がインストールされたら、アプリを起動します。

    ```bash
    bundle exec rails server
    ```

1. Web ブラウザーで `http://localhost:3000` に移動して、ローカルでアプリケーションをテストします。

    ![構成された Hello World](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Web アプリを作成する

1. `myAppServicePlan` App Service プランで [Web アプリ](overview.md#app-service-on-linux)を作成します。 

    Cloud Shell で、[`az webapp create`](/cli/azure/webapp) コマンドを使用することができます。 次の例では、`<app-name>` をグローバルに一意のアプリ名に置き換えてください (有効な文字は `a-z`、`0-9`、`-`)。 ランタイムは `RUBY|2.6` に設定されています。 サポートされているすべてのランタイムを確認するには、[`az webapp list-runtimes --linux`](/cli/azure/webapp) を実行します。 

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'RUBY|2.6' --deployment-local-git
    ```

    Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

    <pre>
    Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
      "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
      "enabled": true,
      &lt; JSON data removed for brevity. &gt;
    }
    </pre>
    
    Git デプロイが有効な、空の新しい Web アプリが作成されました。

    > [!NOTE]
    > Git リモートの URL は `deploymentLocalGitUrl` プロパティに `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` 形式で出力されます。 この URL は後で必要になるので保存しておいてください。
    >

1. アプリを参照して、新たに作成された、組み込みイメージを使用する Web アプリを確認します。 _&lt;app_name>_ は、実際の Web アプリの名前に置き換えます。

    ```bash
    http://<app_name>.azurewebsites.net
    ```

    新しい Web アプリは次のようになります。

    ![スプラッシュ ページ](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>アプリケーションをデプロイする

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure-no-h.md)] 

   <pre>
   remote: Using turbolinks 5.2.0
   remote: Using uglifier 4.1.20
   remote: Using web-console 3.7.0
   remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
   remote: Bundled gems are installed into `/tmp/bundle`
   remote: Zipping up bundle contents
   remote: .......
   remote: ~/site/repository
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   remote: App container will begin restart within 10 seconds.
   To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
      a6e73a2..ae34be9  main -> main
   </pre>

## <a name="browse-to-the-app"></a>アプリの参照

デプロイが完了したら、Web アプリが再起動するまで約 10 秒待ってから、Web アプリに移動して結果を確認します。

```bash
http://<app-name>.azurewebsites.net
```

![更新後の Web アプリ](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> アプリの再起動中に、ブラウザーまたは `Error 503 Server unavailable` の既定のページで、HTTP 状態コード `Hey, Ruby developers!` が表示される場合があります。 アプリが完全に再起動するまで、数分かかる場合があります。
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Ruby on Rails と Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Ruby アプリを構成する](configure-language-ruby.md)
