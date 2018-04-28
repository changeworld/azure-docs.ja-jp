---
title: Azure App Service へのローカル Git デプロイ
description: Azure App Service へのローカル Git デプロイを有効にする方法を説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 842cd6f67a04bec0ed06282bdeeea8b8a51c0667
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service へのローカル Git デプロイ

このハウツー ガイドでは、ローカル コンピューター上の Git リポジトリから [Azure App Service](app-service-web-overview.md) にコードをデプロイする方法を説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順に従うには: 

* [Git をインストールします](http://www.git-scm.com/downloads)。
* デプロイするコードでローカル Git リポジトリのメンテナンスを管理します。

サンプル リポジトリを使用して手順に従うには、ローカルのターミナル ウィンドウで次のコマンドを実行します。

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

## <a name="prepare-your-repository"></a>リポジトリを準備する

リポジトリのルートに、プロジェクトの適切なファイルがあることを確認してください。

| ランタイム | ルート ディレクトリのファイル |
|-|-|
| ASP.NET (Windows のみ) | _*.sln_、_*.csproj_、または _default.aspx_ |
| ASP.NET Core | _*.sln_ または _*.csproj_ |
| PHP | _index.php_ |
| Ruby (Linux のみ) | _Gemfile_ |
| Node.js | _server.js_、_app.js_、または _package.json_ とスタート スクリプト |
| Python (Windows のみ) | _\*.py_、_requirements.txt_、または _runtime.txt_ |
| HTML | _default.htm_、_default.html_、_default.asp_、_index.htm_、_index.html_、または _iisstart.htm_ |
| Web ジョブ | _App\_Data/jobs/continuous_ の _\<job_name>/run.\<extension>_ (継続的 WebJobs) または _App\_Data/jobs/triggered_ (トリガーされた WebJobs)。 詳細については、[Kudu WebJobs のドキュメント](https://github.com/projectkudu/kudu/wiki/WebJobs)をご覧ください |
| Functions | [Azure Functions の継続的なデプロイ](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements)に関するページをご覧ください。 |

デプロイをカスタマイズするには、_.deployment_ ファイルをリポジトリのルートに配置します。 詳細については、「[Custom Deployment (カスタム デプロイ)](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)」および「[Custom deployment script (カスタム デプロイ スクリプト)](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)」を参照してください。

> [!NOTE]
> デプロイするすべての変更に対して必ず `git commit` を実行します。
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>アプリの Git を有効にする

既存の App Service アプリの Git デプロイを有効にするには、Cloud Shell で [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) を実行します。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Git 対応アプリを作成するには、Cloud Shell で [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) を実行するときに `--deployment-local-git` パラメーターを指定します。

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` コマンドの出力は次のようになります。

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

## <a name="deploy-your-project"></a>プロジェクトのデプロイ

"_ローカル ターミナル ウィンドウ_" で、ローカル Git リポジトリに Azure リモートを追加します。 _\<url>_ を、「[アプリの Git を有効にする](#enable-git-for-you-app)」で取得した Git リモートの URL に置き換えます。

```bash
git remote add azure <url>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 パスワードの入力を求められたら、Azure Portal へのログインに使用するパスワードではなく、「[デプロイ ユーザーの構成](#configure-a-deployment-user)」で作成したパスワードを入力するようにしてください。

```bash
git push azure master
```

出力には、MSBuild (ASP.NET の場合)、`npm install` (Node.js の場合)、`pip install` (Python の場合) など、ランタイム固有のオートメーションが表示される場合があります。 

デプロイが完了すると、Azure Portal のアプリの **[デプロイ オプション]** ページに `git push` のレコードが表示されるはずです。

![](./media/app-service-deploy-local-git/deployment_history.png)

アプリに移動して、コンテンツがデプロイされていることを確認します。

## <a name="troubleshooting"></a>トラブルシューティング

Git を使用して Azure の App Service に発行するときの一般的なエラーまたは問題を以下に示します。

---
**症状**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**原因**: このエラーはアプリが実行されていない場合に発生する可能性があります。

**解決策**: Azure Portal でアプリを起動します。 Web アプリが停止している間は、Git デプロイは利用できません。

---
**症状**: `Couldn't resolve host 'hostname'`

**原因**: このエラーは、"azure" リモートを作成するときに入力したアドレス情報が間違っている場合に発生する可能性があります。

**解決策**: `git remote -v` コマンドを使用して、すべてのリモートおよび関連付けられている URL を一覧表示します。 "azure" リモートの URL が正しいことを確認します。 必要に応じて、このリモートを削除し、正しい URL を使用して再作成します。

---
**症状**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**原因**: このエラーは、`git push` でブランチを指定しなかった場合、または `.gitconfig` に `push.default` 値を設定しなかった場合に発生する可能性があります。

**解決策**: master ブランチを指定して、もう一度 `git push` を実行します。 例: 

```bash
git push azure master
```

---
**症状**: `src refspec [branchname] does not match any.`

**原因**: このエラーは、"azure" リモートの master 以外のブランチにプッシュしようとした場合に発生する可能性があります。

**解決策**: master ブランチを指定して、もう一度 `git push` を実行します。 例: 

```bash
git push azure master
```

---
**症状**: `RPC failed; result=22, HTTP code = 5xx.`

**原因**: このエラーは、HTTPS 経由で大規模な Git リポジトリをプッシュしようとした場合に発生する可能性があります。

**解決策**: ローカル コンピューター上の Git 構成を変更して postBuffer を増やします。

```bash
git config --global http.postBuffer 524288000
```

---
**症状**: `Error - Changes committed to remote repository but your web app not updated.`

**原因**: このエラーは、必要な追加モジュールを指定する _package.json_ ファイルを含む Node.js アプリをデプロイする場合に発生する可能性があります。

**解決策**: "npm ERR!" を含む追加のメッセージが、 このエラーの前にログに記録されます。このメッセージによって、このエラーに関する追加のコンテキストが提供される場合があります。 このエラーの既知の原因と、対応する "npm ERR!" メッセージを以下に示します。 メッセージ:

* **形式が正しくない package.json ファイル**: npm ERR! Couldn't read dependencies.
* **Windows 用のバイナリ配布がないネイティブ モジュール**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      または
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>その他のリソース

* [Project Kudu に関するドキュメント](https://github.com/projectkudu/kudu/wiki)
* [Azure App Service への継続的なデプロイ](app-service-continuous-deployment.md)
* [サンプル: Web アプリを作成してローカル Git リポジトリからコードをデプロイする (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [サンプル: Web アプリを作成してローカル Git リポジトリからコードをデプロイする (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
