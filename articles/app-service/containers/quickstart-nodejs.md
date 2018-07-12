---
title: Azure App Service on Linux での Node.js の作成 | Microsoft Docs
description: Azure App Service on Linux で、初めての Node.js の Hello World を数分でデプロイします。
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/07/2017
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 44c3f8ce05854e993ad551a025eec447d882c326
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969546"
---
# <a name="create-a-nodejs-web-app-in-azure-app-service-on-linux"></a>Azure App Service on Linux での Node.js Web アプリの作成

> [!NOTE]
> この記事では、Linux 上の App Service にアプリをデプロイします。 _Windows_ 上の App Service にデプロイするには、「[Azure で Node.js Web アプリを作成する](../app-service-web-get-started-nodejs.md)」を参照してください。
>

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイックスタートでは、[Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を使用して App Service on Linux に Node.js アプリをデプロイする方法を示します。

このクイックスタートは Cloud Shell で行いますが、これらのコマンドは [Azure CLI](/cli/azure/install-azure-cli) を使用してローカルで実行することもできます。

![Azure で実行されるサンプル アプリ](media/quickstart-nodejs/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-web-app-extension-for-cloud-shell"></a>Cloud Shell の Web アプリ拡張機能をインストールする

このクイックスタートを完了するには、[az Web アプリ拡張機能](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add)を追加する必要があります。 この拡張機能が既にインストールされている場合は、最新バージョンに更新してください。 Web アプリ拡張機能を更新するには、「`az extension update -n webapp`」と入力します。

Web アプリ拡張機能をインストールするには、次のコマンドを実行します。

```bash
az extension add -n webapp
```

拡張機能がインストールされると、Cloud Shell に次の例の情報が表示されます。

```bash
The installed extension 'webapp' is in preview.
```

## <a name="download-the-sample"></a>サンプルのダウンロード

Cloud Shell で、クイックスタートのディレクトリを作成し、それに変更します。

```bash
mkdir quickstart

cd quickstart
```

次に、次のコマンドを実行して、サンプル アプリのリポジトリをクイックスタートのディレクトリに複製します。

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

実行中、次の例のような情報が表示されます。

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
````

## <a name="create-a-web-app"></a>Web アプリを作成する

サンプル コードが含まれているディレクトリに移動し、`az webapp up` コマンドを実行します。

次の例で、<app_name> を一意のアプリ名に置き換えます。

```bash
cd nodejs-docs-hello-world

az webapp up -n <app_name>
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。

```json
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Updating app settings to enable build after deployment
Creating zip with contents of dir /home/username/quickstart/nodejs-docs-hello-world ...
Preparing to deploy and build contents to app.
Fetching changes.

Generating deployment script.
Generating deployment script.
Generating deployment script.
Running deployment command...
Running deployment command...
Running deployment command...
Deployment successful.
All done.
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "STANDARD",
  "src_path": "/home/username/quickstart/nodejs-docs-hello-world ",
  "version_detected": "6.9",
  "version_to_create": "node|6.9"
}
```

`az webapp up` コマンドは、次の処理を実行します。

- 既定のリソース グループを作成する。

- 既定の App Service プランを作成する。

- 指定された名前でアプリを作成する。

- 現在の作業ディレクトリから Web アプリにファイルを [zip してデプロイ](https://docs.microsoft.com/azure/app-service/app-service-deploy-zip)する。

## <a name="browse-to-the-app"></a>アプリの参照

Web ブラウザーを使用して、デプロイされたアプリケーションを参照します。 <app_name> は、Web アプリの名前に置き換えます。

```bash
http://<app_name>.azurewebsites.net
```

Node.js のサンプル コードが、組み込みイメージを使用する Web アプリで実行されています。

![Azure で実行されるサンプル アプリ](media/quickstart-nodejs/hello-world-in-browser.png)

**お疲れさまでした。** App Service on Linux に初めての Node.js アプリをデプロイしました。

## <a name="update-and-redeploy-the-code"></a>コードを更新して再デプロイする

Cloud Shell で、「`nano index.js`」と入力して nano テキスト エディターを開きます。

![nano index.js](media/quickstart-nodejs/nano-indexjs.png)

 `response.end` を呼び出すテキストに小さな変更を加えます。

```nodejs
response.end("Hello Azure!");
```

変更内容を保存し、nano を終了します。 コマンド `^O` を使用して保存し、`^X` を使用して終了します。

これでアプリを再デプロイします。 Web アプリの代わりに `<app_name>` を使用します。

```bash
az webapp up -n <app_name>
```

デプロイが完了したら、「**アプリの参照**」の手順で開いたブラウザー ウィンドウに戻り、ページを更新します。

![Azure で実行される更新済みのサンプル アプリ](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>新しい Azure Web アプリを管理する

<a href="https://portal.azure.com" target="_blank">Azure Portal</a> に移動し、作成した Web アプリを管理します。

左側のメニューで **[App Services]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

Web アプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクを完了できます。

![Azure Portal の [App Service] ページ](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

左側のメニューは、アプリを構成するためのさまざまなページを示しています。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来不要であると思われる場合は、Cloud Shell からリソース グループを削除します。 リージョンを変更した場合は、リソース グループ名 `appsvc_rg_Linux_CentralUS` をアプリ固有のリソース グループに更新します。

```azurecli-interactive
az group delete --name appsvc_rg_Linux_CentralUS
```

このコマンドの実行には、少し時間がかかる場合があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Node.js と MongoDB](tutorial-nodejs-mongodb-app.md)
